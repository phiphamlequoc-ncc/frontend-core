# **Overview Javascript Core**

## 1. Event loop

### Khái niệm

`Event loop` là cơ chế giúp Javascript ( ngôn ngữ single-thread ) có thể xử lý các tác vụ bất đồng bộ. Nó liên tục kiểm tra `Stack` và `Queue` để quyết định ai sẽ thực thi tiếp theo.

### Thành phần event loop

- Có 2 thành phần chính: **Stack** và **Queue** (bao gồm cả micro task và macro task)
- Stack: (Ngăn xếp) <br>
  Nơi lưu trữ và thực thi các function calls theo cơ chế **LIFO** ( Last In First Out ) hay còn gọi **vào sau ra trước**.
- Queue: (Hàng đợi) <br>
  Hoạt động theo nguyên tắc **FIFO** ( First In First Out ) hay còn gọi **vào trước ra trước**
  Queue gồm 2 loại chính:
  - MicroTask Queue: Promise, async/await ( ưu tiên cao hơn)
  - MacroTask Queue: setTimeout, setInterval, DOM event

### Cơ chế hoạt động

a. Code đồng bộ chạy đầu tiên -> được đưa vào và thực thi trong stack. <br>
b. Khi gặp async operation, stack được đưa vào vào queue tương ứng <br>
c. Event loop kiểm tra stack

- Nếu stack trống -> lấy task từ queue đưa vào stack
- Micro task luôn được ưu tiên hơn macro task.

```jsx
console.log("1"); // đồng bộ - vào stack ngay

setTimeout(() => {
  console.log("2"); // macro task
}, 1000);

Promise.resolve().then(() => {
  console.log("3"); // micro task
});

console.log("4"); // đồng bộ - vào stack ngay

// Result: 1,4,3,2
```

## 2. Memory Heap/Stack

### Stack

#### Cơ chế hoạt động:

- Hoạt động theo nguyên tắc LIFO (Last In – First Out).
- Phần tử được đưa vào sau cùng sẽ được lấy ra trước.

#### Chức năng:

- Dùng để lưu trữ các biến cục bộ (local variable) và tham số của hàm
- Mỗi khi một hàm được gọi, Javascript sẽ tạo ra một khung ngăn xếp để chứa tham số và biến cục bộ của hàm đó.
- Khi hàm kết thúc, stack frame này sẽ bị loại bỏ, bộ nhớ giải phóng tự động

#### Lợi ích

- Lưu trữ các biến cục bộ và tham số hàm giúp js quản lý bộ nhớ một cách hiệu quả.
- Khi một hàm kết thúc, các biến trong khung ngăn xếp của nó sẽ tự động xóa và giải phóng bộ nhớ.

```jsx
function add(a, b) {
  let sum = a + b; // a , b , sum năm trong Stack
  return sum;
}
// khi add() run -> tạo stack frame -> kết thúc thì xóa
let result = add(1, 2);
console.log(result); // kết quả: 3
```

### Head

#### Chức năng

- Dùng để lưu trữ các đối tượng object, mảng array, hàm hoặc dữ liệu phức tạp khác.
- Khi tạo 1 object/array/function, giá trị phức tạp khác được lưu trong `heap`, còn biến chỉ lưu trữ tham chiếu đến vị trí của giá trị đó trong `heap`

### Hoạt động

- Các giá trị trong Heap có thể được tạo, thay đổi.
- Nhiều biến có thể tham chiếu đến 1 vùng nhớ trong Heap

#### Lợi ích

Lưu trữ các đối tượng và giá trị phức tạp giúp JS quản lý bộ nhớ linh hoạt hơn.
Các giá trị trong Heap có thể được tạo, sửa đổi và xóa mà không ảnh hưởng đến các biến khác.

```jsx
// object được lưu trong heap,biến person giữ tham chiếu
let person = {
  name: "A",
  age: 25,
};

// anotherPerson cũng tham chiếu đến vùng nhớ Heap
let anotherPerson = person;
anotherPerson.age = 20;

console.log(person.age); // kqua : 20, vì cả 2 đều tham chiếu cùng 1 object trong heap
```

## 3. Closure

### Khái niệm

Closure trong JS được tạo ra khi một hàm được định nghĩa bên trong một hàm khác, cho phép hàm bên trong truy cập các biến và tham số của hàm bên ngoài, ngay cả khi hàm bên ngoài đã được thực thi xong.

### Phạm vi truy cập

- Biến toàn cục
- Biến được khai báo ở hàm chứa nó
- Biến trong chính hàm closure đó

### Lợi ích

Closure cho phép hàm bên trong ghi nhớ và truy cập các biến của hàm bên ngoài, ngay khi hàm bên ngoài đã kết thúc thực thi.
Giúp ghi nhớ giá trị của biến sau nhiều lần gọi hàm.

```jsx
function counter() {
  let count = 0; // biến này nằm trong phạm vi của closure
  return function () {
    count++;
    return count;
  };
}

const increase = counter();
increase(); // 1
increase(); // 2
increase(); // 3
// increase là 1 closure, ghi nhớ biến count trong fn counter, mặc dù counter đã thực thi xong.
```

## 4. Immutable

### Khái niệm

Immutable là tính chất không thay đổi của dữ liệu sao khi được tạo ra, có nghĩa là giữ nguyên không thay đổi giá trị của dữ liệu sau khi nó được khai báo. <br>
Khi một đối tượng hoặc giá trị bất biến, bất kỳ thao tác nào dường như thay đổi nó thực chất sẽ tạo ra 1 bản sao mới với các thay đổi, thay vì sửa đổi đối tượng gốc.

Các kiểu dữ liệu nguyên thủy trong JS : string, number, boolean, null, undefined, symbol và bigint đều là `immutable`

```jsx
let string = "Hello";
string[0] = "h"; // không thay đổi vì string là immutable
console.log(string); // Hello

let newString = string + "World"; // tạo ra chuỗi mới
console.log(newString); // Hello World

let x = 10;
x = x + 5;
// tạo ra 1 giá trị number mới 15, gán lại cho biến x
```

### Lợi ích

- Dể dàng theo dõi thay đổi
- Ứng dụng trong React/Redux: Khi state thay đổi, react cần so sánh state cũ và mới. Nếu state là immutable , dể dàng biết có sự thay đổi, render lại UI chính xác
