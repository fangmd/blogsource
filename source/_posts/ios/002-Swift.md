---
title: Swift
date: 2017-05-18 13:18:12
tags: ios
category: ios

---


# 输出

```
pring([string-type])

var friendlyWelcome:string
friendlyWelcome = "fangmingdong"
print("The current value of friendlyWelcome is \(friendlyWelcome)")
```

<!--more-->

# 基本数据

1. 在32位平台上， Int 的长度和 Int32 相同。
2. 在64位平台上， Int 的长度和 Int64 相同。s
3. 在32位平台上， UInt 长度和 UInt32 长度相同。
4. 在64位平台上， UInt 长度和 UInt64 长度相同。
5. Double代表 64 位的浮点数。
6. Float 代表 32 位的浮点数。
7. Bool
8. Tuples
9. Optionals
10. nil


## 常量 变量

```swift

let [value-name] : [value-type]

let [value-name] = [value] // 更具 value 的值，自动对 value-name 变量指定类型

var [value-name] : [value-type]

let constactsvalue = 10

var varvalue = 0

```


## 数值类型转换

整数转换

```
SomeType(ofInitialValue)
```

整数和浮点数转换

```
let three = 3
let pointOneFourOneFiveNine = 0.14159
let pi = Double(three) + pointOneFourOneFiveNine
```


## 类型别名

```
typealias AudioSample = UInt16
var maxAmplitudeFound = AudioSample.min
```


## Tuple

作为函数的返回值很有用。

```
let http404Error = (404, "Not Found")

let (statusCode, statusMessage) = http404Error

print("The status code is \(statusCode)")
print("The status message is \(statusMessage)")

let (justTheStatusCode, _) = http404Error
```

在定义元组的时候给其中的单个元素命名：

```
let http200Status = (statusCode: 200, description: "OK")
print("The status code is \(http200Status.statusCode)")
```

## Optional Binding

```
if let constantName = someOptional { 
    statements 
} 
```

如果 someOptional 里面有值就表示 ture

## Implicitly Unwrapped Optionals

# 错误处理

```
func makeASandwich() throws {
    // ...
}
 
do {
    try makeASandwich()
    eatASandwich()
} catch Error.OutOfCleanDishes {
    washDishes()
} catch Error.MissingIngredients(let ingredients) {
    buyGroceries(ingredients)
}
```

# Assertions

```
assert(_:_:)
assert(age >= 0, "A person's age cannot be less than zero")
assert(age >= 0)
```

# 基本运算符

1. 赋值运算符： = 
2. 算术运算符： + - * / %

    一元减号运算符
    一元加号运算符

3. 组合赋值符号: += -+ /= *=
4. 比较运算符
5. 三元条件运算符
6. 合并空值运算符

    表达式 a 必须是一个可选类型
    a != nil ? a! : b

    ```
    let defaultColorName = "red"
    var userDefinedColorName: String? // defaults to nil
    var colorNameToUse = userDefinedColorName ?? defaultColorName
    ```

```
let three = 3
let minusThree = -three // minusThree equals -3

let minusThree = +three // minusThree equals 3
```    
```


区间运算符:

```
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}
```

半开区间运算符:

```
let names = ["Anna", "Alex", "Brian", "Jack"]
let count = names.count
for i in 0..<count {
    print("Person \(i + 1) is called \(names[i])")
}
```

逻辑运算符

- 逻辑 非  ( !a )
- 逻辑 与  ( a && b )
- 逻辑 或  ( a || b )


# String

## 申明

```swift
var temp = "hello"

var emptyStr = ""

var emptyStr = new String()
```

判断 String 是否为空：

```
emptyStr.isEmpty
```

## 操作字符串

### 遍历:

```swift
for character in "Dog!🐶".characters {
    print(character)
}
```

Character convert to String:

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
```

### concat:

```
let str1 = "hello"
let str2 = "haha"

var newStr = str1 +str2

var newStr2 = ""

newStr2 += newStr

// 追加 Character
let exclamationMark: Character = "!"
newStr2.append(exclamationMark)

```


### 字符串插值: 

```swift
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
```

### 字符统计:

```
var str = "hello"

str.characters.count
```

### 字符串获取：

```
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

如果 String为空，则 startIndex与 endIndex相等


### 插入／删除

使用 `insert(_:at:)` 在指定位置插入一个单个字符，使用 `insert(contentsOf:at:)` 插入字符串

```
var str = "hello"

str.insert("1", at: str.endindex)

str.insert(contentsOf: " there".characters, at: str.index(before: str.endIndex))
```

使用 `remove(at:)` 移除单个字符，`removeSubrange(_:) ` 移除多个字符

```
var welcome = "hello"

welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome now equals "hello there"

let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome now equals "hello"
```

### 比较

==， !=

### 前缀和后缀比较

使用 `hasPrefix(_:)`,`hasSuffix(_:)`


# 集合类型

## 数组

有序，可重复

与 java 相比：这里的数组长度是可变的

### 创建数组

```
var someInts = [int]()

someInts.append(3)

someInts = []

// 初始化的时候设置值
var shoppingList: [String] = ["Eggs", "Milk"]
```

### 创建有默认值的数组

创建固定数量的数组，并设置数组的默认值

```
var threeDoubles = Array(repeating: 0.0, count: 3)
// threeDoubles is of type [Double], and equals [0.0, 0.0, 0.0]
```

### 连接两数组

使用 `+`

### 修改和数组

```
shoppingList.count

shoppingList.isEmpty

shoppingList.append("Flour")

var firstItem = shoppingList[0]

shoppingList[4...6] = ["Bananas", "Apples"]

shoppingList.insert("Maple Syrup", at: 0)

let mapleSyrup = shoppingList.remove(at: 0)

let apples = shoppingList.removeLast()
```

### 循环

```
for item in shoppingList {
    print(item)
}

for (index, value) in shoppingList.enumerated() {
    print("Item \(index + 1): \(value)")
}
```


## Set 合集

无序，不重复

### Set 类型的哈希值

存储在 set 中的元素必须是可哈希的，set 中判断两个元素是不是相同 == 是比较两个元素的哈希值。

### 创建，增，删，改，查

```
// 创建
var letters = Set<String>()

var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
var favoriteGenres: Set = ["Rock", "Classical", "Hip hop"]

// 插入
letters.insert("sd")

// 置空
letters = []

//
favoriteGenres.count
favoriteGenres.isEmpty
let removedGenre = favoriteGenres.remove("Rock") // 返回值为删除的项，或者 nil
favoriteGenres.contains("Funk")
favoriteGenres.sorted()
```

### 基本合集操作

- intersection(_:)
- symmetricDifference(_:)
- union(_:)
- subtracting(_:)

### 合集之间的关系判断

- isSubset(of:)
- isSuperset(of:)
- isStrictSubset(of:)
- isDisjoint(with:)

## Dictionaries

### 语法

```
var namesOfIntegers = [Int: String]()
var airports: [String: String] = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]
var airports = ["YYZ": "Toronto Pearson", "DUB": "Dublin"]

namesOfIntegers[16] = "sixteen"

airports.count

airports.isEmpty

airports["LHR"] = "London"

let oldValue = airports.updateValue("Dublin Airport", forKey: "DUB")

let airportName = airports["DUB"]


for (airportCode, airportName) in airports {
    print("\(airportCode): \(airportName)")
}

for airportCode in airports.keys {
    print("Airport code: \(airportCode)")
}
// Airport code: YYZ
// Airport code: LHR
 
for airportName in airports.values {
    print("Airport name: \(airportName)")
}

let airportCodes = [String](airports.keys)
// airportCodes is ["YYZ", "LHR"]
 
let airportNames = [String](airports.values)
// airportNames is ["Toronto Pearson", "London Heathrow"]

```

# 控制流

## for in

遍历

```
for index in 1...5 {
    print("\(index) times 5 is \(index * 5)")
}

for _ in 1...power {
    answer *= base
}

let minutes = 60
for tickMark in 0..<minutes {
    // render the tick mark each minute (60 times)
}

let minuteInterval = 5
for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
    // render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}

// 闭区间
let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}


```


## while repeat-while


```
while condition{
    statment
}


repeat {
    // ...
} while condition
```

## 条件语句

- if
- if else
- Switch


switch：

1. 每个 case 后面不需要加 break，可以在一个 case 后加多个项来达到原来穿透的效果
2. 支持区间匹配 `case 100..<1000:`
3. 结合 tuple 使用

    ```
    case (0, _):
    print("\(somePoint) is on the y-axis")
    case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
    ```
4. case 中值绑定

    ```
    case (let x, 0):
    ```
5. case 中使用 Where

    ```
    case let (x, y) where x == y:
    ```
6. 支持复合情况

## 流程转移符号

- continue
- break
- fallthrough：在 switch 中使用让 case 贯穿
- return
- throw

## 给语句打标签

```
label name: while condition {
    statements
}
```

配合 break, continue 使用，达到在多层循环中跳转

## 检查 api 可用性

```
if #available(iOS 10, macOS 10.12, *) {
    // Use iOS 10 APIs on iOS, and use macOS 10.12 APIs on macOS
} else {
    // Fall back to earlier iOS and macOS APIs
}
```

# 函数

## 定义和调用函数

```
func greet(person: String) -> String {
    let greeting = "Hello, " + person + "!"
    return greeting
}
```

箭头后面的数据类型表示函数返回的数据类型

## 参数和返回值 parameters and return values


无返回值：实际会返回特殊类型 Void ，是一个空的元组写作 `()`

```
func greet(person: String){
    //..
}
```

## 多个返回值

传入一个数组，返回最大值和最小值

```
func minMax(array: [Int]) -> (i=min: Int, max: Int){
    var currentMin = array[0]
    var currentMax = array[0]

    //....

    return (currentMin, currentMax)
}

let bounds = minMax(array: [8, -3, 120, 23, 23])
print("min is \(bounds.min) and max is \(bounds.max)")
```

### 设置返回空 Tuple

1. 在返回值的后面加 `?` 
2. 在方法里面返回 `nil`

```
func minMax(array: [Int]) -> (min: Int, max:Int)?{
    if array.isEmpty { return nil }
    //...
}
```


## 方法参数 Labels 和 参数名

每个方法参数都有一个 argument label 和 parameter name

### Specifying Argument Labels 定义参数标签

```
func someFunction(argumentLabel parameterName: Int) {
    // In the function body, parameterName refers to the argument value
    // for that parameter.
}
```


参数标签可以让方法更具可读性，比如：

```
func greet(person: String, from hometown: String) -> String {
    return "Hello \(person)!  Glad you could visit from \(hometown)."
}
print(greet(person: "Bill", from: "Cupertino"))
// Prints "Hello Bill!  Glad you could visit from Cupertino."
```

### 忽略 Argument Labels

```
func someFunction(_ firstParameterName: Int, secondParameterName: Int) {
    // In the function body, firstParameterName and secondParameterName
    // refer to the argument values for the first and second parameters.
}
someFunction(1, secondParameterName: 2)

```

使用下划线设置忽略，调用方法的时候就不需要写 参数标签，如果参数有参数标签，在方法被调用的时候就必须要指定参数名

### 默认 Parameer Values

```
func someFunction(parameterWithoutDefault: Int, parameterWithDefault: Int = 12) {
    // If you omit the second argument when calling this function, then
    // the value of parameterWithDefault is 12 inside the function body.
}
someFunction(parameterWithoutDefault: 3, parameterWithDefault: 6) // parameterWithDefault is 6
someFunction(parameterWithoutDefault: 4) // parameterWithDefault is 12
```

### 可变数量的参数

```
func arithmeticMean(_ numbers: Double...) -> Double {
    var total: Double = 0
    for number in numbers {
        total += number
    }
    return total / Double(numbers.count)
}
arithmeticMean(1, 2, 3, 4, 5)
```

### In-Out 参数

```
func swapTwoInts(_ a: inout Int, _ b: inout Int) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

表示在方法内对参数作出的改变，会影响这个参数在方法外的值

## 方法类型

```
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
```

`(Int, Int) -> Int`: 表示方法参数是两个 int 类型的参数，返回值是一个 int 类型的数据。

`() -> Void`: 无参数，无返回值的方法，简写为`()`

### 使用 方法类型

定义一个 方法类型 的变量：

```
var mathFunction: (Int, Int) -> int = addTwoIns

print("Result: \(mathFunction(2, 3))")
// Prints "Result: 5"
```

### 方法类型 作为方法的参数

```
func printMathResult(_ mathFunction: (Int, Int) -> Int, _ a: Int, _ b: Int) {
    print("Result: \(mathFunction(a, b))")
}
printMathResult(addTwoInts, 3, 5)
// Prints "Result: 8"
```

### 方法类型 作为返回值

```
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}

func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}
```

## 方法嵌套

局部方法，在外部不能访问

```
func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    func stepForward(input: Int) -> Int { return input + 1 }
    func stepBackward(input: Int) -> Int { return input - 1 }
    return backward ? stepBackward : stepForward
}
```

# Closures





=========================

# 初始化

为类，结构体或者枚举准备实例的过程。

## 为存储属性设置初始化值

### 初始化器

```
init() {
    // perform some initialization here
}
```

### 初始化形式参数


```
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
}
```


### 无实际参数标签的初始化器形式参数


```
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}
let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```

第三个构造方法被调用的时候不需要指定标签

如果定义了外部参数名就必须用在初始化器里，省略的话会报一个编译时错误



### 可选属性类型

初始化部分属性：需要在属性后面添加 `?` ，自动分配 `nil`

```
class SurveyQuestion {
    var text: String
    var response: String?
    init(text: String) {
        self.text = text
    }
    func ask() {
        print(text)
    }
}
```

### 在初始化中分配常量属性

常量值可以在初始化的时候赋值

## 默认初始化器

对于没有定义 初始化器  的类，swift 提供了默认的 初始化器

### 结构体类型的成员初始化器

如果结构体类型中没有定义任何自定义初始化器，它会自动获得一个成员初始化器。

```
struct Size {
    var width = 0.0, height = 0.0
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

## 值类型的初始化器委托

初始化器委托:初始化器可以调用其他初始化器来执行部分实例的初始化。

值类型(结构体和枚举)不支持继承，所以他它们的初始化器委托的过程相当简单，因为它们只能提供它们自己为另一个初始化器委托。

## 类的继承和初始化

所有类的存储属性——包括从它的父类继承的所有属性——都必须在初始化期间分配初始值。

### 指定初始化器和便捷初始化器

指定初始化器是类的主要初始化器

```
init(parameters) {
    statements
}
```


便捷初始化器是次要的，为一个类支持初始化器。

```
convenience init(parameters) {
    statements
}
```

### 类类型的初始化器委托

为了简化指定和便捷初始化器之间的调用关系，Swift 在初始化器之间的委托调用有下面的三个规则:

1. 指定初始化器必须从它的直系父类调用指定初始化器。
2. 便捷初始化器必须从相同的类里调用另一个初始化器。
3. 便捷初始化器最终必须调用一个指定初始化器。


- 指定初始化器必须总是向上委托。
- 便利初始化器必须总是横向委托。


### 两段式初始化

1. 第一个阶段: 每一个存储属性被引入类为分配了一个初始值
2. 第二个阶段: 每个类都有机会在新的实例准备使用之前来定制它的存储属性

### 初始化器的继承和重写

Swift 的子类不会默认继承父类的初始化器

让子类 初始化器 匹配父类初始化器的时候需要重写： `OVERRIDE`


