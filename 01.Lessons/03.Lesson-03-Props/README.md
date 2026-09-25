# ⭐ Bài 3: JSX Nâng Cao & Props

> 🎯 Mục tiêu: Học viên giải thích được JSX thực chất biên dịch thành gì, truyền được dữ liệu từ Component cha xuống Component con qua Props, khai báo được kiểu cho Props bằng TypeScript, và xây dựng được Component tái sử dụng với `children`.

---

## Phần 1: JSX Thực Chất Là Gì — Cách Nó Biên Dịch Thành JavaScript

### 1.0 Nhắc lại nhanh

Ở Bài 2 ta đã **viết được** JSX (thẻ, `className`, nhúng `{}`, Fragment...). Bài này sẽ trả lời câu hỏi sâu hơn: **JSX thực chất là gì**, và vì sao nó lại có những quy tắc kỳ lạ như `className` hay bắt buộc 1 root element.

### 1.1 JSX không phải HTML — cú pháp đường cho `React.createElement()`

> **JSX không phải là một ngôn ngữ mới, và trình duyệt KHÔNG hiểu được JSX. JSX chỉ là cú pháp đường (syntactic sugar) — khi build, công cụ biên dịch (Babel/TypeScript) sẽ chuyển mỗi thẻ JSX thành một lời gọi hàm `React.createElement()` bình thường.**

```jsx
// Đoạn JSX bạn viết
const element = <h1 className="title">Xin chào</h1>;
```

biên dịch thành:

```javascript
// Đoạn JavaScript thực sự chạy trong trình duyệt
const element = React.createElement(
  'h1',
  { className: 'title' },
  'Xin chào'
);
```

`React.createElement(type, props, ...children)` trả về một **object JavaScript thuần** mô tả "tôi muốn 1 thẻ `h1`, có `className` là `title`, bên trong chứa chữ `Xin chào`" — object này chính là 1 phần tử trong **Virtual DOM** mà ta sẽ tìm hiểu kỹ ở Bài 5. React đọc object này để biết cần vẽ gì lên màn hình.

### 1.2 Xem thử kết quả biên dịch qua Babel REPL

Mở [Babel REPL](https://babeljs.io/repl), bật preset **react**, rồi dán thử lại Component `Header` bạn đã viết ở Bài 2:

```jsx
function Header() {
  return (
    <header>
      <h1>Nguyễn Văn An</h1>
      <p>Frontend Developer</p>
    </header>
  );
}
```

Bạn sẽ thấy Babel biên dịch nó thành nhiều lời gọi `React.createElement()` lồng nhau — thẻ cha bọc thẻ con y hệt cấu trúc JSX bạn đã viết. Đây cũng là lý do vì sao **1 Component chỉ được return 1 root element** (quy tắc đã học ở Bài 2): `createElement()` chỉ nhận về **1 object duy nhất**, nên bạn không thể "return" 2 lời gọi hàm cùng lúc mà không bọc chúng lại.

> 💡 Từ React 17 trở đi, cách biên dịch JSX mặc định (gọi là "new JSX transform") không sinh ra `React.createElement()` mà gọi thẳng một hàm nội bộ (`_jsx()`). Bản chất không đổi — vẫn là biên dịch JSX thành lời gọi hàm JavaScript — nên bạn không cần `import React` chỉ để dùng JSX như các phiên bản React cũ.

### 1.3 Ôn lại quy tắc camelCase — vì sao JSX không dùng y hệt tên thuộc tính HTML

Vì JSX cuối cùng chỉ là **tham số của một hàm JavaScript** (`props` trong `createElement(type, props, ...)`), mà tên thuộc tính trong 1 object JavaScript phải theo quy ước đặt tên biến — đó là lý do JSX dùng **camelCase** thay vì tên thuộc tính HTML gốc:

```jsx
// ❌ Sai - đây là tên thuộc tính HTML gốc, JSX không hiểu
<button onclick={handleClick} tabindex="0">Nhấn vào đây</button>

// ✅ Đúng - camelCase, đúng quy ước đặt tên JavaScript
<button onClick={handleClick} tabIndex={0}>Nhấn vào đây</button>
```

Một vài cặp tên hay gây nhầm lẫn:

| HTML gốc | JSX (camelCase) | Lý do |
|---|---|---|
| `class` | `className` | `class` là từ khóa reserved của JavaScript (đã học ở Bài 2) |
| `for` | `htmlFor` | `for` là từ khóa reserved (dùng cho vòng lặp `for`) |
| `onclick` | `onClick` | Toàn bộ sự kiện trong JSX viết camelCase |
| `style="color: red"` (chuỗi) | `style={{ color: 'red' }}` (object) | `style` trong JSX nhận 1 object JavaScript, không nhận chuỗi CSS (đã học ở Bài 2) |

---

## Phần 2: Những Giới Hạn Của JSX Cần Lưu Ý

### 2.1 JSX là biểu thức (expression), không phải câu lệnh (statement)

Vì cặp `{}` trong JSX chỉ nhận vào **1 biểu thức JavaScript** (thứ trả về 1 giá trị), nên những cấu trúc **câu lệnh** như `if/else` đầy đủ hay vòng lặp `for` **không thể** viết trực tiếp bên trong `{}`:

```jsx
// ❌ Sai - if/else là statement, không phải expression, JSX không nhúng được
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {if (isLoggedIn) { <p>Xin chào!</p> } else { <p>Vui lòng đăng nhập</p> }}
    </div>
  );
}
```

Cách xử lý thay thế:

```jsx
// ✅ Cách 1 - Ternary (điều kiện ? A : B), viết trực tiếp trong JSX
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn ? <p>Xin chào!</p> : <p>Vui lòng đăng nhập</p>}
    </div>
  );
}

// ✅ Cách 2 - if/else đầy đủ đặt TRƯỚC return, gán kết quả vào 1 biến trung gian
function Greeting({ isLoggedIn }) {
  let message;
  if (isLoggedIn) {
    message = <p>Xin chào!</p>;
  } else {
    message = <p>Vui lòng đăng nhập</p>;
  }
  return <div>{message}</div>;
}
```

Tương tự, muốn render 1 danh sách (vòng lặp `for`), ta không viết `for` trong JSX mà dùng `map()` — array method đã ôn ở Bài 1 — để **biến mảng dữ liệu thành mảng JSX**. Đây là kỹ thuật quan trọng bậc nhất của React, sẽ học chính thức ở Bài 5.

> 💡 Toán tử `&&` cũng thường dùng để ẩn/hiện phần tử theo điều kiện (ví dụ `{isOnline && <span>Đang hoạt động</span>}`), nhưng có 1 lỗi khá phổ biến khi giá trị bên trái là số `0`. Ta sẽ mổ xẻ kỹ toán tử này ở Bài 5.

### 2.2 Lỗi thường gặp khi quên đóng thẻ hoặc quên bọc nhiều phần tử

Vì JSX biên dịch thành lời gọi hàm lồng nhau (mục 1.2), một vài lỗi cú pháp sẽ khiến quá trình biên dịch thất bại ngay khi bạn lưu file:

```jsx
// ❌ Sai - quên thẻ tự đóng, Vite/Babel báo lỗi biên dịch ngay
function Avatar() {
  return <img src="/avatar.png">;
}
```

```jsx
// ❌ Sai - 2 phần tử ngang hàng không được bọc chung
// Lỗi: "Adjacent JSX elements must be wrapped in an enclosing tag"
function Info() {
  return (
    <h1>Tiêu đề</h1>
    <p>Mô tả</p>
  );
}
```

Đây chính là 2 quy tắc đã học ở Bài 2 (thẻ tự đóng, 1 root element) — nhắc lại ở đây để hiểu **vì sao** vi phạm chúng lại gây lỗi biên dịch, chứ không chỉ đơn thuần là "quy tắc phải nhớ".

---

## Phần 3: Props Là Gì Và Cách Truyền Dữ Liệu Giữa Component Cha - Con

### 3.0 Định nghĩa

> **Props (viết tắt của "Properties") là cơ chế truyền dữ liệu từ Component cha xuống Component con, theo 1 chiều duy nhất (one-way data flow) — dữ liệu chỉ đi từ trên xuống, Component con không thể tự ý gửi ngược dữ liệu lên cha qua Props.**

### 3.1 Cú pháp truyền Props khi gọi Component

Truyền Props giống hệt cách bạn truyền attribute cho 1 thẻ HTML — chỉ khác là giá trị có thể là bất kỳ kiểu dữ liệu JavaScript nào (nhờ cặp `{}` đã học ở Bài 2), không chỉ chuỗi:

```jsx
function App() {
  return (
    <div>
      <ProductCard name="Áo thun" price={150000} />
      <ProductCard name="Quần jean" price={350000} />
    </div>
  );
}
```

Ở đây, `App` là Component **cha**, truyền xuống `ProductCard` (Component **con**) 2 Props: `name` (kiểu string) và `price` (kiểu number, nhúng bằng `{}`).

### 3.2 Nhận Props trong Component con qua tham số đầu tiên của hàm

React tự động gom toàn bộ Props bạn truyền vào thành **1 object duy nhất**, rồi đưa vào **tham số đầu tiên** của hàm Component:

```jsx
// Cách 1 - nhận nguyên object props, truy cập từng thuộc tính
function ProductCard(props) {
  return (
    <div className="card">
      <h3>{props.name}</h3>
      <p>{props.price}đ</p>
    </div>
  );
}
```

```jsx
// Cách 2 - Destructuring Object (đã ôn ở Bài 1) ngay trong tham số hàm - cách viết phổ biến hơn
function ProductCard({ name, price }) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{price}đ</p>
    </div>
  );
}
```

> 💡 Đây chính là ứng dụng thực tế của **Destructuring Object** đã ôn kỹ ở Bài 1 — lúc đó ta chỉ nói "đây là cách bạn sẽ nhận Props ở Bài 3", giờ bạn đã thấy rõ.

### 3.3 Nguyên tắc Immutability (Tính vô biến - Read-only) của Props

> **Props luôn là read-only (chỉ đọc). Component con TUYỆT ĐỐI không được tự ý thay đổi giá trị Props mà nó nhận được.**

```jsx
// ❌ Sai - Component con cố gắng thay đổi Props nhận được
function ProductCard({ name, price }) {
  price = price * 0.9; // Cố tình giảm giá 10% - SAI NGUYÊN TẮC
  return <h3>{name} - {price}đ</h3>;
}
```

Vì sao đây là lỗi nghiêm trọng: `ProductCard` không "sở hữu" dữ liệu `price`, nó chỉ đang **mượn** dữ liệu đó từ Component cha. Nếu component con tự thay đổi Props, dữ liệu ở nhiều nơi sẽ không đồng bộ và React không có cách nào biết được để re-render đúng.

```jsx
// ✅ Đúng - muốn hiển thị giá trị đã biến đổi, tạo 1 biến MỚI, không gán đè lên Props
function ProductCard({ name, price }) {
  const discountedPrice = price * 0.9; // Biến mới, price gốc không đổi
  return <h3>{name} - {discountedPrice}đ</h3>;
}
```

> ⚠️ Nếu Component con thực sự cần "yêu cầu" Component cha thay đổi dữ liệu (ví dụ: nút "Thêm vào giỏ" bên trong `ProductCard` cần báo cho `App` biết), cách làm đúng là Component cha truyền xuống 1 **Props kiểu function** (callback), Component con **gọi** hàm đó thay vì tự sửa dữ liệu. Đây gọi là **Lifting State Up** — ta sẽ học chi tiết ở Bài 5, sau khi đã có `useState` ở Bài 4.

---

## Phần 4: Định Nghĩa Kiểu Cho Props Với TypeScript

### 4.1 Khai báo `type` Props cho Component

Ở Bài 1, ta đã học dùng `type` để mô tả 1 object. Áp dụng ngay vào Props: khai báo 1 `type` mô tả Component nhận vào những Props gì, rồi gắn kiểu đó cho tham số của hàm Component.

```tsx
type ProductCardProps = {
  name: string;
  price: number;
};

function ProductCard({ name, price }: ProductCardProps) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{price}đ</p>
    </div>
  );
}
```

Lợi ích thấy ngay: nếu bạn gọi Component sai cách, TypeScript báo lỗi **ngay trong VS Code**, không cần chạy thử mới phát hiện:

```tsx
<ProductCard name="Áo thun" price="150000" />
// ❌ Type 'string' is not assignable to type 'number' (price phải là number, không phải string)

<ProductCard name="Áo thun" />
// ❌ Property 'price' is missing in type '{ name: string; }' (thiếu Props bắt buộc)
```

> 💡 Quy ước đặt tên: `type` cho Props của Component `Xyz` thường đặt tên `XyzProps`, giúp dễ tìm và đọc code.

### 4.2 Truyền và nhận Props nhiều kiểu dữ liệu

Props không chỉ nhận kiểu nguyên thủy (string, number, boolean) mà còn nhận được Object, Array, và cả **Function**:

```tsx
type Product = {
  id: number;
  name: string;
  price: number;
};

type ProductListProps = {
  products: Product[];           // Props kiểu Array of Object
  featured: Product;             // Props kiểu Object
  onSelect: (id: number) => void; // Props kiểu Function (callback)
};

function ProductList({ products, featured, onSelect }: ProductListProps) {
  return (
    <div>
      <p>Nổi bật: {featured.name}</p>
      {products.map((p) => (
        <button key={p.id} onClick={() => onSelect(p.id)}>
          {p.name}
        </button>
      ))}
    </div>
  );
}
```

Component cha sử dụng, truyền vào 1 hàm cho Props `onSelect`:

```tsx
function App() {
  const handleSelect = (id: number) => {
    console.log('Đã chọn sản phẩm có id:', id);
  };

  return <ProductList products={products} featured={products[0]} onSelect={handleSelect} />;
}
```

> 💡 Kiểu `(id: number) => void` (đã học ở Bài 1, mục Generics/kiểu function) mô tả: "1 hàm nhận vào 1 số, không trả về gì (`void`)". Đây là cách bạn sẽ khai báo hầu hết các Props kiểu callback trong suốt khóa học — ví dụ `onAdd`, `onDelete`, `onToggle`.

### 4.3 Props tùy chọn (?) và giá trị mặc định bằng Destructuring

Không phải Props nào cũng bắt buộc phải truyền. Đánh dấu Props tùy chọn bằng dấu `?` (đã học ở Bài 1), rồi gán giá trị mặc định ngay khi destructuring:

```tsx
type ButtonProps = {
  label: string;
  variant?: 'primary' | 'secondary' | 'danger'; // Tùy chọn, Union Type giới hạn giá trị
};

function Button({ label, variant = 'primary' }: ButtonProps) {
  return <button className={`btn btn-${variant}`}>{label}</button>;
}
```

```tsx
<Button label="Lưu" />                    {/* variant tự động = 'primary' */}
<Button label="Xóa" variant="danger" />   {/* variant = 'danger' */}
```

### 4.4 Lưu ý: PropTypes và defaultProps đã bị loại bỏ khỏi function component từ React 19

Nếu tìm tài liệu React cũ, bạn sẽ thấy cách kiểm tra kiểu Props bằng thư viện `prop-types` và gán giá trị mặc định bằng `Component.defaultProps`:

```jsx
// ❌ Cách cũ - KHÔNG dùng trong khóa học này
import PropTypes from 'prop-types';

function Button({ label, variant }) { /* ... */ }
Button.propTypes = { label: PropTypes.string, variant: PropTypes.string };
Button.defaultProps = { variant: 'primary' };
```

Từ **React 19**, `defaultProps` không còn tác dụng với function component, và `prop-types` chỉ còn mang tính lịch sử. **TypeScript đã thay thế hoàn toàn** vai trò kiểm tra kiểu này — mạnh hơn vì báo lỗi ngay lúc viết code thay vì lúc chạy — còn giá trị mặc định thì dùng destructuring như mục 4.3.

---

## Phần 5: Props.children Và Composition Pattern

### 5.0 Định nghĩa

> **`children` là 1 Props đặc biệt mà React tự động tạo ra, chứa toàn bộ nội dung được đặt GIỮA cặp thẻ mở-đóng của Component khi gọi nó.**

### 5.1 props.children là gì

```tsx
type CardProps = {
  children: React.ReactNode;
};

function Card({ children }: CardProps) {
  return <div className="card">{children}</div>;
}
```

```tsx
<Card>
  <h3>Tiêu đề</h3>
  <p>Nội dung bất kỳ đặt bên trong Card</p>
</Card>
```

Ở đây, toàn bộ `<h3>...</h3><p>...</p>` được truyền vào `Card` qua Props `children` — `Card` không cần biết trước bên trong nó sẽ hiển thị gì.

> 💡 **`React.ReactNode`** là kiểu dữ liệu do React cung cấp, mô tả "bất kỳ thứ gì render được": 1 thẻ JSX, 1 chuỗi, 1 số, `null`, hoặc 1 mảng các thứ đó. Dùng kiểu này cho `children` vì nội dung bên trong 1 Component có thể là bất kỳ dạng nào.

### 5.2 Ứng dụng thực tế: xây dựng Component Modal/Card dùng chung

```tsx
type ModalProps = {
  title: string;
  children: React.ReactNode;
};

function Modal({ title, children }: ModalProps) {
  return (
    <div className="modal-overlay">
      <div className="modal-box">
        <h2>{title}</h2>
        <div className="modal-body">{children}</div>
      </div>
    </div>
  );
}
```

Cùng 1 Component `Modal`, dùng lại được cho nhiều loại nội dung hoàn toàn khác nhau:

```tsx
<Modal title="Xác nhận xóa">
  <p>Bạn có chắc muốn xóa sản phẩm này?</p>
  <button>Xóa</button>
</Modal>

<Modal title="Đăng ký nhận tin">
  <input type="email" placeholder="Nhập email của bạn" />
  <button>Đăng ký</button>
</Modal>
```

### 5.3 Composition Pattern — ưu điểm so với việc truyền quá nhiều Props rời rạc

Nếu không dùng `children`, để làm được việc tương tự, ta phải "nhồi" mọi khả năng có thể xảy ra vào Props, khiến Component ngày càng cồng kềnh và khó mở rộng:

```tsx
// ❌ Cách tiếp cận cồng kềnh - Modal phải "biết trước" mọi trường hợp sử dụng
type ModalProps = {
  title: string;
  message?: string;
  showInput?: boolean;
  inputType?: string;
  buttonLabel?: string;
  // ... và sẽ còn phình to mãi mỗi khi có 1 use-case mới
};
```

```tsx
// ✅ Composition Pattern - Modal không cần biết nội dung bên trong là gì
type ModalProps = {
  title: string;
  children: React.ReactNode;
};
```

**Composition Pattern** (ghép Component lại với nhau qua `children`) linh hoạt hơn hẳn vì Component cha tự do quyết định nội dung, còn Component con chỉ lo phần khung/layout dùng chung — đúng tinh thần "chia nhỏ, ghép lại" đã học từ Bài 2.

---

## Phần 6: Thực Hành — Xây Dựng Card Component Tái Sử Dụng

### 6.1 Thiết kế kiểu `CardProps`

```tsx
type CardProps = {
  title: string;
  image: string;
  description: string;
  children?: React.ReactNode; // Tùy chọn - phần nội dung thêm (ví dụ nút hành động)
};
```

### 6.2 Viết Component Card và style bằng CSS Module

```css
/* src/components/Card.module.css */
.card {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.image {
  width: 100%;
  height: 160px;
  object-fit: cover;
}

.body {
  padding: 16px;
}

.title {
  margin-bottom: 8px;
}

.description {
  color: #64748b;
}
```

```tsx
// src/components/Card.tsx
import styles from './Card.module.css';

type CardProps = {
  title: string;
  image: string;
  description: string;
  children?: React.ReactNode;
};

function Card({ title, image, description, children }: CardProps) {
  return (
    <div className={styles.card}>
      <img className={styles.image} src={image} alt={title} />
      <div className={styles.body}>
        <h3 className={styles.title}>{title}</h3>
        <p className={styles.description}>{description}</p>
        {children}
      </div>
    </div>
  );
}

export default Card;
```

### 6.3 Tái sử dụng Card với nhiều bộ dữ liệu khác nhau

```tsx
// App.tsx
import Card from './components/Card';

function App() {
  return (
    <div className="app-container">
      <Card
        title="Áo thun basic"
        image="/images/tshirt.jpg"
        description="Chất liệu cotton 100%, thoáng mát"
      >
        <button>Thêm vào giỏ</button>
      </Card>

      <Card
        title="Quần jean slimfit"
        image="/images/jean.jpg"
        description="Form dáng ôm, phù hợp đi làm và đi chơi"
      >
        <button>Thêm vào giỏ</button>
      </Card>
    </div>
  );
}

export default App;
```

Ở đây `Card` được tái sử dụng 2 lần với dữ liệu Props khác nhau (`title`, `image`, `description`), đồng thời vẫn nhận thêm `children` khác nhau (ở đây giống nhau, nhưng hoàn toàn có thể khác — ví dụ 1 Card thêm huy hiệu "Hết hàng").

---

## 🧪 Bài tập thực hành cuối buổi

1. Mở [Babel REPL](https://babeljs.io/repl), dán Component `ProductCard` bạn viết ở Bài 1, quan sát kết quả biên dịch ra `React.createElement()`.
2. Viết Component `Greeting` nhận Props `hour: number` (giờ hiện tại), hiển thị "Chào buổi sáng" / "Chào buổi chiều" / "Chào buổi tối" tương ứng, dùng if/else với biến trung gian (mục 2.1).
3. Viết Component `PriceTag` nhận Props `price: number` và `discountPercent?: number` (tùy chọn, mặc định `0`). Nếu có giảm giá, hiển thị cả giá gốc (gạch ngang) và giá sau giảm; tạo biến mới để tính, không thay đổi Props gốc.
4. Viết Component `Alert` nhận Props `type: 'success' | 'error' | 'warning'` và `children: React.ReactNode`, hiển thị màu nền khác nhau theo `type` và render `children` bên trong.

## 🧪 Bài tập HomeWork

Xem tại file [homeworks.md](homeworks.md)
