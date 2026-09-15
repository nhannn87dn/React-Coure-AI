# Outline lộ trình học React JS cho người mới

## Lesson 1: Giới thiệu React JS & Ôn tập JavaScript ES6+ Cốt Lõi

### Giới thiệu tổng quan về React JS

#### React JS là gì và tại sao nên chọn React?

#### Khái niệm Single Page Application (SPA) vs Multi-Page Application (MPA)

#### Luồng hoạt động cơ bản của Virtual DOM và so sánh với Real DOM

### Ôn tập JavaScript ES6+ bắt buộc cho React

#### Arrow Functions vs Traditional Functions (Cú pháp, từ khóa `this`)

#### Destructuring Assignment đối với Array và Object

#### Rest parameter và Spread operator (`...`) trong thao tác dữ liệu

#### Các phương thức xử lý mảng nâng cao: `map`, `filter`, `reduce`, `find`

#### Cú pháp Module trong ES6: `import` và `export` (Default export vs Named export)

---

## Lesson 2: Component & Cú Pháp JSX

### Khái niệm Component trong React

#### Tư duy chia nhỏ giao diện (Component-Driven Development)

#### Functional Component vs Class Component (Lý do chọn Functional Component)

#### Cấu trúc và quy ước đặt tên Component (PascalCase)

### Tìm hiểu về cú pháp JSX (JavaScript XML)

#### JSX là gì và cơ chế Babel biên dịch JSX sang JavaScript

#### Các quy tắc cú pháp bắt buộc trong JSX (Thẻ đóng, Single Root Element, Fragment)

#### Nhúng biểu thức JavaScript vào JSX sử dụng dấu ngoặc nhọn `{}`

#### Phân biệt thuộc tính HTML và thuộc tính JSX (`className`, `htmlFor`, camelCase styling)

---

## Lesson 3: Props & Tái Sử Dụng Component

### Bản chất của Props trong React

#### Props là gì và vai trò trong việc truyền dữ liệu từ Component cha xuống Component con

#### Nguyên tắc Immutability (Tính vô biến - Read-only) của Props

### Kỹ thuật làm việc với Props

#### Truyền và nhận Props linh hoạt (Primitive types, Objects, Arrays, Functions)

#### Rút gọn code nhận Props với Destructuring và Default Props

#### Tìm hiểu thuộc tính đặc biệt: `children` prop

#### Kiểm tra kiểu dữ liệu của Props với PropTypes

---

## Lesson 4: Quản Lý Trạng Thái Vượt Trội Với useState Hook

### Khái niệm State trong React

#### State là gì và sự khác biệt cốt lõi giữa State vs Props

#### Lý do không thể thay đổi biến thông thường để vẽ lại UI

### Sử dụng useState Hook

#### Cú pháp khai báo `useState` và hàm cập nhật State (updater function)

#### Cơ chế Async của State Update và Batching trong React

#### Cách cập nhật State phụ thuộc vào giá trị State cũ (Functional Update)

#### Quản lý State dạng dữ liệu phức tạp (Object State và Array State)

---

## Lesson 5: Event Handling & Render Theo Điều Kiện (Conditional Rendering)

### Xử lý sự kiện (Event Handling) trong React

#### Khái niệm Synthetic Events trong React

#### Lắng nghe và xử lý các sự kiện phổ biến: `onClick`, `onChange`, `onSubmit`, `onKeyDown`

#### Truyền tham số vào Event Handler đúng cách

### Các kỹ thuật Render theo điều kiện

#### Sử dụng câu lệnh `if/else` truyền thống

#### Sử dụng toán tử 3 ngôi (Ternary Operator `? :`)

#### Sử dụng toán tử điều kiện Logic AND (`&&`)

#### Xử lý giao diện nhiều trạng thái bằng Switch Case hoặc Helper Functions

---

## Lesson 6: Render Danh Sách (List Rendering) & Thẻ Key

### Render danh sách phần tử trong JSX

#### Phương thức `Array.prototype.map` trong việc chuyển đổi mảng dữ liệu thành JSX

#### Lọc và biến đổi dữ liệu danh sách kết hợp `filter` và `map`

### Cơ chế Reconciliation và thuộc tính Key

#### Tại sao React bắt buộc phải có thuộc tính `key` khi render danh sách?

#### Thẻ `key` giúp Virtual DOM tối ưu hóa hiệu năng render như thế nào?

#### Tác hại của việc dùng mảng Index làm `key` và giải pháp chọn Unique ID

---

## Lesson 7: Quản Lý Side Effects Với useEffect Hook

### Khái niệm Side Effect và Vòng đời Component

#### Side Effect là gì trong React (gọi API, tương tác DOM, timer, event listener)?

#### Chu kỳ sống của Component: Mounting, Updating, Unmounting

### Cách sử dụng useEffect Hook chuẩn mực

#### `useEffect` không có Dependency Array (Chạy lại sau mọi lần re-render)

#### `useEffect` với Dependency Array rỗng `[]` (Chạy 1 lần duy nhất khi mount)

#### `useEffect` với danh sách phụ thuộc `[deps]` (Chạy khi giá trị deps thay đổi)

#### Hàm dọn dẹp Cleanup Function và ứng dụng gỡ bỏ Event Listener, Clear Timer

---

## Lesson 8: Quản Lý Form Trong React (Controlled Components)

### Controlled Components trong React

#### Nguyên lý Single Source of Truth trong quản lý dữ liệu Form

#### Ràng buộc ô Input với State thông qua `value` và `onChange`

#### Xử lý Form có nhiều trường nhập liệu (Multi-input Form) chỉ với 1 State Object

### Kỹ thuật Form nâng cao & Uncontrolled Components

#### Giới thiệu Uncontrolled Components và `useRef` Hook khi làm việc với Form

#### Kiểm tra dữ liệu Form (Validation) cơ bản và hiển thị thông báo lỗi

#### Bắt sự kiện Submit Form và ngăn chặn hành vi reload trang (`e.preventDefault()`)

---

## Lesson 9: Gọi API (Data Fetching) & Quản Lý Trạng Thái Tải Dữ Liệu

### Kết nối React với Backend API

#### Tích hợp `fetch` API hoặc thư viện `axios` vào React

#### Kỹ thuật gọi API bên trong `useEffect` Hook

### Quản lý 3 trạng thái cơ bản của Async Data

#### Trạng thái Loading (Hiển thị UI chờ dữ liệu)

#### Trạng thái Success (Xử lý dữ liệu trả về và render danh sách)

#### Trạng thái Error (Bắt lỗi mạng/server và hiển thị thông báo lỗi cho người dùng)

#### Best practices xử lý bất đồng bộ Async/Await sạch sẽ trong useEffect

---

## Lesson 10: Chia Sẻ State Toàn Cục Với Context API & Tổng Kết Mini Project

### Vấn đề Prop Drilling & Giải pháp Context API

#### Prop Drilling là gì và tác hại khi ứng dụng phát triển lớn?

#### Khởi tạo Context với `createContext`

#### Cung cấp dữ liệu toàn cục với `Context.Provider`

#### Truy cập dữ liệu ở bất kỳ đâu bằng `useContext` Hook

### Tổng kết khóa học & Định hướng thực hành Mini Project

#### Tổ chức cấu trúc thư mục dự án React chuẩn mực (Folder Structure)

#### Hướng dẫn xây dựng Mini Project tổng hợp (VD: TodoApp, WeatherApp hoặc Cart System)

#### Lộ trình nâng cao tiếp theo: React Router (Điều hướng), State Management (Zustand / Redux Toolkit), Data Fetching (TanStack Query)