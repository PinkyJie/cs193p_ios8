第二课 笔记
======

Swift
------
* outlet为什么不需要初始化？声明的类型UILabel后面为什么有一个感叹号？
    * 在Controller将View加载进来的时候会对outlet的属性进行初始化。 
    * `UILabel!`这种类型也是Optional类型，与`?`不同的是，在使用的时候会自动unwrap。也就是说，如果这里的`!`换成`?`，那么每次使用变量的时候后面都要加上`!`来强制unwrap。这成为“显式 unwrapped optional”。
* 声明数组：类似java，需指明数组存放的类型，如`Array<Double>`。
* 初始化数组：`var a = Array<Double>()`，调用无参的初始化方法，这里`a`不需要显式的写明类型，因为编译器会自动推断出它的类型。在所有编译器能自动推断类型的情况下推荐不要显式的写出类型。
* computed属性：属性的取值依赖于别的属性。声明时可以指定`get`和`set`方法，分别表示在等号右边和左边时如何求值，在`set`方法中可以使用一个特殊的变量`newValue`，它代表赋给该变量的值，它的类型与该变量类型一致。如：
```
var displayValue: Double {
  get {
    return xxx
  }
  set {
    xxx = newValue
  }
}
```

* `NSNumberFormatter`类
* switch语法：
```
switch xxx {
case xxx:
  xxx
case xxx:
  xxx
default:
  xxx
}
```

* switch必须覆盖所有的情形，即必须有`default`分支。
* 将函数作为一种类型：如`(Double, Double) -> Double`代表这样一种函数，它接受两个Double类型的参数，返回值也是Double。
* 直接将匿名函数作为参数传递：与函数声明时的写法略有不同，大括号的位置提前到参数表之前，大括号原来的位置变成关键字`in`。这种写法成为闭包，如
```
performOperation({ (opt1: Double, opt2: Double) -> Double in
  return opt1 * opt2
})
```

* 利用类型推断简写闭包：
    * 声明函数`perfromOperation`时已经指明了参数类型为一个函数，顾调用时不需要再次指明类型。即
    ```
    performOperation({ (opt1, opt2) in
      return opt1 * opt2
    })
    ```
    * 如果函数体只有一句，则`return`关键字也可以省略。
    * 参数的名称也不是必须的，可以使用`$x`来代替，如`performOperation({ $0 * $1 })`。
* 如果函数参数是这个最后一个参数的话，则调用的时候可以直接将这个函数写在外面，如`performOperation() { $0 * $1 }`。
* 如果函数参数是唯一的一个参数，则调用时的括号也可以省略，如`performOperation { $0 * $1 }`。
* 函数的重载：名字相同，参数不同。
> 视频中得重载方法在Xcode 6.3中会报错，这是因为ViewController继承于UIViewController，而这是一个objc的类，所以两个方法的selector必须不同才行，而视频中的两个方法的selector均为`performOperation(operation:)`，故报错。[来源](http://stackoverflow.com/questions/29457720/swift-compiler-error-which-i-dont-understand)

Xcode
------
* 右击storyboard上面的UI元素，会弹出与这个UI元素相关的所有connection，可以删除多余的connection。
* 如果手动修改了连接后的方法，可能需要重新建立连接。（观察代码中的连接点是否为实心）
* `Cmd+/`注释。

Calculator算法
------
* 设置一个数组模拟栈，每输入一个数字并按下enter，则数字入栈。
* 每次按下加减乘除，则将栈内最上面两个数字出栈做相应运算，然后计算结果入栈。

Auto Layout
-----
* storyboard右下角的按钮可以帮助自动布局。
* 第2个按钮可以添加类似“元素与相邻元素之间”的约束，在添加约束时注意小红杠变红才会生效。
* 第3个按钮可以帮助清除所有的约束。

MVC
------
* 设计模式：将程序中的所有东西归结为这三个阵营。
* Model：程序是干嘛的，比如计算机的核心是数字计算，这块逻辑应该放在Model里，Model与UI是完全独立的。
* Controller：控制Model是如何在UI上进行显示的，即显示相关的逻辑。
* View：通用的UI控件，这些控件具体的作用要靠Controller来决定，因此View被成为Controller的奴隶（minions）。
![](/Notes/imgs/MVC.png)
* 三者之间的通信如上图：
    * 用交通标线来表示，双黄色表示禁止通信，白色虚线表示可以通信。
    * Controller -> Model 通信，Controller必须完全的调用Model的开放API。
    * Controller -> View 通信，通过outlet来实现，即Controller通过自己的属性来指向View从而访问View的相关属性。
    * View -> Controller 通信：
        * 通过action。因为View里全是通用的控件，它只是将发生的事件通过action传递给Controller，而不关心这些事件究竟应该如何被响应。Controller声明一个方法作为target，View将自己的action传递给这个target。
        * 通过delegate。View将自己是否“应该/将要/已经”(should/will/did)发生某项事件等这些问代理给Controller里的方法来回答，should表示我是否能够发生这个事件，will表示我将要发生这个事件要怎么响应，did表示我已经发生这个事件了要怎么应对。
        * 通过data source。适用于需要显示大量数据的View，View将自己的一些属性代理给Controller来处理，比如有多少条数据需要显示？这一行显示的数据是什么？而Controller最终会于Model通信来获取问题的答案。
    * Model -> Controller 通信：通过Notification机制或KVO（Key-Value Observe）机制，当Model的数据发生变化时发出广播，Controller可以接收这些广播信号，进而得知Model已经发生变化，然后去请求Model拿到最新变化。
    * Model <-> View 禁止通信。虽然View也可以接收Model的广播信号，但这违背了MVC的设计原则。
* MVC与MVC之间只有两种类型的通信：
    * 一个大的MVC可以将其他MVC视为View。
    * 不同MVC之间的Model可以通信。
