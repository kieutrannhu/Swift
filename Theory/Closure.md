# Tổng quan về Closure trong Swift
Trong Swift, closure là một khối mã có thể lưu trữ và truyền đi trong chương trình. Nó tương tự như function (có thể hiểu Closure là 1 function không tên), nhưng có thể được sử dụng như một biến hoặc một đối số trong các hàm khác.
Closure giúp code Swift trở nên gọn gàng, linh hoạt và mạnh mẽ! 🚀

## 1. Cú pháp cơ bản của Closure
```
{(paramerters) -> ReturnType in
  //code xử lý
}
```
- ```(parameter)```: Danh sách tham số truyền vào
- ```ReturnType```: Kiểu dữ liệu trả về
- ```in```: Ngăn cách giữa phần khai báo và phần thân của Closure
* Ví dụ:
```
let sayHi = {(firstName:String, lastName:String) -> String in
  return "Hello, i am \(firstName) \(lastName)"
}
sayHi("Kieu", "Tran Nhu")
```
## 2. Closure dạng inline
Closure có thể được truyền trực tiếp vào 1 func
```
func sample( a:Int, b:Int, operation: (Int, Int) ->Int) -> Int{
  return operation( a, b)
}

let result = sample( a:5, b:4, operation:{(x, y) -> Int in
  return x + y
})

print(result) // Output: 9
```
## 3. Viết ngắn gọn Closure bằng cú pháp ngắn hơn
Swift cung cấp nhiều cách viết gọn closure, giúp code dễ đọc hơn.
### 3.1 Bỏ kiểu dữ liệu nếu có thể suy luận
```
let result = sample( a:5, b:4, operation:{ x, y in
  return x + y
})
```
### 3.2. Bỏ luôn return khi chỉ có một dòng
```
let result = sample( a:5, b:4, operation:{ x, y in x + y })
```
### 3.3 Sử dụng tham số ngầm định $0, $1,...
```
let result = sample( a:5, b:4, operation:{ $0 + $1 })
```

### 3.4 Truyền trực tiếp func có sẵn
```
let result = sample( a:5, b:4, operation: + )
```
## 4. Trailing Closure (Closure ở cuối)
Trailing Closure là một cú pháp đặc biệt giúp viết closure gọn gàng hơn khi closure là tham số cuối cùng của một func.
### 4.1. Cú pháp của Trailing Closure
- Ví dụ 1: Closure truyền thống (Không dùng Trailing Closure)
```
func greet(action: (String) -> Void) {
    action("Swift")
}

greet(action: { name in
    print("Hello, \(name)!") //Output: Hello, Swift!

})
```
- Ví dụ 2: Dùng Trailing Closure (Viết gọn hơn)
```
greet { name in
    print("Hello, \(name)!") //Output: Hello, Swift!
}
```
📌 Lưu ý:
- Khi closure là tham số cuối cùng, ta có thể bỏ () và đặt closure ở ngoài.
- Nếu closure là tham số duy nhất, ta có thể bỏ luôn cả tên tham số.
### 4.2. Ứng dụng Trailing Closure trong Swift
- Ví dụ 1: ```sorted()```, ```map()```, ```filter()``` với Trailing Closure
  - Trailing closure rất hữu ích khi làm việc với các hàm bậc cao như map, filter, sorted.

📝 Trước khi dùng Trailing Closure
```
let numbers = [1, 2, 3, 4, 5]

let squares = numbers.map({ number in
    number * number
})

print(squares) // [1, 4, 9, 16, 25]
```

🚀 Dùng Trailing Closure (Viết gọn hơn)
```
let squares = numbers.map { $0 * $0 }
print(squares) // [1, 4, 9, 16, 25]
```

Ví dụ 2: Sử dụng filter()
```
let evenNumbers = numbers.filter { $0 % 2 == 0 }
print(evenNumbers) // [2, 4]
```

Ví dụ 3: sorted()
```
let names = ["Chris", "Alex", "Ewa", "Barry"]

let sortedNames = names.sorted { $0 < $1 }
print(sortedNames) // ["Alex", "Barry", "Chris", "Ewa"]
```
### 4.3. Trailing Closure với Multiple Closure Parameters
Khi một hàm có nhiều closure, chỉ closure cuối cùng được phép sử dụng Trailing Closure.
Ví dụ: Hàm có nhiều Closure
```
func performTask(success: () -> Void, failure: () -> Void) {
    let isSuccess = Bool.random()
    if isSuccess {
        success()
    } else {
        failure()
    }
}
```

🚀 Không dùng Trailing Closure
```
performTask(success: {
    print("Task completed successfully!")
}, failure: {
    print("Task failed!")
})
```

✅ Dùng Trailing Closure cho Closure Cuối Cùng
```
performTask(success: {
    print("Task completed successfully!")
}) {
    print("Task failed!")
}
```

### 4.4 Trailing Closure với UIView.animate (iOS)
Trailing Closure được sử dụng rộng rãi trong lập trình iOS, đặc biệt là khi làm việc với animations.

Ví dụ: Animation với UIView.animate
```
UIView.animate(withDuration: 1.0) {
    myView.alpha = 0.5
}
```
```UIView.animate(withDuration: 1.0, animations: { ... })``` có animations là closure cuối cùng, nên có thể bỏ animations:.

### 4.5 Trailing Closure với Completion Handler
Trailing closure cực kỳ hữu ích khi làm việc với Completion Handler.

Ví dụ: Gọi API với Completion Handler
```
func fetchData(completion: @escaping (String) -> Void) {
    DispatchQueue.global().async {
        sleep(2) // Giả lập thời gian tải dữ liệu
        completion("Data loaded successfully!")
    }
}

fetchData { result in
    print(result)
}
```

✅ Output (Sau 2 giây):
```
Data loaded successfully!
```
#
🔥 Tóm Tắt Quan Trọng
- Trailing Closure giúp viết Closure gọn hơn, đặc biệt khi closure là tham số cuối cùng của hàm.
- Dùng rộng rãi trong Swift, đặc biệt với các hàm như map(), filter(), sorted() và UIView.animate().
- Chỉ closure cuối cùng trong danh sách tham số có thể dùng Trailing Closure.
- Hữu ích khi làm việc với Completion Handlers trong API hoặc xử lý bất đồng bộ.

🚀 Trailing Closure giúp code Swift dễ đọc hơn, ngắn gọn hơn và mạnh mẽ hơn! 🚀

## 5. AutoCapture và Strong/Weak Reference
Closure trong Swift có thể tự động bắt giữ biến bên ngoài, nhưng có thể gây retain cycle (vòng lặp giữ tham chiếu).

Ví dụ: Closure giữ mạnh (strong reference)
```
class Person {
    var name: String
    init(name: String) {
        self.name = name
    }

    func sayHello() {
        let greeting = { print("Hello, I'm \(self.name)") }
        greeting()
    }
}

let person = Person(name: "Swift")
person.sayHello() // Output: Hello, I'm Swift
```

Khắc phục retain cycle bằng ```[weak self]```
```
class Person {
    var name: String

    init(name: String) {
        self.name = name
    }

    func sayHello() {
        let greeting = { [weak self] in
            guard let self = self else { return }
            print("Hello, I'm \(self.name)")
        }
        greeting()
    }
}
```

## 6. Escaping Closures
- Mặc định, closure trong Swift là non-escaping, nghĩa là closure sẽ kết thúc bên trong phạm vi của hàm mà nó được truyền vào.
- Tuy nhiên, nếu closure cần được lưu trữ hoặc sử dụng sau khi hàm kết thúc, ta cần đánh dấu closure đó bằng ```@escaping```.
  
Ví dụ: Closure lưu trữ để sử dụng sau
```
var savedClosure: (() -> Void)?

func storeClosureForLater(action: @escaping () -> Void) {
    savedClosure = action  // Lưu lại closure để dùng sau
}

storeClosureForLater {
    print("This runs later!")
}

// Gọi closure sau đó
savedClosure?()  // Output: This runs later!
```

📌 Vì ```action``` được lưu trữ sau khi ```storeClosureForLater``` kết thúc, nên nó cần ```@escaping```.

### Tại sao cần ```@escaping```?
Closure phải được đánh dấu ```@escaping``` khi:

- Closure được lưu trữ trong một biến hoặc danh sách để sử dụng sau.
- Closure được thực thi bất đồng bộ (asynchronously), ví dụ trong API call.
  
📌 Ví dụ 1: Closure lưu trữ (Như trên)
```
var completionHandlers: [() -> Void] = []

func addCompletionHandler(handler: @escaping () -> Void) {
    completionHandlers.append(handler) // Lưu closure vào danh sách
}
```
📌 Closure handler được lưu trữ trong danh sách nên cần ```@escaping```.

📌 Ví dụ 2: Escaping Closure trong API Call
Trong lập trình iOS, Escaping Closures thường được sử dụng với networking (API calls).

```
func fetchData(completion: @escaping (String) -> Void) {
    DispatchQueue.global().async {
        sleep(2) // Giả lập thời gian tải dữ liệu
        completion("Data loaded successfully!")
    }
}

fetchData { result in
    print(result)
}
```

✅ Output (Sau 2 giây):

```
Data loaded successfully!
```
📌 ```completion``` được gọi bất đồng bộ (sau khi ```fetchData``` kết thúc), nên nó cần ```@escaping```.

### Escaping Closure và Capture List ([weak self])
Khi dùng escaping closure, nếu closure giữ self mạnh (strong reference), nó có thể gây ra retain cycle (memory leak).

🔴 Lỗi retain cycle
```
class APIManager {
    var data: String?

    func loadData(completion: @escaping () -> Void) {
        DispatchQueue.global().async {
            self.data = "Fetched Data"
            completion()
        }
    }
}
```
📌 Closure giữ mạnh self, có thể gây retain cycle.

✅ Giải pháp: Dùng [weak self]
```
class APIManager {
    var data: String?

    func loadData(completion: @escaping () -> Void) {
        DispatchQueue.global().async { [weak self] in
            self?.data = "Fetched Data"
            completion()
        }
    }
}
```
✅ Giờ self không bị giữ mạnh, giúp tránh memory leak.

### Escaping Closure với Autoclosure (@autoclosure @escaping)

```@autoclosure``` giúp biến một biểu thức thành một closure. Kết hợp với ```@escaping```, nó có thể lưu trữ và gọi sau.

Ví dụ: ```@autoclosure``` với ```@escaping```
```
var pendingTasks: [() -> Bool] = []

func addTask(_ task: @escaping @autoclosure () -> Bool) {
    pendingTasks.append(task)
}

addTask(5 > 3) // Biểu thức `5 > 3` sẽ tự động được đóng gói thành closure

print(pendingTasks[0]()) // Output: true
```
📌 Thay vì truyền { 5 > 3 }, ta chỉ cần truyền 5 > 3, giúp code gọn hơn.

### Tóm lại, ```@escaping``` rất quan trọng khi closure được lưu trữ hoặc chạy bất đồng bộ. Luôn kiểm tra retain cycle khi dùng Escaping Closure trong class! 🚀

## 📌 Tóm tắt
1. Closure là một khối mã có thể lưu trữ và truyền đi trong chương trình.
2. Có cú pháp { (parameters) -> ReturnType in code }.
3. Có thể viết gọn bằng cách bỏ return, sử dụng $0, $1.
4. @escaping được dùng khi closure được lưu trữ để sử dụng sau này.
5. Sử dụng [weak self] để tránh retain cycle.
6. Ứng dụng phổ biến: map(), filter(), sort(), completion handler trong API.

   Closure giúp code Swift trở nên gọn gàng, linh hoạt và mạnh mẽ! 🚀

# Mở rộng
## 🧠 Closure được lưu trên bộ nhớ như thế nào trong Swift?
Closure trong Swift có cách quản lý bộ nhớ đặc biệt do khả năng bắt giữ biến (capture variables) từ phạm vi bên ngoài. Điều này có thể gây ra retain cycle (vòng lặp giữ tham chiếu) nếu không được quản lý đúng cách.

## 1. Cách Closure Lưu Trữ Trên Memory
Khi bạn khai báo một closure trong Swift, hệ thống sẽ lưu trữ nó trong heap nếu nó được gán cho một biến hoặc được sử dụng ở nơi cần lưu trữ lâu dài.

- Closure là tham chiếu (reference type), ngay cả khi khai báo nó như một biến.
- Nếu closure không bắt giữ biến bên ngoài, nó sẽ hoạt động như một hàm bình thường và không cần lưu trên heap.
- Nếu closure bắt giữ biến bên ngoài, nó sẽ tạo một closure context (bối cảnh closure) trên heap để giữ giá trị của biến.

## 2. Capture List: Closure Bắt Giữ Biến Như Thế Nào?
Closure có thể bắt giữ biến theo hai cách:

- Strong Capture (Mặc định - Giữ mạnh) 🟥
    - Closure sẽ giữ mạnh (strong reference) biến bên ngoài.
    - Nếu closure và biến đối tượng tham chiếu lẫn nhau, có thể gây ra retain cycle.
- Weak & Unowned Capture (Giữ yếu - Tránh retain cycle) 🟩
    - weak: Giữ yếu, có thể bị nil (cần Optional).
    - unowned: Không giữ, nhưng nếu đối tượng bị giải phóng thì sẽ crash nếu truy cập.
 
📌 Ví dụ minh họa
🟥 Strong Capture (Gây retain cycle)
```
class Person {
    var name: String
    var greeting: (() -> Void)?

    init(name: String) {
        self.name = name
    }

    func sayHello() {
        greeting = {
            print("Hello, I'm \(self.name)")
        }
    }
}

var person: Person? = Person(name: "Swift")
person?.sayHello()
person = nil // ❌ Person không được giải phóng do retain cycle
```
Lỗi retain cycle xảy ra vì greeting giữ mạnh self, ngăn Person bị giải phóng.

🟩 Sử dụng ```[weak self]``` để tránh retain cycle
```
class Person {
    var name: String
    var greeting: (() -> Void)?

    init(name: String) {
        self.name = name
    }

    func sayHello() {
        greeting = { [weak self] in
            guard let self = self else { return }
            print("Hello, I'm \(self.name)")
        }
    }
}

var person: Person? = Person(name: "Swift")
person?.sayHello()
person = nil // ✅ Person được giải phóng
```
```[weak self]``` giúp self không bị giữ mạnh, tránh retain cycle.
### 3. Closure Lưu Trữ Ở Stack Hay Heap?
- Closure đơn giản (không bắt giữ biến): lưu trữ tại Stack
- Closure bắt giữ biến bên ngoài: lưu trữ tại Heap ()
  Ví dụ so sánh
  
✅ Closure KHÔNG bắt giữ biến (Lưu trên Stack)
```
let simpleClosure = { print("Hello, Swift!") }
simpleClosure() // Lưu trên stack, không cần reference trên heap.
```

🚀 Closure CÓ bắt giữ biến (Lưu trên Heap)
```
var number = 42
let captureClosure = { print("Number is \(number)") }
captureClosure() // Lưu trên heap vì giữ `number`
```
captureClosure cần giữ number, nên nó sẽ được lưu trên heap.

## 4. Làm sao để tránh memory leak với Closure?
🔥 Nguyên nhân gây Memory Leak
- Closure giữ mạnh self (Strong Reference)
- Bị retain cycle với property closure trong class
✅ Cách khắc phục
- Dùng ```[weak self]``` hoặc ```[unowned self]``` trong Capture List
- Giải phóng Closure khi không cần nữa (nil)
- Tránh lưu Closure không cần thiết trong property của class
Ví dụ:
```
class ViewController {
    var myClosure: (() -> Void)?

    func setupClosure() {
        myClosure = { [weak self] in
            print("Closure executed")
        }
    }
}
```
## Tóm tắt
### 1. Closure có thể lưu trên stack hoặc heap

Nếu không bắt giữ biến, nó nằm trên stack.
Nếu bắt giữ biến, nó nằm trên heap.
### 2. Closure giữ biến theo 3 cách

Mặc định: Strong Capture (Giữ mạnh)
```[weak self]```: Giữ yếu (Có thể nil)
```[unowned self]```: Không giữ (Không nil, nhưng có thể crash nếu bị giải phóng)
### 3. Avoid retain cycle (Vòng lặp giữ bộ nhớ)

Dùng ```[weak self]``` hoặc ```[unowned self]``` nếu cần.
Set closure = nil khi không cần thiết.
💡 Closure là một tính năng mạnh mẽ, nhưng cần hiểu cách nó lưu trữ trên bộ nhớ để tránh lỗi retain cycle và memory leak. 🚀














