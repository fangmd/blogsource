---
title: Django Request/response objects
date: 2016-12-20 13:18:12
tags: [Django, Request, Response ]
category: Python

---


# HttpRequest

## Attributes

- HttpRequest.scheme
- HttpRequest.body
- HttpRequest.path

    Example: "/music/bands/the_beatles/"

- HttpRequest.method
- HttpRequest.encoding
- HttpRequest.content_type
- HttpRequest.content_params
- HttpRequest.GET
- HttpRequest.POST
- HttpRequest.COOKIES
- HttpRequest.FILES
- HttpRequest.META

    HTTP headers

    - CONTENT_LENGTH
    - CONTENT_TYPE
    - HTTP_ACCEPT
    - HTTP_ACCEPT_ENCODING
    - HTTP_ACCEPT_LANGUAGE
    - HTTP_HOST
    - HTTP_REFERER: 进站前链接网页，如果有的话。
    - HTTP_USER_AGENT: 用户浏览器的user-agent字符串，如果有的话。
    - QUERY_STRING
    - REMOTE_ADDR: 客户端IP
    - REMOTE_HOST
    - REMOTE_USER
    - REQUEST_METHOD
    - SERVER_NAME
    - SERVER_PORT

    获取请求头代码例子：

        try:
            ua = request.META['HTTP_USER_AGENT']
        except KeyError:
            ua = 'unknown'

        # 或者
        ua = request.META.get('HTTP_USER_AGENT', 'unknown')

- HttpRequest.resolver_match

## Methods

- HttpRequest.get_host()

    Example: "127.0.0.1:8000"
- HttpRequest.get_port()
- HttpRequest.get_full_path()

    Example: "/music/bands/the_beatles/?print=true"
- HttpRequest.build_absolute_uri

    Example: "https://example.com/music/bands/the_beatles/?print=true"
- HttpRequest.get_signed_cookie(key, default=RAISE_ERROR, salt='', max_age=None)
- HttpRequest.is_secure():Returns True if the request is secure; that is, if it was made with HTTPS.
- HttpRequest.is_ajax()
- HttpRequest.read(size=None)
- HttpRequest.readline()
- HttpRequest.readlines()
- HttpRequest.xreadlines()
- HttpRequest.__iter__()


# HttpResponse
