# Class và Struct trong Swift
## 1. Class
Trong Swift, ```class``` là một kiểu dữ liệu quan trọng để định nghĩa đối tượng (```object```) theo lập trình hướng đối tượng (OOP). 
```class``` hỗ trợ kế thừa (```inheritance```), tham chiếu (```reference type```), và deinitializer (```deinit```).

### 1.1 Khai báo ```class```
```
class Person {
    var name: String
    var age: Int

    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }

    func introduce() {
        print("Hello, my name is \(name) and I'm \(age) years old.")
    }
}

```

Tạo 1 đối tượng ```(instance)``` của ```class```
```
let person = Person(name: "Nhu", age: 27)
person.introduce() // Output: Hello, my name is Nhu and I'm 27 years old.
```

## 1.2 Class là Reference Type (Kiểu Tham Chiếu)

Khi bạn gán một ```object``` cho một biến khác, cả hai biến sẽ tham chiếu đến cùng một vùng nhớ.
Điều này khác với ```struct``` (kiểu giá trị - Value Type), khi bạn sao chép nó sẽ tạo một bản sao độc lập.

- Ví dụ về Reference Type: dùng ```class``` Person ở mục 1.1
```
let person1 = Person(name: "Alice", age: 30)
let person2 = person1 // person2 tham chiếu đến cùng vùng nhớ với person1

person2.name = "Bob"

print(person1.name) // Output: Bob
print(person2.name) // Output: Bob
```
📌 Cả ```person1``` và ```person2``` cùng tham chiếu đến một vùng nhớ, nên thay đổi trên ```person2``` cũng làm thay đổi ```person1```.

### 1.3 Kế thừa (```Inheritance```)

Class trong Swift hỗ trợ kế thừa, giúp tạo ra class con kế thừa từ class cha
- Ví dụ về kế thừa: dùng ```Person``` ở mục 1.1 làm ```class``` cha
```
class Employee: Person {
    var jobTitle: String

    init(name: String, age: Int, jobTitle: String) {
        self.jobTitle = jobTitle
        super.init(name: name, age: age) // Gọi init của class cha
    }

    override func introduce() {
        print("Hello, my name is \(name), I'm \(age) years old and I work as a \(jobTitle).")
    }
}

let employee = Employee(name: "Kieu Tran Nhu", age: 27, jobTitle: "iOS Developer")
employee.introduce()
// Output: Hello, my name is Kieu Tran Nhu, I'm 27 years old and I work as a iOS Developer.
```
 Lưu ý:
  - Dùng ```super.init(...)``` để gọi initializer của class cha.
  - Dùng ```override``` để ghi đè phương thức của class cha.
### 1.4 Deinitializer (```deinit```) - Dọn Dẹp Bộ Nhớ
Class có ```deinit```, được gọi khi object bị giải phóng khỏi bộ nhớ.
```
class Car {
    var model: String

    init(model: String) {
        self.model = model
        print("\(model) is created")
    }

    deinit {
        print("\(model) is being deallocated")
    }
}

var myCar: Car? = Car(model: "Tesla Model S")
myCar = nil // Output: Tesla Model S is being deallocated
```
Dùng ```deinit``` để giải phóng tài nguyên hoặc làm sạch dữ liệu khi ```object``` bị huỷ

### 1.5 Computed Properties trong Class

```
class Rectangle {
    var width: Double
    var height: Double

    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }

    var area: Double {
        return width * height
    }
}

let rect = Rectangle(width: 5, height: 10)
print(rect.area) // Output: 50

```
```area``` là ```computed property``` (không lưu giá trị, chỉ thực hiện tính toán khi gọi đến)

### 1.6 Static Properties & Methods

- ```static```: Thuộc tính dùng chung cho toàn bộ ```class```, không thuộc về ```instance``` cụ thể.
- ```class```: Cho phép ```override``` trong ```subclass```.
```
class Math {
    static let pi = 3.14159

    static func square(_ number: Int) -> Int {
        return number * number
    }
}

print(Math.pi) // Output: 3.14159
print(Math.square(5)) // Output: 25
```

### 1.7  Protocols với Class

Class có thể tuân theo ```(conform)``` một protocol.
```
protocol Drawable {
    func draw()
}

class Circle: Drawable {
    func draw() {
        print("Drawing a circle")
    }
}

let shape: Drawable = Circle()
shape.draw() // Output: Drawing a circle
```
📌 Dùng ```protocol``` để đảm bảo ```class``` thực hiện các phương thức cần thiết.

### 1.8 Weak và Unowned References (Tránh Retain Cycle)
```Class``` sử dụng reference type, dễ gây retain cycle (vòng lặp giữ bộ nhớ).

Ví dụ Retain Cycle
```
class Person {
    var name: String
    var pet: Pet?

    init(name: String) { self.name = name }
    deinit { print("\(name) is deallocated") }
}

class Pet {
    var owner: Person?

    init() {}
    deinit { print("Pet is deallocated") }
}

var person: Person? = Person(name: "John")
var pet: Pet? = Pet()

person?.pet = pet
pet?.owner = person

person = nil
pet = nil // ❌ Retain cycle xảy ra, không giải phóng bộ nhớ
```
#### Dùng ```[weak]``` hoặc ```[unowned]``` để tránh Retain Cycle

```
class Person {
    var name: String
    weak var pet: Pet? // Dùng weak

    init(name: String) { self.name = name }
    deinit { print("\(name) is deallocated") }
}

class Pet {
    weak var owner: Person? // Dùng weak

    init() {}
    deinit { print("Pet is deallocated") }
}

var person: Person? = Person(name: "John")
var pet: Pet? = Pet()

person?.pet = pet
pet?.owner = person

person = nil // ✅ Giải phóng bộ nhớ
pet = nil // ✅ Giải phóng bộ nhớ
```
📌 Dùng ```[weak]``` khi có thể là nil, dùng ```[unowned]``` khi chắc chắn không bị nil.

### 🔥 Tóm Tắt

✅ ```class``` là reference type, hỗ trợ kế thừa và deinitializer (```deinit```).

✅ Dùng ```@objc``` nếu cần tương thích với Objective-C.

✅ Tránh retain cycle bằng ```weak``` hoặc ```unowned```.

✅ Dùng ```static``` cho thuộc tính chung toàn bộ ```class```.

🚀 Swift class mạnh mẽ giúp xây dựng ứng dụng iOS chuyên nghiệp! 🚀

## 2. Struct
Trong Swift, ```struct``` (cấu trúc) là một kiểu dữ liệu quan trọng, hoạt động như một value type (kiểu giá trị). 
So với ```class```, ```struct``` có hiệu năng tốt hơn trong nhiều trường hợp do nó lưu trên stack và không gây ra retain cycle.

### 2.1 Khai Báo struct Trong Swift
Cú pháp của struct tương tự như class, nhưng không hỗ trợ kế thừa.
```
struct Person {
    var name: String
    var age: Int

    func introduce() {
        print("Hello, my name is \(name) and I'm \(age) years old.")
    }
}

// Tạo một instance
var person1 = Person(name: "Alice", age: 25)
person1.introduce() // Output: Hello, my name is Alice and I'm 25 years old.
```

📌 Điểm quan trọng:

- ```struct``` tự động có ```initializer``` mặc định.
- Các ```instance``` của ```struct``` là ```immutable``` (nếu không khai báo var).

### 2.2 Struct Là Value Type (Kiểu Giá Trị)
Khác với ```class``` (kiểu tham chiếu - reference type), khi gán một ```struct``` cho biến khác, Swift sao chép giá trị thay vì tạo một tham chiếu.

Ví dụ về Value Type
```
struct Animal {
    var name: String
}

var cat1 = Animal(name: "Kitty")
var cat2 = cat1  // Sao chép giá trị

cat2.name = "Tom"

print(cat1.name) // Output: Kitty
print(cat2.name) // Output: Tom
```
📌 Khác biệt với class: Mỗi biến có một bản sao độc lập.

### 2.3 Mutating Methods (Thay Đổi Thuộc Tính Trong struct)
Vì ```struct``` là kiểu giá trị, bạn không thể thay đổi thuộc tính trong một hàm bình thường. Để thay đổi, bạn cần dùng ```mutating```.
```
struct Counter {
    var value: Int = 0

    mutating func increment() {
        value += 1
    }
}

var counter = Counter()
counter.increment()  
print(counter.value) // Output: 1
```
📌 Hàm ```mutating``` giúp thay đổi dữ liệu bên trong ```struct```.

### 2.4 Computed Properties Trong struct
struct có thể có computed properties (thuộc tính được tính toán, không lưu giá trị).
```
struct Rectangle {
    var width: Double
    var height: Double

    var area: Double { // Computed property
        return width * height
    }
}

let rect = Rectangle(width: 5, height: 10)
print(rect.area) // Output: 50
```
📌 ```area``` không được lưu trữ, mà chỉ tính toán khi gọi.

### 2.5 Static Properties & Methods
```struct``` có thể có ```static properties``` và ```methods```, dùng chung cho toàn bộ kiểu dữ liệu.
```
struct Math {
    static let pi = 3.14159

    static func square(_ number: Int) -> Int {
        return number * number
    }
}

print(Math.pi) // Output: 3.14159
print(Math.square(5)) // Output: 25
```
📌 Dùng ```static``` khi không cần mỗi ```instance``` có giá trị riêng biệt.

### 2.6 Custom Initializers
Bạn có thể tạo ```initializer``` tùy chỉnh trong ```struct```.
```
struct Car {
    var brand: String
    var year: Int

    init(brand: String, year: Int = 2024) { // Giá trị mặc định
        self.brand = brand
        self.year = year
    }
}

let car1 = Car(brand: "Toyota")
let car2 = Car(brand: "Honda", year: 2022)

print(car1.year) // Output: 2024
print(car2.year) // Output: 2022
```
📌 Swift tự động cung cấp ```initializer``` mặc định nếu bạn không khai báo.

### 2.7 Struct Với Protocols
```struct``` có thể tuân theo (conform) một ```protocol```.
```
protocol Drawable {
    func draw()
}

struct Circle: Drawable {
    func draw() {
        print("Drawing a circle")
    }
}

let shape: Drawable = Circle()
shape.draw() // Output: Drawing a circle
```
📌 ```Protocol``` giúp ```struct``` có thể mở rộng dễ dàng.

### 2.8 Nested Structs (Struct Lồng Nhau)
Bạn có thể định nghĩa ```struct``` bên trong một ```struct``` khác.
```
struct Car {
    struct Engine {
        var horsepower: Int
    }

    var model: String
    var engine: Engine
}

let myCar = Car(model: "Tesla", engine: Car.Engine(horsepower: 500))
print(myCar.engine.horsepower) // Output: 500
```
📌 Dùng ```struct``` lồng nhau để tổ chức dữ liệu tốt hơn.

### 2.9 Tuple vs Struct
Bạn có thể dùng ```tuple``` thay vì ```struct``` cho dữ liệu đơn giản.
```
let point = (x: 10, y: 20) // Tuple
print(point.x) // Output: 10
```
📌 ```Tuple``` chỉ phù hợp cho dữ liệu tạm thời, không có logic.

### 🔥 Tóm Tắt

✅ ```struct``` là value type, mỗi ```instance``` là một bản sao riêng biệt.

✅ Dùng ```mutating``` để thay đổi thuộc tính trong ```struct```.

✅ ```struct``` nhanh hơn ```class``` vì không cần quản lý bộ nhớ trên heap.

✅ ```struct``` không hỗ trợ kế thừa, nhưng có thể mở rộng bằng ```protocol```.

✅ Dùng ```static``` để tạo thuộc tính chung cho toàn bộ ```struct```.

🚀 Chọn ```struct``` cho dữ liệu nhỏ, bất biến và tránh rủi ro về bộ nhớ! 🚀

## 3. So sánh ```class``` và ```struct```

- ```class``` là ```reference type``` còn ```struct``` là ```value type```
- Nơi lưu trữ: ```class``` được lưu trữ trên Heap còn ```struct``` lưu trữ trên Stack
- Khi gán biến: ```class``` tạo tham chiếu còn ```struct``` tạo bản sao độc lập
- ```class``` có Deinitializer ```(deinit)``` còn ```struct``` không có
- Mutability(khả năng biến đổi) ```class``` dùng ```var``` còn ```struct``` dùng ```mutating```
  
### Khi Nào Dùng ```class``` Thay Vì ```struct```?

Dùng ```class``` khi:

✅ Cần kế thừa (inheritance).

✅ Cần sử dụng tham chiếu chung giữa nhiều biến.

✅ Cần deinitializer ```(deinit)```.

Dùng ```struct``` khi:

✅ Dữ liệu không cần thay đổi nhiều.

✅ Không cần kế thừa.

✅ Dữ liệu có ý nghĩa là một value type.




















