---
title: A Web Crawler With asyncio Coroutines
date: 2016-08-28 13:18:12
tags: [Python, 500Lines or Less]
category: Python

---

原文地址：[http://aosabook.org/en/500L/a-web-crawler-with-asyncio-coroutines.html](http://aosabook.org/en/500L/a-web-crawler-with-asyncio-coroutines.html)

## The Traditional Approach

传统的写法，创建一个线程池，每个线程通过socket处理一个页面，下载`xkcd.com`:

    def fetch(url):
        sock = socket.socket()
        sock.connect(('xkcd.com', 80))
        request = 'GET {} HTTP/1.0\r\nHost: xkcd.com\r\n\r\n'.format(url)
        sock.send(request.encode('ascii'))
        response = b''
        chunk = sock.recv(4096)
        while chunk:
            response += chunk
            chunk = sock.recv(4096)

        # Page is now downloaded
        links = parse_links(response)
        q.add(links)

socket的操作都是阻塞操作。如果在同一时刻需要处理大量的页面就需要大量的线程。通常一个成熟的应用程序会通过使用线程池来管理线程达到减少不断创建线程导致的性能消耗。

使用线程是非常消耗系统资源的


<!--more-->

## Async
异步框架(Asynchronous I/O frameworks),保证在一个线程中执行异步操作。

在连接前设置socket不阻塞

    sock = socket.socket()
    sock.setblocking(False)
    try:
        sock.connect(('xkcd.com', 80))
    except BlockingIOError:
        pass

但是，设置了不阻塞后socket会报错，即时是程序正常运行了

    request = 'GET {} HTTP/1.0\r\nHost: xkcd.com\r\n\r\n'.format(url)
    encoded = request.encode('ascii')
    
    while True:
        try:
            sock.send(encoded)
            break
        except OSError as e:
            pass
    print('sent')

上面的方法不能实现多请求的情况，需要使用Python3.4中的`DefaultSelector`

    from selectors import DefaultSelector, EVENT_WRITE

    selector = DefaultSelector()

    sock = socket.socket()
    sock.setblocking(False)
    try:
        sock.connect(('xkcd.com', 80))
    except BlockingIOError:
        pass

    def connected():
        selector.unregister(sock.fileno())
        print('connected!')

    selector.register(sock.fileno(), EVENT_WRITE, connected)

上面的方法忽略了伪报错，并调用`selector.register`方法。

处理IO事件当selector接受到事件，在loop中：

    def loop():
        while True:
            events = selector.select()
            for event_key, event_mask in events:
                callback = event_key.data
                callback()

上面`event_key.data`就是之前`selector.register`中的参数3，回调方法，在socket连接上的时候就会执行


## Programming With Callbacks

    urls_todo = set(['/'])
    seen_urls = set(['/'])

抓取一个页面需要发起多个callbakcs，`connected`回调方法会在socket连接后执行

把所有的callbacks都放在`Fetcher`对象中，它需要URL，socket，响应的容器

    class Fetcher:
        def __init__(self, url):
            self.response = b''
            self.url = url
            self.sock = None

启动方法：

    # Method on Fetcher class
    def fetch(self):
        self.sock = socket.socket()
        self.sock.setblocking(False)
        try:
            self.sock.connect(('xkcd.com'),80)
        except BlockingIOError:
            pass

        # Register next callback
        selector.register(self.sock.fileno(), EVENT_WRITE, self.connected)

`fetch`方法开始连接一个socket，但是这个方法在建立连接前就会返回，所以需要使用event loop来等待连接建立：

    # Begin fetching http://xkcd.com/353/
    fetcher = Fetcher('/353/')
    fetcher.fetch()

    while True:
        events = selector.select()
        for event_key, event_mask in events:
            callback = event_key.data
            callback(event_key, event_mask)

    # Method on Fetcher class
    def connected(self, key, mask):
        print('connected !')
        selector.unregister(key.fd)
        request = 'GET {} HTTP/1.0\r\nHost:xkcd.com\r\n\r\n.format(self.url)'
        self.sock.send(request.encode('ascii'))

        #Register the next callback.
        selector.register(key.fd, EVENT_READ, self.read_response)

这个方法发送一个GET请求，其他的方法会去处理接受到的结果：

    # Method On Fetch class
    def read_response(self, key, mask):
        global stopped

        chunk = self.sock.recv(4096)
        if chunk:
            self.response += chunk
        else:
            selector.unregister(key.fd) # Done reading
            links = self.parse_links()

            #Python set-logic:
            for link in links.difference(seen_urls):
                urls.todo.add(link)
                Fetcher(link).fetch() # New Fetcher.

            seen_urls.update(links)
            urls_todo.remove(self.url)
            if not urls_todo:
                stopped = True

添加的全局变量`stopped`在loop中使用：

    stopped = False

    def loop():
        while not stopped:
            events = selector.select()
            for event_key, event_mask in events:
                callback = event_key.data
                callback()

当所有的页面都下载后就停止`stopped`

## Coroutines

`aiohttp`库：

    @asyncio.coroutine
    def fetch(self, url)
        response = yield from self.senssion.get(url)
        body = yield from response.read()

创建一个线程消耗50k内存，创建一个协程子需要3k

多线程的时候由系统决定运行哪个线程，而多协程中它们自己控制停止和开始，运行哪个协程。

有很多创建协程的方法，标准的方法就是`asyncio`库，基于generator

## How Python Generators Work

一个generator方法：

    def gen_fn():
        result = yield 1
        print('result of yield:{}'.format(result))
        result2 = yield 2
        print('result of 2nd yield:{}'.format(result2))
        return 'done'

当Python解释器处理上面的方法的时候，看待`yield`就会知道这个方法属于`generator`

Python在运行`generator`的时候不会执行函数，而是会生成一个`generator`对象

    gen = gen_fn()
    type(gen)  # <class 'generator'>

    gen.send('hello') # result of yield: hello
    gen.send('goodbye') # result of yield: goodbye

## Building Coroutines With Generators

    class Future:
        def __init__(self):
            self.result = None
            self._callbacks = []

        def add_done_callback(self, fn):
            self._callbacks.append(fn)

        def set_result(self, result):
            self.result = result
            for fn in self._callbacks:
                fn(self)

    class Fetcher:
        def fetch(self):
            self.sock = socket.socket()
            self.sock.setblocking(False)
            try:
                self.sock.connect(('xkcd.com', 80))
            except BlockingIOError:
                pass
            selector.register(self.sock.fileno(), EVENT_WRITE, self.connected)

        def connected(self, key, mask):
            print('connected!..')
            # And so on..

`fetch`方法开始连接socket，然后注册回调方法，当网络连接后，执行回调方法，现在将这两个步骤放在一个协程中：

    def fetch(self):
        sock = socket.socket()
        sock.setblocking(False)
        try:
            sock.connect(('xkcd.com', 80))
        except BlockingIOError:
            pass

        f = Future()

        def on_connected():
            f.set_result(None)

        selector.register(sock.fileno(), EVENT_WRITE, on_connected)
        yield f
        selector.unregister(sock.fileno())
        print('connected!')

现在`fetch`一个`generator`方法。

如何恢复generator，需要一个coroutine driver：

    class Task:
        def __init__(self, coro):
            self.coro = coro
            f = Future()
            f.set_result(None)
            self.step(f)

        def step(self, future):
            try:
                next_future = self.coro.send(future.result)
            except StopIteration:
                return

            next_future.add_done_callback(self.step)

    # Begin fetching http://xkcd.com/353/
    fetcher = Fetcher('/353/')
    Task(fetcher.fetch())

    loop()

task通过发送一个`None`来开启`generator`的`fetch`,`fetch`运行到`yield`的


## Factoring Coroutines With yield from

    def fetch(self):
        # ... connection logic from above, then:
        sock.send(request.encode('ascii'))

        while True:
            f = Future()

            def on_readable():
                f.set_result(sock.recv(4096))

            selector.register(sock.fileno(), EVENT_READ, on_readable)
            chunk = yield f
            selector.unregister(sock.fileno())
            if chunk:
                self.response += chunk
            else:
                # Done reading
                break
上面的代码从socket中读取内容

用简单的代码解释 `yield from`:

    def gen_fn():
        result = yield 1
        print('result of yield: {}'.format(result))
        result = yield 2
        print('result of 2nd yield: {}'.format(result2))
        return 'done'
这个generator跳到另一个generator，使用`yield from`

    # Generator function:
    def caller_fn():
        gen = gen_fn()
        rv = yield from gen
        print('return value of yield-from:{}'.format(rv))

    # Make a generator from the generator function
    caller = caller_fn()

The `caller` generator acts as if it were `gen`, the generator it is delegationg to:

    >>> caller.send(None) 
    1
    >>> caller.gi_frame.f_lasti
    15
    >>> caller.send('hello') 
    result of yield: hello 
    2
    >>> caller.gi_frame.f_lasti # Hasn't advanced.
    15
    >>> caller.send('goodbye')
    result of 2nd yield: goodbye
    return value of yield-from: done
    Traceback (most recent call last):
        File "<input>", line 1, in <module>
    StopIteration

协程可以把任务委派给子协程（通过 yield from），然后接收处理结果。


创建一个`read`协程：

    def read(sock):
        f = Future()

        def on_readable():
            f.set_result(sock.recv(4096))

        selector.register(sock.fileno(), EVENT_READ, on_readable)
        chunk = yield f # Read one chunk
        selector.unregister(sock.fileno())
        return chunk
创建一个`read`,内部由`read_all`协程，用来接收全部的信息：

    def read_all(sock):
        response = []
        # Read whole response.
        chunk = yield from read(sock)
        while chunk:
            response.append(chunk)
            chunk = yield from read(sock)

        return b''.join(response)

在运行的总流程中，开始：

    class Fetcher:
        def fetch(self):
            # ... connection logic from above, then:
            sock.send(request.encode('ascii'))
            self.response = yield from read_all(sock)

`Task`不需要修改，还是：

    Task(fetcher.fetch())
    loop()

为了。。。让Future类变成可迭代：

    # Method on Future class.
    def __iter__(Self):
        # Tell Task to resume me here.
        yield self
        return self.result

replace code:

    # f is a Future.
    yield f
...with this:

    # f is a Funture
    yield from f

    @asyncio.coroutine
    def fetch(self, url):
        response = yield from self.session.get(url)
        body = yield from response.read()

## Coordinating Coroutines
We began by describing how we want our crawler to work. Now it is time to implement it with asyncio coroutines.

我们从描述了爬虫如何工作，现在开始来用异步协程来实现它

>Our crawler will fetch the first page, parse its links, and add them to a queue. After this it fans out across the website, fetching pages concurrently. But to limit load on the client and server, we want some maximum number of workers to run, and no more. Whenever a worker finishes fetching a page, it should immediately pull the next link from the queue. We will pass through periods when there is not enough work to go around, so some workers must pause. But when a worker hits a page rich with new links, then the queue suddenly grows and any paused workers should wake and get cracking. Finally, our program must quit once its work is done.

爬虫会抓取第一个页面，解析其中的链接，并把它们加到列队中。然后爬取整个网站。为了限制负载，我们需要设置最大工作数。当一个worker爬取完一个链接后，又马上从queue中获取新的链接。我们会耗费时间当没有足够多的work，所以在这个时候需要关闭一部分worker，但是当一个worker解析页面发现大量的链接的时候，那些被停止的worker需要重新开启，最后，需要马上停止程序，当所有的work完成的时候。

>Imagine if the workers were threads. How would we express the crawler's algorithm? We could use a synchronized queue11 from the Python standard library. Each time an item is put in the queue, the queue increments its count of "tasks". Worker threads call task_done after completing work on an item. The main thread blocks on Queue.join until each item put in the queue is matched by a task_done call, then it exits.

想象一个下入锅没个worker是线程，那么该如何设置程序？我们可以使用`synchronized queue`。每个链接加入到queue中后，queue增加任务的数量。worker线程处理完work的时候调用`task_done`。主线程在`Queue.join`阻塞直到queue中的每个work都被处理完成，然后退出。

>Coroutines use the exact same pattern with an asyncio queue! First we import it

协程使用的是同一个模型！ 首先实现：

    try:
        from asyncio import JoinableQueue as Queue
    except ImportError:
        # In Python 3.5, asyncio.JoinableQueue is merged into Queue
        from asyncio import Queue

>We collect the workers' shared state in a crawler class, and write the main logic in its crawl method. We start crawl on a coroutine and run asyncio's event loop until crawl finishes:

将worker的共享状态存放在`crawler`类，在`crawl`方法中写主逻辑，在协程中开始`crawl`方法并且运行异步事件`loop`直到`crawl`方法执行完毕：

    loop = asyncio.get_evet_loop()
    crawler = crawling.Crawler('http://xkcd.com', max_redirect=10)
    loop.run_until_complete(crawler.crawl())

>The crawler begins with a root URL and max_redirect, the number of redirects it is willing to follow to fetch any one URL. It puts the pair (URL, max_redirect) in the queue. (For the reason why, stay tuned.)

用根url和max_redirect创建一个crawler，......, (后面解释原因)

    class Crawbler:
        def __init__(self, root_url, max_redirect):
            self.max_tasks = 10
            self.max_redirect = max_redirect
            self.q = Queue()
            self.seen_urls = set()

            # aiohttp's ClientSession does connection pooling and HTTP keep-alives for us.
            self.session = aiohttp.ClientSession(loop=loop)

            # Put (URL, max_redirest) in the queue.
            self.q.put((root_url, self.max_redirect))

>The number of unfinished tasks in the queue is now one. Back in our main script, we launch the event loop and the crawl method:

在queue中未完成的任务为1。回看主脚本，启动event loop并且开始`crawl`方法：

    loop.cun_until_complete(crawler.crawl())

>The crawl coroutine kicks off the workers. It is like a main thread: it blocks on join until all tasks are finished, while the workers run in the background.

`crawl`协程开始workers。它就像主线程：当workers在后台运行的时候，它阻塞在`join`直到所有的任务完成。

    @asyncio.coroutine
    def crawl(self):
        """ Run the crawler untill all work is done."""
        workers = [asyncio.Task(self.work()) for _ in range(self.max_tasks)]

        # When all works is done, exit.
        yield from self.q.join()
        for w in workers:
            w.cancel()

>
>If the workers were threads we might not wish to start them all at once. To avoid creating expensive threads until it is certain they are necessary, a thread pool typically grows on demand. But coroutines are cheap, so we simply start the maximum number allowed.

如果workers是线程，我们会希望他们不要马上开始。为了避免创建不必要的线程，通常会使用线程池来管理线程。但是协程对内存的消耗比线程小的多，所以开启大量的协程是可以的。

>It is interesting to note how we shut down the crawler. When the join future resolves, the worker tasks are alive but suspended: they wait for more URLs but none come. So, the main coroutine cancels them before exiting. Otherwise, as the Python interpreter shuts down and calls all objects' destructors, living tasks cry out:

需要注意的是如何关闭`crawler`，当`join`的未来决定的时候，worker开始运行，但是出于等待状态：等待更多的URLs。所以，主协程需要关闭这些worker在推出前。否则，当Python编译器停止的时候会报错：

    ERROR: asyncio:Task was destoryed but it is pending!

>And how does cancel work? Generators have a feature we have not yet shown you. You can throw an exception into a generator from outside:

那个`cancel`如何工作？Generators有一个功能，可以在generator外加一个exception：

    >>>gen = gen_fn()
    >>>gen.send(None) # Start the generator as usual.
    1
    >>> gen.throw(Exception('error'))

>The generator is resumed by throw, but it is now raising an exception. If no code in the generator's call stack catches it, the exception bubbles back up to the top. So to cancel a task's coroutine:

generator被`throw`恢复，当是发送了一个异常。如果generator内部没有catches这个异常，这个异常就会停止这个协程：

    # Method of Task class.
    def cancel(self):
        self.coro.throw(CancelledError)

>Wherever the generator is paused, at some yield from statement, it resumes and throws an exception. We handle cancellation in the task's step method:

当一个协程停止的时候，它会抛出异常，我们需要在`strp`方法中处理异常：

    # Method of Task class.
    def step(self, future):
        try:
            next_future = self.coro.send(future.result)
        except CancelledError:
            self.cancelled = True
        except StopIteration:
            return

        next_future.add_done_callback(self.step)

>Now the task knows it is cancelled, so when it is destroyed it does not rage against the dying of the light.

现在task就知道被取消了。

>Once crawl has canceled the workers, it exits. The event loop sees that the coroutine is complete (we shall see how later), and it too exits:

当crawl取消所有workers，它就会退出。evet loop知道协程结束后也会退出：

    loop.run_unitl_complete(crawler.crawl())

>The crawl method comprises all that our main coroutine must do. It is the worker coroutines that get URLs from the queue, fetch them, and parse them for new links. Each worker runs the work coroutine independently:

`crawl`方法包含了主协程要处理的事情。获取从queue中获取URLs，抓取内容，解析内容获取新的链接。每个worker运行`work`独立：

    @asyncio.coroutine
    def work(self):
        while True:
            url, max_redirect = yield from self.q.get()

            # Download page and add new links to self.q.
            yield from self.fetch(uel, max_redirect)
            self.q.task_done()

>Python sees that this code contains yield from statements, and compiles it into a generator function. So in crawl, when the main coroutine calls self.work ten times, it does not actually execute this method: it only creates ten generator objects with references to this code. It wraps each in a Task. The Task receives each future the generator yields, and drives the generator by calling send with each future's result when the future resolves. Because the generators have their own stack frames, they run independently, with separate local variables and instruction pointers.

Python解析上面的代码含有`yield from`语句快，会把这个方法解析成`generator` 方法。所以`crawl`在主协程中执行`self.work`方法多次的时候，不会实际执行多次这个方法：而是创建了多个`generator`对象。它们都被`Task`接收，`Task`接收generator的yield，并且调用`send`来驱动generator。因为generator有各自的堆栈，所以它们是独立运行的，它们的本地变量也是独立的。

>The worker coordinates with its fellows via the queue. It waits for new URLs with:

工作协程和子协程交互通过：

    ur, max_redirect = yield from self.q.get()

>The queue's get method is itself a coroutine: it pauses until someone puts an item in the queue, then resumes and returns the item.

queue的`get`方法：它会阻塞直到有协程将任务存入queue，然后继续运行并返回任务

>Incidentally, this is where the worker will be paused at the end of the crawl, when the main coroutine cancels it. From the coroutine's perspective, its last trip around the loop ends when yield from raises a CancelledError.

另外，这里也是worker将会被停止的地方发，主协程调用`cancel`.从整个协程的工作流程中可以知道这是最后一个流程当`yeild from`返回一个`CanceledError`

>When a worker fetches a page it parses the links and puts new ones in the queue, then calls task_done to decrement the counter. Eventually, a worker fetches a page whose URLs have all been fetched already, and there is also no work left in the queue. Thus this worker's call to task_done decrements the counter to zero. Then crawl, which is waiting for the queue's join method, is unpaused and finishes.

当一个worker抓去一个页面的时候会解析页面中包含的新的链接，并把它们放到`queue`中，调用`task.done`来减小计数器。最终，一个worker抓去页面中的所有URLs，并且这些被抓取的URLs也被解析完成，没有多余的work在queue中。`task_done`将计数器降为0.然后`crawl`停止

>We promised to explain why the items in the queue are pairs, like:

解释为什么queue中加入work的时候是成对的：

    # URL to fetch, and the number of redirects left.
    ('http://xkcd.com/353', 10)

>New URLs have ten redirects remaining. Fetching this particular URL results in a redirect to a new location with a trailing slash. We decrement the number of redirects remaining, and put the next location in the queue:

新的URLs有十个重定向剩余，抓取有本网站的相关URL结果。减少数量，添加下一个位置到queue中：

    # URL with a trialing slash. Nine redirects left
    ('http://xkcd.com/353/', 9)

>the aiohttp package we use would follow redirects by default and give us the final response. We tell it not to, however, and handle redirects in the crawler, so it can coalesce redirect paths that lead to the same destination: if we have already seen this URL, it is in self.seen_urls and we have already started on this path from a different entry point:

![](http://aosabook.org/en/500L/crawler-images/redirects.png)

`aiohttp`工具会通过重定向规则返回最终的URL。

>The crawler fetches "foo" and sees it redirects to "baz", so it adds "baz" to the queue and to seen_urls. If the next page it fetches is "bar", which also redirects to "baz", the fetcher does not enqueue "baz" again. If the response is a page, rather than a redirect, fetch parses it for links and puts new ones in the queue.

`crawler`抓取“foo”，解析到了“baz”，将它加到了queue中。如果解析到“bar”不会将它加到队列中。

    @asyncio.coroutine
    def fetch(self, url, max_redirect):
        # Handle redirects ourselves.
        response = yield from self.session.get(url, allow_redirects = False)

        try:
            if is_redirect(response):
                if max_redirect > 0:
                    next_url = response.headers['location']
                    if next_url in self.seen_urls:
                        # We have been down this path before.
                        return

                    # Remember we have seen this URL.
                    self.seen_urls.add(next_url)

                    # Follow the redirect. One less redirect remains.
                    self.q.put_nowait((next_url, max_redirect - 1))
            else:
                links = yield from self.parse_links(response)
                # Python set-logic:
                for link in links.difference(self.seen_urls):
                    self.q.put_nowait((link, self.max_redirect))
                self.seen_urls.update(links)
        finally:
            # Return connection to pool.
            yield from response.release()

>If this were multithreaded code, it would be lousy with race conditions. For example, the worker checks if a link is in seen_urls, and if not the worker puts it in the queue and adds it to seen_urls. If it were interrupted between the two operations, then another worker might parse the same link from a different page, also observe that it is not in seen_urls, and also add it to the queue. Now that same link is in the queue twice, leading (at best) to duplicated work and wrong statistics.

如果上面的代码是多线程代码，会发生并发问题。比如，如果一个worker检查一个link是否属于`seen_urls`,并且还没有把链接加入到queue中。这个时候线程停止了，另一个线程进入处理同一个url地址在不同的页面中也去判断这个链接是否属于`seen_urls`就会出现两个相同的url加入到queue中。

>However, a coroutine is only vulnerable to interruption at yield from statements. This is a key difference that makes coroutine code far less prone to races than multithreaded code: multithreaded code must enter a critical section explicitly, by grabbing a lock, otherwise it is interruptible. A Python coroutine is uninterruptible by default, and only cedes control when it explicitly yields.

然而，协程只会在语句`yield from`下会停止，这使它和多线程不同。他可以通过代码控制协程的运行和停止，切换。而多线程的执行是随机的由cpu决定，只能通过锁来保证安全。

>We no longer need a fetcher class like we had in the callback-based program. That class was a workaround for a deficiency of callbacks: they need some place to store state while waiting for I/O, since their local variables are not preserved across calls. But the fetch coroutine can store its state in local variables like a regular function does, so there is no more need for a class.

我们不再需要`fetcher`类因为使用了回调程序。这个类是没有回调的一个解决方法：通过它来存储I/O状态，因为本地变量没有保存在跨协程中。但是`fetch`协程可以存储状态，所以就不需要这个类了。

>When fetch finishes processing the server response it returns to the caller, work. The work method calls task_done on the queue and then gets the next URL from the queue to be fetched.

当`fetch`处理服务器响应结束的时候返回到调用点`work`,`work`方法调用`task_done`在队列中然后获取下一个URL进行抓取。

>When fetch puts new links in the queue it increments the count of unfinished tasks and keeps the main coroutine, which is waiting for q.join, paused. If, however, there are no unseen links and this was the last URL in the queue, then when work calls task_done the count of unfinished tasks falls to zero. That event unpauses join and the main coroutine completes.

当`fetch`将一个新的链接加入到queue减少未完成任务数，。。。。。

>The queue code that coordinates the workers and the main coroutine is like this13:

queue协调workers和主协程的代码：

    class Queue:
        def __init__(self):
            self._join_future = Future()
            self._unfinished_tasks = 0
            # ... other intializatioin....

        def put_nowait(self, item):
            self._unfinished_tasks += 1
            # ... store the item ...

        def task.done(self):
            self._unfibished_tasks -= 1
            if self._unfinished_tasks == 0:
                self._join_future.set_result(None)

        @asyncio.coroutine
        def join(self):
            if self._unfinished_tasks > 0:
                yield from self._join_future

>The main coroutine, crawl, yields from join. So when the last worker decrements the count of unfinished tasks to zero, it signals crawl to resume, and finish.

主协程`crawl`方法代码`yeild from join`阻塞等待queue中没有任务的时候退出。

>The ride is almost over. Our program began with the call to crawl:

程序从调用`crawl`方法开始：

    loop.run_until_complete(self.crawler.crawl())

>How does the program end? Since crawl is a generator function, calling it returns a generator. To drive the generator, asyncio wraps it in a task:

程序如何停止？ 

    class EventLoop:
        def run_until_complete(self, coro):
            """ Run until the coroutine is done.  """
            task = Task(coro)
            task.add_done_callback(stop_callback)
            try:
                self.run_forever()
            except StopError
                pass

    class StopError(BaseException):
        """ Raised to stop the event loop. """

    def stop_callback(future):
        raise StopError

>When the task completes, it raises StopError, which the loop uses as a signal that it has arrived at normal completion.

当任务完成，抛出异常，loop捕获异常知道任务完成了。

>But what's this? The task has methods called add_done_callback and result? You might think that a task resembles a future. Your instinct is correct. We must admit a detail about the Task class we hid from you: a task is a future.

但是`add_done_callback(.)`是什么？。。。

    class Task(Future):
        """ A coroutine wrapped in a Future. """

>Normally a future is resolved by someone else calling set_result on it. But a task resolves itself when its coroutine stops. Remember from our earlier exploration of Python generators that when a generator returns, it throws the special StopIteration exception:

通常一个future被调用`set_result`决定了结果，但是task停止了他自己。会报异常`StopIteration`:

    # Method of class Task.
    def step(self, future):
        try:
            next_future = self.coro.send(future.result)
        except CancelledError:
            self.cancelled = True
            return 
        except StopIteration as exc:

            # Task resolves itself with coro's return value.
            self.set_result(exc.value)
            return

        next_future.add_done_callback(self.step)

>So when the event loop calls task.add_done_callback(stop_callback), it prepares to be stopped by the task. Here is run_until_complete again:

所以当

    # Method of event loop
    def run_until_complete(self, coro):
        task = Task(coro)
        task.add_done_callback(stop_callback)
        try:
            self.run_forever()
        except StopError:
            pass

>When the task catches StopIteration and resolves itself, the callback raises StopError from within the loop. The loop stops and the call stack is unwound to run_until_complete. Our program is finished.


