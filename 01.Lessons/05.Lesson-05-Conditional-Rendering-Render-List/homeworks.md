# 📝 Bài Tập Về Nhà — Bài 5: Conditional Rendering, Render Danh Sách & Lifting State Up

> Mục tiêu: Xây dựng được trang tìm kiếm/lọc dữ liệu qua nhiều Component chia sẻ State bằng Lifting State Up, hiển thị đúng trạng thái rỗng và số kết quả bằng Derived State.

---

## Bài 1 — Trang danh sách sản phẩm: tìm kiếm + lọc danh mục

**1. Dữ liệu ban đầu:**

```ts
type Product = {
  id: number;
  name: string;
  category: 'ao' | 'quan' | 'phu-kien';
  price: number;
};

const products: Product[] = [
  { id: 1, name: 'Áo thun basic', category: 'ao', price: 150000 },
  { id: 2, name: 'Áo sơ mi trắng', category: 'ao', price: 280000 },
  { id: 3, name: 'Quần jean slimfit', category: 'quan', price: 350000 },
  { id: 4, name: 'Quần kaki', category: 'quan', price: 320000 },
  { id: 5, name: 'Mũ lưỡi trai', category: 'phu-kien', price: 120000 },
  { id: 6, name: 'Thắt lưng da', category: 'phu-kien', price: 180000 },
];
```

**2. Yêu cầu:** Tách giao diện thành 3 Component:

- `SearchBox`: ô input tìm kiếm theo tên, Props `keyword: string` và `onChange: (keyword: string) => void`.
- `CategoryFilter`: nhóm nút hoặc `<select>` chọn danh mục (`'all' | 'ao' | 'quan' | 'phu-kien'`), Props `category` và `onChange`.
- `ProductGrid`: chỉ nhận `products: Product[]` (đã lọc sẵn) qua Props và hiển thị — **không tự chứa logic lọc/tìm kiếm bên trong Component này**.

Đặt State `keyword` và `category` ở Component cha (`App`) theo đúng kỹ thuật **Lifting State Up** đã học ở Phần 6. Danh sách hiển thị trong `ProductGrid` phải thỏa **đồng thời cả 2 điều kiện**: đúng từ khóa tìm kiếm (không phân biệt hoa thường) **và** đúng danh mục đang chọn.

**Yêu cầu bổ sung (Derived State — Phần 7):**

- Hiển thị dòng chữ **"Tìm thấy N sản phẩm"** (N tính trực tiếp từ mảng đã lọc, không lưu vào `useState` riêng).
- Khi không có sản phẩm nào khớp điều kiện, `ProductGrid` hiển thị **"Không tìm thấy sản phẩm phù hợp"** thay vì để trống.
- Render danh sách bằng `map()` với `key` là `product.id` (không dùng `index`).

---

## Bài 2 — Danh sách nhân viên: sắp xếp và đếm theo phòng ban

**1. Dữ liệu ban đầu:**

```ts
type Employee = {
  id: number;
  name: string;
  department: 'sales' | 'engineering' | 'design';
};

const employees: Employee[] = [
  { id: 1, name: 'An', department: 'engineering' },
  { id: 2, name: 'Bình', department: 'sales' },
  { id: 3, name: 'Chi', department: 'design' },
  { id: 4, name: 'Dũng', department: 'engineering' },
];
```

**2. Yêu cầu:** Xây dựng Component `EmployeeDirectory`:

- Nhóm nút lọc theo `department` (`'all' | 'sales' | 'engineering' | 'design'`).
- Với mỗi phòng ban trong nhóm nút lọc, hiển thị kèm **số lượng nhân viên** thuộc phòng ban đó ngay trên nút (ví dụ: "Engineering (2)") — số này phải là **Derived State**, tính bằng `filter().length` từ mảng `employees` gốc, không lưu vào `useState` riêng.
- Danh sách nhân viên hiển thị bên dưới hiển thị "Không có nhân viên nào trong phòng ban này" khi bộ lọc không khớp nhân viên nào.

---

## Bài 3 — Tìm và giải thích lỗi `key`

**1. Dữ liệu ban đầu:**

```tsx
type Message = { id: number; author: string; text: string };

function ChatBox() {
  const [messages, setMessages] = useState<Message[]>([
    { id: 1, author: 'An', text: 'Chào bạn' },
    { id: 2, author: 'Bình', text: 'Chào An' },
  ]);
  const [draft, setDraft] = useState('');

  const handleSend = () => {
    setMessages((prev) => [{ id: Date.now(), author: 'Tôi', text: draft }, ...prev]);
    setDraft('');
  };

  return (
    <div>
      {messages.map((msg, index) => (
        <div key={index}>
          <b>{msg.author}:</b> {msg.text}
        </div>
      ))}
      <input value={draft} onChange={(e) => setDraft(e.target.value)} />
      <button onClick={handleSend}>Gửi</button>
    </div>
  );
}
```

**Yêu cầu:**

1. Chạy thử đoạn code trên (tin nhắn mới được thêm vào **đầu** danh sách bằng `[newMessage, ...prev]`). Thêm 1 hiệu ứng phụ dễ quan sát lỗi: cho mỗi `<div>` tin nhắn 1 `background-color` ngẫu nhiên khác nhau bằng `useState` cục bộ bên trong 1 Component `MessageItem` riêng (tách `<div>` tin nhắn ra thành Component con để mỗi item tự chọn màu 1 lần duy nhất). Gửi vài tin nhắn liên tiếp và mô tả hiện tượng bạn quan sát được (liên hệ lại mục 4.1 của bài học — vì sao `key={index}` gây ra hiện tượng này khi phần tử mới được thêm vào **đầu** danh sách thay vì cuối).
2. Sửa `key={index}` thành `key={msg.id}` và xác nhận hiện tượng đã hết.
