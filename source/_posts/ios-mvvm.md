---
title: ios架构学习笔记
date: 2019-01-18 16:11:47
tags:
- ios
---

# ios中的架构设计

### 前言

在 iOS 客户端开发中，MVC 作为官方推荐的主流架构，不但 SDK 已经为我们实现好了 UIView、UIViewController 等相关的组件，更是有大量的文档和范例供我们参考学习，可以说是一种非常通用而成熟的架构设计，但 MVC 也有他的坏处。由于 MVC 的概念过于简单朴素，已经越来越难以适应如今客户端的需求，大量的代码逻辑在 MVC 中并没有定义得很清楚究竟应该放在什么地方，导致他们很容易就会堆积在 Controller 里：

- 初始化界面；
- 请求网络数据或响应用户操作；
- 根据数据或操作更新界面。

成为了人们所说的 Massive View Controller。

<!--more-->

### 如何简化Controller的工作

设计一个可读性强和扩展性强的viewController非常重要，如何简化controller的工作，对模块进行解耦呢？现在流行的iOS架构大概有MVC，MVCS，MVVM。

### MVC

最经典的软件结构设计模式，也是曾经Apple推荐的设计模式，苹果的官方文档中还能够看到MVC的痕迹[Apple MVC](https://link.jianshu.com/?t=https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html)。在MVC中，因为viewController既要负责页面的所有生命周期，又要负责网络处理，还要处理用户的交互操作，导致大量的代码逻辑堆积在viewController，极大的影响了代码的可读性和扩展性。因此，为了解决上述问题，又衍生出了MVCS和MVVM等模式。

## MVCS

MVCS是在MVC的基础上衍生出来的架构模式，S是Store的意思。可以简单的理解为，MVCS是把网络层从Controller中剥离出来，使用独立的Store类进行网络数据的请求和数据处理，从而达到减轻viewController压力的目的。在苹果很多的官方示例中都有这种结构的写法。

下面一个简单的例子：

```swift
class ALYViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        //处理页面逻辑
        self.view.addSubview(self.displayView)
        //处理网络逻辑
        self.fetchDataFromServer()
    }
    
    func fetchDataFromServer() -> Void {
        self.dataStore.fetchDataFromServiceWithCompletion { [weak self]() in
            if self?.dataStore.dataSource.count > 0 {
                self?.displayView.updateViewWithData((self?.dataStore.dataSource)! )
            } else {
                //默认处理
            }
        }
    }
    
    lazy var dataStore : ALYDataStore = {
        var _dataStore = ALYDataStore()
        return _dataStore
    }()
    
    lazy var displayView : ALYDisplayView = {
        var _displayView = ALYDisplayView()
        return _displayView
    }()
}
class ALYDisplayView: UIView {
    let label = UILabel()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        self.addSubview(label)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    func updateViewWithData(dataSource:Array<ALYModel>) -> Void {
        self.label.text = dataSource[0].text
    }
}

class ALYDataStore: NSObject {
    //用来存储取回来的数据，字典或数组
    var dataSource : [ALYModel] = []
    
    func fetchDataFromServiceWithCompletion(completion:(Void)->Void) -> Void {
        let url = "http://alibaba-inc.com"
        let parameters = ["username":"zhaizun", "password":"123456"]
        
        self.fetchServiceListWithUrl(url, parameters: parameters, completion: completion)
        
    }
    
    func fetchServiceListWithUrl(url:String, parameters:NSDictionary, completion:(Void)->Void) -> Void {
        let request = NSURLRequest(URL:  NSURL.init(string: url)!)
        let connection = NSURLConnection.init(request: request, delegate: self)
        connection?.start()
let finishCallBack : (Void)->Void = { [weak self](Void)->Void in
            //伪造的json数据
            let json = [["a":"1231"], ["b":"32131"], ["c":"312312"]]
            //从服务器回来的model处理
            for object in json {
                let model = ALYModel()
                model.jsonToModel(object)
                self?.dataSource.append(model)
            }
            completion()
        }
        
        finishCallBack()
    }
}

class ALYModel: NSObject {
    var text : String = ""
    
    func jsonToModel(data:[String:AnyObject]) -> ALYModel {
        let model = ALYModel()
        //Model转化逻辑
        //......
        return model
    }
}
```

这算是瘦Model的一种方案，它把Controller专门负责存取数据的那部分抽离出来，交给另一个对象去做，这个对象就是Store。

### MVVM

MVVM是Model，View和ViewModel的缩写，ViewModel不是Controller，它所做的事情依然是帮助Controller减负。把服务于View的逻辑从View，Controller和Model中剥离出来，放到ViewModel中，让ViewModel去处理View和Model之间的交互，加工和展示工作，而Controller只是在其中起到一个桥梁连接的工具。不过正是因为这个桥梁，使得Controller和ViewModel之间需要大量的粘合代码，这也正是ViewModel开发成本高的地方。

下面是一个例子

```swift
class ALYViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        //处理页面逻辑
        self.view.addSubview(self.displayView)
        //网络处理服务
        self.displayViewModel.fetchDataFromServiceWithCompletion { [weak self](success, error) in
            if success == true {
                self?.displayView.updateViewWithViewModel(self!.displayViewModel)
            } else {
                //默认处理
                print(error)
            }
        }
    }
    
    lazy var displayViewModel : ALYDisplayViewModel = {
        var _displayViewModel = ALYDisplayViewModel()
        return _displayViewModel
    }()
    
    lazy var displayView : ALYDisplayView = {
        var _displayView = ALYDisplayView()
        
        return _displayView
    }()
}

class ALYDisplayView : UIView {
    
    let nameLabel = UILabel()
    let ageLabel = UILabel()
    let dateLabel = UILabel()

    override init(frame: CGRect) {
        super.init(frame: frame)
        self.addSubview(nameLabel)
        self.addSubview(ageLabel)
        self.addSubview(dateLabel)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    func updateViewWithViewModel(viewModel:ALYDisplayViewModel) -> Void {
        
        let model = viewModel.dataSource
        
        self.nameLabel.text = model.name
        self.ageLabel.text = model.age
        self.dateLabel.text = model.date
    }
}

class ALYDisplayViewModel: NSObject {
    
    var dataSource : ALYModel = ALYModel()
    
    func fetchDataFromServiceWithCompletion(completion:(success:Bool, error:NSError?)->Void) -> Void {
        let url = "http://alibaba-inc.com"
        let parameters = ["username":"zhaizun", "password":"123456"]
        
        self.fetchServiceListWithUrl(url, parameters: parameters, completion: completion)
        
    }
    
    func fetchServiceListWithUrl(url:String, parameters:NSDictionary, completion:(success:Bool, error:NSError?)->Void) -> Void {
        let request = NSURLRequest(URL:  NSURL.init(string: url)!)
        let connection = NSURLConnection.init(request: request, delegate: self)
        connection?.start()
        
        let finishCallBack : (Void)->Void = { [weak self](Void)->Void in
            //伪造的json数据
            let json = ["name":"1231", "age":"32131", "date":"2016-04-12"]
            //从服务器回来的model处理
            self!.dataSource.jsonToModel(json)
            
            completion(success: true, error: nil)
        }
        
        finishCallBack()
    }
}

class ALYModel: NSObject {
    var name : String = ""
    var age : String = ""
    var date : String = ""

    func jsonToModel(data:[String:AnyObject]) -> ALYModel {
        let model = ALYModel()
        //Model转化逻辑
        //......
        return model
    }
}
```

上面的例子中，View和Model的部分逻辑都移动到了ViewModel，减轻了Controller的负担。另外通过分离出来的 ViewModel 获得了更好的测试性，我们可以针对 ViewModel 来测试，解决了界面元素难于测试的问题。

但因为model更新了之后还要手动的去让Controller用ViewModel去更新View，感觉多次一举。如果能够实现Model更新了自动去更新View，或者View变化了可以直接去更新Model，那么MVVM就显得更加优雅了。基于这种双向绑定机制的想法，ReactiveCocoa就出现了（或者使用KVO）。但是ReactiveCocoa的使用门槛很高，而且调试困难（建议大家慎重使用，不要为了MVVM而MVVM。）

### 题外话   胖model和瘦model（`Fat model, skinny controller`）

- 什么叫胖Model？

`胖Model包含了部分弱业务逻辑`。胖Model要达到的目的是，`Controller从胖Model这里拿到数据之后，不用额外做操作或者只要做非常少的操作，就能够将数据直接应用在View上`。FatModel做了这些弱业务之后，Controller就能变得非常skinny，Controller只需要关注强业务代码就行了。

把timestamp转换成具体业务上所需要的字符串，这属于业务代码，算是弱业务。众所周知，强业务变动的可能性要比弱业务大得多，弱业务相对稳定，所以弱业务塞进Model里面是没问题的。另一方面，弱业务重复出现的频率要大于强业务，对复用性的要求更高，如果这部分业务写在Controller，类似的代码会洒得到处都是，一旦弱业务有修改（弱业务修改频率低不代表就没有修改），这个事情就是一个灾难。如果塞到Model里面去，改一处很多地方就能跟着改，就能避免这场灾难。

然而其缺点就在于，胖Model相对比较难移植，虽然只是包含弱业务，但好歹也是业务，迁移的时候很容易拔出萝卜带出泥。另外一点，MVC的架构思想更加倾向于Model是一个Layer，而不是一个Object，不应该把一个Layer应该做的事情交给一个Object去做。最后一点，软件是会成长的，FatModel很有可能随着软件的成长越来越Fat，最终难以维护。

- 什么叫瘦Model？

`瘦Model只负责业务数据的表达，所有业务无论强弱一律扔到Controller`。瘦Model要达到的目的是，`尽一切可能去编写细粒度Model，然后配套各种helper类或方法来对弱业务做抽象，强业务依旧交给Controller`。

由于SlimModel跟业务完全无关，它的数据可以交给任何一个能处理它数据的Helper或其他的对象，来完成业务。在代码迁移的时候独立性很强，很少会出现拔出萝卜带出泥的情况。另外，由于SlimModel只是数据表达，对它进行维护基本上是0成本，软件膨胀得再厉害，SlimModel也不会大到哪儿去。

缺点就在于，Helper这种做法也不见得很好，这里有一篇[文章](http://nicksda.apotomo.de/2011/10/rails-misapprehensions-helpers-are-shit/)批判了这个事情。另外，由于Model的操作会出现在各种地方，SlimModel在一定程度上违背了DRY（Don't Repeat Yourself）的思路，Controller仍然不可避免在一定程度上出现代码膨胀。

### 选择

选个好上手的试试水吧，MVCS。

### 参考

1. [苹果开发文档](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Introduction.html)
2. [iOS开发架构的思考](https://www.jianshu.com/p/18a0c3fdc371)
3. [猿题库 iOS 客户端架构设计](http://gracelancy.com/blog/2016/01/06/ape-ios-arch-design/)
4. [iOS应用架构谈 view层的组织和调用方案](https://casatwy.com/iosying-yong-jia-gou-tan-viewceng-de-zu-zhi-he-diao-yong-fang-an.html)

