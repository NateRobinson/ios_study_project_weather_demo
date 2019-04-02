# iOS Study Project -> 天气预告应用

通过这个项目可以学到

1. 如何使用 CocoaPods 初始化一个项目

    首先：

    ```
    cd {project root path}
    pod init
    ```

    再编辑 Podfile 为项目添加需要的三方库的依赖，可以去 [https://cocoapods.org/](https://cocoapods.org/) 关键字搜索需要的库

    ```
    open -a Xcode Podfile (使用 Xcode 打开 Podfile 编辑更加的友好)
    ```

    编辑完之后，需要执行 install

    ```
    pod install
    ```
    以后，如果库有更新，执行 update 即刻

    ```
    pod update
    ```

    安装结束之后，需要通过打开白色的 `{project name}.xcworkspace` 在 Xcode 中打开项目

2. 如何使用 Segue 串联页面并实现参数的传递

    首先，要创建 Segue，必然要存在大于 1 个的 storyboard， 可以通过在某个控价上右击鼠标产生 Segue，
    也可以通过右击鼠标拖动 ViewController 顶部三个 Icon 中第一个 View Controller 按钮到想要跳转的页面。

    这两者的区别：

    1. 使用控件拖拽产生的 segue 会默认关联在控件上，
    2. 使用 ViewController 顶部三个 Icon 中第一个 View Controller  拖拽产生的 segue 需要指定唯一的 id，并在代码中主动的使用

        ```
       performSegue(withIdentifier: "segue id", sender:self)
       ```

    完成跳转。

    跳转完成只是第一步，页面间数据的传递也非常重要。这里分两种情况：

    1. A-》B， A 需要带数据到 B，这个只需要 override A 中的  `prepare`  方法即可，在里面通过判等逻辑，根据 segue 的 id，获取到下个页面的 ViewController，进而
    直接给下个页面的 ViewController 里面的参数赋值, 这里使用 `as!` 使用做了一个 父 到 子 的强制转换

        ```swift
        override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
            if segue.identifier == "changeCityName" {
            let destinationVC = segue.destination as! ChangeCityViewController
            destinationVC.delegate = self
            }
        } 
       ``` 

    2. B 回到 A ， 类似于 Android 中的 onActivityResult ，在 Swift 中，我们通过设置回调协议方法

        1. 定义一个回调协议，在 B 中定义一个此协议的变量，这里的 `?` 表示，这个变量可能为 `nil`
        
            ```swift
           var delegate: ChangeCityDelegate?
           ```
        
        2. 让 A ViewController 实现这个协议，然后在 `prepare`  方法中先通过 segue id 获取到 B 的 ViewController，并为上一步的 delegate 赋值
        
            ```swift
           override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
            if segue.identifier == "changeCityName" {
            let destinationVC = segue.destination as! ChangeCityViewController
            destinationVC.delegate = self
            }
           }
           ```
        
        3. 在 B 要结束并要传参的时候，调用一下 delegate 的方法即可, 这里的 `delegate?` 是因为，这个 `delegate` 可能为 `nil`，如果是 `nil`，就不会执行后面的 `userEnteredANewCityName` 部分
        
            ```swift
            //1 Get the city name the user entered in the text field
            let cityName = changeCityTextField.text!
            
            //2 If we have a delegate set, call the method userEnteredANewCityName
            delegate?.userEnteredANewCityName(city: cityName)
            
            //3 dismiss the Change City View Controller to go back to the WeatherViewController
            self.dismiss(animated: true, completion: nil)
           ```



