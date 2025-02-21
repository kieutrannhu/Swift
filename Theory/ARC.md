# Automatic Reference Counting

Automatic Reference Counting (ARC) là một cơ chế quản lý bộ nhớ trong ngôn ngữ lập trình Swift. 
ARC tự động giải phóng bộ nhớ mà một đối tượng chiếm giữ khi không còn có tham chiếu nào đến đối tượng đó. 
Điều này giúp giảm thiểu rủi ro của các lỗi bộ nhớ như rò rỉ bộ nhớ (memory leaks) và trỏ treo (dangling pointers).
ARC hoạt động bằng cách theo dõi số lượng “tham chiếu mạnh” (strong references) đến một đối tượng. 
Nếu không còn tham chiếu mạnh nào, đối tượng sẽ được giải phóng.

#
## Chi tiết

### Cách hoạt động của ARC

ARC hoạt động bằng cách theo dõi số lượng tham chiếu mạnh đến một đối tượng.

- Mỗi khi tạo một tham chiếu mạnh mới đến một đối tượng, ARC tăng số lượng tham chiếu của đối tượng đó lên.
- Khi tham chiếu mạnh đến một đối tượng bị giải phóng, ARC giảm số lượng tham chiếu của đối tượng đó đi.
- Khi số lượng tham chiếu của một đối tượng đạt đến 0, nghĩa là không còn tham chiếu mạnh nào đến đối tượng đó. ARC giải phóng bộ nhớ mà đối tượng đó đang chiếm giữ.

-> Không cần phải nghĩ về việc giải phóng bộ nhớ mỗi khi tạo một đối tượng
``` Swift
class MyClass {
    var name: String

    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }

    deinit {
        print("\(name) is being deinitialized")
    }
}

var reference1: MyClass? = MyClass(name: "My Class Instance")
var reference2 = reference1
var reference3 = reference1

reference1 = nil
reference2 = nil
reference3 = nil // Khi gán nil cho reference3 thì bộ nhớ được giải phóng
```

### Tham chiếu mạnh (Strong References)

Trong ngữ cảnh của ARC trong Swift, một tham chiếu mạnh (Strong Reference) là một tham chiếu đến một đối tượng.
Mà không cho phép đối tượng đó bị giải phóng bởi hệ thống quản lý bộ nhớ tự động (ARC) miễn là tham chiếu mạnh đó vẫn tồn tại.

Tuy nhiên, tham chiếu mạnh có thể dẫn đến vấn đề  Retain cycle, nơi mà hai đối tượng hoặc nhiều hơn tham chiếu lẫn nhau mà không giải phóng, dẫn đến rò rỉ bộ nhớ.

Cách xử lý vấn đề Retain cycle là sử dụng ```weak reference``` (tham chiếu yếu) hoặc ```unowned reference``` (tham chiếu không sở hữu)

``` Swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}


class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    unowned var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john

john = nil
unit4A = nill // Gây ra Retain cycle

```
Trong ví dụ trên, tạo hai lớp ```Person``` và ```Apartment```. 
Mỗi ```Person``` có thể có ```Apartment```, và mỗi ```Apartment``` có một ```tenant``` là một ```Person```. 
Điều này tạo ra một chu trình tham chiếu mạnh giữa hai đối tượng ```Person``` và ```Apartment```.

Để giải quyết vấn đề này, sử dụng một tham chiếu không sở hữu ```(unowned reference)``` cho thuộc tính ```tenant``` của ```Apartment```. 
Điều này có nghĩa là khi ```Apartment``` bị giải phóng, nó không giữ lại tham chiếu mạnh đến ```tenant``` của nó, do đó không ngăn ```tenant``` đó được giải phóng.

Khi gán ```nil``` cho ```john``` và ```unit4A```, cả hai đối tượng đều được giải phóng, và không có rò rỉ bộ nhớ nào xảy ra.

``` Swift
unowned var tenant: Person?
```

### Tham chiếu yếu (Weak References)

Một tham chiếu yếu ```(Weak Reference)``` là một loại tham chiếu không tăng số lượng tham chiếu của một đối tượng.
Điều này có nghĩa là nó không ngăn cản ARC giải phóng đối tượng mà nó tham chiếu khi đối tượng đó không còn tham chiếu mạnh nào khác.

Tham chiếu yếu thường được sử dụng để tránh vòng tham chiếu mạnh ```(strong reference cycles)```. Khi hai đối tượng tham chiếu lẫn nhau.

Một tham chiếu yếu phải được khai báo như một biến ```optional```. 
Vì nó có thể trở thành ```nil``` bất cứ lúc nào.
Khi đối tượng mà nó tham chiếu không còn tồn tại, tham chiếu yếu sẽ tự động trở thành ```nil```.

Ví dụ:
``` Swift
class Person {
    let name: String
    weak var friend: Person?

    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) is being deinitialized")
    }
}

var alice: Person? = Person(name: "Alice")
var bob: Person? = Person(name: "Bob")

alice?.friend = bob
bob?.friend = alice

alice = nil
bob = nil
```
Khi chúng ta gán ```nil``` cho ```alice``` và ```bob```, cả hai đối tượng đều được giải phóng, và không có rò rỉ bộ nhớ nào xảy ra.


### Tham chiếu không sở hữu (Unowned References)

Tham chiếu không sở hữu không tăng số lượng tham chiếu của một đối tượng. Tuy nhiên, khác với tham chiếu yếu,

Tham chiếu không sở hữu không phải là ```optional```.
Không tự động trở thành ```nil``` khi đối tượng mà nó tham chiếu bị giải phóng.
Do đó, nên chỉ sử dụng tham chiếu không sở hữu khi chắc chắn rằng đối tượng mà nó tham chiếu sẽ luôn tồn tại trong thời gian mà tham chiếu không sở hữu tồn tại.

Ví dụ ở mục Tham chiếu mạnh
``` Swift
unowned var tenant: Person?
```

### Chu trình tham chiếu mạnh (Strong Reference Cycles hoặc Retain cycle)

“Strong Reference Cycles” hoặc “Retain Cycles” trong Swift là tình huống mà hai đối tượng hoặc nhiều hơn tham chiếu lẫn nhau thông qua tham chiếu mạnh, tạo ra một chuỗi tham chiếu không bao giờ giải phóng.
-> Gây rò rỉ bộ nhớ, vì ARC không thể giải phóng bất kỳ đối tượng nào trong chuỗi tham chiếu, ngay cả khi chúng không còn được sử dụng nữa.
Ví dụ 
``` Swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}


class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
    var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}

var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john

john = nil
unit4A = nill // Gây ra Retain cycle

```

Để giải quyết vấn đề, sử dụng ```unowwned reference``` 

#### Retain Cycles và ảnh hưởng tới hệ thống
Hậu quả của việc này là:

- Bộ nhớ không được giải phóng: Khi một đối tượng không còn được sử dụng, nó nên được giải phóng để giải phóng bộ nhớ. 
Tuy nhiên, trong một Retain Cycle, các đối tượng không thể được giải phóng. Dẫn đến việc bộ nhớ không được giải phóng.

- Tăng tiêu thụ bộ nhớ: Khi các đối tượng không được giải phóng. Chúng tiếp tục chiếm bộ nhớ, dẫn đến việc tiêu thụ bộ nhớ tăng lên.
  
- Hiệu suất ứng dụng giảm: Khi bộ nhớ bị chiếm dụng bởi các đối tượng không còn được sử dụng. Có ít bộ nhớ hơn cho các hoạt động khác, có thể làm giảm hiệu suất của ứng dụng.

- Ứng dụng có thể bị crash: Nếu ứng dụng tiếp tục tạo ra các đối tượng mà không giải phóng chúng. Ứng dụng có thể sử dụng hết bộ nhớ. Và cuối cùng bị hệ điều hành tắt để giải phóng bộ nhớ.


### Chu trình tham chiếu mạnh giữa đối tượng và closure (Strong Reference Cycles Between Class Instances and Closures)

Xảy ra khi một ```closure``` nắm giữ một tham chiếu mạnh đến một đối tượng. Và đối tượng đó cũng nắm giữ một tham chiếu mạnh đến ```closure``` đó tạo ra một chu trình tham chiếu mạnh không thể giải phóng.

Điều này thường xảy ra khi bạn gán một ```closure``` cho một thuộc tính của đối tượng. Và ```closure``` đó truy cập đến ```self``` (đối tượng chứa nó). Khi đó, ```closure``` sẽ nắm giữ một tham chiếu mạnh đến ```self```, tạo ra một chu trình tham chiếu mạnh.

Để giải quyết vấn đề này, có thể sử dụng ```[weak self]``` hoặc ```[unowned self]``` trong danh sách nắm giữ ```(capture list)``` của ```closure``` để tạo một tham chiếu yếu hoặc không sở hữu đến ```self```. Giúp ngăn chặn việc tạo ra chu trình tham chiếu mạnh.

Ví dụ:
``` Swift
class MyClass {
    var value = 0
    lazy var closure: () -> () = {
        self.value += 1
    }

    deinit {
        print("MyClass instance is being deinitialized")
    }
}

var instance: MyClass? = MyClass()
instance?.closure()
instance = nil
```

```closure``` nắm giữ một tham chiếu mạnh đến ```self``` (đối tượng MyClass).
Vì nó truy cập vào thuộc tính ```value``` của ```self```.
Đồng thời, ```self``` cũng nắm giữ một tham chiếu mạnh đến ```closure```, vì ```closure``` là một thuộc tính của ```self```.
Điều này tạo ra một chu trình tham chiếu mạnh.

Khi gán ```nil``` cho ```instance```, đối tượng ```MyClass``` không được giải phóng. Vì ```closure``` vẫn nắm giữ một tham chiếu mạnh đến nó.

Điều này dẫn đến rò rỉ bộ nhớ.

Để giải quyết vấn đề này, có thể sử dụng ```[weak self]``` hoặc ```[unowned self]``` trong danh sách nắm giữ (capture list) của ```closure```:

``` Swift
class MyClass {
    var value = 0
    lazy var closure: () -> () = { [weak self] in
        self?.value += 1
    }
    deinit {
        print("MyClass instance is being deinitialized")
    }
}
var instance: MyClass? = MyClass()
instance?.closure()
instance = nil
```

Bây giờ, khi gán ```nil``` cho ```instance```, đối tượng ```MyClass``` được giải phóng, và không có rò rỉ bộ nhớ nào xảy ra.

## Tóm tắt

Automatic Reference Counting (ARC) là một cơ chế quản lý bộ nhớ tự động trong Swift. ARC hoạt động bằng cách theo dõi và quản lý số lượng tham chiếu mạnh đến một đối tượng.

Điểm chú ý:

- ARC chỉ áp dụng cho các đối tượng của lớp. Không áp dụng cho các kiểu dữ liệu giá trị như struct hay enum.

- ARC tự động giải phóng một đối tượng khi không còn tham chiếu mạnh nào đến đối tượng đó.

- ARC không thể giải phóng một đối tượng, nếu có một chu trình tham chiếu mạnh.

Hậu quả:

- Rò rỉ bộ nhớ: Khi có một chu trình tham chiếu mạnh, các đối tượng trong chu trình không thể được giải phóng. Dẫn đến rò rỉ bộ nhớ.

- Tiêu thụ bộ nhớ tăng lên: Khi các đối tượng không được giải phóng. Chúng tiếp tục chiếm bộ nhớ, dẫn đến việc tiêu thụ bộ nhớ tăng lên.

- Hiệu suất ứng dụng giảm: Khi bộ nhớ bị chiếm dụng bởi các đối tượng không còn được sử dụng. Thì sẽ có ít bộ nhớ hơn cho các hoạt động khác. Và có thể làm giảm hiệu suất của ứng dụng.

Cách khắc phục:

- Sử dụng tham chiếu yếu ```(weak reference)``` hoặc tham chiếu không sở hữu ```(unowned reference)```. Để ngăn chặn chu trình tham chiếu mạnh.
  
- Trong các ```closure```, sử dụng danh sách nắm giữ (capture list) với ```[weak self]``` hoặc ```[unowned self]``` để ngăn chặn chu trình tham chiếu mạnh giữa đối tượng và ```closure```.

## Mở rộng
### Trỏ treo (dangling pointers)

Trỏ treo ```(Dangling Pointer)``` là một con trỏ tham chiếu đến một vùng bộ nhớ đã bị giải phóng hoặc không còn hợp lệ, gây ra lỗi truy cập bộ nhớ hoặc hành vi không xác định ```(undefined behavior)```.

🚨 Trong Swift, dangling pointers ít xảy ra do cơ chế Automatic Reference Counting (ARC) và không có con trỏ trực tiếp như trong C/C++. 
Tuy nhiên, chúng vẫn có thể xuất hiện trong một số trường hợp đặc biệt, như ```unowned references```, ```Unsafe Pointers```, hoặc khi làm việc với Objective-C APIs.

#### Ví Dụ 1: Trỏ Treo Với ```unowned Reference```
Nếu một biến ```unowned``` tham chiếu đến một đối tượng đã bị ```deallocated```, truy cập nó sẽ gây lỗi crash.

``` Swift
class Person {
    var name: String
    var card: CreditCard?

    init(name: String) {
        self.name = name
    }

    deinit {
        print("\(name) is being deinitialized")
    }
}

class CreditCard {
    var number: String
    unowned var owner: Person  // Không thể là nil, nhưng có nguy cơ trở thành dangling pointer

    init(number: String, owner: Person) {
        self.number = number
        self.owner = owner
    }

    deinit {
        print("Credit Card \(number) is being deinitialized")
    }
}

// Tạo một đối tượng Person
var bob: Person? = Person(name: "Bob")
var card: CreditCard? = CreditCard(number: "1234-5678", owner: bob!)

bob = nil  // Bob bị deallocated, nhưng card vẫn giữ `unowned owner`
print(card?.owner.name)  // ❌ Lỗi: Truy cập bộ nhớ đã bị giải phóng
```

📌 Lỗi xảy ra:

```bob``` bị giải phóng khi gán ```nil```.

```card?.owner``` bây giờ là dangling pointer, nhưng vẫn trỏ đến vùng nhớ cũ.
Truy cập ```owner.name``` sẽ gây crash!

✅ Cách khắc phục:

Dùng ```weak var owner: Person?``` thay vì ```unowned``` nếu đối tượng có thể bị giải phóng.

#### Ví Dụ 2: Trỏ Treo Khi Sử Dụng ```UnsafePointer```
 
Trong Swift, ```UnsafePointer``` cho phép truy cập trực tiếp vào vùng nhớ như trong C/C++. Tuy nhiên, nếu sử dụng sai, bạn có thể gặp trỏ treo (dangling pointers) – con trỏ trỏ đến vùng nhớ đã bị giải phóng hoặc không hợp lệ, dẫn đến lỗi truy cập bộ nhớ ```(undefined behavior)``` hoặc crash chương trình.

#### Trỏ Treo Khi Trả Về ```UnsafePointer``` Từ Biến Cục Bộ

❌ Ví dụ Lỗi: Trả về con trỏ trỏ vào biến cục bộ

``` Swift
func getPointer() -> UnsafePointer<Int> {
    var number = 42
    return UnsafePointer(&number)  // ❌ Trả về con trỏ trỏ đến biến cục bộ
}

let ptr = getPointer()
print(ptr.pointee)  // ❌ Lỗi: Truy cập bộ nhớ đã bị giải phóng
```

📌 Lỗi xảy ra:

```number``` là biến cục bộ, được lưu trên ```stack```.

Khi ```getPointer()``` kết thúc, biến ```number``` bị giải phóng, nhưng ```ptr``` vẫn trỏ vào đó.

Truy cập ```ptr.pointee``` sẽ dẫn đến hành vi không xác định ```(undefined behavior)```.

✅ Cách sửa: Sử dụng cấp phát bộ nhớ động ```(malloc)``` hoặc biến toàn cục.

✅ Cách Đúng: Dùng ```withUnsafePointer```

``` Swift
func getPointerSafely() {
    var number = 42
    withUnsafePointer(to: &number) { ptr in
        print(ptr.pointee)  // ✅ Hợp lệ vì số vẫn còn trong phạm vi hàm
    }
}

getPointerSafely()  // Output: 42
```

📌 Lý do đúng:

```withUnsafePointer(to:)``` đảm bảo ```number``` vẫn tồn tại trong suốt thời gian ```closure``` chạy.

Khi ```withUnsafePointer``` kết thúc, con trỏ không còn hợp lệ, tránh lỗi.

#### Trỏ Treo Khi Dùng ```UnsafeMutablePointer```

❌ Ví dụ Lỗi: Dùng con trỏ trỏ đến vùng nhớ đã bị giải phóng

``` Swift
func createPointer() -> UnsafeMutablePointer<Int> {
    let ptr = UnsafeMutablePointer<Int>.allocate(capacity: 1)
    ptr.initialize(to: 100)
    ptr.deallocate()  // ❌ Giải phóng bộ nhớ ngay lập tức
    return ptr
}

let ptr = createPointer()
print(ptr.pointee)  // ❌ Lỗi: Truy cập vùng nhớ đã giải phóng
```

📌 Lỗi xảy ra:

```ptr``` được cấp phát động, nhưng bị giải phóng ngay sau khi cấp phát.

Khi truy cập ```ptr.pointee```, nó trỏ vào vùng nhớ không hợp lệ, gây crash hoặc lỗi không xác định.

✅ Cách sửa: Không giải phóng bộ nhớ quá sớm.

✅ Cách Đúng: Giải phóng bộ nhớ sau khi sử dụng

``` Swift
func createPointerSafely() -> UnsafeMutablePointer<Int> {
    let ptr = UnsafeMutablePointer<Int>.allocate(capacity: 1)
    ptr.initialize(to: 100)
    return ptr  // Không giải phóng ngay
}

let ptr = createPointerSafely()
print(ptr.pointee)  // ✅ Output: 100

ptr.deallocate()  // ✅ Giải phóng sau khi sử dụng
```

📌 Lý do đúng:

Chỉ ```deallocate()``` khi chắc chắn không còn cần con trỏ.

#### Trỏ Treo Khi Làm Việc Với Mảng (UnsafeBufferPointer)
❌ Ví dụ Lỗi: Trả về con trỏ trỏ vào vùng nhớ tạm thời

``` Swift
func getBufferPointer() -> UnsafeBufferPointer<Int> {
    let array = [1, 2, 3]
    return UnsafeBufferPointer(start: array, count: array.count) // ❌ Trỏ vào vùng nhớ bị giải phóng
}

let buffer = getBufferPointer()
print(buffer[0])  // ❌ Lỗi: Truy cập vào vùng nhớ không hợp lệ
```

📌 Lỗi xảy ra:

```array``` là biến cục bộ, bị giải phóng khi ```getBufferPointer()``` kết thúc.

```UnsafeBufferPointer``` vẫn trỏ vào vùng nhớ cũ, gây lỗi.

✅ Cách sửa: Giữ ```array``` sống đủ lâu hoặc dùng ```withUnsafeBufferPointer```.

✅ Cách Đúng: Dùng ```withUnsafeBufferPointer```

``` Swift
let array = [1, 2, 3]

array.withUnsafeBufferPointer { buffer in
    print(buffer[0])  // ✅ Output: 1
}
```

📌 Lý do đúng:

```withUnsafeBufferPointer``` đảm bảo vùng nhớ của ```array``` tồn tại trong suốt ```closure```.

#### 🚀 Tóm lại:

Không bao giờ trả về con trỏ trỏ vào biến cục bộ.

Không giải phóng bộ nhớ quá sớm khi vẫn còn sử dụng con trỏ.

Dùng ```withUnsafePointer``` hoặc ```withUnsafeBufferPointer``` để tránh lỗi.

⚠️ Chỉ dùng ```UnsafePointer``` khi thực sự cần hiệu suất cao hoặc làm việc với C API!

#### Ví Dụ 3: Trỏ Treo Với Objective-C API
Khi sử dụng Core Foundation hoặc Objective-C APIs, có thể xảy ra lỗi trỏ treo do cách quản lý bộ nhớ khác nhau.

``` Swift
import Foundation

class MyClass {
    var callback: (() -> Void)?

    deinit {
        print("MyClass is being deinitialized")
    }
}

var obj: MyClass? = MyClass()
obj?.callback = {
    print("Callback executed")
}

obj = nil  // ❌ Nếu callback vẫn giữ `self`, có thể gây dangling reference
```
✅ Cách khắc phục:

Sử dụng ```[weak self]``` trong ```closures``` để tránh giữ tham chiếu không hợp lệ.

``` Swift
obj?.callback = { [weak obj] in
    print("Callback executed safely")
}
```

#### Tóm Tắt
✅ Trỏ treo xảy ra khi:

Dùng unowned reference trỏ đến một đối tượng đã bị giải phóng.

Dùng UnsafePointer trỏ đến bộ nhớ không còn hợp lệ.

Khi làm việc với Objective-C APIs, không xử lý memory management đúng cách.

✅ Cách tránh:

Dùng weak thay vì unowned nếu đối tượng có thể bị nil.

Không sử dụng con trỏ không an toàn (UnsafePointer) trỏ vào biến cục bộ.

Cẩn thận với memory management khi dùng Objective-C APIs.








