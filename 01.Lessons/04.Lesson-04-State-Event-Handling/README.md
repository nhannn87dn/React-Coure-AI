# ⭐ Bài 4: State & Event Handling

> 🎯 Mục tiêu: Học viên phân biệt được State và Props, sử dụng đúng `useState` (kể cả Object State và Array State) theo nguyên tắc Immutability, xử lý được sự kiện chuột/bàn phím có kiểu dữ liệu TypeScript, và debug được State bằng React DevTools.

---

## Phần 1: Tại Sao Cần State — Component Không Tự "Nhớ" Dữ Liệu

### 1.0 Nhắc lại nhanh

Ở Bài 3 ta đã học **Props** — dữ liệu Component con nhận từ cha, chỉ đọc, không được sửa. Nhưng nếu bản thân **1 Component** cần tự lưu và tự thay đổi dữ liệu của chính nó (ví dụ: số lượng trong giỏ hàng, nội dung ô input) thì Props không giải quyết được — đó là lúc cần **State**.

### 1.1 State là gì và sự khác biệt cốt lõi giữa State vs Props

> **State là dữ liệu nội bộ mà 1 Component tự quản lý và có thể tự thay đổi theo thời gian, thường do tương tác của người dùng (click, gõ phím...). Khi State thay đổi, React tự động vẽ lại giao diện để phản ánh giá trị mới.**

| Tiêu chí | Props | State |
|---|---|---|
| Nguồn gốc | Component **cha** truyền xuống | Component **tự khai báo** cho chính nó |
| Ai được sửa | Chỉ đọc (read-only) | Component sở hữu có thể tự sửa |
| Khi nào đổi | Khi Component cha truyền giá trị mới | Khi gọi hàm cập nhật (`setState`) |

### 1.2 Vấn đề: thay đổi biến JavaScript thường không làm giao diện re-render

Thử dùng 1 biến `let` bình thường để lưu số đếm:

```tsx
function Counter() {
  let count = 0;

  const handleClick = () => {
    count = count + 1;
    console.log(count); // Log ra đúng: 1, 2, 3...
  };

  return (
    <div>
      <p>Số lần bấm: {count}</p>
      <button onClick={handleClick}>Bấm vào đây</button>
    </div>
  );
}
```

Bấm nút, `console.log` vẫn in đúng số tăng dần, nhưng **giao diện không hề đổi** — `<p>` luôn hiển thị `0`. Đây là vì `count` chỉ là 1 biến JavaScript thường: React không hề biết nó vừa thay đổi, nên không có lý do gì để vẽ lại `<p>`.

### 1.3 Khái niệm "re-render" là gì

> **Re-render là khi React gọi lại hàm Component để tính ra JSX mới, rồi so sánh với JSX cũ để cập nhật đúng phần đã thay đổi trên giao diện.** React chỉ tự động re-render 1 Component khi **State** (hoặc Props nhận được) của nó thay đổi — thay đổi 1 biến JavaScript thường không kích hoạt việc này.

`useState` chính là cách để "khai báo" với React: "đây là 1 giá trị tôi cần theo dõi — mỗi khi nó đổi, hãy re-render giúp tôi".

---

## Phần 2: Sử Dụng `useState` Hook

### 2.1 Cú pháp `const [state, setState] = useState(initialValue)`

```tsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Số lần bấm: {count}</p>
      <button onClick={() => setCount(count + 1)}>Bấm vào đây</button>
    </div>
  );
}
```

`useState(0)` trả về 1 **mảng có đúng 2 phần tử**: giá trị hiện tại (`count`) và hàm dùng để cập nhật nó (`setCount`). Ta lấy ra bằng **Array Destructuring** — đúng cú pháp đã ôn ở Bài 1, lúc đó có nhắc trước "đây chính là cú pháp của `useState`".

Lần này, gọi `setCount(count + 1)` sẽ báo cho React biết "giá trị mới của `count` là ...", React tự động **re-render** lại Component, `<p>` hiển thị đúng số mới.

### 2.2 Kiểu dữ liệu cho State

TypeScript **tự suy ra kiểu** (Type Inference — đã học ở Bài 1) từ giá trị khởi tạo, nên phần lớn trường hợp bạn không cần khai báo gì thêm:

```tsx
const [count, setCount] = useState(0);        // TypeScript tự hiểu: number
const [name, setName] = useState('');         // TypeScript tự hiểu: string
const [isOpen, setIsOpen] = useState(false);  // TypeScript tự hiểu: boolean
```

Chỉ cần khai báo rõ kiểu bằng Generic (`useState<T>`) khi giá trị khởi tạo là `null`/`undefined` nhưng State thực tế sẽ có kiểu khác — TypeScript không thể tự đoán được:

```tsx
type User = {
  id: number;
  name: string;
};

// ❌ Không khai báo Generic - TypeScript hiểu user luôn là null, không set được User thật vào
const [user, setUser] = useState(null);
setUser({ id: 1, name: 'An' }); // ❌ Argument of type 'User' is not assignable to parameter of type 'null'

// ✅ Khai báo rõ Union Type (đã học ở Bài 1) - user có thể là User hoặc null
const [user, setUser] = useState<User | null>(null);
setUser({ id: 1, name: 'An' }); // ✅ Đúng
```

### 2.3 Quy tắc Hook — chỉ gọi `useState` ở top-level của Component

```tsx
// ❌ Sai - gọi useState bên trong if
function Counter({ enabled }: { enabled: boolean }) {
  if (enabled) {
    const [count, setCount] = useState(0); // Vi phạm Rules of Hooks
  }
  // ...
}
```

```tsx
// ✅ Đúng - useState luôn ở top-level, không lồng trong if/for/function khác
function Counter({ enabled }: { enabled: boolean }) {
  const [count, setCount] = useState(0);
  if (!enabled) return null;
  // ...
}
```

> ⚠️ React dựa vào **thứ tự gọi Hook** giữa các lần render để biết Hook nào ứng với State nào. Gọi Hook trong điều kiện khiến thứ tự có thể thay đổi giữa các lần render, làm React gán nhầm State — lỗi rất khó debug. Quy tắc này áp dụng cho **mọi Hook**, không riêng `useState`; ta sẽ nhắc lại kỹ hơn ở Bài 7.

### 2.4 Immutability — vì sao không được thay đổi trực tiếp State

```tsx
// ❌ Sai - gọi push() làm thay đổi trực tiếp mảng gốc trong bộ nhớ
const [items, setItems] = useState<string[]>(['Áo', 'Quần']);

const handleAdd = () => {
  items.push('Mũ'); // Mutate trực tiếp - SAI
  setItems(items);  // React thấy vẫn là cùng 1 mảng (cùng địa chỉ bộ nhớ) -> KHÔNG re-render
};
```

> **Immutability (tính bất biến) trong React nghĩa là: không bao giờ chỉnh sửa trực tiếp giá trị State hiện tại. Muốn cập nhật, luôn tạo ra 1 giá trị HOÀN TOÀN MỚI rồi truyền vào hàm `setState`.**

React so sánh State cũ và mới bằng cách kiểm tra xem có phải **cùng 1 tham chiếu** (reference) trong bộ nhớ hay không (`Object.is`) — chứ không dò từng thuộc tính bên trong. `push()` chỉnh sửa mảng gốc rồi trả về **chính mảng đó**, nên với React, "chưa có gì thay đổi", nó sẽ không re-render.

```tsx
// ✅ Đúng - dùng Spread Operator (đã ôn ở Bài 1) để tạo mảng MỚI
const handleAdd = () => {
  setItems([...items, 'Mũ']); // Mảng mới, tham chiếu khác -> React nhận ra và re-render
};
```

---

## Phần 3: Lưu Ý Quan Trọng — Cập Nhật State Không Ngay Lập Tức

### 3.1 Demo: `console.log(state)` ngay sau `setState()`

```tsx
function Counter() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
    console.log(count); // In ra giá trị CŨ, không phải giá trị vừa set!
  };

  return <button onClick={handleClick}>Bấm ({count})</button>;
}
```

Bấm nút lần đầu (`count` đang là `0`), console vẫn in ra `0` chứ không phải `1`. Đây **không phải lỗi** — `setCount()` không thay đổi giá trị `count` ngay lập tức trong lần render hiện tại; nó chỉ **báo cho React biết** "ở lần render tiếp theo, hãy dùng giá trị mới này". Biến `count` trong function hiện tại vẫn giữ nguyên giá trị cũ cho đến khi Component re-render.

### 3.2 Lỗi thường gặp: gọi `setCount(count + 1)` nhiều lần liên tiếp

```tsx
// ❌ Sai - gọi 3 lần liên tiếp nhưng chỉ tăng đúng 1 lần
const handleTripleClick = () => {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
};
// Nếu count đang là 0, sau khi bấm: count = 1 (không phải 3!)
```

Vì cả 3 dòng đều đọc **cùng 1 giá trị `count`** (giá trị tại thời điểm hàm `handleTripleClick` được tạo ra), nên cả 3 lần gọi đều là `setCount(0 + 1)` — React chỉ ghi nhận "giá trị cuối cùng là 1".

### 3.3 Cách khắc phục: Functional Update `setCount(c => c + 1)`

```tsx
// ✅ Đúng - truyền vào 1 hàm nhận giá trị MỚI NHẤT làm tham số
const handleTripleClick = () => {
  setCount((c) => c + 1);
  setCount((c) => c + 1);
  setCount((c) => c + 1);
};
// count = 3 (tăng đúng 3 lần)
```

> 💡 **Quy tắc thực tế:** khi giá trị State mới **phụ thuộc vào giá trị State cũ**, luôn dùng Functional Update (`setState(prev => ...)`) thay vì đọc trực tiếp biến State. Đây là thói quen nên áp dụng ngay từ bây giờ, kể cả khi chỉ gọi 1 lần — nó sẽ tránh được rất nhiều lỗi khó phát hiện khi code phức tạp lên (ví dụ gọi `setState` bên trong 1 callback bất đồng bộ, sẽ gặp ở Bài 6).

---

## Phần 4: Quản Lý Nhiều State Trong Một Component

### 4.1 Khai báo nhiều `useState` độc lập cho từng loại dữ liệu

```tsx
// ✅ Đúng - mỗi loại dữ liệu độc lập, 1 useState riêng
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [showPassword, setShowPassword] = useState(false);
  // ...
}
```

```tsx
// ❌ Không khuyến khích - gộp mọi thứ vào 1 object State lớn ngay từ đầu
function LoginForm() {
  const [form, setForm] = useState({ email: '', password: '', showPassword: false });
  // Mỗi lần chỉ sửa 1 field cũng phải spread lại toàn bộ object - phiền hơn không cần thiết
}
```

**Quy tắc thực tế:** những giá trị **không liên quan** với nhau (ví dụ `email` và `showPassword`) nên tách thành các `useState` riêng biệt cho dễ đọc, dễ cập nhật. Chỉ gộp vào 1 Object State khi các giá trị đó **thực sự đi cùng nhau** (mục 5).

### 4.2 Cập nhật 1 phần của Object State — lỗi thường gặp khi quên spread

```tsx
const [user, setUser] = useState({ name: 'An', age: 25, city: 'Đà Nẵng' });

// ❌ Sai - object mới chỉ còn lại "age", các thuộc tính khác bị MẤT
setUser({ age: 26 });
// user bây giờ là { age: 26 } - "name" và "city" đã biến mất!
```

```tsx
// ✅ Đúng - Spread Operator giữ lại toàn bộ thuộc tính cũ, chỉ ghi đè "age"
setUser({ ...user, age: 26 });
// user bây giờ là { name: 'An', age: 26, city: 'Đà Nẵng' }
```

---

## Phần 5: Quản Lý State Dạng Dữ Liệu Phức Tạp (Object State và Array State)

### 5.1 Cập nhật Object State đúng cách

```tsx
type UserState = {
  name: string;
  age: number;
};

const [user, setUser] = useState<UserState>({ name: 'An', age: 25 });

const handleBirthday = () => {
  setUser((prev) => ({ ...prev, age: prev.age + 1 })); // Functional Update + Spread
};
```

> 💡 Kết hợp **Functional Update** (mục 3.3) với **Spread Operator** là cách an toàn nhất để cập nhật Object State: vừa đảm bảo dùng đúng giá trị mới nhất, vừa không làm mất các thuộc tính khác.

### 5.2 Cập nhật Object State lồng nhau — vì sao spread 1 lớp là chưa đủ

```tsx
type UserState = {
  name: string;
  address: {
    city: string;
    district: string;
  };
};

const [user, setUser] = useState<UserState>({
  name: 'An',
  address: { city: 'Đà Nẵng', district: 'Hải Châu' },
});

// ❌ Sai - chỉ spread lớp ngoài, "address" vẫn là THAM CHIẾU CŨ y hệt, không tạo object mới
setUser({ ...user, address: { city: 'Hà Nội' } });
// address.district bị MẤT vì ta gán đè nguyên object address bằng 1 object thiếu district
```

```tsx
// ✅ Đúng - spread TỪNG CẤP: cấp ngoài (user) và cấp trong (address)
setUser((prev) => ({
  ...prev,
  address: { ...prev.address, city: 'Hà Nội' },
}));
// district vẫn được giữ nguyên, chỉ city thay đổi
```

> ⚠️ Càng nhiều cấp lồng nhau, spread thủ công càng dễ viết sai và khó đọc. Với dữ liệu lồng sâu nhiều cấp, cách xử lý thực tế thường là **tách nhỏ State** ra (đưa `address` thành 1 `useState` riêng) hoặc dùng thư viện hỗ trợ — ta sẽ bàn thêm khi học Zustand ở Bài 9.

### 5.3 Cập nhật Array State — thêm phần tử mới

```tsx
type Todo = { id: number; text: string };

const [todos, setTodos] = useState<Todo[]>([]);

const handleAdd = (text: string) => {
  const newTodo: Todo = { id: Date.now(), text };
  setTodos((prev) => [...prev, newTodo]); // Spread + phần tử mới, KHÔNG dùng push()
};
```

### 5.4 Xóa phần tử khỏi Array State bằng `filter()`

```tsx
const handleRemove = (id: number) => {
  setTodos((prev) => prev.filter((todo) => todo.id !== id)); // Trả về mảng MỚI, đã loại bỏ phần tử
};
```

> 💡 `filter()` luôn trả về 1 mảng hoàn toàn mới (đã ôn ở Bài 1), nên không cần spread thêm — bản thân `filter()` đã đảm bảo tính bất biến.

### 5.5 Sửa 1 phần tử trong Array State bằng `map()`

```tsx
type Todo = { id: number; text: string; completed: boolean };

const handleToggle = (id: number) => {
  setTodos((prev) =>
    prev.map((todo) =>
      todo.id === id ? { ...todo, completed: !todo.completed } : todo // Tìm đúng id -> tạo bản sao đã đổi; các phần tử khác giữ nguyên
    )
  );
};
```

`map()` duyệt qua toàn bộ mảng, trả về 1 mảng mới có cùng độ dài: phần tử có `id` trùng khớp được thay bằng **bản sao đã cập nhật** (spread + ghi đè), các phần tử còn lại giữ nguyên.

### 5.6 Lỗi thường gặp: React không re-render vì thay đổi trực tiếp Object/Array

```tsx
// ❌ Sai - gán trực tiếp vào thuộc tính của object bên trong mảng
const handleToggleWrong = (id: number) => {
  const todo = todos.find((t) => t.id === id);
  if (todo) {
    todo.completed = !todo.completed; // Mutate trực tiếp phần tử bên trong mảng gốc
    setTodos(todos); // Vẫn là mảng cũ (cùng tham chiếu) -> React KHÔNG re-render
  }
};
```

Đây là lỗi rất dễ mắc vì bản thân **giá trị đã thực sự đổi** trong bộ nhớ (`todo.completed` đúng là đã đổi), nhưng **tham chiếu của mảng `todos` không đổi**, nên React không nhận ra cần re-render. Luôn dùng `map()`/`filter()`/spread (mục 5.3-5.5) để đảm bảo tạo ra tham chiếu mới.

---

## Phần 6: Xử Lý Sự Kiện (Event Handling) Trong React

### 6.1 Khái niệm Synthetic Events và kiểu dữ liệu Event trong TypeScript

> **Synthetic Event là 1 lớp "bọc" (wrapper) mà React tạo ra xung quanh sự kiện gốc của trình duyệt, giúp sự kiện hoạt động nhất quán trên mọi trình duyệt.** Cách dùng gần như giống hệt sự kiện DOM thông thường, chỉ khác ở tên thuộc tính (camelCase, đã học ở Bài 3).

TypeScript cung cấp sẵn kiểu cho từng loại sự kiện, giúp bạn gõ `e.` là VS Code gợi ý đủ thuộc tính có sẵn:

```tsx
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
  console.log(e.clientX, e.clientY); // Vị trí con trỏ chuột lúc click
}

function handleKeyDown(e: React.KeyboardEvent<HTMLInputElement>) {
  console.log(e.key); // Phím vừa nhấn
}
```

### 6.2 Sự kiện Mouse

```tsx
function ProductActions() {
  const handleClick = () => console.log('Đã click');
  const handleDoubleClick = () => console.log('Đã double click');

  return (
    <button onClick={handleClick} onDoubleClick={handleDoubleClick}>
      Thao tác
    </button>
  );
}
```

Truyền tham số vào Event Handler bằng Arrow Function inline (đã dùng ở mục 5.5, 5.4 — `onClick={() => handleRemove(id)}`):

```tsx
{todos.map((todo) => (
  <li key={todo.id}>
    {todo.text}
    <button onClick={() => handleRemove(todo.id)}>Xóa</button>
  </li>
))}
```

> ⚠️ Chú ý cú pháp: `onClick={() => handleRemove(todo.id)}` (đúng — bọc trong 1 arrow function) khác hoàn toàn với `onClick={handleRemove(todo.id)}` (❌ sai — gọi hàm **ngay khi render**, không phải khi click, vì `{}` chỉ nhúng biểu thức và kết quả của `handleRemove(todo.id)` mới là thứ được gán cho `onClick`).

`onMouseEnter`/`onMouseLeave` cho hiệu ứng hover:

```tsx
function HoverCard() {
  const [isHovered, setIsHovered] = useState(false);

  return (
    <div
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
      style={{ backgroundColor: isHovered ? '#f0f0f0' : 'white' }}
    >
      Di chuột vào đây
    </div>
  );
}
```

### 6.3 Sự kiện Keyboard

```tsx
function SearchBox() {
  const [keyword, setKeyword] = useState('');

  const handleKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
    if (e.key === 'Enter') {
      console.log('Tìm kiếm:', keyword);
    }
  };

  return (
    <input
      value={keyword}
      onChange={(e) => setKeyword(e.target.value)}
      onKeyDown={handleKeyDown}
      placeholder="Nhấn Enter để tìm kiếm"
    />
  );
}
```

> 💡 `onChange` và cách quản lý giá trị `input` bằng State (Controlled Input) sẽ được học chính thức ở Bài 7 — ở đây chỉ dùng tạm để có ví dụ thực tế cho sự kiện bàn phím.

### 6.4 Ngăn hành vi mặc định của trình duyệt

```tsx
function LinkWithConfirm() {
  const handleClick = (e: React.MouseEvent<HTMLAnchorElement>) => {
    e.preventDefault(); // Chặn hành vi mặc định: điều hướng sang trang khác
    const confirmed = window.confirm('Bạn có chắc muốn rời khỏi trang?');
    if (confirmed) window.location.href = 'https://example.com';
  };

  return <a href="https://example.com" onClick={handleClick}>Đi tới trang khác</a>;
}
```

`e.preventDefault()` dùng để chặn hành vi mặc định của trình duyệt gắn liền với sự kiện đó (thẻ `<a>` tự điều hướng, thẻ `<form>` tự reload trang khi submit...). Ta sẽ dùng lại kỹ thuật này rất nhiều khi xử lý Form ở Bài 7.

---

## Phần 7: Debug State Với React DevTools

### 7.1 Cài đặt extension React Developer Tools

Cài extension **React Developer Tools** cho Chrome/Edge từ Chrome Web Store. Sau khi cài, mở DevTools (F12) trên 1 trang web React đang chạy (ví dụ `pnpm run dev`), bạn sẽ thấy thêm 2 tab mới: **Components** và **Profiler**.

### 7.2 Tab Components — xem cây Component, Props và State

Tab **Components** hiển thị toàn bộ cây Component đang render (giống cấu trúc cha-con đã học ở Bài 2), click vào 1 Component bất kỳ sẽ thấy panel bên phải hiển thị đầy đủ **Props** nó đang nhận và **State** (hooks) nó đang giữ.

### 7.3 Sửa State trực tiếp trên DevTools để thử nghiệm

Trong panel **hooks**, bạn có thể double-click vào giá trị State và **sửa trực tiếp** — giao diện sẽ cập nhật ngay lập tức. Đây là cách rất nhanh để thử các trường hợp khó tái hiện bằng tay (ví dụ: State với giá trị rỗng, giá trị âm, mảng rất dài) mà không cần sửa code.

### 7.4 "Highlight updates when components render"

Trong tab **Components**, bật tùy chọn **"Highlight updates when components render"** (biểu tượng bánh răng ⚙️). Khi bật, mỗi lần 1 Component re-render, nó sẽ nhấp nháy viền màu trên giao diện thật — giúp bạn quan sát trực quan **Component nào đang re-render**, từ đó phát hiện sớm những chỗ re-render thừa (chủ đề sẽ đào sâu ở Bài 9 với `memo`/`useMemo`).

---

## 🧪 Bài tập thực hành cuối buổi

### 1. Counter với nút tăng/giảm/reset

Xây dựng Component `Counter` dùng `useState`, có 3 nút: **Tăng** (+1), **Giảm** (-1, không cho xuống dưới 0), **Reset** (về 0). Dùng Functional Update cho cả 3 hành động.

### 2. Toggle Switch

Xây dựng Component `ToggleSwitch` dùng `useState<boolean>`, click vào để ẩn/hiện 1 đoạn nội dung bất kỳ. Style khác nhau rõ ràng giữa 2 trạng thái bật/tắt.

### 3. Mini-game phản xạ bằng phím

Xây dựng Component `KeyGame`:

- Hiển thị ngẫu nhiên 1 chữ cái (A-Z) yêu cầu người chơi nhấn đúng phím đó.
- Dùng `onKeyDown` ở cấp `<div>` (nhớ thêm `tabIndex={0}` để `div` nhận được sự kiện bàn phím) để bắt phím vừa nhấn, so sánh với chữ cái yêu cầu.
- Nếu đúng: cộng điểm (`useState<number>`) và đổi sang chữ cái ngẫu nhiên mới. Nếu sai: hiển thị thông báo "Sai rồi, thử lại".

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
