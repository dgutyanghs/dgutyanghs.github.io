---
layout: post
title: RxSwift实践（响应函数式编程）
date: 2017-07-12 
tag: iOS
---
<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#org65a3852">1. RxSwift的实践</a>
<ul>
<li><a href="#org974de37">1.1. 函数响应式编程（FRP）的兴起</a>
<ul>
<li><a href="#orgbbbc278">1.1.1. ReactiveX</a></li>
</ul>
</li>
<li><a href="#org0a5ccad">1.2. 实践例子</a>
<ul>
<li><a href="#orgaf79710">1.2.1. RxCocoa神奇tableView中的绑定</a></li>
<li><a href="#orgd20d2a0">1.2.2. Variable类型</a></li>
<li><a href="#org9ea9a90">1.2.3. 视频下载的Progress进度更新</a></li>
<li><a href="#org653f933">1.2.4. Event的变换和合并</a></li>
<li><a href="#org5b8c463">1.2.5. https 访问限制的应对方法</a></li>
<li><a href="#org4b230dc">1.2.6. 总结</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>

<a id="org65a3852"></a>

# RxSwift的实践


<a id="org974de37"></a>

## 函数响应式编程（FRP）的兴起


<a id="orgbbbc278"></a>

### ReactiveX

ReactiveX是一个大家庭，RxSwift,RxPy,RxKotlin,RxJava &#x2026;,[ReactiveX官网](http://reactivex.io/) .
作为iOS的开发者，相信大家都很熟悉通知中心："NotificationCenter.default"，这个是UIKit中帮我们实现好的观察者模式（observer pattern）。
我们通过NotificationCenter.default.post(notification)向订阅者发送通知。

RxSwift对观察者模式进行了扩展，很好地简化程序设计。RxSwift最重要的三个类型： `Observable` （可被观察者／Event发出者）,  `Observer` （观察者／Event接收者）,  `Subject` （Observer + Observable／既能发送Event，也能接收）.
所谓响应式，事件发生时， `Observable` 就会发出Event消息， `Observer` 接收到订阅的Event后，可对事件进行加工处理.做出回应。 
关于更多RxSwift的基础知识，和优点请浏览官网


<a id="org0a5ccad"></a>

## 实践例子

Flashmob 小Demo，本来打算上架后再开源，但Apple的审核者拒绝了2次，第一次理由是：不支持IPv6，我修复后提交，第二次理由时：“只有视频下载和播放，浏览器也能办到”。还邮件警告说以后再提交如此简单的App，
将封号&#x2026;. 想必Apple的审核者，都是开发高手！！！唉，苦了我那几天熬夜搭建的视频服务器。

实现功能：1.视频列表展示; 2.视频下载; 3.播放。下面简述程序，请先下载源代码。


<a id="orgaf79710"></a>

### RxCocoa神奇tableView中的绑定

平时我用UITableView,总要手动声明遵守相关的Protocol:UITableViewDataSource, UITableViewDelegate. 

    tableView.delegate = self
    tableView.dataSource = self
    /// ...

然后实现Protocol的方法：

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        <#code#>
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        <#code#>
    }

细心的读者可能已经发现，我们的类： `FirstViewController` 和 `SecondViewController` 并不需要这样的代码
先看下 `SecondViewController.swift` 中的tableView数据显示实现

            let bag = DisposeBag()
    /// ...........................
    
            let cellID = "aboutMeCell"
            let aboutInfo = Observable.of([("版本", "version:1.0.0"),
            ("作者","Alex Yang"),
            ("E-mail", "yanghs.dgut@gmail.com"),
            ("博客","https://dgutyanghs.github.io/")])
    
            aboutInfo.bind(to: tableView.rx.items(cellIdentifier: cellID, cellType: UITableViewCell.self )) {
                _, item, cell  in
                cell.textLabel?.text = item.0
                cell.detailTextLabel?.text = item.1
            }.addDisposableTo(bag)

`aboutInfo` 是一个Observable,它将向订阅者发送一组tuple。
RxCocoa库中UITableView+Rx.swift的items函数：

    public func items<S: Sequence, Cell: UITableViewCell, O : ObservableType>
        (cellIdentifier: String, cellType: Cell.Type = Cell.self)
        -> (_ source: O)
        -> (_ configureCell: @escaping (Int, S.Iterator.Element, Cell) -> Void)
        -> Disposable
        where O.E == S {
        return { source in
            return { configureCell in
                let dataSource = RxTableViewReactiveArrayDataSourceSequenceWrapper<S> { (tv, i, item) in
                    let indexPath = IndexPath(item: i, section: 0)
                    let cell = tv.dequeueReusableCell(withIdentifier: cellIdentifier, for: indexPath) as! Cell
                    configureCell(i, item, cell)
                    return cell
                }
                return self.items(dataSource: dataSource)(source)
            }
        }
    }

bindto 函数：

    public func bind<O>(to observer: O) -> Disposable where O : ObserverType, O.E == Self.E


<a id="orgd20d2a0"></a>

### Variable类型

上面的例子中 `aboutInfo` 的数据为固定的。而在项目实际开发中，数据是经常更新的。
也就是说，我们更新数据源时，TableView的Cell跟着更新。
需要 `aboutInfo` 既能emit数据（Observable），也能接受收据：（Observer）.
在RxSwift中，Variable类型做为Subject类型的一种，它很适合用来存储时常需要更新TableView数据源。
Subject类型既能作Observable，也能作Observer。
打开 `FirstViewController.swift` 文件:

    /// videoArray 声明为一个可存储Video的数组
    var videoArray = Variable<[Video]> ([])
    
    /// videoArray.asObservable 作为Observable
    videoArray.asObservable().observeOn(MainScheduler.instance).bind(to: tableView.rx.items(cellIdentifier: cellID, cellType: VideoInfoCell.self)) {
                _, videoItem, cell  in
                cell.titleLabel.text = "  " + videoItem.title
                cell.bgImageView.af_setImage(withURL: URL.init(string: videoItem.cover)!)
                cell.textView.text = videoItem.desc
                cell.progressBar.progress = videoItem.progress
                if videoItem.progress == 1.0 {
                    cell.titleLabel.textColor = UIColor.green
                }else {
                    cell.titleLabel.textColor = UIColor.white
                }
            }.addDisposableTo(bag)
    
    /// 下载Video列表info，success是，可更新数据。对self.VideoArray.value赋值后。会自动上面代码的Observable,从而reload tableView.
        func downloadVideoInfo(from index: Int = 0) {
                let urlString = "https://..."
                let param = ["index": index]
                _ = Alamofire.request(urlString, method: .post, parameters: param)
                    .responseJSON { [weak self] (response) in
                        switch response.result {
                        case .success(let json):
                            let newArray = Video.generateModel(with:JSON(json))
                            if let `self` = self {
                                self.videoArray.value = self.mergeVideoArrays(originArray: self.videoArray.value, newArray: newArray)
                            }
                        case .failure(let error):
                            print(error.localizedDescription)
                        }
                    }
           }

videoArray既是Observer,也是Observable，当它收到更新时，会自动触发Observable，将自己的value全部发送。 


<a id="org9ea9a90"></a>

### 视频下载的Progress进度更新

    func downloadVideo(video: Video, indexPath: IndexPath) {
      /// ...
        .subscribe(onNext: {
            [weak self] progress in
            print(" index:\(indexPath.row) :\(video.title) progress : \(progress.completed)")
            let cell = self?.tableView.cellForRow(at: indexPath) as? VideoInfoCell
            cell?.progressBar.progress = progress.completed
        }, onCompleted: {
            [weak self] in
            print("download completed")
    
            if let `self` = self {
                self.videoArray.value[indexPath.row].setProgress(value: 1.0)
                let item = self.videoArray.value[indexPath.row]
                self.showMessage(item.title, title:"下载完成", theme: .success)
                ///save Plist
                Video.saveValuesToDefaults(newValues: self.videoArray.value, key: self.videoArrayKey)
            }
    
        }).addDisposableTo(bag)
    
    }
    
    ///查看downloadVideo中的subscribe部分，订阅了 onNext, 和onComplete事件，
    ///onNext更新进度，onCompleted更新UI，保存数据。


<a id="org653f933"></a>

### Event的变换和合并

RxCocoa中，当用户点击选择UITableViewCell时，会触发两个Observable。

    /// video 数据
    tableView.rx.modelSelected(Video.self)
    /// tableView cell 对应的indexPath
    tableView.rx.itemSelected

这个Demo的设计是，当用户选择某个Cell时，如果该cell的视频数据未在本地，则启动下载。反之，播放视频。
你可以尝试这样写：

    let cellObservable = tableView.rx.modelSelected(Video.self).share()
    
     cellObservable.filter { (video) -> Bool in
         return video.progress == 1.0
         }.subscribe(onNext: {
             self.playVideo($0)
         }).addDisposableTo(bag)
    
     cellObservable.filter { (video) -> Bool in
         return video.progress < 1.0 ? true : false
         }.subscribe(onNext: {
             _ in
             print("start download video file")
         }).addDisposableTo(bag)

当下载Video时，没有IndexPath参数，导致无法更新cell的进度条。
能不能既有video，也有indexPath，这是要用到Operator(combineLatest)了。将两个Observable的事件合并

    let cellTapedObservable = Observable.combineLatest(tableView.rx.modelSelected(Video.self), tableView.rx.itemSelected) { (videoItem, indexPath) -> (Video, IndexPath)   in
        return (videoItem, indexPath)
    }
    
    cellTapedObservable.debounce(0.5, scheduler: ConcurrentMainScheduler.instance)
        .subscribe(onNext: {
       [weak self]  tuple in
    
        let video = tuple.0
        /// 1: video has download finished, play it
        if video.progress == 1.0 {
            self?.playVideo(video)
        }else {
        /// 2: start to download video file
           self?.downloadVideo(video: tuple.0, indexPath: tuple.1)
        }
    
    }).addDisposableTo(bag)


<a id="org5b8c463"></a>

### https 访问限制的应对方法

如果你的服务器有正规的https证书的，不用考虑此步骤。

1.  修改你App项目中的Info.plist文件，添加以下项

        <key>NSAppTransportSecurity</key>
          <dict>
            <key>NSAllowsArbitraryLoads</key>
            <false/>
            <key>NSExceptionDomains</key>
            <dict>
              <key>120.25.206.78</key> 
              <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSExceptionRequiresForwardSecrecy</key>
                <false/>
                <key>NSIncludesSubdomains</key>
                <true/>
              </dict>
              <key>www.popiano.org</key>
              <dict>
                <key>NSExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSExceptionRequiresForwardSecrecy</key>
                <false/>
                <key>NSIncludesSubdomains</key>
                <true/>
              </dict>
            </dict>
          </dict>
    
    你的服务器没申请域名的话，也可用直接写上IP地址（如：120.25.206.78） 
    如有就直接写上（如：www.popiano.org）

2.  App启动是初始话网络配置

    在文件AppDelegate.swift 中启动函数加载它
    
        import Alamofire
        /// .................
        
        
        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool {
                // Override point for customization after application launch.
        
                configureAlamofireManager()
        
                return true
            }
        
                /// MARK: HTTPS 认证 处理
            func configureAlamofireManager() {
                let manager = SessionManager.default
        
                manager.delegate.sessionDidReceiveChallenge = { session, challenge in
                    var disposition: URLSession.AuthChallengeDisposition = .performDefaultHandling
                    var credential: URLCredential?
        
                    if challenge.protectionSpace.authenticationMethod == NSURLAuthenticationMethodServerTrust {
                        disposition = URLSession.AuthChallengeDisposition.useCredential
                        credential = URLCredential(trust: challenge.protectionSpace.serverTrust!)
                    } else {
                        if challenge.previousFailureCount > 0 {
                            disposition = .cancelAuthenticationChallenge
                        } else {
                            credential = manager.session.configuration.urlCredentialStorage?.defaultCredential(for: challenge.protectionSpace)
        
                            if credential != nil {
                                disposition = .useCredential
                            }
                        }
                    }
                    return (disposition, credential)
                }
            }
    
    至此，你的App的Https网络访问问题解决。

3.  笔者的新服务器

    为了解决第一次IPv6被拒，笔者的新服务器架到国外了。带宽高速度快,居然还比国内的VPS要便宜，域名和SSL证书也都申请完成，成正规军了。不用再为HTTPS的问题烦恼了。各位看官，买VPS时，货比3家。


<a id="org4b230dc"></a>

### 总结

Function Reactive  Programming （FRP)的方式编程，难点在于理解Event流和对各种数据的操作，变换。
对以往命令式编程是一种颠覆。
代表先进的生产力；代表广大码农的前进方向；&#x2026;.

