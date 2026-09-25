# 📝 Bài Tập Về Nhà — Bài 6: useEffect Hook & Component Life Cycle

> Mục tiêu: Tìm và sửa đúng các lỗi thường gặp với `useEffect` (thiếu Cleanup, Infinite Loop, dùng Effect không cần thiết).

---

## Bài 1 — Tìm và sửa lỗi thiếu Cleanup

```tsx
function ChatRoom({ roomId }: { roomId: string }) {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const socket = connectToRoom(roomId); // Hàm giả lập kết nối realtime tới phòng chat
    socket.onMessage((msg: string) => {
      setMessages((prev) => [...prev, msg]);
    });
  }, [roomId]);

  return (
    <ul>
      {messages.map((msg, i) => (
        <li key={i}>{msg}</li>
      ))}
    </ul>
  );
}
```

**Yêu cầu:**

1. Giải thích: điều gì xảy ra nếu người dùng chuyển từ phòng chat `roomId="1"` sang `roomId="2"` nhiều lần liên tiếp? (liên hệ mục 4.3 của bài học — hàm `connectToRoom` giả lập trả về 1 object có phương thức `disconnect()`).
2. Sửa lại đoạn code để gọi `socket.disconnect()` đúng thời điểm trong Cleanup Function.
3. Sửa luôn lỗi `key={i}` (dùng index) đã học ở Bài 5 — thay bằng key phù hợp hơn cho từng tin nhắn.

---

## Bài 2 — Tìm và sửa lỗi Infinite Loop

```tsx
type Product = { id: number; name: string; views: number };

function ProductDetail({ product }: { product: Product }) {
  const [viewCount, setViewCount] = useState(product.views);

  useEffect(() => {
    setViewCount(viewCount + 1);
  }, [viewCount]);

  return (
    <div>
      <h3>{product.name}</h3>
      <p>Lượt xem: {viewCount}</p>
    </div>
  );
}
```

**Yêu cầu:**

1. Giải thích chính xác chuỗi sự kiện gây ra Infinite Loop trong đoạn code trên (liên hệ mục 7.1).
2. Mục tiêu mong muốn thực ra chỉ là "tăng lượt xem thêm 1 lần duy nhất khi Component mount". Sửa lại đoạn code cho đúng mục tiêu này (gợi ý: dependency array nên là gì?).

---

## Bài 3 — Tìm và sửa lỗi dùng Effect không cần thiết

```tsx
type Todo = { id: number; text: string; completed: boolean };

function TodoSummary({ todos }: { todos: Todo[] }) {
  const [activeCount, setActiveCount] = useState(0);
  const [hasCompletedTodos, setHasCompletedTodos] = useState(false);

  useEffect(() => {
    setActiveCount(todos.filter((t) => !t.completed).length);
  }, [todos]);

  useEffect(() => {
    setHasCompletedTodos(todos.some((t) => t.completed));
  }, [todos]);

  return (
    <div>
      <p>Còn {activeCount} việc chưa hoàn thành</p>
      {hasCompletedTodos && <p>Bạn đã hoàn thành ít nhất 1 việc!</p>}
    </div>
  );
}
```

**Yêu cầu:**

1. Giải thích vì sao cả 2 `useEffect` trong đoạn code trên đều **không cần thiết** (liên hệ mục 8.1 và khái niệm Derived State đã học ở Bài 5).
2. Viết lại `TodoSummary` không dùng `useEffect` nào cả, chỉ tính `activeCount` và `hasCompletedTodos` trực tiếp khi render.

---

## Bài 4 — Tìm và sửa lỗi race condition đơn giản (chuẩn bị cho Bài 10)

```tsx
type User = { id: number; name: string };

function UserProfile({ userId }: { userId: number }) {
  const [user, setUser] = useState<User | null>(null);

  useEffect(() => {
    async function fetchUser() {
      const response = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
      const data: User = await response.json();
      setUser(data); // Không kiểm tra gì trước khi setState
    }
    fetchUser();
  }, [userId]);

  if (!user) return <p>Đang tải...</p>;
  return <p>{user.name}</p>;
}
```

**Yêu cầu:**

1. Đây chỉ là bài tập **nhận diện vấn đề** (chưa cần giải pháp hoàn chỉnh bằng `AbortController`, sẽ học kỹ ở Bài 10): thêm 1 nút đổi nhanh `userId` giữa 2 giá trị (ví dụ 2 nút "Xem user 1" / "Xem user 2"), bấm chuyển đổi thật nhanh liên tiếp nhiều lần và mô tả hiện tượng bạn quan sát được (dữ liệu hiển thị có luôn khớp với `userId` đang chọn không?).
2. Thêm 1 biến cờ (flag) cục bộ bên trong Effect (ví dụ `let ignore = false`) và Cleanup set `ignore = true`; chỉ gọi `setUser(data)` khi `ignore` vẫn là `false`. Kiểm tra lại hiện tượng ở bước 1 đã cải thiện chưa.
