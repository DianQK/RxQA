> Swift 3.0
> Alamofire 4.0.0
> RxSwift 1.0.0-beta.1

这个恐怕是 RxSwift 中重复 Issue 频率最高的问题。

Cell 重用导致多重订阅。解释起来也很简单，滑动的时候导致**订阅**代码重复执行，就比如：

```swift
let button = UIButton()
button.rx.tap
    .subscribe(onNext: {
        print("Tap")
    })
    .addDisposableTo(rx.disposeBag)

button.rx.tap
    .subscribe(onNext: {
        print("Tap")
    })
    .addDisposableTo(rx.disposeBag)
```

上述代码很好的说明了问题，订阅代码执行了两次，自然会有**点击一次，执行两次**的情况。

Cell 的解决方案也很简单，使用 `func prepareForReuse()`：

```swift
var disposeBag = DisposeBag()

override func prepareForReuse() {
    super.prepareForReuse()
    disposeBag = DisposeBag()
}
```

在 Cell 中添加如上代码即可。

> 当然，您也可以用 Method Swizzle 完成。也可以参考 https://github.com/ivanbruel/Cell-Rx 的实现。
