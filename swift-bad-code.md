#我从烂代码中学会了什么 (Swift)

### 目录

* [不要遗留 Objective-C 的习惯](#不要遗留 Objective-C 的习惯)


* [强制解包到处都是](#强制解包到处都是)
* [色调的使用](#色调的使用)

###不要遗留 Objective-C 的习惯

很多 Swift 开发者都是从 Objective-C 转过来的，虽然用的都是一样的 SDK，但是 Swift 和 Objective-C 实际上并不是一样的语言。因此，开发者们往往会遗留很多 Objective-C 中带来的习惯，这个对于 Swift 来说是不应该存有的。

比如说有这么一段代码：

```swift
let MAX_DOG_COUNT = 100;  // 1

class SIDogBox: CustomStringConvertible {  // 2
    var dogs: NSMutableArray!  // 3
    var boxName: NSString = ""  // 4
    var boxSize: CGSize = CGSizeZero  // 5

    var description: String {
        get {  // 6
            return "boxName: \(boxName), boxFrame: \(boxSize), dogs: \(dogs)"
        }
    }

    init(boxName: String, boxSize: CGSize) {
        self.boxName = boxName
        self.boxSize = boxSize
    }

    func addDog(dog: String)
    {  // 7
        self.dogs.addObject(dog)
    }

    func getDogsWithName(name: String) -> [String] {
        var dogs = Array<String>()  // 8
        for (var i = 0; i < dogs.count; i++) {  // 9
            if ((self.dogs[i] as! NSString).isEqualToString(name)) {  // 10
                dogs.append(self.dogs[i] as! String)
            }
        }
        return dogs
    }
}

var boxA = SIDogBox(boxName: "Best", boxSize: CGSizeMake(50, 50))  // 11
```

这段代码看起来实在是糟糕透顶，不是么？那么，Swift 风格究竟是什么样子的呢？让我们一点一点来进行分析吧！

####1. 命名

对于 Swift 来说，我们需要使用**驼峰式命名法**，也就是类名、协议名、枚举名等开头需要大写，变量、方法等开头需要以小写字母开头。

因此，`1` 上面所定义的那个全局变量，就不应该这样定义，而是应该遵循驼峰式命名法。

> 之所以会这么定义，是因为在翻译过程中，Objective-C 中使用的是宏来进行的定义，而宏的通用命名标准就是字母全部大写，单词之间加上下划线。

此外，对于类名、协议名、枚举名之类来说，我们不需要在前面加上 `SI` 之类的前缀。因为 Swift 拥有良好的命名空间管理模块，因此没有必要加上前缀。如果不同模块的命名冲突了，那么加上模块名就可以消除歧义了。这样 `2` 上面所述的命名方式就是不建议的。

####1. 一般不要加分号

Swift中，每条语句后的分号都不是必需的。只有在一行中有多条语句时才需要添加上分号。

####2. 尽量使用结构体而不是类

在 Objective-C 中，枚举、结构体的作用并不是那么强大，因此开发者们往往习惯性直接使用类。对于 Swift 来说，由于结构体是值类型，可以有效减少循环引用等类会导致的问题。因此，除非在必要的情况下（比如说需要使用继承、引用等类特有的特性时），那么应该尽可能使用结构体而不是类。

####2. 协议实现

和 Objective-C 不同，我们不应该将协议所需要实现的方法写在一起。我们应该另写多个 `extension`，让每个 `extension` 中分别实现一个协议，这样可以保证协议所需要实现的计算属性、方法能够集中在一起，并且还方便管理。

####3. 不必要暴露的属性或者方法尽量加上 `private` 关键字

在 Objective-C 中，由于其动态特性导致的并不存在严格的访问权限控制，因此往往开发者们就会忽略掉这个有用的特性。而 Swift 则就有严格的访问权限控制，因此出于一个良好的开发习惯，不必要暴露在外的属性或者方法前面最好都要加上 `private` 关键字。

####4. 尽量使用 Swift 自带的类型

我们应该尽量使用 Swift 自带的类型，比如说使用 `String`、`Array`、`Dictionary`，而不是使用 `NSString`、`NSArray`、`NSDictionary`。

####5. 尽量使用 Swift 风格的方法、属性来获取值

我们应该尽量使用 Swift 风格的方法、属性来获取值，就比如说在这个例子中，我们应该使用 `CGSize.zero` 来替代 `CGSizeZero` 这个等价宏替代物。那么怎么才能判断什么不是 Swift 风格呢？一般而言，如果直接用一个符号而没有使用任何点语法就能获取到某个值的话，那么这个使用风格就是 Objective-C 风格的，您应当考虑将其替代掉。

####6. 只读属性不要写多余的 get

对于只读属性来说，多余的 `get` 是完全没有必要的，Swift 会自动推断出来。

####7. 缩进空格和括号位置

Swift 建议使用 2 个空格进行缩进，并且左括号应该总是和语句位于同一行，而不是另起一行。这样 `7` 之类的风格是 Swift 不建议的。

####7. 避免 `self`

在 Swift 中访问一个对象的属性或者调用它的方法并不需要使用 `self`，所以请避免使用它。

####8. 使用更便捷的类型定义语法

对于 Array、Dictionary 来说，尽量使用诸如 `[String]`、`[String: String]` 来进行类型定义，而不要使用 `Array<String>` 之类的定义方式。

####9. 一般而言，表达式中的括号是没有必要的

对于 Swift 来说，除非会有歧义或者优先级特定的情况外，一般而言是不需要在 `if`、`for` 之类的语句当中添加传统的 `()` 的。比如说在这里的 `for` 循环定义中，括号是没有必要写的。

####9. 不要使用传统的 `for` 循环

尽可能使用 `for-in` 循环而不是传统的 `for` 循环。

####10. 使用等价的 Swift 原生语法

尽量使用等价的 Swift 原生语法，比如说这里判断字符串相等，直接使用 `String` 的 `==` 进行判断即可，而不是使用 `NSString` 的 `isEqualToString`。

####10. 使用 Swift 提供的函数来完成功能

尽可能使用 Swift 已经提供的函数来实现所需要的功能。比如说在这里，使用 `filter` 函数就可以完成检索操作了。此外，另说一点，对于**闭包**而言，尽可能多的使用**尾随闭包**语法。例如应该写成：

```swift
dogs.filter { $0 == name }
```

而不是：

```swift
dogs.filter({ includeElement -> Bool in
   return includeElement == name
})
```

####11. 使用构造器而不是便利方法

在 Objective-C 中，提供了一些诸如 `CGSizeMake` 之类的便利构造方法，但是我们应该使用 `CGSize` 自带的构造器方法来完成类型构造，而不是使用遍历方法。



因此，我们更改过的代码应该如下所示，是不是更 Swift 风格化了呢？

```swift
private let maxDogCount = 100

struct DogBox {
  private var dogs = [String]()
  var name: String
  var size: CGSize

  init(boxName: String, boxSize: CGSize) {
    self.name = boxName
    self.size = boxSize
  }

  mutating func addDog(dog: String) {
    dogs.append(dog)
  }

  func getDogsWithName(name: String) -> [String] {
    return dogs.filter { $0 == name }
  }
}

extension DogBox: CustomStringConvertible {
  var description: String {
    return "boxName: \(name), boxFrame: \(size), dogs: \(dogs)"
  }
}

var boxB = DogBox(boxName: "Best", boxSize: CGSize(width: 50, height: 50))
```



###强制解包到处都是

Swift 有一个很重要的概念，那就是**可选值 (Optional)** 的规定。这让很多初学 Swift 的人往往无所适从。尤其是在对 `NSDictionary` 之类的结构当中，很多人往往都这样直接 `!` 了事：

```swift
let name = dictionary["Name"] as! String
```

这种做法是很不安全的，因为万一这个结构发生了变化，导致其中并没有包含相应的值时，就会导致应用崩溃、闪退，这对导致很糟糕的用户体验。

很多人总是在抱怨 Swift 这个坏处，因为 Objective-C 不会对他们这么严格。我觉得，他们是被 Objective-C 惯坏了，保证值始终存在是一个安全的做法，能够把问题在开发周期中就暴露出来。

因此，我们往往需要进行可选值绑定的操作：

```swift
if let name = dictionary["Name"] as? String {
  // Do Something...
}
```

或者还可以：

```swift
guard let name = dictionary["Name"] as? String else { return }
```

尽量避免 `!` 的出现，即使您觉得万无一失！



###色调的使用

在开发应用的过程中，我们往往会遇到对应用色调使用自定义颜色的情况，很多时候大家经常这么做，这种做法实在是丑：

```swift
self.view.backgroundColor = UIColor(red: 233/255, green: 167/255, blue: 18/255, alpha: 1)
```

这个时候，我们其实可以对 `UIColor` 撰写 extension，使用的时候会方便很多：

```swift
extension UIColor {
  class var backgroundColor: UIColor { return self.whiteColor() }
  class var tabbarColor: UIColor { return self.blueColor() }
}
```

有时候，我们可能需要给用户提供自定义色调的功能的话，我们应该把它迁移到 `enum` 当中，这样就可以更为方便地读取配置文件了。

```swift
enum ConfigAppColor {
  case BackgroundColor
  case TabbarColor
  case NavigationBarColor

  var color: UIColor { return UIColor(RGB: self.getRGBFromConfigFile(self)) }

  private func getRGBFromConfigFile(type: ConfigAppColor) -> String {
    // 从配置文件中读取 RGB 字符串
  }
}
```