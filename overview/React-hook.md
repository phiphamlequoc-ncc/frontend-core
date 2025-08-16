# **Overview React Hook**

## 1. useState

### Khái niệm:

`useState` dùng để quản lý state trong component. Mỗi lần component render lại ghi nhớ và cập nhật lại state qua các lần render component. Dùng để khai báo và quản lý trạng thái của một function component.

### Hoạt động

Ban đầu khởi tạo giá trị cho state và React sẽ lưu giá trị state, mỗi lần gọi setState component sẽ render và update lại state mới và hiển thị giá trị state mới đó.

```jsx
const [state, setState] = useState(initialValue);
```

- **initialValue**: giá trị khởi tạo ban đầu của state
- **state**: định nghĩa tên biến có thể lưu giá trị hiện tại của state (có thể là chuỗi, số, Object, Array, Boolean,...)
- **setState**: hàm dùng để thay đổi giá trị của state. Khi gọi hàm setState thì React sẽ trigger render component và update lại state mới.

### Những trường hợp dùng useState

- Dùng để toggle giao diện UI
- Quản lý dữ liệu form
- Cập nhật trạng thái UI

=> **Kết luận**: `useState` dùng khi cần render UI và data có thể sẽ được cập nhật. Nếu data tĩnh thì không cần dùng `useState`

## 2. useEffect

## Khái niệm

`useEffect` dùng để quản lý vòng đời lifecycle trong React. Xử lý các tác vụ side effect trong function component

## Hoạt động

`useEffect` bao gồm 3 giai đoạn:

```jsx
useEffect(() => {
  return () => {};
}, [dependency]);
```

- Mounting: component sẽ chạy lần đầu tiền thì useEffect sẽ chạy sau khi component render lần đầu tiên
- Updating: Khi state thay đổi hoặc props thay đổi thì useEffect sẽ chạy và sẽ phụ thuộc vào dependencies. <br>
  Dependencies có thể là state , props hoặc tất cả các biến và hàm được khai báo trực tiếp trong thành phần của component.
  - Nếu truyền [] : sẽ chạy 1 lần sau khi render
  - Nếu truyền [params]: sẽ chạy sao khi params thay đổi
  - Nếu không truyền gì thì chạy mỗi lần component render và mỗi lần render thì useEffect được gọi.
- Unmounting: Xóa component ra khỏi DOM, hàm cleanup trong useEffect sẽ được gọi. <br>
  Hàm cleanup có thể return 1 function khác và function này sẽ được thực thi khi component đó được unmount.<br>
  cleanup function thường dùng để remove listener + timer, cancel call api.

### Những trường hợp sử dụng useEffect

- Call api , event listener
- Thiết lập hoặc xóa timer (setInterval, setTimeout)

### Tại sao lại cần useEffect

- Giúp viết code clean và rõ ràng hơn, chia theo các tác vụ cụ thể
- Giúp kiểm soát khi nào sideEffect được thực thi như chạy khi component render lần đầu tiên hoặc khi 1 state , props cụ thể thay đổi
- Giúp trong việc xử lý các tác vụ bất đồng bộ như call api và nó cho phép xử lý kết quả trả về sau khi component được render

### 3. Problem

- Trong js, mỗi khi định nghĩa 1 fn mới dù có nội dung giống nhau thì mỗi hàm vẫn có địa chỉ khác nhau trong bộ nhớ.

```jsx
const a = () => {
  console.log("1");
};
const b = () => {
  console.log("1");
};
console.log(a === b);
```

- Ban đầu chương trình trên, thấy nội dung giống nhau đều in ra 1 và ban đầu nghĩ fn a sẽ bằng với fn b -> true
- Nhưng thực tế là: Mỗi lần component render lại thì fn a và b sẽ được tạo lại mới và tham chiếu đến 1 địa chỉ khác nhau -> React sẽ thấy khác nhau -> component sẽ re-reder. <br>
  => Vậy kết quả trên sẽ là false.
- Trong react, mỗi lần component re-render thì tất cả các hàm được định nghĩa trong component sẽ được tạo lại <br>
  Điều này dẫn đến thay đổi tham chiếu, khiến react nghỉ prop truyền xuống component con đã thay đổi và component con sẽ bị render không cần thiết

## 4. useCallback

### Khái niệm

`useCallback` giúp ghi nhớ 1 function đã được định nghĩa giữa mỗi lần component render lại giúp tránh re-render khi truyền xuống component con, tránh việc render không cần thiết.

### Hoạt động
```jsx
useCallback(() => {
  //logic 
},[dependencies])
```
- Khi component được re-render lại thì các function được khai báo trong component sẽ được tạo lại
- `useCallback` giúp ghi nhớ lại hàm fn và chỉ tạo lại mới khi dependency thay đổi
- Trong những lần render tiếp theo, react sẽ so sánh dependencies hiện tại với dependencies của lần trước.
- Nếu dependencies không đổi: React sẽ trả về fn cũ đã được ghi nhớ ( không tạo lại hàm mới )
- Nếu dependencies có thay đổi: React sẽ trả về hàm mới và ghi nhớ lại.

### Những trường hợp sử dụng
- Khi truyền hàm xuống component con và component con được bao bọc bởi React.memo()
- Khi hàm nằm trong dependencies của `useEffect`

### Tại sao cần dùng useCallback
- Ngăn chặn khởi tạo lại 1 hàm nào đó mỗi khi component re-render 
- Tránh re-render không cần thiết. 

## 5. useMemo
### Khái niệm
useMemo giúp ghi nhớ lại kết quả tính toán của một biểu thức nặng, tránh việc tính toán lại mỗi lần component re-render.

### Hoạt động
```jsx
useMemo(() => {

},[dependencies])
```
- Khi component render lần đầu tiên, React sẽ chạy lại callback, tính toán và ghi nhớ kết quả tính toán lại
- Trong các lần render tiếp theo: 
  * React sẽ so sánh dependencies mới với dependencies trước đó
  * Nếu dependencies không đổi -> React sẽ trả về giá trị đã ghi nhớ, không tính toán lại
  * Nếu dependencies thay đổi -> React chạy lại và tính toán lại giá trị mới và lưu lại cho các lần render tiếp theo

### Những trường hợp sử dụng useMemo
- Khi xử lý logic nặng như filter mảng, sort hay các tính toán phức tạp 
- Giúp tránh chạy lại mỗi khi component re-render

### Tác hại lạm dụng useCallback và useMemo
- Không nên lạm dụng trong những trường hợp không cần thiết vì gây tốn RAM

### Kết luận
- Vì vậy nên sử dụng useMemo và useCallback trong những trường hợp không cần thiết hoặc tối ưu cho component con quan trọng, hàm và biểu thức nằm trong `useEffect`. 

## 6 React.memo
### Khái niệm
`React.memo` giúp ghi nhớ kết quả render của component con
Nếu props hay state component không thay đổi thì React sẽ bỏ qua render lại và dùng lại kết quả render trước đó.

### Hoạt động
- Khi component cha re-render thì tất cả component con cũng sẽ re-render
- Với React.memo
  * React sẽ so sánh props hay state hiện tại với props và state trước đó 
  * Nếu props hay state không thay đổi -> component con sẽ không re-render
  * Nếu props hay state thay đổi -> component con sẽ render lại

### Dùng trong những trường hợp
+ Kết hợp với useCallback 
+ Component con không cần re-render mỗi khi component cha render (khi props hay state không thay đổi)

## 7. useRef
### Khái niệm
Dùng để tạo một đối tượng tham chiếu có thể lưu trữ giá trị qua các lần render nhưng không làm component re-render khi giá trị thay đổi

### Hoạt động
```jsx
const ref = useRef(initialValue)
```
* useRef sẽ nhận một giá trị khởi tạo 
Khi component render lần đầu tiên, React trả về 1 object có dạng 
```jsx
{
  current: initialValue
}
```
* Object này sẽ giữ nguyên tham chiếu trong suốt vòng đời component
* Trong các lần render tiếp theo
  - React sẽ không tạo lại object mới cho useRef, luôn trả về cùng một object (cùng địa chỉ trong bộ nhớ)
  - Nếu thay đổi ref.current, giá trị thay đổi nhưng không trigger re-render

* **Kết luận**: Là 1 function nhận 1 giá trị khởi tạo , trả về 1 object có key là current, khi component re-render nó đều tham chiếu đến 1 object duy nhất và không tạo lại object mới.

### Dùng trong những trường hợp
- **Tham chiếu đến phần tử DOM**  
  Ví dụ: focus input, scroll vào một phần tử, trigger click.  

- **Lưu giá trị qua các lần render mà không trigger re-render**  
  Dùng để giữ biến tạm, counter, timeout, interval... mà không cần hiển thị ra UI.  

- **Kết hợp với useEffect để kiểm soát side effect**  
  Dùng như một cờ (flag) để tránh việc gọi API nhiều lần, đặc biệt trong React khi StrictMode khiến `useEffect` chạy 2 lần trong môi trường dev.

- **Dùng useRef để tracking giá trị mới nhất của useState**
### Tại sao cần sử dụng useRef
- Giữ nguyên giá trị giữa các lần re-render component mà không bị reset lại mỗi lần component re-render
- Giữ được tham chiếu đến DOM element
- `useRef` khi thay đổi ref.current khi component sẽ không bị render và không làm thay đổi UI
- Giá trị luôn được giữ lại khi mỗi lần component re-render lại, việc thay đổi giá trị của 1 tham chiếu sẽ không gây ra component re-render.

### 8. So sánh giữa useRef và useState

- **Giống nhau**: Đều khởi tạo 1 giá trị ban đầu và thay đổi được giá trị
- **Khác nhau**: 
  - `useRef`: Khi mà thay đổi giá trị không gây ra render component và không hiển thị ra UI
  - `useState`: Khi mà thay đổi lại giá trị state thì component sẽ render và hiện thị ra UI <br>
- **Kết luận**: 
  - Khi muốn lưu dữ liệu và cập nhật dữ liệu hiển thị UI -> `useState`
  - Khi muốn lưu dữ liệu và cập nhật dữ liệu không làm component re-render và không hiển thị ra UI -> `useRef`


event loop, hositing, closure 
memory head/stack , immutable
