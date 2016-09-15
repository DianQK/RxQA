> Swift 3.0
> Alamofire 4.0.0
> RxSwift 1.0.0-beta.1

这是一个比较常见的问题，我们了解一下 `Observable` 的 `create` 方法即可。这个问题并不复杂，我在这里直接贴出一个 sample 供参考。

以 Alamofire 为例。

```swift
func request(_ url: URLConvertible, method: Alamofire.HTTPMethod, parameters: Parameters? = nil) -> Observable<Data> {
    return Observable.create { (observer) -> Disposable in
        let task = Alamofire
            .request(url, method: method, parameters: parameters)
            .responseData { (response) in
            switch response.result {
            case let .success(data):
                observer.on(.next(data))
                observer.on(.completed)
            case let .failure(error):
                observer.on(.error(error))
            }
        }
        return Disposables.create(with: task.cancel)
    }
}
```
