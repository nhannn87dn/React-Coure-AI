# ⭐ Bài 6: useEffect Hook & Component Life Cycle

> 🎯 Mục tiêu: Học viên giải thích được Side Effect và vòng đời Component, sử dụng đúng `useEffect` với 3 dạng dependency và Cleanup Function, và nhận biết được khi nào KHÔNG cần dùng `useEffect`.

---

## Phần 1: Khái Niệm Side Effect Và Component Life Cycle

### 1.0 Nhắc lại nhanh

Từ Bài 4, ta đã biết: khi State thay đổi, Component **re-render** để tính ra JSX mới. Ở Bài 5, ta học **Derived State** — dữ liệu tính được từ State khác thì tính trực tiếp khi render, không tạo thêm State riêng. Bài này bàn đến 1 nhóm việc **khác hẳn** việc tính toán JSX: những việc cần "giao tiếp" với thế giới bên ngoài Component.

### 1.1 Định nghĩa Side Effect

> **Side Effect (tác vụ phụ) là bất kỳ thao tác nào khiến Component "giao tiếp" với 1 hệ thống bên ngoài phạm vi của chính nó** — gọi API, đọc/ghi `localStorage`, thao tác trực tiếp DOM ngoài JSX, đặt Timer (`setInterval`/`setTimeout`), subscribe 1 sự kiện của trình duyệt (`resize`, `scroll`)...

Điểm chung của các Side Effect: chúng không liên quan đến việc "tính ra JSX trông như thế nào" — chúng là những việc **xảy ra** bên cạnh quá trình render.

### 1.2 Vì sao không nên gọi Side Effect trực tiếp trong thân hàm Component

```tsx
// ❌ Sai - gọi fetch() ngay trong thân hàm Component
function UserProfile({ userId }: { userId: number }) {
  fetch(`https://api.example.com/users/${userId}`); // Chạy lại MỖI LẦN Component re-render

  return <div>...</div>;
}
```

Thân hàm Component được React gọi lại **liên tục** — mỗi lần render (kể cả khi chỉ 1 State không liên quan thay đổi) là 1 lần hàm này chạy lại từ đầu. Nếu đặt `fetch()` trực tiếp ở đây, request sẽ bị gọi lại **vô tội vạ**, không đồng bộ với thời điểm thực sự cần (ví dụ: chỉ cần gọi khi `userId` đổi, hoặc chỉ khi Component xuất hiện lần đầu).

### 1.3 `useEffect` là "cửa thoát hiểm" để đồng bộ Component với hệ thống bên ngoài

> **`useEffect` là Hook cho phép bạn khai báo: "sau khi Component render xong, hãy chạy đoạn code này để đồng bộ với 1 hệ thống bên ngoài" — và kiểm soát chính xác NÓ CHẠY LẠI KHI NÀO.**

`useEffect` tách phần Side Effect ra khỏi luồng tính JSX chính, để React có thể kiểm soát **thời điểm** chạy nó một cách hợp lý (sau khi giao diện đã hiển thị xong, không chặn người dùng).

### 1.4 Component Life Cycle: Mounting, Updating, Unmounting

Một Component trải qua 3 giai đoạn trong "vòng đời" của nó:

| Giai đoạn | Khi nào xảy ra |
|---|---|
| **Mounting** | Component xuất hiện lần đầu tiên trên giao diện |
| **Updating** | Component re-render do State/Props thay đổi (đã học ở Bài 4) |
| **Unmounting** | Component bị gỡ bỏ hoàn toàn khỏi giao diện (ví dụ: điều hướng sang trang khác, hoặc bị ẩn đi bởi 1 điều kiện — Bài 5) |

`useEffect` cho phép bạn "móc" (hook) vào cả 3 giai đoạn này — chạy code khi mount, khi update (theo dependency cụ thể), và dọn dẹp khi unmount (Cleanup Function — Phần 3).

---

## Phần 2: `useEffect` Cơ Bản — Cú Pháp Và Dependency Array

### 2.1 Cú pháp `useEffect(() => {...}, [dependencies])`

```tsx
import { useEffect, useState } from 'react';

function WelcomeMessage() {
  const [name] = useState('An');

  useEffect(() => {
    console.log(`Xin chào, ${name}!`); // Đây là Side Effect (console.log ra "bên ngoài")
  }, [name]);

  return <p>Chào mừng!</p>;
}
```

`useEffect` nhận vào 2 tham số: 1 hàm chứa đoạn Side Effect cần chạy, và 1 **mảng dependency** (mảng phụ thuộc) quyết định **khi nào** hàm đó được chạy lại.

### 2.2 Effect chạy sau khi Component render xong

Thứ tự thực thi quan trọng cần nhớ: React render Component (tính JSX, cập nhật lên màn hình) **trước**, rồi mới chạy các `useEffect` bên trong Component đó. Effect **không chặn** việc hiển thị giao diện — người dùng luôn thấy giao diện cập nhật ngay, Effect chạy "phía sau" ngay sau đó.

### 2.3 Đọc State/Props mới nhất bên trong Effect

```tsx
function SearchResults({ keyword }: { keyword: string }) {
  useEffect(() => {
    console.log('Từ khóa hiện tại:', keyword); // Luôn đọc đúng giá trị "keyword" của lần render này
  }, [keyword]);

  return <div>Kết quả cho: {keyword}</div>;
}
```

Mỗi lần Component re-render, `useEffect` được định nghĩa lại (với 1 "bản sao" mới của hàm), nên bên trong nó luôn đọc được giá trị State/Props **mới nhất tại thời điểm render đó** — không có tình trạng đọc "State cũ" như một số framework khác.

---

## Phần 3: Phân Biệt 3 Dạng Dependency

### 3.1 Không truyền mảng dependency — Effect chạy lại sau MỌI lần render

```tsx
useEffect(() => {
  console.log('Effect này chạy sau MỌI LẦN render, kể cả khi không có gì thay đổi');
}); // Không có tham số thứ 2
```

Cách này hiếm khi cần dùng trong thực tế (dễ gây chạy lại quá thường xuyên không cần thiết) — chủ yếu dùng để debug tạm thời.

### 3.2 Mảng rỗng `[]` — Effect chỉ chạy đúng 1 lần khi Component mount

```tsx
useEffect(() => {
  console.log('Effect này CHỈ chạy 1 lần duy nhất, ngay sau khi Component mount');
}, []); // Mảng rỗng
```

Mảng rỗng nghĩa là "Effect này không phụ thuộc vào bất kỳ giá trị nào có thể thay đổi" — nên React chỉ cần chạy nó đúng 1 lần, ở giai đoạn **Mounting** (Phần 1.4).

### 3.3 Mảng có giá trị `[deps]` — Effect chạy lại mỗi khi giá trị trong mảng thay đổi

```tsx
function ProductDetail({ productId }: { productId: number }) {
  useEffect(() => {
    console.log('Tải lại chi tiết sản phẩm cho id:', productId);
  }, [productId]); // Chạy lại mỗi khi productId đổi

  return <div>...</div>;
}
```

Mỗi lần Component re-render, React so sánh giá trị hiện tại trong mảng dependency với giá trị của lần render trước — nếu **có ít nhất 1 giá trị khác đi**, Effect được chạy lại. Nếu tất cả giá trị vẫn giống hệt, Effect được **bỏ qua** ở lần render đó.

> ⚠️ **Quy tắc quan trọng:** mảng dependency phải liệt kê **đầy đủ** mọi State/Props mà Effect có sử dụng bên trong. ESLint plugin `react-hooks` (đã cài sẵn trong template Vite, sẽ nhắc lại ở Bài 7) sẽ tự động cảnh báo nếu bạn quên khai báo 1 dependency đang được dùng bên trong Effect.

---

## Phần 4: Cleanup Function — Tránh Memory Leak

### 4.1 Cú pháp `return` function cleanup bên trong `useEffect`

```tsx
useEffect(() => {
  console.log('Effect chạy');

  return () => {
    console.log('Cleanup chạy - dọn dẹp trước khi Effect kế tiếp hoặc khi unmount');
  };
}, []);
```

Nếu hàm truyền vào `useEffect` có `return` ra **1 hàm khác**, React sẽ coi đó là **Cleanup Function** — đoạn code chuyên trách việc "dọn dẹp" những gì Effect đã thiết lập trước đó.

### 4.2 Khi nào Cleanup Function được gọi

> **Cleanup Function được React gọi ở 2 thời điểm: (1) ngay TRƯỚC KHI Effect chạy lại ở lần render kế tiếp (do dependency thay đổi), và (2) khi Component UNMOUNT (bị gỡ khỏi giao diện — Phần 1.4).**

Nói cách khác: cứ mỗi lần Effect "sắp chạy lại" hoặc Component "sắp biến mất", React luôn gọi Cleanup của lần chạy **trước đó** để dọn dẹp, rồi mới chạy tiếp (nếu có).

### 4.3 Ví dụ Memory Leak thực tế nếu quên Cleanup Timer

```tsx
// ❌ Sai - không cleanup, mỗi lần Component mount lại sẽ có THÊM 1 interval chạy song song
function Clock() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    setInterval(() => {
      setTime(new Date());
    }, 1000);
  }, []);

  return <p>{time.toLocaleTimeString()}</p>;
}
```

Nếu `Clock` bị unmount (ví dụ người dùng chuyển sang trang khác) rồi mount lại nhiều lần, mỗi lần mount lại tạo thêm **1 interval mới chạy song song** — các interval cũ **không tự dừng**, chúng vẫn tiếp tục chạy trong bộ nhớ dù Component đã biến mất, gây tốn tài nguyên và có thể gọi `setTime` trên 1 Component không còn tồn tại. Đây chính là **Memory Leak** (rò rỉ bộ nhớ).

```tsx
// ✅ Đúng - Cleanup Function gọi clearInterval khi unmount
function Clock() {
  const [time, setTime] = useState(new Date());

  useEffect(() => {
    const intervalId = setInterval(() => {
      setTime(new Date());
    }, 1000);

    return () => clearInterval(intervalId); // Dọn dẹp: dừng interval trước khi unmount
  }, []);

  return <p>{time.toLocaleTimeString()}</p>;
}
```

> 💡 **Quy tắc thực tế:** bất kỳ Effect nào **thiết lập** 1 thứ gì đó có thể tiếp diễn theo thời gian (Timer, Event Listener, subscription...) đều nên có Cleanup Function **hủy** đúng thứ đã thiết lập đó.

---

## Phần 5: StrictMode — Vì Sao Effect Chạy 2 Lần Khi Dev

### 5.1 Hiện tượng: Effect chạy 2 lần ở môi trường dev

Chạy đoạn code ở Phần 4.3 (bản đã sửa đúng) với `pnpm run dev`, mở Console, bạn sẽ thấy `useEffect` có vẻ **chạy 2 lần liên tiếp** ngay khi mount — nếu Effect có `console.log`, log đó xuất hiện 2 lần dù chỉ mount 1 Component.

### 5.2 Nguyên nhân: `StrictMode` cố tình mount → unmount → mount lại

Trong `src/main.tsx`, dự án Vite mặc định bọc `<App />` bằng `<StrictMode>`:

```tsx
createRoot(document.getElementById('root')!).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

> **`StrictMode` là 1 công cụ hỗ trợ phát triển (chỉ hoạt động ở môi trường dev) giúp phát hiện sớm các Effect thiếu Cleanup. Nó cố tình: mount Component → chạy Effect → unmount ngay → chạy Cleanup → mount lại → chạy lại Effect.** Nếu Cleanup Function của bạn viết đúng (Phần 4), quá trình "giả lập" 2 lần này sẽ **vô hại** — mọi thứ được dọn dẹp sạch sẽ trước khi Effect chạy lại. Nếu Cleanup thiếu hoặc sai, bạn sẽ thấy hậu quả rõ ràng ngay khi dev (ví dụ: 2 `interval` cùng chạy — Phần 4.3), thay vì phải đợi đến khi gặp lỗi khó hiểu lúc lên production.

### 5.3 Chỉ xảy ra khi dev, không ảnh hưởng production

Hành vi "chạy 2 lần" này **chỉ xảy ra ở môi trường dev** — khi build production (`pnpm run build`, sẽ học ở Bài 12), `StrictMode` không còn giả lập mount/unmount kép nữa, Effect chỉ chạy đúng 1 lần như bình thường.

> ⚠️ **Cách xử lý đúng khi thấy Effect chạy 2 lần là viết lại Cleanup Function cho đúng (Phần 4), KHÔNG PHẢI xóa `<StrictMode>` đi.** Xóa `StrictMode` chỉ "che giấu" triệu chứng — lỗi thiếu Cleanup vẫn còn đó, và có thể gây ra Memory Leak thật sự khi lên production dù không còn hiện tượng chạy 2 lần để cảnh báo bạn nữa.

---

## Phần 6: Trường Hợp Sử Dụng Thực Tế

### 6.1 Gọi API lấy dữ liệu khi Component mount

```tsx
type User = {
  id: number;
  name: string;
  email: string;
};

function UserList() {
  const [users, setUsers] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    async function fetchUsers() {
      const response = await fetch('https://jsonplaceholder.typicode.com/users');
      const data: User[] = await response.json();
      setUsers(data);
      setIsLoading(false);
    }

    fetchUsers();
  }, []); // Chỉ gọi 1 lần khi mount

  if (isLoading) return <p>Đang tải...</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

> ⚠️ Không thể khai báo trực tiếp `useEffect(async () => {...}, [])` — hàm truyền cho `useEffect` không được phép là `async` (vì `async function` luôn trả về 1 `Promise`, còn `useEffect` chỉ chấp nhận `undefined` hoặc 1 Cleanup Function ở giá trị trả về). Cách làm đúng là khai báo 1 hàm `async` **riêng** bên trong Effect rồi gọi nó ngay, như ví dụ trên.

### 6.2 Subscribe/Unsubscribe sự kiện DOM

```tsx
function WindowSize() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize); // Cleanup - gỡ listener
  }, []);

  return <p>Chiều rộng cửa sổ: {width}px</p>;
}
```

### 6.3 Đặt và dọn dẹp `setInterval`/`setTimeout` đúng cách

Đã thấy ở Phần 4.3 (`Clock`) — quy tắc chung: hễ `setInterval`/`setTimeout` xuất hiện trong Effect, gần như luôn cần `clearInterval`/`clearTimeout` tương ứng trong Cleanup.

---

## Phần 7: Lỗi Phổ Biến — Infinite Loop Với `useEffect`

### 7.1 Nguyên nhân: cập nhật State bên trong Effect nhưng State đó lại nằm trong dependency

```tsx
// ❌ Sai - Infinite Loop
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    setCount(count + 1); // Cập nhật "count"
  }, [count]); // ...nhưng "count" lại là dependency -> Effect chạy lại -> lại setCount -> lặp vô hạn

  return <p>{count}</p>;
}
```

Chuỗi sự kiện: Component render → Effect chạy vì `count` có mặt trong dependency (lần đầu luôn chạy) → Effect gọi `setCount` → `count` đổi → Component re-render → `count` trong dependency lại khác giá trị trước đó → Effect chạy lại → lại `setCount`... cứ thế lặp lại **không bao giờ dừng**.

### 7.2 Cách phát hiện Infinite Loop

- **Qua Console:** nếu Effect có `console.log`, bạn sẽ thấy log đó in ra **liên tục không ngừng**, không phải 1-2 lần như StrictMode (Phần 5).
- **Qua tab Network:** nếu Effect gọi API, tab Network sẽ hiện **request lặp đi lặp lại liên tục** thay vì chỉ 1 lần.
- Trình duyệt có thể trở nên đứng/lag rõ rệt vì Component re-render với tốc độ cực nhanh.

### 7.3 Hướng khắc phục

```tsx
// ✅ Đúng - dùng Functional Update (đã học ở Bài 4), bỏ "count" khỏi dependency
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1); // Không cần đọc "count" từ closure -> không cần đưa vào dependency
    }, 1000);
    return () => clearInterval(id);
  }, []); // Effect chỉ setup 1 lần

  return <p>{count}</p>;
}
```

> 💡 Khi gặp Infinite Loop, luôn kiểm tra lại: **Effect có đang `setState` cho đúng State nằm trong dependency array của chính nó không?** Nếu có, cân nhắc: dùng Functional Update để loại State đó khỏi dependency (như ví dụ trên), hoặc xem lại liệu giá trị đó có thực sự cần là `useEffect` hay chỉ là Derived State (Phần 8).

---

## Phần 8: Khi Nào KHÔNG Cần `useEffect`

### 8.1 Dữ liệu tính được từ State/Props → dùng Derived State, không dùng Effect để đồng bộ

```tsx
// ❌ Không cần thiết - dùng useEffect chỉ để đồng bộ 1 State sang 1 State khác
function Cart({ items }: { items: { price: number }[] }) {
  const [total, setTotal] = useState(0);

  useEffect(() => {
    setTotal(items.reduce((sum, item) => sum + item.price, 0));
  }, [items]); // Effect thừa - gây thêm 1 lần re-render không cần thiết

  return <p>Tổng: {total}đ</p>;
}
```

```tsx
// ✅ Đúng - Derived State (đã học ở Bài 5), tính trực tiếp khi render
function Cart({ items }: { items: { price: number }[] }) {
  const total = items.reduce((sum, item) => sum + item.price, 0);

  return <p>Tổng: {total}đ</p>;
}
```

Cách dùng `useEffect` ở ví dụ sai không chỉ thừa mà còn tệ hơn: nó khiến trang phải re-render **thêm 1 lần nữa** sau khi Effect chạy xong (lần đầu hiển thị `total = 0`, rồi mới nhảy lên giá trị đúng) — trong khi cách tính trực tiếp cho ra kết quả đúng **ngay từ lần render đầu tiên**, không có độ trễ thừa nào.

### 8.2 Logic xảy ra do hành động của người dùng → đặt trong Event Handler, không đặt trong Effect

```tsx
// ❌ Không cần thiết - dùng Effect để "phản ứng" lại 1 lần click
function SubmitButton() {
  const [clicked, setClicked] = useState(false);

  useEffect(() => {
    if (clicked) {
      console.log('Đã gửi form!');
      // Gọi API submit ở đây...
    }
  }, [clicked]);

  return <button onClick={() => setClicked(true)}>Gửi</button>;
}
```

```tsx
// ✅ Đúng - xử lý ngay trong Event Handler, không cần State trung gian "clicked"
function SubmitButton() {
  const handleClick = () => {
    console.log('Đã gửi form!');
    // Gọi API submit ở đây...
  };

  return <button onClick={handleClick}>Gửi</button>;
}
```

> ⚠️ **Quy tắc phân biệt:** `useEffect` dùng để đồng bộ Component với **hệ thống bên ngoài** sau khi render (Phần 1.3) — ví dụ tải dữ liệu khi 1 tham số URL đổi. Còn hành động xảy ra **trực tiếp do người dùng thao tác** (click, submit, gõ phím) luôn nên xử lý ngay trong Event Handler tương ứng (đã học ở Bài 4) — không cần "vòng qua" 1 State trung gian rồi bắt Effect theo dõi State đó.

---

## 🧪 Bài tập thực hành cuối buổi

### 1. Đồng hồ đếm ngược

Xây dựng Component `CountdownTimer` nhận Props `seconds: number`, hiển thị số giây còn lại, có 3 nút: **Start** (bắt đầu đếm ngược bằng `setInterval`), **Pause** (dừng tạm, không mất giá trị hiện tại), **Reset** (về lại giá trị `seconds` ban đầu). Đảm bảo `clearInterval` đúng cách trong Cleanup Function khi Pause hoặc khi Component unmount.

### 2. Theo dõi kích thước cửa sổ

Hoàn thiện Component `WindowSize` ở mục 6.2 thành 1 trang thực hành đầy đủ: hiển thị cả `width` và `height`, thử resize cửa sổ trình duyệt (hoặc dùng chế độ responsive của DevTools) để quan sát giá trị cập nhật theo thời gian thực.

### 3. Tải danh sách người dùng từ API

Hoàn thiện Component `UserList` ở mục 6.1: thêm State `error: string | null`, dùng `try/catch` (đã ôn ở Bài 1) bên trong hàm `fetchUsers`, hiển thị thông báo lỗi nếu gọi API thất bại (thử đổi URL thành 1 địa chỉ sai để kiểm tra nhánh lỗi).

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
