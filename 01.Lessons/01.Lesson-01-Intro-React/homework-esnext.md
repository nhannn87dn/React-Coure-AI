# 📝 Bài Tập Về Nhà — Bài 1: ESNext & TypeScript Cơ Bản

> Mục tiêu: Luyện thành thạo các cú pháp ESNext dùng hằng ngày trong React (destructuring, spread, array methods, `?.`/`??`, async/await) và biết gắn kiểu TypeScript cho dữ liệu và hàm.
>
> Cách làm: tạo thư mục `src/homework/` trong dự án Vite ở buổi học. **Phần A** có thể viết bằng JavaScript (`.js`). **Phần B** yêu cầu chuyển lời giải Phần A sang TypeScript (`.ts`) và làm thêm các bài riêng về TypeScript.

---

## Phần A — JavaScript ESNext

## Bài 1 — Chuẩn hóa và gộp cấu hình hệ thống

**1. Dữ liệu ban đầu:**

```javascript
const defaultConfig = {
  theme: 'light',
  showSidebar: true,
  timeout: 3000,
  version: '1.0.0'
};

const userConfig = {
  apiKey: 'secret-key-123',
  timeout: 5000
};
```

**2. Dựa vào kiến thức:**

* **Object Destructuring** (Phân rã object)
* **Spread Operator** (`...`)
* **Default Values** (Giá trị mặc định cho thuộc tính)

**3. Yêu cầu:**

* Viết hàm `mergeConfig(defaultConfig, userConfig)` trả về một cấu hình mới.
* Ưu tiên áp dụng thuộc tính của `userConfig` đè lên `defaultConfig`. Nếu `theme` trong `userConfig` không được định nghĩa, mặc định chọn `"light"`.
* Bóc tách riêng 2 thuộc tính `apiKey` và `version` nằm ở cấp ngoài cùng của object trả về, tất cả các thuộc tính còn lại gộp gọn vào một object con đặt tên là `settings`.

---

## Bài 2 — Thống kê điểm số linh hoạt

**1. Dữ liệu ban đầu:**

```javascript
// Dữ liệu tham số đầu vào linh hoạt (số lượng điểm không cố định)
// Ví dụ 1: analyzeScores("Toán", 8.5, 9.0, 6.5, 7.0, 10.0)
// Ví dụ 2: analyzeScores("Văn", 7.0, 8.0)
```

**2. Dựa vào kiến thức:**

* **Rest Parameters** (`...scores`)
* **Arrow Functions**
* **Array Methods** (`reduce`, `Math.max`, `Math.min`)

**3. Yêu cầu:**

* Viết hàm `analyzeScores(subjectName, ...scores)` nhận tham số đầu tiên là tên môn học và các tham số tiếp theo là các con điểm.
* Không dùng vòng lặp `for`, `while` truyền thống.
* Trả về object có dạng:

```javascript
{
  subject: "Toán",
  totalStudents: 5,
  highest: 10.0,
  lowest: 6.5,
  average: 8.2 // Làm tròn 2 chữ số thập phân
}
```

---

## Bài 3 — Bóc tách dữ liệu an toàn từ payload API

**1. Dữ liệu ban đầu:**

```javascript
const responseComplete = {
  user: {
    profile: { fullName: "Nguyễn Văn A", avatar: { url: "https://img.com/a.png" } },
    notifications: { unreadCount: 0 }
  }
};

const responseIncomplete = {
  user: {
    profile: null
  }
};
```

**2. Dựa vào kiến thức:**

* **Optional Chaining** (`?.`)
* **Nullish Coalescing Operator** (`??`)
* **Nested Destructuring**

**3. Yêu cầu:**

* Viết hàm `getUserDisplayInfo(apiResponse)` trích xuất thông tin an toàn, không làm ứng dụng bị văng lỗi (`TypeError`).
* `name`: Lấy từ `profile.fullName`, nếu thiếu thì lấy mặc định `"Khách"`.
* `avatar`: Lấy từ `profile.avatar.url`, nếu thiếu thì lấy mặc định `"/assets/default-avatar.png"`.
* `unreadMessages`: Lấy từ `notifications.unreadCount`. Nếu bằng `0` phải giữ nguyên `0` (không dùng toán tử `||` vì sẽ biến `0` thành giá trị mặc định), nếu `null`/`undefined` mới lấy `0`.

---

## Bài 4 — Cập nhật state bất biến (Immutability)

**1. Dữ liệu ban đầu:**

```javascript
const initialState = { user: "An", role: "admin" };
const targetKey = "theme";
const targetValue = "dark";
```

**2. Dựa vào kiến thức:**

* **Computed Property Names** (`[key]`)
* **Property Value Shorthand**
* **Immutability (Tính bất biến)**

**3. Yêu cầu:**

* Viết hàm `updateState(currentState, key, value)` cập nhật thuộc tính động `key` với giá trị `value` vào state mà không làm thay đổi (`mutate`) `currentState` ban đầu (phải trả về một object hoàn toàn mới).
* Viết hàm `createAction(type, payload)` sử dụng **Property Value Shorthand** để trả về object theo cấu trúc `{ type, payload, timestamp: Date.now() }`.

> 💡 Cú pháp `[key]: value` chính là cách bạn sẽ xử lý Form nhiều trường chỉ với 1 hàm `onChange` ở Bài 7.

---

## Bài 5 — Phân tách và hoán đổi hạng thi đấu

**1. Dữ liệu ban đầu:**

```javascript
const leaderboard = ["Alice", "Bob", "Charlie", "David", "Eva", "Frank"];
```

**2. Dựa vào kiến thức:**

* **Array Destructuring**
* **Rest Element in Destructuring** (`...others`)
* **Variable Swapping** (Tráo đổi giá trị biến)

**3. Yêu cầu:**

* Dùng **Array Destructuring** trích xuất vận động viên hạng nhất vào biến `gold`, hạng nhì vào biến `silver`, và gom tất cả các vận động viên còn lại vào mảng `others`.
* Khi có sự cố đổi hạng giữa `gold` và `silver`, sử dụng cú pháp hoán đổi giá trị biến của ES6 để tráo đổi giá trị của `gold` và `silver` chỉ trong **1 dòng code** duy nhất.

---

## Bài 6 — Xử lý giỏ hàng không làm thay đổi dữ liệu gốc

**1. Dữ liệu ban đầu:**

```javascript
const cart = [
  { id: 1, name: 'Áo thun', price: 150000, quantity: 2 },
  { id: 2, name: 'Quần jean', price: 350000, quantity: 1 },
];

const newProduct = { id: 3, name: 'Mũ lưỡi trai', price: 120000 };
```

**2. Dựa vào kiến thức:**

* **Array Methods** (`map`, `filter`, `find`, `reduce`)
* **Spread Operator** (`...`)
* **Immutability (Tính bất biến)**

**3. Yêu cầu:**

* Viết hàm `addToCart(cart, product)`: nếu sản phẩm đã có trong giỏ (dùng `find`) thì tăng `quantity` thêm 1, nếu chưa có thì thêm mới với `quantity: 1`.
* Viết hàm `updateQuantity(cart, id, quantity)`: cập nhật số lượng của sản phẩm theo `id` (dùng `map`). Nếu `quantity <= 0` thì xóa sản phẩm khỏi giỏ.
* Viết hàm `removeFromCart(cart, id)`: xóa sản phẩm theo `id` (dùng `filter`).
* Viết hàm `getCartTotal(cart)` tính tổng tiền và `getCartCount(cart)` tính tổng số lượng sản phẩm (dùng `reduce`).
* **Tất cả các hàm phải trả về mảng mới**, không dùng `push`, `splice` hay gán trực tiếp `item.quantity = ...`. Sau mọi thao tác, `console.log(cart)` phải cho thấy mảng `cart` ban đầu **không bị thay đổi**.

> 💡 Đây chính là logic bạn sẽ dùng lại khi cập nhật Array State ở Bài 4 và xây dựng giỏ hàng với Zustand ở Bài 9.

---

## Bài 7 — Gọi nhiều API cùng lúc với Async/Await

**1. Dữ liệu ban đầu:**

```javascript
// API công khai miễn phí:
// Danh sách người dùng: https://jsonplaceholder.typicode.com/users
// Danh sách công việc:  https://jsonplaceholder.typicode.com/todos
// Mỗi todo có dạng: { userId, id, title, completed }
```

**2. Dựa vào kiến thức:**

* **Async/Await** và **try/catch**
* **Promise.all** (tự tìm hiểu thêm: chạy nhiều Promise song song)
* **Array Methods** (`map`, `filter`, sắp xếp bằng `sort`)

**3. Yêu cầu:**

* Viết hàm `getUserTodoStats()` gọi **đồng thời** 2 API trên bằng `Promise.all`.
* Trả về mảng các object dạng `{ id, name, completedTodos }`, trong đó `completedTodos` là số công việc đã hoàn thành (`completed: true`) của người dùng đó. Sắp xếp giảm dần theo `completedTodos`.
* Nếu có lỗi khi gọi API: log ra `"Không tải được dữ liệu"` và trả về mảng rỗng `[]`.
* Dùng `console.time()`/`console.timeEnd()` so sánh thời gian giữa cách `await` lần lượt từng API và cách dùng `Promise.all`.

---

## Phần B — TypeScript

## Bài 8 — Chuyển giỏ hàng (Bài 6) sang TypeScript và tổ chức thành Module

**1. Dữ liệu ban đầu:** Lời giải Bài 6 của bạn.

**2. Dựa vào kiến thức:**

* **`type`** mô tả object, **thuộc tính tùy chọn** (`?`)
* **Khai báo kiểu cho tham số và giá trị trả về** của function
* **Named Export / Default Export**, import với alias (`as`)

**3. Yêu cầu:**

* Tạo cấu trúc file:

```text
src/homework/
├── types.ts    # type Product, type CartItem
├── cart.ts     # các hàm addToCart, updateQuantity, removeFromCart, getCartTotal, getCartCount (named export)
├── format.ts   # hàm formatCurrency (default export), trả về dạng "150.000đ"
└── main.ts     # import và chạy thử toàn bộ các hàm
```

* `Product` gồm `id`, `name`, `price`. `CartItem` gồm các thuộc tính của `Product` và thêm `quantity`.
* Khai báo kiểu cho **toàn bộ** tham số của các hàm. Không dùng `any`.
* Trong `main.ts`, import `formatCurrency` với tên khác (ví dụ `formatVND`) bằng alias.
* VS Code không được còn lỗi gạch đỏ nào.

---

## Bài 9 — Tìm và sửa lỗi TypeScript

**1. Dữ liệu ban đầu:**

```ts
type Student = {
  id: number;
  name: string;
  score: number;
  email?: string;
};

const students: Student[] = [
  { id: 1, name: 'An', score: '8.5' },
  { id: 2, name: 'Bình', score: 9, phone: '0901234567' },
  { id: 3, score: 7 },
];

function getTopStudent(list: Student[]): Student {
  return list.find((s) => s.score >= 9);
}

function getEmailDomain(student: Student): string {
  return student.email.split('@')[1];
}
```

**2. Dựa vào kiến thức:**

* **Kiểu dữ liệu cơ bản** và **`type`**
* **Thuộc tính tùy chọn** (`?`) và **Union Type** với `undefined`
* **Optional Chaining** (`?.`) và **Nullish Coalescing** (`??`)

**3. Yêu cầu:**

* Đoạn code trên có **5 lỗi** TypeScript. Dán vào VS Code hoặc [TypeScript Playground](https://www.typescriptlang.org/play), tìm đủ 5 lỗi.
* Với mỗi lỗi: ghi lại thông báo lỗi (tiếng Anh), giải thích bằng tiếng Việt vì sao sai, rồi sửa lại.
* **Không được** sửa bằng cách dùng `any`, `as` hay dấu `!`. Gợi ý: `find()` có thể không tìm thấy phần tử nào; `email` có thể không tồn tại.

---

## Bài 10 — Mô tả trạng thái gọi API bằng Union Type

**1. Dữ liệu ban đầu:**

```ts
type RequestStatus = 'idle' | 'loading' | 'success' | 'error';

type User = {
  id: number;
  name: string;
  email: string;
};
```

**2. Dựa vào kiến thức:**

* **Literal Type** và **Union Type**
* **`type`** cho object lồng nhau
* **Immutability** với Spread Operator

**3. Yêu cầu:**

* Viết hàm `getStatusMessage(status: RequestStatus): string` dùng `switch` để trả về thông báo tiếng Việt tương ứng (ví dụ `'loading'` → `"Đang tải dữ liệu..."`). Thử gọi `getStatusMessage('done')` và ghi lại lỗi TypeScript báo ra.
* Khai báo `type ApiState = { status: RequestStatus; data: User[]; error: string | null }`.
* Viết 3 hàm, mỗi hàm nhận vào một `ApiState` và trả về **object mới** (không mutate):
  * `startLoading(state)`: chuyển `status` thành `'loading'`, xóa `error` về `null`.
  * `loadSuccess(state, users: User[])`: chuyển `status` thành `'success'`, gán `data`.
  * `loadError(state, message: string)`: chuyển `status` thành `'error'`, gán `error`.

> 💡 Cấu trúc `status` + `data` + `error` này chính là cách quản lý trạng thái gọi API mà bạn sẽ gặp lại ở Bài 10.
