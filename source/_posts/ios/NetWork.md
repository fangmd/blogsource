---
title: 网络请求
date: 2017-05-18 13:18:12
tags: ios
category: ios

---



# Swift 

## Moya

参考：[https://www.thedroidsonroids.com/blog/ios/rxswift-examples-3-networking/](https://www.thedroidsonroids.com/blog/ios/rxswift-examples-3-networking/)

### 基本使用

参考：[http://www.jianshu.com/p/e864a61053a4](http://www.jianshu.com/p/e864a61053a4)

<!--more-->

1. 创建 API 枚举

```
enum ApiManager{
    case getNewsLastest
    case getNewsDetail(Int)
    case postSomthing(title: String, body: String, userId: Int)
}
```

2. 实现 TargetType 协议

设置 url，请求方法，请求参数

```
extension ApiManager: TargetType {

    /// The target's base `URL`.
    var baseURL: URL {
        return URL.init(string: "http://news-at.zhihu.com/api/")!
    }

    /// The path to be appended to `baseURL` to form the full `URL`.
    var path: String {
        switch self {
        case .getNewsLatest:
            return "4/news/latest"
        case .getNewsDetail(let id):
            return "4/news/\(id)"
        case .postSomthing(_, _, _)
            return "/posts"
        }
    }

    /// The HTTP method used in the request.
    var method: Moya.Method {
        switch self{
        case .getNewsLatest:
            continue
        case .getNewsDetail(let id):
            return .get
        case .postSomthing(_, _, _):
            .posr
        }
    }

    /// The parameters to be incoded in the request.
    var parameters: [String: Any]? {
        switch self{
        case .postSomthing(let title, let body, let userId):
            return ["title": title, "body": body, "userId": userId]
        }
        return nil
    }

    /// The method used for parameter encoding.
    var parameterEncoding: ParameterEncoding {
        return URLEncoding.default
    }

    /// Provides stub data for use in testing.
    var sampleData: Data {
        return "".data(using: String.Encoding.utf8)!
    }

    /// The type of HTTP task to be performed.
    var task: Task {
        return .request
    }

    /// Whether or not to perform Alamofire validation. Defaults to `false`.
    var validate: Bool {
        return false
    }

}
```

3. 使用

```
let provider = MoyaProvider<ApiManager>()
provider.request(.getNewsLatest){ result in
    // do something with result
}
```

### 请求头

### 配合 RxSwift


```
let provider = RxMoyaProvider<ApiManager>()

provider.request(.getNewsLatest)
    .filterSuccessfulStatusCodes()
    .mapJSON()
    .subscribe(onNext: { (json) in
            print(json)
        }).addDisposableTo(disposeBag)


provider
    .request(.getThemes)
    .mapModel(MenuModel.self)
    .subscribe(onNext: { (model) in
            completed(model)
        }, onError: { (error) in
    }, onCompleted: nil, onDisposed: nil).addDisposableTo(dispose)
```

- RxMoyaProvider是MoyaProvider的子类，是对RxSwift的扩展
- mapJSON()也是Moya RxSwift的扩展方法，可以把返回的数据解析成 JSON 格式
- addDisposableTo(disposeBag) 是 RxSwift 的一个自动内存处理机制，跟ARC有点类似，会自动清理不需要的对象。

#### 常用的 rx 操作符

```
filter(statusCodes:) 过滤response状态码
filterSuccessfulStatusCodes() 过滤状态码为请求成功的
mapJSON() 将请求response转化为JSON格式
mapString() 将请求response转化为String格式
```


#### mapModel 的实现，需要自定义

扩展 ObservableType 和 Response：（下面使用了第三方库：`HandyJSON`）

```
// 2 下面的更合适

extension Response {
     // 这一个主要是将JSON解析为单个的Model 
    public func mapObject<T: BaseMappable>(_ type: T.Type) throws -> T {
        guard let object = Mapper<T>().map(JSONObject: try mapJSON()) else {
            throw MoyaError.jsonMapping(self)
        }
        return object
    }
    
    // 这个主要是将JSON解析成多个Model并返回一个数组，不同的json格式写法不相同
    public func mapArray<T: BaseMappable>(_ type: T.Type) throws -> [T] {
        let json = JSON(data: self.data)
        let jsonArray = json["data"]["data"]
        
        guard let array = jsonArray.arrayObject as? [[String: Any]],
            let objects = Mapper<T>().mapArray(JSONArray: array) else {
            throw MoyaError.jsonMapping(self)
        }
        return objects
    }
}
extension ObservableType where E == Response {
    // 这个是将JSON解析为Observable类型的Model
    public func mapObject<T: BaseMappable>(_ type: T.Type) -> Observable<T> {
        return flatMap { response -> Observable<T> in
            return Observable.just(try response.mapObject(T.self))
        }
    }
    
    // 这个是将JSON解析为Observable类型的[Model]
    public func mapArray<T: BaseMappable>(_ type: T.Type) -> Observable<[T]> {
        return flatMap { response -> Observable<[T]> in
            return Observable.just(try response.mapArray(T.self))
        }
    }
}
```


### 插件

Moya 在初始化 Provider 的时候可以传入一些插件，Moya库中默认有4个插件。

- AccessTokenPlugin 管理AccessToken的插件
- CredentialsPlugin 管理认证的插件
- NetworkActivityPlugin 管理网络状态的插件
- NetworkLoggerPlugin 管理网络log的插件


#### 利用插件，当请求失败的时候弹出一个alert

```
final class RequestAlertPlugin: PluginType {

    private let viewController: UIViewController

    init(viewController: UIViewController) {
        self.viewController = viewController
    }

    func willSend(request: RequestType, target: TargetType) {
        ........//实现发送请求前需要做的事情
    }

    func didReceive(result: Result<Response, MoyaError>, target: TargetType) {
        guard case Result.failure(_) = result else { return }//只监听失败

        // 弹出Alert
        let alertViewController = UIAlertController(title: "Error", message: "Request failed with status code: \(error.response?.statusCode ?? 0)", preferredStyle: .alert)
        alertViewController.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
        viewController.present(viewControllerToPresent: alertViewController, animated: true)
    }
}
```


#### 利用插件 实现 log


方法一： NetworkLoggerPlugin


方法二：[https://github.com/artsy/eidolon/blob/master/Kiosk/App/Networking/NetworkLogger.swift](https://github.com/artsy/eidolon/blob/master/Kiosk/App/Networking/NetworkLogger.swift)


## post json 数据 同时设置 url 参数

[https://github.com/Moya/Moya/issues/1119](https://github.com/Moya/Moya/issues/1119)

```
public var parameters: [String: Any]? {
    var params:[String: Any] = [:]
    params["query"] = ["access_token":getAccessToken()]
    params["body"] = ["user_name":"Pete"]

    return params
}

public var parameterEncoding: ParameterEncoding {
    return CompositeEncoding()
}


struct CompositeEncoding: ParameterEncoding {
        
    public func encode(_ urlRequest: URLRequestConvertible, with parameters: Parameters?) throws -> URLRequest {
        guard let parameters = parameters else {
            return try urlRequest.asURLRequest()
        }
            
        let queryParameters = (parameters["query"] as? Parameters)
        let queryRequest = try URLEncoding(destination: .queryString).encode(urlRequest, with: queryParameters)
            
        if let body = parameters["body"] {
            let bodyParameters = (body as! Parameters)
            var bodyRequest = try JSONEncoding().encode(urlRequest, with: bodyParameters)
                
            bodyRequest.url = queryRequest.url
            return bodyRequest
        } else {
            return queryRequest
        }
    }
}
```


# Objective-C AFNetworking