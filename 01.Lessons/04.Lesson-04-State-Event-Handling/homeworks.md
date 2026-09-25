# 📝 Bài Tập Về Nhà — Bài 4: State & Event Handling

> Mục tiêu: Quản lý đúng Array State và Object State theo nguyên tắc Immutability, sửa được các lỗi mutate State thường gặp.

---

## Bài 1 — Giỏ hàng đơn giản trong 1 Component

**1. Dữ liệu ban đầu:**

```ts
type CartItem = {
  id: number;
  name: string;
  price: number;
  quantity: number;
};

const initialProducts: Omit<CartItem, 'quantity'>[] = [
  { id: 1, name: 'Áo thun', price: 150000 },
  { id: 2, name: 'Quần jean', price: 350000 },
  { id: 3, name: 'Mũ lưỡi trai', price: 120000 },
];
```

**2. Yêu cầu:** Xây dựng Component `ShoppingCart` với:

- **Array State** `cart: CartItem[]` (khởi tạo rỗng `[]`) lưu các sản phẩm đã thêm vào giỏ.
- Danh sách `initialProducts` hiển thị cố định, mỗi sản phẩm có nút **"Thêm vào giỏ"**:
  - Nếu sản phẩm **chưa có** trong `cart` → thêm mới với `quantity: 1`.
  - Nếu **đã có** → tăng `quantity` của sản phẩm đó lên 1 (không thêm dòng trùng).
- Khu vực giỏ hàng hiển thị từng `CartItem` kèm 2 nút **"+"** / **"−"** để tăng/giảm `quantity` (nếu giảm về `0` thì tự động xóa khỏi giỏ) và 1 nút **"Xóa"** để xóa hẳn khỏi giỏ bất kể số lượng.
- Hiển thị **tổng tiền** (tính từ `cart`, không lưu vào 1 State riêng — tính trực tiếp khi render).
- **Toàn bộ thao tác cập nhật `cart` phải dùng Functional Update kết hợp `map()`/`filter()`/spread** (mục 5.3-5.5 của bài học) — không dùng `push()`, `splice()`, hoặc gán trực tiếp `item.quantity = ...`.

---

## Bài 2 — Tìm và sửa lỗi mutate State

Đoạn code dưới đây có nhiều lỗi vi phạm nguyên tắc Immutability. Hãy tìm và viết lại cho đúng, **không thay đổi hành vi mong muốn** của từng hàm.

```tsx
type Todo = { id: number; text: string; completed: boolean };

function TodoApp() {
  const [todos, setTodos] = useState<Todo[]>([
    { id: 1, text: 'Học React', completed: false },
    { id: 2, text: 'Làm bài tập', completed: false },
  ]);
  const [filter, setFilter] = useState({ keyword: '', onlyPending: false });

  // Lỗi 1: thêm todo mới
  const handleAdd = (text: string) => {
    todos.push({ id: Date.now(), text, completed: false });
    setTodos(todos);
  };

  // Lỗi 2: toggle trạng thái hoàn thành
  const handleToggle = (id: number) => {
    const todo = todos.find((t) => t.id === id);
    if (todo) {
      todo.completed = !todo.completed;
      setTodos(todos);
    }
  };

  // Lỗi 3: cập nhật 1 phần của Object State - mất field còn lại
  const handleKeywordChange = (keyword: string) => {
    setFilter({ keyword });
  };

  // Lỗi 4: xóa todo bằng splice
  const handleRemove = (id: number) => {
    const index = todos.findIndex((t) => t.id === id);
    todos.splice(index, 1);
    setTodos(todos);
  };

  // ... phần render
}
```

**Yêu cầu:** Với mỗi lỗi, ghi chú ngắn giải thích **vì sao sai** (liên hệ lại mục 5.6 của bài học — vì sao React không re-render), sau đó sửa lại đúng theo nguyên tắc Immutability đã học (Functional Update + spread/`map`/`filter`).

---

## Bài 3 — Bảng xếp hạng có sắp xếp và tìm kiếm

**1. Dữ liệu ban đầu:**

```ts
type Player = {
  id: number;
  name: string;
  score: number;
};

const initialPlayers: Player[] = [
  { id: 1, name: 'An', score: 85 },
  { id: 2, name: 'Bình', score: 92 },
  { id: 3, name: 'Chi', score: 78 },
];
```

**2. Yêu cầu:** Xây dựng Component `Leaderboard`:

- **Array State** `players: Player[]`, khởi tạo từ `initialPlayers`.
- Ô input cho phép **cộng điểm** cho 1 người chơi theo `id` được chọn (dùng `<select>` liệt kê tên người chơi + input số điểm cộng thêm) — cập nhật đúng bằng `map()`, không mutate.
- Nút **"Sắp xếp theo điểm giảm dần"**: tạo 1 **mảng mới đã sắp xếp** để hiển thị (gợi ý: `[...players].sort(...)` — không được gọi `players.sort()` trực tiếp vì `sort()` làm thay đổi mảng gốc).
- Ô tìm kiếm theo tên: lọc danh sách hiển thị bằng `filter()` dựa trên State `keyword: string`, không ảnh hưởng đến State `players` gốc.
