# ⭐ Bài 5: Conditional Rendering, Render Danh Sách & Lifting State Up

> 🎯 Mục tiêu: Học viên render được giao diện theo điều kiện và render được danh sách với `key` đúng cách, giải thích được cơ chế Virtual DOM/Reconciliation, chia sẻ được State giữa các Component bằng Lifting State Up, và nhận biết được dữ liệu nào nên là Derived State thay vì lưu vào `useState`.

---

## Phần 1: Các Cách Render Có Điều Kiện

### 1.0 Nhắc lại nhanh

Ở Bài 3 (mục 2.1) ta đã biết: JSX chỉ nhận **biểu thức**, không nhận trực tiếp `if/else` đầy đủ bên trong `{}`. Bài này đi sâu vào từng cách "chọn JSX để render" theo điều kiện, và khi nào nên dùng cách nào.

### 1.1 Dùng if/else thông thường trước `return`

```tsx
type OrderStatusProps = {
  status: 'pending' | 'shipped' | 'delivered';
};

function OrderStatus({ status }: OrderStatusProps) {
  if (status === 'pending') {
    return <p>Đơn hàng đang chờ xử lý</p>;
  }
  if (status === 'shipped') {
    return <p>Đơn hàng đang giao</p>;
  }
  return <p>Đơn hàng đã giao thành công</p>;
}
```

Cách này phù hợp khi Component có **nhiều nhánh riêng biệt**, mỗi nhánh trả về hẳn 1 cấu trúc JSX khác nhau — code dễ đọc hơn so với việc nhồi hết vào 1 biểu thức.

### 1.2 Toán tử Ternary — viết inline ngay trong JSX

```tsx
function LoginButton({ isLoggedIn }: { isLoggedIn: boolean }) {
  return (
    <button>{isLoggedIn ? 'Đăng xuất' : 'Đăng nhập'}</button>
  );
}
```

Ternary phù hợp khi chỉ cần chọn giữa **2 giá trị đơn giản** (1 đoạn text, 1 class, 1 phần tử nhỏ) ngay bên trong JSX, không cần tách hẳn ra ngoài như mục 1.1.

### 1.3 Toán tử `&&` — ẩn/hiện phần tử khi không cần nhánh `else`

```tsx
function Notification({ unreadCount }: { unreadCount: number }) {
  return (
    <div>
      <span>Hộp thư</span>
      {unreadCount > 0 && <span className="badge">{unreadCount}</span>}
    </div>
  );
}
```

`&&` hoạt động dựa trên nguyên lý: nếu vế trái là `false`/`falsy`, JavaScript **không cần đánh giá** vế phải, cả biểu thức trả về vế trái; nếu vế trái là `true`/`truthy`, cả biểu thức trả về **vế phải**. Ở đây, nếu `unreadCount > 0` là `false`, React nhận về `false` và **không render gì cả** — React tự bỏ qua giá trị `false`, `null`, `undefined` khi render.

### 1.4 Lỗi thường gặp: `&&` với giá trị `0` sẽ render ra số `0` ngoài ý muốn

```tsx
// ❌ Sai - khi cartItems.length là 0, "0" bị RENDER RA MÀN HÌNH
function CartBadge({ cartItems }: { cartItems: string[] }) {
  return <div>{cartItems.length && <span className="badge">{cartItems.length}</span>}</div>;
}
```

Khác với `false`/`null`/`undefined`, React **không bỏ qua số `0`** — vì `0` là 1 giá trị hợp lệ để hiển thị (ví dụ hiển thị điểm số `0`). Khi `cartItems.length` là `0` (falsy nhưng vẫn là số), biểu thức `0 && <span>...</span>` trả về **chính số `0`**, và React render luôn số `0` đó lên màn hình — ngoài ý muốn.

```tsx
// ✅ Đúng - ép về boolean bằng so sánh tường minh, vế trái luôn là true/false
function CartBadge({ cartItems }: { cartItems: string[] }) {
  return <div>{cartItems.length > 0 && <span className="badge">{cartItems.length}</span>}</div>;
}
```

> ⚠️ **Quy tắc thực tế:** khi dùng `&&` với 1 giá trị số/chuỗi bên trái, luôn viết tường minh phép so sánh (`> 0`, `!== ''`, `.length > 0`...) thay vì để nguyên giá trị đó — tránh render nhầm `0` hoặc chuỗi rỗng.

---

## Phần 2: Render Danh Sách Với `Array.map()` Và Vai Trò Của `key`

### 2.1 Dùng `map()` để biến mảng dữ liệu thành mảng JSX Element

```tsx
type Todo = {
  id: number;
  text: string;
};

const todos: Todo[] = [
  { id: 1, text: 'Học React' },
  { id: 2, text: 'Làm bài tập' },
];

function TodoList() {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

Đây chính là ứng dụng trực tiếp của `map()` đã ôn ở Bài 1 (lúc đó có nhắc trước "sẽ học sâu ở Bài 5"): với mỗi phần tử trong mảng `todos`, ta trả về 1 phần tử JSX (`<li>`) tương ứng — kết quả là 1 **mảng JSX Element**, và React biết cách render 1 mảng JSX Element y hệt như render từng phần tử riêng lẻ.

### 2.2 Prop `key` là gì

> **`key` là 1 Props đặc biệt (không nằm trong Props thường mà bạn tự định nghĩa) mà React yêu cầu bắt buộc phải có ở phần tử ngoài cùng khi render 1 danh sách bằng `map()`. `key` giúp React "nhận diện" — biết chính xác phần tử nào đã thay đổi, được thêm, hay bị xóa giữa 2 lần render.**

Không có `key` (hoặc `key` sai), React sẽ hiện cảnh báo trong console và có thể render sai — ta sẽ thấy ví dụ cụ thể ở Phần 4, sau khi hiểu cơ chế Reconciliation ở Phần 3.

### 2.3 Vì sao nên dùng ID duy nhất làm `key`

```tsx
// ❌ Không khuyến khích - dùng index của mảng làm key
{todos.map((todo, index) => (
  <li key={index}>{todo.text}</li>
))}

// ✅ Đúng - dùng ID duy nhất của chính dữ liệu đó
{todos.map((todo) => (
  <li key={todo.id}>{todo.text}</li>
))}
```

Dùng `index` (vị trí trong mảng) làm `key` **có vẻ hoạt động bình thường** nếu danh sách chỉ hiển thị tĩnh, không bao giờ thêm/xóa/sắp xếp lại. Nhưng ngay khi danh sách có thể thay đổi thứ tự, `index` sẽ gây lỗi hiển thị nghiêm trọng — lý do cụ thể sẽ được giải thích ở Phần 3 và minh họa ở Phần 4.

---

## Phần 3: Virtual DOM Và Cơ Chế Reconciliation (Giải Thích Trực Quan)

### 3.1 Real DOM là gì và vì sao thao tác trực tiếp tốn hiệu năng

**Real DOM** (Document Object Model) là cấu trúc cây mà trình duyệt dùng để biểu diễn trang HTML thật sự đang hiển thị. Mỗi lần bạn thay đổi 1 phần tử DOM (thêm/xóa/sửa), trình duyệt phải tính toán lại **layout** (vị trí, kích thước của mọi phần tử liên quan) và **vẽ lại (paint)** phần giao diện bị ảnh hưởng — đây là những thao tác **tốn hiệu năng**, đặc biệt nếu lặp lại liên tục hoặc trên cây DOM lớn.

### 3.2 Virtual DOM là gì

> **Virtual DOM là 1 bản sao nhẹ (lightweight copy) của Real DOM, tồn tại dưới dạng các object JavaScript thuần trong bộ nhớ — chính là kết quả của `React.createElement()` mà ta đã học ở Bài 3.** Thao tác trên object JavaScript trong bộ nhớ nhanh hơn rất nhiều so với thao tác trực tiếp lên Real DOM.

Mỗi khi Component re-render (do State/Props thay đổi — đã học ở Bài 4), React không sửa Real DOM ngay lập tức. Thay vào đó, React tạo ra **1 cây Virtual DOM mới**, dựa trên State/Props mới nhất.

### 3.3 Diffing Algorithm — cách React so sánh Virtual DOM cũ và mới

> **Diffing là quá trình React so sánh cây Virtual DOM MỚI (vừa tính ra sau re-render) với cây Virtual DOM CŨ (của lần render trước), để tìm ra CHÍNH XÁC những gì đã thay đổi.**

Với 1 danh sách render bằng `map()`, đây là lúc **`key`** phát huy vai trò: `key` chính là "căn cước" giúp thuật toán Diffing biết phần tử Virtual DOM mới ứng với phần tử Virtual DOM cũ nào — dù vị trí của chúng trong mảng có thể đã thay đổi. Không có `key` ổn định, React chỉ có thể so sánh dựa theo **vị trí**, dễ dẫn đến kết luận sai (ví dụ tưởng nhầm 1 phần tử "bị sửa nội dung" trong khi thực ra nó chỉ đổi chỗ).

### 3.4 Reconciliation — chỉ cập nhật đúng phần đã thay đổi

> **Reconciliation là bước cuối cùng: dựa trên kết quả Diffing, React chỉ áp dụng các thay đổi TỐI THIỂU cần thiết lên Real DOM** (ví dụ: chỉ đổi nội dung chữ của đúng 1 thẻ `<li>`, thay vì xóa và vẽ lại toàn bộ `<ul>`).

Đây chính là lý do declarative UI (đã học ở Bài 1) hiệu quả: bạn chỉ cần khai báo "giao diện nên trông ra sao" ứng với State hiện tại, còn việc "tính toán chỗ nào cần sửa trên Real DOM" là do React tự lo thông qua Virtual DOM + Diffing + Reconciliation, thay vì bạn phải tự tay chỉ định từng thao tác DOM như cách làm Vanilla JS đã thấy ở Bài 1.

---

## Phần 4: Lỗi Thường Gặp Khi Dùng `key` Sai Cách

### 4.1 Dùng `index` làm `key` gây lỗi hiển thị sai

Xét 1 danh sách công việc, mỗi việc có 1 ô input để ghi chú riêng:

```tsx
type Todo = { id: number; text: string };

function TodoListWithNote({ todos }: { todos: Todo[] }) {
  return (
    <ul>
      {todos.map((todo, index) => (
        <li key={index}>
          {todo.text}
          <input type="text" placeholder="Ghi chú..." />
        </li>
      ))}
    </ul>
  );
}
```

Thử gõ ghi chú vào ô input của việc **đầu tiên**, rồi **xóa việc đầu tiên** đó khỏi danh sách. Kết quả: ghi chú bạn vừa gõ **không biến mất theo việc đã xóa**, mà lại "nhảy" sang hiển thị ở việc kế tiếp!

**Nguyên nhân:** khi xóa phần tử đầu tiên, các phần tử còn lại bị dịch chuyển vị trí (`index` của chúng giảm đi 1). Vì `key` chính là `index`, React thấy `key={0}` **vẫn tồn tại** ở lần render mới (giờ ứng với việc thứ 2 cũ) và kết luận "đây vẫn là component `<li>` cũ, chỉ đổi nội dung text" — nên nó **giữ nguyên DOM node input cũ** (cùng với giá trị người dùng đã gõ), chỉ cập nhật lại phần chữ `{todo.text}`. Đây chính là hệ quả trực tiếp của cơ chế Diffing/Reconciliation vừa học ở Phần 3.

```tsx
// ✅ Đúng - key là id thật của dữ liệu, không đổi khi danh sách bị xóa/sắp xếp lại
{todos.map((todo) => (
  <li key={todo.id}>
    {todo.text}
    <input type="text" placeholder="Ghi chú..." />
  </li>
))}
```

Với `key={todo.id}`, khi xóa 1 việc, React biết chính xác **`key` nào đã biến mất** khỏi danh sách, và xóa đúng DOM node tương ứng — ghi chú của các việc còn lại không bị xáo trộn.

### 4.2 Cảnh báo "Warning: each child in a list should have a unique key"

Nếu quên hẳn `key` khi render danh sách, React in ra console cảnh báo:

```text
Warning: Each child in a list should have a unique "key" prop.
```

Đây không phải lỗi làm crash ứng dụng, nhưng là dấu hiệu React **không thể tối ưu** việc Diffing cho danh sách này, và có nguy cơ gặp lỗi y hệt mục 4.1. Luôn xử lý triệt để cảnh báo này ngay khi thấy nó xuất hiện.

### 4.3 `key` phải duy nhất trong phạm vi danh sách anh em (sibling)

```tsx
// ✅ Đúng - key trùng nhau nhưng ở 2 danh sách KHÁC NHAU (không phải anh em với nhau) vẫn được
function App() {
  return (
    <>
      <ul>{shirts.map((p) => <li key={p.id}>{p.name}</li>)}</ul>
      <ul>{pants.map((p) => <li key={p.id}>{p.name}</li>)}</ul>
    </>
  );
}
```

`key` chỉ cần **duy nhất giữa các phần tử anh em** (cùng render ra từ cùng 1 `map()`, cùng nằm cạnh nhau), **không cần** duy nhất trên toàn bộ ứng dụng. Vì vậy `shirts` và `pants` ở ví dụ trên hoàn toàn có thể dùng chung tập giá trị `id` (ví dụ cả 2 đều có sản phẩm `id: 1`) mà không xảy ra xung đột.

---

## Phần 5: Kết Hợp Conditional Rendering Và List Rendering

### 5.1 Hiển thị thông báo "Không có dữ liệu" khi mảng rỗng

```tsx
function ProductList({ products }: { products: Product[] }) {
  if (products.length === 0) {
    return <p>Không có sản phẩm nào.</p>;
  }

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  );
}
```

> 💡 Luôn kiểm tra trường hợp mảng rỗng **trước khi** gọi `map()`. Về mặt kỹ thuật, `[].map(...)` không gây lỗi (trả về mảng rỗng), nhưng nếu không xử lý riêng, người dùng sẽ thấy 1 khoảng trắng khó hiểu thay vì thông báo rõ ràng.

### 5.2 Lọc dữ liệu bằng `filter()` trước khi `map()`

```tsx
function ExpensiveProducts({ products }: { products: Product[] }) {
  const expensiveProducts = products.filter((p) => p.price > 200000);

  if (expensiveProducts.length === 0) {
    return <p>Không có sản phẩm nào trên 200.000đ.</p>;
  }

  return (
    <ul>
      {expensiveProducts.map((p) => (
        <li key={p.id}>{p.name} - {p.price}đ</li>
      ))}
    </ul>
  );
}
```

Kết hợp `filter()` (ôn ở Bài 1) trước `map()` là cách phổ biến nhất để render danh sách "theo điều kiện" — ví dụ tìm kiếm, lọc theo danh mục, ẩn phần tử không thỏa điều kiện.

### 5.3 Kết hợp nhiều điều kiện lồng nhau trong cùng 1 danh sách

```tsx
type Order = { id: number; customerName: string; status: 'pending' | 'shipped' | 'delivered' };

function OrderList({ orders }: { orders: Order[] }) {
  return (
    <ul>
      {orders.map((order) => (
        <li key={order.id}>
          {order.customerName} —{' '}
          {order.status === 'pending' && <span className="text-yellow">Đang chờ</span>}
          {order.status === 'shipped' && <span className="text-blue">Đang giao</span>}
          {order.status === 'delivered' && <span className="text-green">Đã giao</span>}
        </li>
      ))}
    </ul>
  );
}
```

Bên trong mỗi phần tử của danh sách, ta hoàn toàn có thể dùng lại các kỹ thuật Conditional Rendering ở Phần 1 (ở đây dùng `&&`) để hiển thị khác nhau theo từng thuộc tính của phần tử đó.

---

## Phần 6: Lifting State Up — Chia Sẻ State Giữa Các Component

### 6.0 Vấn đề: 2 Component anh em cần dùng chung 1 dữ liệu

Giả sử ta có `TodoForm` (ô nhập + nút thêm việc) và `TodoList` (hiển thị danh sách việc) là **2 Component riêng biệt, cùng cấp** (anh em — sibling):

```tsx
// ❌ Vấn đề: State "todos" nằm trong TodoList, TodoForm không thể "gọi hàm" của TodoList
function TodoForm() {
  const [text, setText] = useState('');
  const handleSubmit = () => {
    // Làm sao thêm "text" này vào state "todos" đang nằm ở Component KHÁC?
  };
  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={handleSubmit}>Thêm</button>
    </div>
  );
}

function TodoList() {
  const [todos, setTodos] = useState<Todo[]>([]); // State chỉ TodoList tự "nhìn thấy"
  return (
    <ul>{todos.map((t) => <li key={t.id}>{t.text}</li>)}</ul>
  );
}
```

`TodoForm` và `TodoList` là 2 Component **độc lập hoàn toàn** — mỗi Component chỉ tự quản lý được State của chính nó (đã học ở Bài 4). `TodoForm` không có cách nào "với tới" State `todos` đang nằm trong `TodoList` để thêm việc mới vào đó.

### 6.1 Giải pháp: "nâng" State lên Component cha chung gần nhất

> **Lifting State Up là kỹ thuật: khi 2 (hoặc nhiều) Component cần dùng chung 1 dữ liệu, ta chuyển State đó lên Component CHA gần nhất đang bao cả 2 Component đó, rồi truyền dữ liệu xuống các Component con qua Props.**

```tsx
type Todo = { id: number; text: string };

// ✅ State "todos" giờ nằm ở App - cha chung của cả TodoForm và TodoList
function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  const handleAdd = (text: string) => {
    setTodos((prev) => [...prev, { id: Date.now(), text }]); // Nhắc lại kỹ thuật Bài 4
  };

  return (
    <div>
      <TodoForm onAdd={handleAdd} />
      <TodoList todos={todos} />
    </div>
  );
}
```

### 6.2 Con báo ngược lên cha qua Callback Props

```tsx
type TodoFormProps = {
  onAdd: (text: string) => void; // Callback Props - đã học kiểu function ở Bài 3
};

function TodoForm({ onAdd }: TodoFormProps) {
  const [text, setText] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault(); // Đã học ở Bài 4 - chặn reload trang
    if (text.trim() === '') return;
    onAdd(text); // Gọi callback - "báo" cho App biết cần thêm việc này
    setText('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button type="submit">Thêm</button>
    </form>
  );
}
```

```tsx
type TodoListProps = {
  todos: Todo[]; // Chỉ nhận dữ liệu để hiển thị, không tự quản lý State todos
};

function TodoList({ todos }: TodoListProps) {
  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

> **Nguyên tắc cốt lõi: "data đi xuống, event đi lên"** (data down, events up). Dữ liệu (`todos`) luôn truyền theo 1 chiều từ cha xuống con qua Props — đúng nguyên tắc one-way data flow đã học ở Bài 3. Khi Component con cần "thay đổi" dữ liệu đó, nó không tự sửa mà **gọi 1 callback** để yêu cầu Component cha thực hiện thay đổi giúp mình.

### 6.3 Component con "không trạng thái" — dễ tái sử dụng và dễ kiểm soát

Sau khi lift, `TodoList` ở mục 6.2 hoàn toàn không còn `useState` cho `todos` nữa — nó chỉ nhận Props và hiển thị. Component kiểu này (chỉ nhận `value` + `callback` từ cha, không tự giữ State riêng cho dữ liệu chính) dễ tái sử dụng hơn nhiều: bạn có thể đặt `TodoList` ở bất kỳ đâu, truyền cho nó bất kỳ mảng `todos` nào, mà không sợ nó "tự ý" có 1 phiên bản dữ liệu riêng không đồng bộ với phần còn lại của ứng dụng.

### 6.4 Khi nào KHÔNG nên lift

Không phải State nào cũng cần lift lên cao. Ví dụ, State `text` (nội dung đang gõ dở trong ô input) ở mục 6.2 **chỉ `TodoForm` cần dùng** — không có Component nào khác cần đọc giá trị đó, nên nó **nên ở lại** trong `TodoForm`, không cần lift lên `App`.

> ⚠️ **Lift càng cao, càng nhiều Component trung gian phải "chuyền tay" Props chỉ để đưa dữ liệu xuống Component thực sự cần dùng** — hiện tượng này gọi là **Prop Drilling**. Khi cây Component sâu 3-4 cấp trở lên, Prop Drilling khiến code khó bảo trì. Bài 9 sẽ giới thiệu Context API và Zustand để giải quyết vấn đề này — quy tắc thực tế: **chỉ lift lên đến đúng Component cha chung gần nhất cần dữ liệu đó**, không lift cao hơn mức cần thiết.

---

## Phần 7: Derived State — Không Lưu Vào State Những Gì Tính Ra Được

### 7.0 Định nghĩa

> **Derived State (dữ liệu dẫn xuất) là dữ liệu có thể TÍNH RA trực tiếp từ State/Props đã có sẵn. Nguyên tắc: nếu 1 giá trị tính được từ State khác, đừng tạo thêm 1 `useState` mới cho nó — chỉ cần tính nó ngay trong lúc render.**

### 7.1 Ví dụ: số việc đã hoàn thành, danh sách đã lọc

```tsx
type Todo = { id: number; text: string; completed: boolean };

function TodoStats({ todos }: { todos: Todo[] }) {
  // ✅ Derived State - tính trực tiếp mỗi lần render, KHÔNG dùng useState riêng
  const completedCount = todos.filter((t) => t.completed).length;
  const totalCount = todos.length;

  return (
    <p>Đã hoàn thành {completedCount}/{totalCount} việc</p>
  );
}
```

`completedCount` không cần là State riêng — nó luôn tính được từ `todos` (State gốc), nên chỉ cần 1 dòng `filter().length` ngay trong thân Component, mỗi lần render sẽ tự động ra kết quả đúng nhất theo `todos` mới nhất.

### 7.2 Lỗi thường gặp: lưu trùng dữ liệu vào 2 State khiến chúng bị lệch nhau

```tsx
// ❌ Sai - completedCount là 1 useState RIÊNG, phải tự nhớ đồng bộ thủ công
function TodoStats() {
  const [todos, setTodos] = useState<Todo[]>([]);
  const [completedCount, setCompletedCount] = useState(0); // Dữ liệu trùng lặp

  const handleToggle = (id: number) => {
    setTodos((prev) =>
      prev.map((t) => (t.id === id ? { ...t, completed: !t.completed } : t))
    );
    // Quên cập nhật completedCount ở đây -> completedCount hiển thị SAI, không khớp với todos thật
  };
  // ...
}
```

Vấn đề: `todos` và `completedCount` là **2 nguồn sự thật (source of truth) khác nhau** cho cùng 1 thông tin. Mỗi lần `todos` thay đổi, lập trình viên phải **tự nhớ** cập nhật `completedCount` theo — chỉ cần quên 1 chỗ là 2 giá trị lệch nhau (out of sync), và lỗi này rất khó phát hiện vì code vẫn chạy, chỉ hiển thị sai số liệu.

```tsx
// ✅ Đúng - chỉ 1 nguồn sự thật duy nhất (todos), completedCount luôn được tính lại đúng
function TodoStats() {
  const [todos, setTodos] = useState<Todo[]>([]);
  const completedCount = todos.filter((t) => t.completed).length; // Không thể lệch, vì luôn tính lại từ todos mới nhất

  const handleToggle = (id: number) => {
    setTodos((prev) =>
      prev.map((t) => (t.id === id ? { ...t, completed: !t.completed } : t))
    );
  };
  // ...
}
```

> 💡 **Câu hỏi tự kiểm tra:** trước khi tạo 1 `useState` mới, hãy tự hỏi "giá trị này có thể tính ra được từ State/Props đã có sẵn không?". Nếu có — đừng tạo State mới, chỉ cần tính trực tiếp. Nguyên tắc này sẽ còn quan trọng hơn nữa ở Bài 6, khi bàn về việc **không nên dùng `useEffect`** chỉ để đồng bộ 1 State sang 1 State khác.

---

## Phần 8: Thực Hành — Ứng Dụng Todo Nhiều Component

### 8.1 Tách giao diện thành nhiều Component

```text
src/
├── types.ts        # type Todo = { id: number; text: string; completed: boolean }
├── App.tsx          # Giữ State todos + filter (Lifting State Up), ghép các Component con
└── components/
    ├── TodoForm.tsx   # Props: { onAdd: (text: string) => void }
    ├── TodoFilter.tsx # Props: { filter: FilterType; onChange: (filter: FilterType) => void }
    ├── TodoList.tsx   # Props: { todos: Todo[]; onToggle: (id: number) => void; onRemove: (id: number) => void }
    └── TodoItem.tsx   # Props: { todo: Todo; onToggle: (id: number) => void; onRemove: (id: number) => void }
```

### 8.2 Đặt State `todos` và `filter` ở `App`

```tsx
// App.tsx
import { useState } from 'react';
import type { Todo } from './types';
import TodoForm from './components/TodoForm';
import TodoFilter from './components/TodoFilter';
import TodoList from './components/TodoList';

type FilterType = 'all' | 'active' | 'completed';

function App() {
  const [todos, setTodos] = useState<Todo[]>([]);
  const [filter, setFilter] = useState<FilterType>('all');

  const handleAdd = (text: string) => {
    setTodos((prev) => [...prev, { id: Date.now(), text, completed: false }]);
  };

  const handleToggle = (id: number) => {
    setTodos((prev) =>
      prev.map((t) => (t.id === id ? { ...t, completed: !t.completed } : t))
    );
  };

  const handleRemove = (id: number) => {
    setTodos((prev) => prev.filter((t) => t.id !== id));
  };

  // Derived State - tính trực tiếp, KHÔNG lưu vào useState riêng (Phần 7)
  const filteredTodos = todos.filter((t) => {
    if (filter === 'active') return !t.completed;
    if (filter === 'completed') return t.completed;
    return true;
  });
  const remainingCount = todos.filter((t) => !t.completed).length;

  return (
    <div>
      <TodoForm onAdd={handleAdd} />
      <TodoFilter filter={filter} onChange={setFilter} />
      <TodoList todos={filteredTodos} onToggle={handleToggle} onRemove={handleRemove} />
      <p>Còn lại {remainingCount} việc</p>
    </div>
  );
}

export default App;
```

### 8.3 Hiển thị danh sách đã lọc và số việc còn lại bằng Derived State

Chú ý ở ví dụ trên: `filteredTodos` và `remainingCount` **đều là Derived State** (Phần 7) — cả 2 đều tính trực tiếp từ `todos` và `filter`, không có `useState` riêng nào cho chúng. `TodoList` chỉ nhận `filteredTodos` (đã lọc sẵn) qua Props và hiển thị — bản thân `TodoList` không cần biết `filter` đang là gì.

---

## 🧪 Bài tập thực hành cuối buổi

1. Viết Component `PriceDisplay` nhận Props `price: number` và `discountPercent: number` (có thể là `0`). Dùng `&&` để hiển thị badge "Giảm giá" — cố tình để `discountPercent` là `0` và quan sát lỗi ở mục 1.4, sau đó tự sửa lại cho đúng.
2. Từ 1 mảng `students: { id: number; name: string; score: number }[]`, render danh sách bằng `map()` với `key` đúng, hiển thị thêm dòng "Không có học sinh nào" khi mảng rỗng (thử đổi mảng thành `[]` để kiểm tra).
3. Tự tay tái hiện lại lỗi ở mục 4.1 (dùng `index` làm `key` với danh sách có ô input), sau đó sửa lại bằng `id` và xác nhận lỗi đã hết.
4. Tách 1 Component `Counter` (đã làm ở Bài 4) thành 2 Component con: `CounterDisplay` (chỉ hiển thị số, nhận Props `count: number`) và `CounterButtons` (chỉ có 3 nút, nhận Props `onIncrement`, `onDecrement`, `onReset`). Áp dụng Lifting State Up: State `count` đặt ở Component cha chứa cả 2 Component con này.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
