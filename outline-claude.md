# Outline lộ trình học React JS cho người mới

> 📌 **Thông tin chung**
> - Thời lượng: 12 buổi × 4 giờ, 3 buổi/tuần (4 tuần, 48 giờ)
> - Tech stack: React 19 + **TypeScript** + Vite + pnpm · React Router v7 · Zustand v5 · TanStack Query v5 · Axios · Vercel
> - Mini Project: giao đề cuối Bài 7, làm tại nhà
> - Capstone Project: giao đề cuối Bài 12, làm tại nhà, nộp sau khóa học
> - Bài Bonus (tự chọn): Bài 13 Tailwind CSS, Bài 14 StyleX, Bài 15 Tối ưu hiệu năng - xem phần "Phần Bonus" cuối file

---

## ⭐ Bài 1: Giới Thiệu React, Ôn Tập JavaScript ESNext & TypeScript Cơ Bản

> 🎯 **Mục tiêu:** Giải thích được React giải quyết vấn đề gì, tự tạo được dự án React + TypeScript bằng Vite, sử dụng thành thạo các cú pháp ESNext hay dùng trong React và khai báo được kiểu dữ liệu cơ bản bằng TypeScript.
>
> 🔁 **Ôn tập đầu buổi:** Không (buổi đầu tiên)

### ✨ React là gì và tại sao nó thay đổi cách viết Frontend

#### 🔹Lịch sử ra đời của React và vấn đề nó giải quyết (thao tác DOM thủ công với jQuery/Vanilla JS)

#### 🔹Khái niệm "Declarative UI" vs "Imperative UI" - so sánh bằng ví dụ code song song

#### 🔹Hệ sinh thái xoay quanh React (Next.js, React Native, Remix) - cái nhìn tổng quan

#### 🔹So sánh nhanh React với Vue/Angular - vì sao chọn React để học đầu tiên

#### 🔹Mức độ phổ biến của React (GitHub Star, Google Trends) - cái nhìn tổng quan về độ phổ biến

#### 🔹Những gì có thể xây dựng được với React (web, mobile qua React Native, desktop qua Electron...)

#### 🔹Một số dự án/công ty lớn đang dùng React trong thực tế

### ✨ Cài đặt môi trường phát triển (Node.js, pnpm, Vite, VS Code Extensions)

#### 🔹Cài đặt Node.js và kiểm tra phiên bản (node -v, npm -v)

#### 🔹Cài đặt pnpm (npm install -g pnpm) - vì sao dùng pnpm thay npm

#### 🔹Tạo dự án React + TypeScript đầu tiên với Vite (pnpm create vite@latest my-app --template react-ts)

#### 🔹Cấu trúc thư mục mặc định của dự án Vite (src, public, main.tsx, App.tsx, tsconfig.json, vite.config.ts)

#### 🔹Cài đặt VS Code Extensions cần thiết (ES7+ React Snippets, Prettier, ESLint, Error Lens)

#### 🔹Chạy dự án với pnpm run dev và làm quen với Hot Module Replacement (HMR)

### ✨ Ôn tập Arrow Function, Destructuring, Spread/Rest Operator

#### 🔹Arrow Function - cú pháp rút gọn và sự khác biệt về `this` so với function thường

#### 🔹Destructuring Object - lấy nhanh nhiều giá trị từ object, đổi tên biến, giá trị mặc định

#### 🔹Destructuring Array - áp dụng khi làm việc với useState (const [count, setCount] = ...)

#### 🔹Spread Operator (...) - copy object/array, merge nhiều object không làm thay đổi bản gốc

#### 🔹Rest Operator - gom nhiều tham số hoặc phần còn lại của object/array vào 1 biến

### ✨ Ôn tập Template Literals, Optional Chaining (?.) và Nullish Coalescing (??)

#### 🔹Template Literals - nối chuỗi và nhúng biến bằng dấu backtick, so sánh với concat truyền thống

#### 🔹Optional Chaining (?.) - tránh lỗi "Cannot read property of undefined" khi truy cập object lồng nhau

#### 🔹Nullish Coalescing (??) - gán giá trị mặc định, phân biệt với toán tử `||` khi giá trị là 0 hoặc chuỗi rỗng

#### 🔹Kết hợp ?. và ?? trong tình huống thực tế: hiển thị dữ liệu API có thể null/undefined

### ✨ Ôn tập Array Methods quan trọng (map, filter, reduce, find)

#### 🔹map() - biến đổi mảng dữ liệu, nền tảng để render danh sách trong React

#### 🔹filter() - lọc phần tử theo điều kiện, ứng dụng cho search/filter UI

#### 🔹find() - tìm 1 phần tử duy nhất thỏa điều kiện, phân biệt với filter()

#### 🔹reduce() - tính tổng/gộp dữ liệu, ví dụ tính tổng tiền giỏ hàng

#### 🔹Kết hợp nhiều Array Method liên tiếp (method chaining) trong 1 biểu thức

### ✨ Ôn tập Module (import/export) và Promise/Async-Await cơ bản

#### 🔹Named Export vs Default Export - khi nào dùng loại nào

#### 🔹Cú pháp import với alias (as) và import toàn bộ module (* as)

#### 🔹Promise là gì - 3 trạng thái pending/fulfilled/rejected

#### 🔹Async/Await - cú pháp giúp code bất đồng bộ trông như đồng bộ

#### 🔹Xử lý lỗi với try/catch khi dùng Async/Await

### ✨ TypeScript tối thiểu để bắt đầu với React

#### 🔹TypeScript là gì - vì sao dùng TypeScript với React (bắt lỗi ngay khi code, gợi ý code tốt hơn), phân biệt file .ts và .tsx

#### 🔹Kiểu dữ liệu cơ bản (string, number, boolean, string[]) và Type Inference - khi nào TypeScript tự suy ra kiểu, không cần khai báo

#### 🔹Mô tả Object bằng `type` và `interface`, thuộc tính tùy chọn (?) - quy ước khóa học dùng `type`, `interface` chỉ cần đọc hiểu

#### 🔹Union Type và Literal Type (`'idle' | 'loading' | 'error'`), kiểu `null`/`undefined` - liên hệ với ?. và ?? vừa ôn

#### 🔹Khai báo kiểu cho tham số và giá trị trả về của function, kiểu `Promise<T>` cho hàm async

#### 🔹Generics ở mức "đọc hiểu" (`Array<T>`, `Promise<T>`) - sẽ gặp lại với `useState<T>` ở Bài 4

#### 🔹Tránh dùng `any` - dùng `unknown` khi chưa biết kiểu dữ liệu

### 📝 Homework

- Bài tập ESNext (destructuring, spread, array methods, ?./??, async/await) xử lý dữ liệu sản phẩm/giỏ hàng
- Khai báo `type`/`interface` cho dữ liệu và gắn kiểu cho toàn bộ các hàm trong bài tập, không dùng `any`

---

## ⭐ Bài 2: Component Đầu Tiên & JSX Cơ Bản

> 🎯 **Mục tiêu:** Chia được một giao diện thành các Component, viết được JSX đúng cú pháp trong file .tsx, và dựng được một trang tĩnh hoàn chỉnh từ nhiều Component.
>
> 🔁 **Ôn tập đầu buổi:** Arrow Function, Module import/export, Template Literals, `type`/`interface`

### ✨ Component là gì - tư duy chia nhỏ giao diện (Component-Based Thinking)

#### 🔹Function Component - cách viết một Component chuẩn (PascalCase, return JSX)

#### 🔹Tư duy chia nhỏ UI thành các khối độc lập, tái sử dụng được (ví dụ: Header, Sidebar, Button)

#### 🔹Component cha - con (Parent - Child) và cách chúng lồng nhau tạo thành cây giao diện

#### 🔹Import và Export Component để sử dụng ở file khác

### ✨ Viết JSX Cơ Bản - Ngôn Ngữ Bên Trong Component

#### 🔹JSX trông giống HTML - cách viết thẻ, text, và các thuộc tính quen thuộc (src, href, id...)

#### 🔹className thay cho class - lý do liên quan đến từ khóa reserved trong JavaScript

#### 🔹Thẻ tự đóng bắt buộc với thẻ không có children (`<img />`, `<input />`, `<br />`)

#### 🔹Quy tắc bắt buộc: 1 Component chỉ được return 1 root element (dùng Fragment `<>...</>`)

### ✨ Nhúng Biểu Thức JavaScript Vào JSX

#### 🔹Cú pháp dấu ngoặc nhọn {} - nhúng biến, phép tính, gọi hàm

#### 🔹Nhúng thuộc tính động vào style và các Prop khác (ví dụ: src ảnh lấy từ biến)

#### 🔹Comment trong JSX - cú pháp `{/* ... */}` khác với HTML comment

### ✨ Cấu trúc một dự án React + TypeScript tạo bằng Vite

#### 🔹Vai trò của file index.html, main.tsx và App.tsx trong luồng khởi chạy ứng dụng

#### 🔹Thư mục src/assets, src/components - quy ước tổ chức code phổ biến

#### 🔹File vite.config.ts, tsconfig.json và package.json - các thông tin quan trọng cần biết

### ✨ Các cách style Component

#### 🔹Import Global CSS vào Component - cách Vite xử lý file .css

#### 🔹CSS Module (.module.css) - tránh xung đột tên class giữa các Component

#### 🔹Giới thiệu nhanh Tailwind CSS - cách style phổ biến trong dự án thực tế (khuyến khích dùng cho Mini Project/Capstone)

### ✨ Thực hành: Xây dựng trang giới thiệu cá nhân tĩnh từ nhiều Component

#### 🔹Tách trang thành các Component nhỏ (Header, Introduction, Footer)

#### 🔹Viết nội dung JSX tĩnh cho từng Component, nhúng vài biến động bằng {}

#### 🔹Ghép các Component lại trong App.tsx để hoàn thiện 1 trang hoàn chỉnh

### 📝 Homework

- Sửa lỗi JSX trong đoạn code có sẵn, chuyển HTML tĩnh sang JSX hợp lệ
- Dựng trang tĩnh nhiều Component qua nhiều file .tsx, có dùng CSS Module

---

## ⭐ Bài 3: JSX Nâng Cao & Props

> 🎯 **Mục tiêu:** Giải thích được JSX biên dịch thành gì, truyền được dữ liệu từ cha xuống con qua Props, khai báo được kiểu cho Props bằng TypeScript và xây dựng được Component tái sử dụng với `children`.
>
> 🔁 **Ôn tập đầu buổi:** Quy tắc JSX, Component cha - con, Destructuring Object, `type`/`interface`

### ✨ JSX Thực Chất Là Gì - Cách Nó Biên Dịch Thành JavaScript

#### 🔹JSX không phải HTML - bản chất là cú pháp đường (syntactic sugar) cho React.createElement()

#### 🔹Xem thử kết quả biên dịch JSX qua Babel Repl (minh họa trực quan) - liên hệ lại các Component đã viết ở Bài 2

#### 🔹Ôn lại quy tắc camelCase (onClick, tabIndex, backgroundColor trong style) - vì sao JSX không dùng y hệt tên thuộc tính HTML

### ✨ Những Giới Hạn Của JSX Cần Lưu Ý

#### 🔹Những gì KHÔNG thể nhúng trực tiếp vào JSX (if/else, for loop) và cách xử lý thay thế (ternary, biến trung gian)

#### 🔹Lỗi thường gặp khi quên đóng thẻ hoặc quên bọc nhiều phần tử trong 1 root element

### ✨ Props Là Gì Và Cách Truyền Dữ Liệu Giữa Component Cha - Con

#### 🔹Props là gì - cơ chế truyền dữ liệu một chiều (one-way data flow) từ cha xuống con

#### 🔹Cú pháp truyền Props khi gọi Component (giống truyền attribute trong HTML)

#### 🔹Nhận Props trong Component con qua tham số đầu tiên của hàm

#### 🔹Nguyên tắc Immutability (Tính vô biến - Read-only) của Props

### ✨ Định Nghĩa Kiểu Cho Props Với TypeScript

#### 🔹Khai báo `type`/`interface` Props cho Component - TypeScript báo lỗi ngay khi truyền thiếu hoặc sai kiểu

#### 🔹Truyền và nhận Props nhiều kiểu dữ liệu (Primitive, Object, Array, Function - ví dụ `onSelect: (id: number) => void`)

#### 🔹Props tùy chọn (?) và giá trị mặc định bằng Destructuring (`{ variant = 'primary' }`)

#### 🔹Lưu ý: PropTypes và defaultProps đã bị loại bỏ khỏi function component từ React 19 - TypeScript thay thế vai trò kiểm tra kiểu

### ✨ Props.children Và Composition Pattern

#### 🔹props.children là gì - cách Component "bọc" nội dung bên trong nó, kiểu `React.ReactNode`

#### 🔹Ứng dụng thực tế: xây dựng Component Modal/Card dùng chung cho nhiều loại nội dung

#### 🔹Composition Pattern - ưu điểm so với việc truyền quá nhiều Props rời rạc

### ✨ Thực hành: Xây dựng Card component tái sử dụng, nhận dữ liệu qua Props

#### 🔹Thiết kế kiểu `CardProps` (title, image, description, children)

#### 🔹Viết Component Card và style bằng CSS Module

#### 🔹Tái sử dụng Card với nhiều bộ dữ liệu khác nhau trong cùng 1 trang (truyền Props khác nhau)

### 📝 Homework

- Xây dựng bộ Component UI dùng chung (Button có nhiều variant, Badge, Card) với Props có kiểu đầy đủ
- Dựng trang danh sách khóa học/sản phẩm tĩnh từ mảng dữ liệu, tái sử dụng Card

---

## ⭐ Bài 4: State & Event Handling

> 🎯 **Mục tiêu:** Phân biệt được State và Props, sử dụng đúng useState (kể cả Object/Array State) theo nguyên tắc Immutability, xử lý được sự kiện chuột/bàn phím có kiểu dữ liệu và debug State bằng React DevTools.
>
> 🔁 **Ôn tập đầu buổi:** Props và one-way data flow, Spread Operator, map()/filter(), Union Type

### ✨ Tại sao cần State - Component không tự "nhớ" dữ liệu

#### 🔹 State là gì và sự khác biệt cốt lõi giữa State vs Props

#### 🔹 Vấn đề: thay đổi biến JavaScript thường không làm giao diện re-render

#### 🔹 Khái niệm "re-render" là gì trong vòng đời của React

### ✨ Sử dụng useState Hook

#### 🔹 Cú pháp const [state, setState] = useState(initialValue)

#### 🔹 Kiểu dữ liệu cho State: TypeScript tự suy ra từ giá trị khởi tạo, khi nào cần khai báo rõ `useState<User | null>(null)`

#### 🔹 Quy tắc Hook - chỉ gọi useState ở top-level của Component, không gọi trong if/loop

#### 🔹 Immutability - vì sao không được thay đổi trực tiếp State (ví dụ lỗi khi push() vào mảng State)

### ✨ Lưu Ý Quan Trọng: Cập Nhật State Không Ngay Lập Tức

#### 🔹 Demo: gọi console.log(state) ngay sau setState() và thấy giá trị chưa đổi - state chỉ cập nhật ở lần render kế tiếp

#### 🔹 Lỗi thường gặp: gọi setCount(count + 1) nhiều lần liên tiếp nhưng chỉ tăng 1 lần

#### 🔹 Cách khắc phục: dùng Functional Update setCount(c => c + 1) khi cần cập nhật dựa trên giá trị trước đó

### ✨ Quản lý nhiều State trong một Component

#### 🔹 Khai báo nhiều useState độc lập cho từng loại dữ liệu (thay vì gộp vào 1 object lớn)

#### 🔹 Cập nhật 1 phần của object State - lỗi thường gặp khi quên spread phần còn lại

### ✨ Quản Lý State Dạng Dữ Liệu Phức Tạp (Object State và Array State)

#### 🔹 Cập nhật Object State đúng cách - dùng spread {...state, key: newValue} thay vì gán trực tiếp state.key = newValue

#### 🔹 Cập nhật Object State lồng nhau (nested object) - vì sao spread 1 lớp là chưa đủ, cần spread từng cấp

#### 🔹 Cập nhật Array State - thêm phần tử mới bằng [...array, newItem] thay vì dùng push()

#### 🔹 Xóa phần tử khỏi Array State bằng filter() - tạo mảng mới thay vì splice() trực tiếp

#### 🔹 Sửa 1 phần tử trong Array State bằng map() - tìm đúng phần tử theo id rồi trả về bản sao đã cập nhật

#### 🔹 Lỗi thường gặp: React không re-render vì thay đổi trực tiếp Object/Array mà không tạo tham chiếu mới

### ✨ Xử lý sự kiện (Event Handling) trong React

#### 🔹 Khái niệm Synthetic Events và kiểu dữ liệu Event trong TypeScript (`React.MouseEvent`, `React.KeyboardEvent`)

#### 🔹 Sự kiện Mouse: onClick, onDoubleClick, truyền tham số vào Event Handler bằng Arrow Function inline, onMouseEnter/onMouseLeave cho hover effect

#### 🔹 Sự kiện Keyboard: onKeyDown, onKeyUp, lấy phím vừa nhấn qua e.key, ứng dụng bắt phím Enter để thực hiện hành động

#### 🔹 Ngăn hành vi mặc định của trình duyệt khi cần thiết (e.preventDefault())

### ✨ Debug State với React DevTools

#### 🔹 Cài đặt extension React Developer Tools, tab Components - xem cây Component, Props và State

#### 🔹 Sửa State trực tiếp trên DevTools để thử nghiệm giao diện

#### 🔹 Bật "Highlight updates when components render" để quan sát Component nào đang re-render

### ✨ Thực hành: Counter, Toggle Switch và Mini-game phản xạ bằng phím

#### 🔹 Xây dựng Counter với nút tăng/giảm/reset sử dụng useState và sự kiện Click

#### 🔹 Xây dựng Toggle Switch (ẩn/hiện nội dung) bằng boolean State

#### 🔹 Xây dựng Mini-game đơn giản: nhấn đúng phím được yêu cầu để cộng điểm (kết hợp State + Keyboard Event)

### 📝 Homework

- Xây dựng giỏ hàng đơn giản trong 1 Component: thêm sản phẩm, tăng/giảm số lượng, xóa (Array State + Object State)
- Viết lại các đoạn code cập nhật State sai (mutate trực tiếp) cho đúng nguyên tắc Immutability

---

## ⭐ Bài 5: Conditional Rendering, Render Danh Sách & Lifting State Up

> 🎯 **Mục tiêu:** Render được giao diện theo điều kiện và danh sách có key đúng, giải thích được cơ chế Virtual DOM/Reconciliation, chia sẻ được State giữa các Component bằng Lifting State Up và nhận biết được dữ liệu nào là Derived State.
>
> 🔁 **Ôn tập đầu buổi:** useState với Array State, Props kiểu function (callback), map()/filter()

### ✨ Các cách render có điều kiện (if/else, ternary, &&)

#### 🔹Dùng if/else thông thường trước return để chọn JSX render ra

#### 🔹Toán tử Ternary (điều kiện ? A : B) - viết inline ngay trong JSX

#### 🔹Toán tử && - ẩn/hiện phần tử khi không cần nhánh else

#### 🔹Lỗi thường gặp: && với giá trị 0 sẽ render ra số 0 ngoài ý muốn

### ✨ Render danh sách với Array.map() và vai trò của Key

#### 🔹Dùng map() để biến mảng dữ liệu (có kiểu, ví dụ `Todo[]`) thành mảng JSX Element

#### 🔹Prop key là gì - giúp React nhận diện phần tử nào thay đổi/thêm/xóa

#### 🔹Vì sao nên dùng ID duy nhất làm key, tránh dùng index khi danh sách có thể thay đổi thứ tự

### ✨ Virtual DOM và Cơ Chế Reconciliation (Giải Thích Trực Quan)

#### 🔹Real DOM là gì và vì sao thao tác trực tiếp Real DOM tốn hiệu năng

#### 🔹Virtual DOM là gì - bản sao nhẹ (lightweight copy) của Real DOM trong bộ nhớ

#### 🔹Diffing Algorithm - cách React so sánh Virtual DOM cũ và mới để tìm thay đổi, key chính là "căn cước" giúp so sánh phần tử trong danh sách

#### 🔹Reconciliation - quá trình cập nhật Real DOM chỉ ở phần thay đổi, không render lại toàn bộ trang

### ✨ Lỗi thường gặp khi dùng Key sai cách

#### 🔹Dùng index làm key gây lỗi hiển thị sai khi thêm/xóa/sắp xếp lại danh sách (demo với input trong từng item)

#### 🔹Cảnh báo "Warning: each child in a list should have a unique key" - nguyên nhân và cách sửa

#### 🔹Key phải duy nhất trong phạm vi danh sách anh em (sibling), không cần duy nhất toàn cục

### ✨ Kết hợp Conditional Rendering và List Rendering

#### 🔹Hiển thị thông báo "Không có dữ liệu" khi mảng rỗng trước khi map()

#### 🔹Lọc dữ liệu bằng filter() trước khi map() để render danh sách theo điều kiện

#### 🔹Kết hợp nhiều điều kiện lồng nhau trong cùng 1 danh sách (ví dụ: phân loại theo trạng thái)

### ✨ Lifting State Up - Chia Sẻ State Giữa Các Component

#### 🔹Vấn đề: 2 Component anh em cần dùng chung 1 dữ liệu (ví dụ TodoForm thêm việc, TodoList hiển thị) - State nằm trong 1 Component con thì Component kia không đọc được

#### 🔹Giải pháp: "nâng" State lên Component cha chung gần nhất, rồi truyền xuống các con qua Props

#### 🔹Con báo ngược lên cha qua Callback Props (`onAdd: (text: string) => void`) - nguyên tắc "data đi xuống, event đi lên"

#### 🔹Component con "không trạng thái" - chỉ nhận value + callback từ cha, dễ tái sử dụng và dễ kiểm soát

#### 🔹Khi nào KHÔNG nên lift: State chỉ 1 Component dùng thì giữ tại chỗ; lift quá cao dẫn đến Prop Drilling (sẽ giải quyết ở Bài 9)

### ✨ Derived State - Không Lưu Vào State Những Gì Tính Ra Được

#### 🔹Ví dụ: số việc đã hoàn thành, danh sách đã lọc - tính trực tiếp khi render thay vì tạo thêm useState

#### 🔹Lỗi thường gặp: lưu trùng dữ liệu vào 2 State khiến chúng bị lệch nhau (out of sync)

### ✨ Thực hành: Ứng dụng Todo nhiều Component

#### 🔹Tách giao diện thành TodoForm, TodoList, TodoItem, TodoFilter; khai báo kiểu `Todo {id, text, completed}`

#### 🔹Đặt State `todos` và `filter` ở App (Lifting State Up), truyền dữ liệu và callback thêm/toggle/xóa xuống các Component con

#### 🔹Hiển thị danh sách đã lọc (Tất cả/Đang làm/Hoàn thành) và số việc còn lại bằng Derived State

### 📝 Homework

- Xây dựng trang danh sách sản phẩm: ô tìm kiếm + bộ lọc danh mục (Component riêng) cùng điều khiển danh sách (Component riêng) qua Lifting State Up
- Hiển thị trạng thái rỗng "Không tìm thấy sản phẩm" và số kết quả tìm được bằng Derived State

---

## ⭐ Bài 6: useEffect Hook & Component Life Cycle

> 🎯 **Mục tiêu:** Giải thích được Side Effect và vòng đời Component, sử dụng đúng useEffect với 3 dạng dependency và Cleanup Function, nhận biết được khi nào KHÔNG cần dùng useEffect.
>
> 🔁 **Ôn tập đầu buổi:** Re-render, Async/Await, Derived State (Bài 5)

### ✨ Khái niệm Side Effect và Component Life Cycle

#### 🔹Định nghĩa Side Effect - các thao tác "giao tiếp với thế giới bên ngoài" Component (API, DOM, Timer)

#### 🔹Vì sao không nên gọi Side Effect trực tiếp trong thân hàm Component

#### 🔹useEffect là "cửa thoát hiểm" để đồng bộ Component với hệ thống bên ngoài

#### 🔹Component Life Cycle: Mounting, Updating, Unmounting

### ✨ useEffect cơ bản - cú pháp và Dependency Array

#### 🔹Cú pháp useEffect(() => {...}, [dependencies])

#### 🔹Effect chạy sau khi Component render xong (không chặn giao diện)

#### 🔹Đọc State/Props mới nhất bên trong Effect

### ✨ Phân biệt 3 dạng dependency: [], [deps], không có mảng

#### 🔹Không truyền mảng dependency - Effect chạy lại sau MỌI lần render

#### 🔹Mảng rỗng [] - Effect chỉ chạy đúng 1 lần khi Component mount

#### 🔹Mảng có giá trị [deps] - Effect chạy lại mỗi khi giá trị trong mảng thay đổi

### ✨ Cleanup Function - tránh Memory Leak

#### 🔹Cú pháp return function cleanup bên trong useEffect

#### 🔹Khi nào Cleanup Function được gọi (trước lần Effect kế tiếp, hoặc khi Component unmount)

#### 🔹Ví dụ Memory Leak thực tế nếu quên cleanup Timer hoặc Event Listener

### ✨ StrictMode - Vì Sao Effect Chạy 2 Lần Khi Dev

#### 🔹Hiện tượng: console.log trong Effect in ra 2 lần, API bị gọi 2 lần ở môi trường dev

#### 🔹Nguyên nhân: StrictMode cố tình mount → unmount → mount lại để phát hiện Effect thiếu Cleanup

#### 🔹Chỉ xảy ra khi dev, không ảnh hưởng production - cách xử lý đúng là viết Cleanup, không phải xóa StrictMode

### ✨ Trường hợp sử dụng thực tế: gọi API, subscribe event, đặt Timer

#### 🔹Gọi API lấy dữ liệu khi Component mount (async function bên trong Effect, khai báo kiểu cho dữ liệu trả về)

#### 🔹Subscribe/Unsubscribe sự kiện DOM (ví dụ: window resize, scroll)

#### 🔹Đặt và dọn dẹp setInterval/setTimeout đúng cách

### ✨ Lỗi phổ biến: Infinite Loop với useEffect

#### 🔹Nguyên nhân: cập nhật State bên trong Effect nhưng State đó lại nằm trong dependency

#### 🔹Cách phát hiện Infinite Loop qua Console (log liên tục) hoặc tab Network gọi API liên tục

#### 🔹Hướng khắc phục: kiểm tra lại dependency array và tách logic hợp lý

### ✨ Khi Nào KHÔNG Cần useEffect

#### 🔹Dữ liệu tính được từ State/Props → tính trực tiếp khi render (Derived State ở Bài 5), không dùng Effect để "đồng bộ" State này sang State khác

#### 🔹Logic xảy ra do hành động của người dùng (click, submit) → đặt trong Event Handler, không đặt trong Effect

### ✨ Thực hành: Đồng hồ đếm ngược, theo dõi kích thước cửa sổ, tải danh sách người dùng

#### 🔹Đồng hồ đếm ngược với nút Start/Pause/Reset (setInterval + Cleanup)

#### 🔹Hiển thị kích thước cửa sổ trình duyệt theo thời gian thực (resize listener + Cleanup)

#### 🔹Tải danh sách người dùng từ API khi mount, hiển thị trạng thái đang tải

### 📝 Homework

- Tìm và sửa lỗi trong các đoạn code useEffect có sẵn (thiếu Cleanup, Infinite Loop, dùng Effect không cần thiết)

---

## ⭐ Bài 7: Forms & Custom Hooks

> 🎯 **Mục tiêu:** Xây dựng được Form có validate với Controlled Component, sử dụng được useRef để truy cập DOM, và tự viết được Custom Hook có kiểu Generic để tái sử dụng logic.
>
> 🔁 **Ôn tập đầu buổi:** Event Handling, Object State, useEffect với localStorage, Generics

### ✨ Xử Lý Sự Kiện Form (onChange, onSubmit)

#### 🔹 Bắt sự kiện onChange để đồng bộ giá trị input với State (Controlled Input cơ bản), kiểu `React.ChangeEvent<HTMLInputElement>`

#### 🔹 Xử lý onSubmit và ngăn reload trang mặc định của thẻ form, kiểu `React.FormEvent<HTMLFormElement>`

#### 🔹 Validate dữ liệu đơn giản trước khi submit (kiểm tra rỗng, độ dài)

### ✨ Controlled vs Uncontrolled Component

#### 🔹 Nguyên lý Single Source of Truth trong quản lý dữ liệu Form

#### 🔹 Controlled Component - giá trị input hoàn toàn do State React quản lý (value + onChange)

#### 🔹 Uncontrolled Component - giá trị input để DOM tự quản lý, lấy dữ liệu qua ref khi cần

#### 🔹 So sánh ưu/nhược điểm và khi nào nên chọn cách nào

#### 🔹 Xử lý Form có nhiều trường nhập liệu (Multi-input Form) chỉ với 1 State Object và 1 handler dùng `[e.target.name]`

### ✨ useRef - truy cập DOM trực tiếp khi cần thiết

#### 🔹 Cú pháp `useRef<HTMLInputElement>(null)` và gắn ref vào phần tử JSX, kiểm tra null trước khi dùng

#### 🔹 Phân biệt useRef với useState - useRef không gây re-render khi thay đổi giá trị .current

#### 🔹 Ứng dụng thực tế: tự động focus vào input, đọc giá trị input không qua State (Uncontrolled Form)

### ✨ Tại sao cần Custom Hook - tránh lặp code logic

#### 🔹 Vấn đề: nhiều Component dùng chung 1 đoạn logic Hook giống hệt nhau

#### 🔹 Custom Hook là gì - bản chất chỉ là 1 hàm JavaScript thường bắt đầu bằng "use"

#### 🔹 Custom Hook giúp tách UI (JSX) ra khỏi Logic (State, Effect) như thế nào

### ✨ Xây dựng Custom Hook thực tế: useToggle, useLocalStorage

#### 🔹 Viết useToggle - đóng gói logic bật/tắt boolean dùng lại ở nhiều Component

#### 🔹 Viết `useLocalStorage<T>` với Generic - đồng bộ State bất kỳ kiểu nào với localStorage của trình duyệt

#### 🔹 Sử dụng lại 2 Custom Hook này trong nhiều Component khác nhau để thấy lợi ích tái sử dụng

### ✨ Rules of Hooks - ôn lại và công cụ hỗ trợ

#### 🔹 Ôn nhanh 2 quy tắc: chỉ gọi Hook ở top-level, chỉ gọi từ Function Component hoặc Custom Hook

#### 🔹 Vì sao vi phạm quy tắc này gây lỗi khó debug (thứ tự Hook bị xáo trộn giữa các lần render)

#### 🔹 ESLint plugin react-hooks (có sẵn trong template Vite) tự phát hiện vi phạm và cảnh báo thiếu dependency

### ✨ Thực hành: Xây dựng Form đăng nhập hoàn chỉnh với validate và Custom Hook

#### 🔹 Xây dựng Form đăng nhập với 2 trường input, validate và hiển thị thông báo lỗi

#### 🔹 Áp dụng useToggle để ẩn/hiện mật khẩu

#### 🔹 Áp dụng useLocalStorage để lưu lại "Ghi nhớ đăng nhập"

### 📝 Homework

- Xây dựng Form đăng ký nhiều trường (họ tên, email, mật khẩu, xác nhận mật khẩu, giới tính, đồng ý điều khoản) với validate đầy đủ
- Viết thêm Custom Hook `useDebounce<T>` và áp dụng cho ô tìm kiếm
- Bắt đầu Mini Project (xem đề bên dưới)

---

## 🧩 Mini Project Giữa Khóa (giao đề cuối Bài 7, làm tại nhà)

> Tổng hợp kiến thức Bài 1-7 (bao gồm Form có validate và Custom Hook vừa học ở Bài 7). Học viên chọn 1 trong 3 đề.

### ✨ Đề tài gợi ý

#### 🔹Ứng dụng Quản lý chi tiêu cá nhân - thêm/sửa/xóa khoản chi, lọc theo danh mục và tháng, hiển thị tổng chi

#### 🔹Ứng dụng Quiz trắc nghiệm - tải câu hỏi từ file JSON/API, đếm giờ mỗi câu, tính điểm và hiển thị kết quả

#### 🔹Pomodoro & Task Tracker - danh sách công việc kết hợp đồng hồ Pomodoro, đếm số phiên đã hoàn thành cho mỗi việc

### ✨ Yêu cầu tối thiểu

#### 🔹Tối thiểu 5 Component, Props và State có kiểu đầy đủ, không dùng `any`

#### 🔹Có Lifting State Up, render danh sách với key đúng, có ít nhất 1 Derived State

#### 🔹Có ít nhất 1 useEffect với Cleanup đúng (Timer, Event Listener hoặc gọi API)

#### 🔹Có ít nhất 1 Form dùng Controlled Component với validate cơ bản, và ít nhất 1 Custom Hook tự viết

### ✨ Tiêu chí chấm điểm

#### 🔹Chức năng hoạt động đúng yêu cầu (50%)

#### 🔹Chất lượng code: tách Component/Custom Hook hợp lý, TypeScript đúng, không mutate State (30%)

#### 🔹Giao diện rõ ràng, có xử lý trạng thái rỗng/đang tải (20%)

---

## ⭐ Bài 8: React Router - Điều Hướng & Multi-Page App

> 🎯 **Mục tiêu:** Cấu hình được React Router cho ứng dụng nhiều trang có Layout chung, điều hướng bằng Link/useNavigate, đọc được tham số từ URL (useParams, useSearchParams) và bảo vệ được các trang yêu cầu đăng nhập.
>
> 🔁 **Ôn tập đầu buổi:** props.children/Composition, Custom Hook useLocalStorage, Conditional Rendering

### ✨ Single Page Application (SPA) là gì và vai trò của Router

#### 🔹SPA là gì - chỉ load 1 lần HTML, JavaScript thay đổi nội dung mà không reload trang

#### 🔹Vấn đề nếu không có Router - làm sao thay đổi "trang" mà URL vẫn đúng

#### 🔹Cài đặt thư viện react-router (từ v7, react-router-dom đã được gộp vào react-router)

### ✨ Cài đặt và cấu hình React Router (createBrowserRouter)

#### 🔹Cú pháp createBrowserRouter() định nghĩa danh sách route

#### 🔹Bọc ứng dụng bằng RouterProvider tại file main.tsx

#### 🔹Cấu trúc 1 route object cơ bản: path và element

### ✨ Định nghĩa Routes, Nested Routes và trang 404

#### 🔹Tạo nhiều Route cho các trang khác nhau (Home, About, Contact)

#### 🔹Nested Routes - Route lồng nhau dùng chung Layout (Outlet component)

#### 🔹Cấu hình Route bắt lỗi 404 khi không khớp path nào (path: "*")

### ✨ Component Link/NavLink và điều hướng bằng code với useNavigate

#### 🔹Thẻ Link thay cho thẻ `<a>` - điều hướng không reload trang

#### 🔹NavLink - tự động thêm class active khi đang ở đúng route

#### 🔹useNavigate - điều hướng bằng code (ví dụ sau khi submit form thành công)

### ✨ Lấy tham số từ URL với useParams

#### 🔹Định nghĩa Dynamic Route với cú pháp path có dấu hai chấm (/product/:id)

#### 🔹Dùng useParams() để đọc giá trị tham số động - lưu ý giá trị luôn là `string | undefined`, cần chuyển kiểu khi dùng

#### 🔹Ứng dụng thực tế: trang chi tiết sản phẩm dựa theo ID trên URL

### ✨ Query String với useSearchParams

#### 🔹Query String là gì (`/products?q=ao&sort=price&page=2`) - khác gì với tham số động của useParams

#### 🔹Đọc và cập nhật Query String bằng useSearchParams()

#### 🔹Ứng dụng: tìm kiếm/lọc/phân trang giữ nguyên trạng thái khi reload trang hoặc chia sẻ link - so sánh với lưu bằng useState

### ✨ Bảo vệ Route (Protected Route) - ví dụ trang yêu cầu đăng nhập

#### 🔹Khái niệm Protected Route - chặn truy cập nếu chưa đăng nhập

#### 🔹Lưu trạng thái đăng nhập tạm thời bằng useLocalStorage (Bài 7) - sẽ chuyển sang Zustand ở Bài 9

#### 🔹Viết Component bọc (Wrapper) kiểm tra điều kiện rồi Navigate hoặc render Outlet

#### 🔹Áp dụng Protected Route cho các Route cần bảo vệ trong cấu hình Router

### ✨ Thực hành: Mini Shop nhiều trang

#### 🔹Layout chung có thanh điều hướng NavLink và trang 404

#### 🔹Trang danh sách sản phẩm (dữ liệu tĩnh) tìm kiếm/lọc qua useSearchParams, trang chi tiết /products/:id

#### 🔹Trang Login và trang Dashboard được bảo vệ bằng Protected Route

### 📝 Homework

- Mở rộng Mini Shop: thêm phân trang bằng Query String, trang chi tiết có nút "Quay lại" giữ nguyên bộ lọc
- Chuyển hướng về đúng trang người dùng định vào sau khi đăng nhập thành công

---

## ⭐ Bài 9: Quản Lý Global State Với Zustand

> 🎯 **Mục tiêu:** Giải thích được vấn đề Prop Drilling, sử dụng được Context API và Zustand (có kiểu TypeScript) để quản lý Global State, áp dụng được Selector/persist, và biết khi nào cần tối ưu re-render bằng memo/useMemo/useCallback.
>
> 🔁 **Ôn tập đầu buổi:** Lifting State Up (Bài 5), Protected Route (Bài 8), Custom Hook

### ✨ Vấn đề Prop Drilling - demo trực quan bằng cây Component 3-4 cấp truyền Props xuyên suốt

#### 🔹Ví dụ thực tế: Lifting State Up quá cao - truyền thông tin user từ App xuống Component cháu chắt qua 3-4 tầng Props

#### 🔹Vì sao Prop Drilling gây khó bảo trì và dễ lỗi khi dự án lớn dần

### ✨ Giải pháp "vanilla" của React - dùng Context API thuần để giải quyết Prop Drilling

#### 🔹Tạo Context có kiểu bằng `createContext<AuthContextType | null>(null)` và bọc cây Component bằng Provider

#### 🔹Đọc giá trị Context ở Component con bất kỳ bằng useContext(), đóng gói thành Custom Hook `useAuth()` có kiểm tra null

### ✨ Hạn chế của Context thuần và so sánh với Zustand

#### 🔹Vấn đề re-render thừa - mọi Component dùng Context đều render lại dù chỉ cần 1 phần dữ liệu

#### 🔹Code trở nên cồng kềnh khi cần thêm nhiều loại State khác nhau hoặc logic cập nhật phức tạp

#### 🔹Context phù hợp cho dữ liệu ít thay đổi (theme, ngôn ngữ), Zustand phù hợp cho State thay đổi liên tục - bảng so sánh hiệu năng, độ phức tạp code, khả năng mở rộng

### ✨ Cài đặt và tạo Store đầu tiên với Zustand

#### 🔹Cài đặt thư viện zustand qua pnpm

#### 🔹Tạo Store có kiểu bằng `create<CartState>()(...)` - định nghĩa State và Action trong cùng 1 nơi

#### 🔹Cấu trúc file store chuẩn để dễ maintain khi dự án lớn lên

### ✨ Đọc và cập nhật State trong Component với useStore

#### 🔹Gọi Hook Store trực tiếp trong Component để lấy State cần dùng

#### 🔹Selector Pattern - chỉ lấy đúng phần State cần thiết để tránh re-render thừa

#### 🔹Lỗi thường gặp: Selector trả về object/array mới mỗi lần gây re-render liên tục - cách xử lý bằng useShallow

#### 🔹Gọi Action từ Store để cập nhật State, không cần Provider bọc ngoài như Context

### ✨ Middleware phổ biến: persist (lưu State vào localStorage)

#### 🔹Middleware là gì trong Zustand - cách "bọc thêm" hành vi cho Store

#### 🔹Cấu hình persist() để tự động lưu và khôi phục State từ localStorage

#### 🔹Ứng dụng thực tế: giữ giỏ hàng/thông tin đăng nhập sau khi reload trang

### ✨ Tối ưu re-render với React.memo, useMemo, useCallback

#### 🔹Component cha re-render kéo theo toàn bộ Component con re-render - quan sát bằng React DevTools Profiler

#### 🔹React.memo - bỏ qua re-render khi Props không đổi; useMemo - ghi nhớ kết quả tính toán nặng; useCallback - giữ nguyên tham chiếu function truyền xuống con

#### 🔹Nguyên tắc: đo trước, tối ưu sau - React Compiler (React 19) có thể tự động memo, nhưng vẫn cần hiểu để đọc code dự án cũ và trả lời phỏng vấn

### ✨ Thực hành: Giỏ hàng (Shopping Cart) dùng Zustand

#### 🔹Thiết kế Store cho giỏ hàng: State (danh sách sản phẩm) và Action (thêm/xóa/cập nhật số lượng)

#### 🔹Xây dựng Component hiển thị danh sách sản phẩm và nút "Thêm vào giỏ", Component Giỏ hàng đọc State từ Store và tính tổng tiền

#### 🔹Chuyển trạng thái đăng nhập của Mini Shop (Bài 8) từ useLocalStorage sang Auth Store dùng persist

### 📝 Homework

- Thêm tính năng Danh sách yêu thích (Wishlist) dùng Store riêng có persist, hiển thị số lượng trên Header
- Thêm chế độ Dark/Light theme bằng Context API và giải thích vì sao chọn Context thay vì Zustand cho trường hợp này

---

## ⭐ Bài 10: Data Fetching Cơ Bản (Fetch API & Axios)

> 🎯 **Mục tiêu:** Gọi được API bằng Fetch và Axios có khai báo kiểu cho dữ liệu trả về, tổ chức được Axios Instance với Interceptor, xử lý đầy đủ trạng thái Loading/Error/Success và khắc phục được Race Condition.
>
> 🔁 **Ôn tập đầu buổi:** Async/Await + try/catch, useEffect + Cleanup, `type` cho dữ liệu

### ✨ Gọi API với Fetch - cú pháp và xử lý lỗi

#### 🔹API dùng xuyên suốt Bài 10-11: DummyJSON cho dữ liệu đọc (GET), json-server hoặc MockAPI cho CRUD (DummyJSON chỉ giả lập thêm/sửa/xóa, không lưu thật)

#### 🔹Cú pháp cơ bản fetch(url).then().catch() và cách dùng với Async/Await

#### 🔹Chuyển response về JSON bằng response.json() và gắn kiểu cho kết quả (response.json() trả về `any`)

#### 🔹Xử lý lỗi HTTP (fetch không tự throw lỗi khi status 404/500 - cách kiểm tra response.ok)

### ✨ Cài đặt và sử dụng Axios - vì sao Axios tiện hơn Fetch

#### 🔹Cài đặt axios qua pnpm và cú pháp gọi GET/POST cơ bản, khai báo kiểu với `axios.get<Product[]>()`

#### 🔹So sánh Axios và Fetch: tự động parse JSON, tự throw lỗi khi status lỗi, cú pháp ngắn gọn hơn

#### 🔹Truyền params, headers và body trong request bằng Axios

### ✨ Tạo Axios Instance với base URL và Interceptor

#### 🔹Tạo instance riêng bằng axios.create() với baseURL đọc từ biến môi trường `import.meta.env.VITE_API_URL`

#### 🔹Request Interceptor - tự động gắn token xác thực vào mọi request

#### 🔹Response Interceptor - xử lý lỗi tập trung (ví dụ tự động logout khi gặp lỗi 401)

### ✨ Xử lý trạng thái Loading, Error, Success thủ công

#### 🔹Khai báo 3 State riêng: loading, error, data để phản ánh từng giai đoạn gọi API (hoặc 1 State status dạng Union Type)

#### 🔹Hiển thị Spinner/Skeleton khi loading, thông báo lỗi khi error, dữ liệu khi success

#### 🔹Cấu trúc code chuẩn: try/catch/finally kết hợp cập nhật 3 State trên

### ✨ Gọi API bên trong useEffect - lỗi race condition thường gặp

#### 🔹Vì sao cần gọi API trong useEffect thay vì trực tiếp trong thân Component

#### 🔹Race Condition là gì - dữ liệu trả về không đúng thứ tự khi params đổi liên tục (ví dụ gõ tìm kiếm nhanh)

#### 🔹Cách khắc phục cơ bản bằng biến cờ (flag) hoặc AbortController trong Cleanup Function

### ✨ Thực hành: Trang danh sách sản phẩm từ API

#### 🔹Tạo Axios Instance và hàm gọi API có kiểu cho danh sách sản phẩm (DummyJSON)

#### 🔹Hiển thị Skeleton khi tải, thông báo lỗi kèm nút "Thử lại"

#### 🔹Tìm kiếm theo từ khóa (kết hợp useDebounce ở Bài 7 và AbortController) và phân trang

### 📝 Homework

- Tích hợp API vào Mini Shop (Bài 8-9): danh sách sản phẩm, trang chi tiết theo ID, lọc theo danh mục
- Đóng gói logic gọi API thành Custom Hook `useFetch<T>` và đánh giá các hạn chế của nó (chuẩn bị cho Bài 11)

---

## ⭐ Bài 11: Data Fetching Nâng Cao Với TanStack Query

> 🎯 **Mục tiêu:** Giải thích được sự khác nhau giữa Server State và Client State, sử dụng được useQuery/useMutation để đọc và ghi dữ liệu, và tự động cập nhật dữ liệu sau khi thay đổi bằng Invalidate Query.
>
> 🔁 **Ôn tập đầu buổi:** Loading/Error/Success thủ công, Axios Instance, Custom Hook useFetch (homework Bài 10)

### ✨ Vấn đề của cách fetch thủ công - vì sao cần Server State Library

#### 🔹Những gì phải tự viết lặp lại nhiều lần: loading/error state, cache, refetch, retry khi lỗi

#### 🔹Khái niệm Server State khác với Client State như thế nào (dữ liệu không thuộc sở hữu hoàn toàn của Client)

### ✨ Cài đặt TanStack Query và cấu hình QueryClientProvider

#### 🔹Cài đặt @tanstack/react-query qua pnpm

#### 🔹Khởi tạo QueryClient và bọc ứng dụng bằng QueryClientProvider tại main.tsx

#### 🔹Cài đặt React Query Devtools để quan sát trạng thái Cache trực quan

### ✨ useQuery - fetch dữ liệu với Cache tự động và Query Key

#### 🔹Cú pháp cơ bản useQuery({queryKey, queryFn}) - kiểu của data tự suy ra từ queryFn

#### 🔹Query Key là gì - vai trò định danh và cache dữ liệu theo key

#### 🔹Nhận về sẵn các trạng thái isPending, isError, data mà không cần tự khai báo State - phân biệt isPending và isLoading ở v5

#### 🔹Đóng gói useQuery thành Custom Hook (`useProducts`, `useProduct(id)`) để tái sử dụng

### ✨ useMutation - xử lý POST/PUT/DELETE dữ liệu

#### 🔹Cú pháp cơ bản useMutation({mutationFn}) và gọi bằng hàm mutate()

#### 🔹Xử lý các callback onSuccess, onError, onSettled sau khi Mutation hoàn tất

#### 🔹Hiển thị trạng thái isPending khi đang xử lý Mutation (ví dụ disable nút Submit)

### ✨ Invalidate Query - tự động cập nhật dữ liệu sau khi Mutation

#### 🔹Vấn đề: dữ liệu cũ trong Cache không tự cập nhật sau khi thêm/sửa/xóa

#### 🔹Dùng queryClient.invalidateQueries() để báo Cache đó đã "cũ", cần fetch lại

#### 🔹Ứng dụng thực tế: danh sách Todo tự cập nhật ngay sau khi thêm Todo mới qua useMutation

### ✨ So sánh thực tế: Trước và sau khi dùng TanStack Query

#### 🔹Đối chiếu số dòng code và độ phức tạp giữa cách fetch thủ công (Bài 10) và TanStack Query

#### 🔹Demo tính năng tự động refetch khi quay lại tab trình duyệt (refetchOnWindowFocus)

#### 🔹Tổng kết khi nào nên dùng TanStack Query, khi nào fetch thủ công vẫn đủ dùng

### ✨ Thực hành: Ứng dụng CRUD với json-server

#### 🔹Dựng json-server (hoặc MockAPI) làm backend giả có lưu dữ liệu thật

#### 🔹Hiển thị danh sách bằng useQuery, thêm/sửa/xóa bằng useMutation + invalidateQueries

#### 🔹Disable nút và hiển thị trạng thái đang xử lý trong lúc Mutation chạy

### 📝 Homework

- Chuyển toàn bộ phần gọi API của Mini Shop sang TanStack Query
- Thêm trang quản lý sản phẩm (Admin) có CRUD đầy đủ với json-server/MockAPI

---

## ⭐ Bài 12: Deploy Ứng Dụng React Với Vercel

> 🎯 **Mục tiêu:** Chuẩn bị và build được dự án cho production (không lỗi TypeScript, biến môi trường đúng), deploy được lên Vercel với React Router hoạt động đúng, và hiểu được quy trình tự động deploy khi push code.
>
> 🔁 **Ôn tập đầu buổi:** React Router, Axios Instance với biến môi trường, quy trình git cơ bản

### ✨ Chuẩn bị dự án trước khi Deploy (build production, kiểm tra lỗi, biến môi trường .env)

#### 🔹Rà soát console.log, code test còn sót lại trước khi deploy

#### 🔹Tạo file .env và quy tắc đặt tên biến môi trường với Vite (tiền tố VITE_), khai báo kiểu cho biến môi trường trong vite-env.d.ts

#### 🔹Kiểm tra .gitignore đã loại trừ file .env và node_modules chưa

### ✨ Build ứng dụng React với Vite (pnpm run build) và kiểm tra kết quả với pnpm run preview

#### 🔹Chạy pnpm run build và giải thích thư mục dist được tạo ra

#### 🔹Lệnh build chạy kiểm tra TypeScript (tsc -b) trước - lỗi kiểu dữ liệu sẽ làm build thất bại, cần sửa hết trước khi deploy

#### 🔹Dùng pnpm run preview để xem thử bản build trước khi đưa lên Vercel

#### 🔹Kiểm tra dung lượng file build và các cảnh báo tối ưu (nếu có) từ Vite

### ✨ Code Splitting - giảm dung lượng tải lần đầu

#### 🔹Vấn đề: toàn bộ ứng dụng bị gộp vào 1 file JavaScript lớn

#### 🔹Dùng React.lazy + Suspense để tách code theo từng Route, so sánh dung lượng trước và sau

### ✨ Đưa dự án lên GitHub - quy trình chuẩn (commit, push, .gitignore)

#### 🔹Khởi tạo Git repository (git init) nếu dự án chưa có

#### 🔹Quy trình git add, git commit, git push lên GitHub Repository

#### 🔹Viết commit message rõ ràng theo quy ước cơ bản

### ✨ Giới thiệu Vercel - đăng ký tài khoản và kết nối với GitHub Repository

#### 🔹Đăng ký tài khoản Vercel bằng GitHub (Sign in with GitHub)

#### 🔹Import Repository từ GitHub vào Vercel Dashboard

#### 🔹Vercel tự động nhận diện dự án Vite/React và đề xuất cấu hình mặc định

### ✨ Deploy dự án lên Vercel - cấu hình Build Command & Output Directory

#### 🔹Kiểm tra và chỉnh Build Command (pnpm run build) nếu Vercel nhận diện sai

#### 🔹Kiểm tra Output Directory đúng là "dist" đối với dự án Vite

#### 🔹Theo dõi quá trình Deploy qua Log trên Vercel Dashboard và nhận link demo

### ✨ Xử lý lỗi thường gặp khi Deploy React Router (404 khi refresh trang - cấu hình rewrites)

#### 🔹Nguyên nhân lỗi 404 - Vercel tìm file vật lý theo đường dẫn URL thay vì để React Router xử lý

#### 🔹Tạo file vercel.json với cấu hình rewrites trỏ mọi route về index.html

#### 🔹Deploy lại và kiểm tra refresh trang ở route con không còn lỗi 404

### ✨ Cấu hình biến môi trường (Environment Variables) trên Vercel cho các API Key

#### 🔹Vào Project Settings > Environment Variables trên Vercel Dashboard để khai báo biến

#### 🔹Phân biệt môi trường Production/Preview/Development khi khai báo biến

#### 🔹Redeploy lại dự án để biến môi trường mới có hiệu lực

### ✨ Custom Domain và tự động Deploy khi Push code mới (CI/CD cơ bản với Vercel)

#### 🔹Gắn Custom Domain riêng cho dự án (nếu học viên có domain sẵn)

#### 🔹Cơ chế tự động Deploy mỗi khi Push code mới lên nhánh chính (Continuous Deployment)

#### 🔹Preview Deployment tự động cho mỗi Pull Request - ứng dụng khi làm việc nhóm

### ✨ Tổng kết khóa học, giao đề Capstone Project và lộ trình học tiếp

#### 🔹Ôn lại nhanh các kỹ năng đã học qua 12 buổi theo dòng chảy 1 dự án thực tế hoàn chỉnh

#### 🔹Checklist Best Practices: cấu trúc thư mục, tách Component/Hook, quản lý State hợp lý, TypeScript không dùng `any`

#### 🔹Công bố đề Capstone Project (làm tại nhà), tiêu chí chấm điểm và deadline nộp bài

#### 🔹Lộ trình học tiếp: Next.js, react-hook-form + zod, testing với Vitest + React Testing Library, các tính năng mới của React 19 (Actions, useActionState)

### 📝 Homework

- Deploy Mini Shop lên Vercel, cấu hình biến môi trường và rewrites, nộp link demo + link GitHub
- Bắt đầu Capstone Project (xem đề bên dưới)

---

## 🏁 Capstone Project Cuối Khóa (giao đề cuối Bài 12, làm tại nhà)

> Tổng hợp toàn bộ khóa học, bắt buộc gọi API thực tế và deploy lên Vercel. Học viên chọn 1 trong 3 đề.

### ✨ Đề tài gợi ý

#### 🔹E-commerce mini - danh sách/chi tiết sản phẩm từ API, tìm kiếm/lọc/phân trang qua URL, giỏ hàng persist, trang thanh toán yêu cầu đăng nhập

#### 🔹Task Manager (Kanban) - quản lý dự án và công việc theo cột trạng thái, CRUD đầy đủ với json-server/MockAPI, lọc theo người phụ trách/hạn chót

#### 🔹Movie Explorer - tra cứu phim từ TMDB API, trang chi tiết phim, danh sách yêu thích persist, lịch sử tìm kiếm

### ✨ Yêu cầu tối thiểu

#### 🔹Tối thiểu 4 trang với React Router (có Nested Route, Dynamic Route, Protected Route, trang 404)

#### 🔹Gọi API bằng TanStack Query (có ít nhất 1 useMutation), Global State bằng Zustand

#### 🔹Có ít nhất 1 Form có validate và 1 Custom Hook tự viết

#### 🔹TypeScript cho toàn bộ dự án, không dùng `any`; deploy lên Vercel, có README hướng dẫn chạy dự án

### ✨ Tiêu chí chấm điểm (Rubric)

#### 🔹Chức năng (40%): đầy đủ yêu cầu tối thiểu, hoạt động ổn định, xử lý trạng thái loading/error/rỗng

#### 🔹Chất lượng code (30%): cấu trúc thư mục rõ ràng, tách Component/Hook hợp lý, TypeScript chặt chẽ, quản lý State đúng chỗ (local/lifted/global/server)

#### 🔹UI/UX (20%): giao diện nhất quán, responsive, phản hồi rõ ràng khi người dùng thao tác

#### 🔹Deploy & tài liệu (10%): link demo hoạt động, không lỗi 404 khi refresh, README đầy đủ, commit history rõ ràng

---

# 🎁 Phần Bonus — Bài Học Mở Rộng (Tự Chọn)

> 📌 **Thông tin chung**
> - 3 bài tự chọn, không bắt buộc, không tính vào điểm Mini Project/Capstone (được khuyến khích áp dụng để cộng điểm tiêu chí UI/UX)
> - Hình thức **tự học**: không dạy trên lớp, không có bài tập trên lớp và homework. Mỗi bài chỉ có 1 file `README.md`, viết theo kiểu hướng dẫn từng bước để học viên tự đọc và làm theo, kết thúc bằng 1 ví dụ tổng hợp có code đầy đủ và danh sách tài liệu tham khảo
> - Thư mục: `02.Lessons-Bonus/`
>
> | Bài | Nên học sau | Lý do |
> |---|---|---|
> | 13 — Tailwind CSS | Bài 7 | Dùng Props/State/Custom Hook; kịp áp dụng vào Mini Project |
> | 14 — StyleX | Bài 9 và Bài 13 | Cần khái niệm theme (Bài 9) và Tailwind để so sánh |
> | 15 — Tối ưu hiệu năng | Bài 12 | Cần Zustand, TanStack Query, build production, `React.lazy` |

---

## ⭐ Bài 13 (Bonus): Tailwind CSS — Style Nhanh Theo Hướng Utility-First

> 🎯 **Mục tiêu:** Cài đặt được Tailwind CSS v4 vào dự án Vite + React, dựng được giao diện responsive bằng utility class, xử lý được trạng thái tương tác và dark mode, và tổ chức được class có điều kiện gọn gàng bằng `clsx` + `tailwind-merge`.
>
> 🔁 **Kiến thức cần nắm trước:** Global CSS và CSS Module, phần giới thiệu Tailwind ở mục 4.4.3 (Bài 2), Props và `children` (Bài 3), Conditional Rendering (Bài 5)

### ✨ Utility-First là gì - vì sao Tailwind phổ biến

#### 🔹So sánh cùng 1 Component viết bằng CSS thuần/CSS Module và bằng utility class

#### 🔹Ưu điểm (không phải đặt tên class, không chuyển qua lại giữa file, CSS build ra nhỏ vì chỉ sinh class được dùng) và nhược điểm (className dài, cần thời gian làm quen)

#### 🔹Khi nào nên và không nên dùng Tailwind

### ✨ Cài đặt Tailwind CSS v4 với Vite

#### 🔹Cài `tailwindcss` + `@tailwindcss/vite`, khai báo plugin trong `vite.config.ts`

#### 🔹Thêm `@import "tailwindcss";` vào file CSS gốc - từ v4 không còn bắt buộc file `tailwind.config.js`

#### 🔹Công cụ hỗ trợ: extension Tailwind CSS IntelliSense, `prettier-plugin-tailwindcss` tự sắp xếp thứ tự class

### ✨ Nhóm utility class cốt lõi

#### 🔹Spacing và kích thước (`p-`, `m-`, `w-`, `h-`, `gap-`) theo thang đo mặc định

#### 🔹Chữ, màu, viền, bóng (`text-`, `font-`, `bg-`, `border-`, `rounded-`, `shadow-`)

#### 🔹Bố cục với Flexbox và Grid (`flex`, `items-center`, `justify-between`, `grid`, `grid-cols-3`)

#### 🔹Giá trị tùy ý (arbitrary values) như `w-[137px]`, `bg-[#1da1f2]` - khi nào nên dùng

### ✨ Responsive Design theo hướng Mobile-First

#### 🔹Tiền tố breakpoint `sm:`, `md:`, `lg:`, `xl:` - class không có tiền tố áp dụng cho màn hình nhỏ nhất

#### 🔹Ví dụ: lưới sản phẩm 1 cột trên điện thoại → 2 cột từ `md` → 4 cột từ `lg`

#### 🔹Lỗi thường gặp: hiểu nhầm `sm:` là "chỉ áp dụng cho màn hình nhỏ"

### ✨ Trạng thái tương tác và Dark Mode

#### 🔹Variant `hover:`, `focus:`, `active:`, `disabled:`

#### 🔹`group` và `peer` - style phần tử con theo trạng thái của phần tử cha hoặc phần tử anh em

#### 🔹Dark mode với `dark:` - mặc định theo cài đặt hệ điều hành; chuyển sang bật/tắt thủ công bằng class `.dark` qua `@custom-variant`, lưu lựa chọn bằng `useLocalStorage` (Bài 7)

### ✨ Tùy biến theme với `@theme`

#### 🔹Khai báo màu thương hiệu, font chữ, breakpoint riêng bằng CSS variable trong `@theme` (cách làm mới của v4)

#### 🔹Dùng class sinh ra từ theme tùy biến (ví dụ `bg-brand-500`)

### ✨ Tổ chức class trong Component React

#### 🔹Class có điều kiện theo Props/State: template literal so với `clsx`

#### 🔹Xung đột class khi Component nhận thêm `className` từ bên ngoài → `tailwind-merge`, viết helper `cn()`

#### 🔹Tách Component tái sử dụng (Button nhiều variant) thay vì lạm dụng `@apply`; giới thiệu `class-variance-authority` (cva) ở mức làm quen

### ✨ Ví dụ tổng hợp: Dựng lại trang danh sách sản phẩm bằng Tailwind (hướng dẫn từng bước)

#### 🔹Chuyển `ProductCard`, `SearchBox`, `CategoryFilter` (homework Bài 5) từ CSS Module sang Tailwind

#### 🔹Lưới sản phẩm responsive, thanh điều hướng có menu thu gọn trên điện thoại (dùng `useToggle` - Bài 7)

#### 🔹Nút chuyển Dark/Light mode, lưu lựa chọn bằng `useLocalStorage`

### 📚 Tài liệu tham khảo

- Tài liệu chính thức Tailwind CSS (Installation with Vite, Core concepts, Theme variables, Dark mode)
- README của `clsx`, `tailwind-merge`, `class-variance-authority`

---

## ⭐ Bài 14 (Bonus): StyleX — CSS-in-JS Biên Dịch Sẵn Của Meta

> 🎯 **Mục tiêu:** Giải thích được sự khác nhau giữa CSS-in-JS chạy lúc runtime và biên dịch lúc build, viết được style type-safe bằng `stylex.create`/`stylex.props`, xây dựng được Component cho phép ghi đè style có giới hạn qua Props, tạo được theme bằng `defineVars`/`createTheme`, và chọn được công cụ style phù hợp cho từng loại dự án.
>
> 🔁 **Kiến thức cần nắm trước:** CSS Module (Bài 2), Props và Composition (Bài 3), dark mode với Context (homework Bài 9), Tailwind (Bài 13)

### ✨ CSS-in-JS là gì - runtime so với compile-time

#### 🔹Vấn đề của CSS toàn cục, CSS Module giải quyết được 1 phần; ý tưởng CSS-in-JS: viết style bằng object ngay cạnh Component

#### 🔹Thư viện runtime (styled-components, Emotion) chèn style lúc chạy nên tốn hiệu năng; StyleX biên dịch sẵn thành atomic CSS lúc build

#### 🔹StyleX do Meta phát triển, dùng cho Facebook/Instagram/WhatsApp bản web; điểm mạnh: type-safe, thứ tự áp dụng style dự đoán được, file CSS nhỏ

### ✨ Cài đặt StyleX vào dự án Vite

#### 🔹Cài `@stylexjs/stylex` và plugin build chính thức cho Vite, cấu hình trong `vite.config.ts`

#### 🔹Lưu ý: cách tích hợp với Vite thay đổi khá nhanh theo phiên bản - luôn đối chiếu tài liệu chính thức tại thời điểm dạy

#### 🔹ESLint plugin `@stylexjs/eslint-plugin` bắt lỗi viết style sai ngay khi gõ

### ✨ Viết style cơ bản với `stylex.create` và `stylex.props`

#### 🔹`stylex.create` định nghĩa style, `stylex.props` gắn vào JSX bằng cú pháp spread

#### 🔹Quy tắc quan trọng: style phải phân tích tĩnh được lúc build (không tạo key động, không lấy giá trị từ biến ở file thường)

#### 🔹So sánh cùng 1 Card viết bằng CSS Module (Bài 2), Tailwind (Bài 13) và StyleX

### ✨ Pseudo-class, Media Query và style động

#### 🔹Pseudo-class và media query viết lồng bên trong giá trị của property (`default`, `':hover'`, `'@media ...'`)

#### 🔹Style có điều kiện: `stylex.props(styles.base, isActive && styles.active)` - style đứng sau luôn thắng

#### 🔹Style động theo Props/State bằng hàm trong `stylex.create`

### ✨ Tái sử dụng và ghi đè style qua Props

#### 🔹Component nhận thêm style từ bên ngoài qua prop kiểu `StyleXStyles`

#### 🔹Giới hạn những property được phép ghi đè bằng `StyleXStyles<{...}>` - TypeScript báo lỗi nếu truyền sai

#### 🔹So sánh với cách truyền `className` + `cn()` ở Bài 13

### ✨ Biến CSS và Theme với `defineVars` / `createTheme`

#### 🔹`stylex.defineVars` khai báo design token (màu, khoảng cách, bo góc) - bắt buộc đặt trong file đuôi `.stylex.ts`

#### 🔹`stylex.createTheme` tạo theme sáng/tối, áp dụng cho 1 nhánh cây Component

#### 🔹Chuyển theme bằng State (lưu với `useLocalStorage` - Bài 7); tạo animation với `stylex.keyframes`

### ✨ So sánh CSS Module, Tailwind và StyleX - nên chọn công cụ nào

#### 🔹Bảng so sánh: cách viết, type-safety, kích thước CSS, hệ sinh thái/độ phổ biến, độ khó khi học

#### 🔹Khi nào chọn StyleX (dự án lớn, design system dùng chung nhiều team) và khi nào Tailwind là đủ

### ✨ Ví dụ tổng hợp: Xây dựng mini design system với StyleX (hướng dẫn từng bước)

#### 🔹File `tokens.stylex.ts` chứa màu, khoảng cách, bo góc

#### 🔹Component `Button` (variant + size) cho phép ghi đè style có giới hạn, Component `Card`

#### 🔹Theme sáng/tối bằng `createTheme` và nút chuyển theme; minh họa thêm theme thứ 3 mà không cần sửa Component nào

### 📚 Tài liệu tham khảo

- Tài liệu chính thức StyleX (Installation, Defining styles, Using styles, Theming, Static types)
- Hướng dẫn tích hợp StyleX với Vite trong tài liệu chính thức (kiểm tra phiên bản mới nhất khi đọc)

---

## ⭐ Bài 15 (Bonus): Tối Ưu Hiệu Năng Ứng Dụng React

> 🎯 **Mục tiêu:** Đo được hiệu năng bằng công cụ (Lighthouse, Core Web Vitals, React DevTools Profiler) trước khi tối ưu, tìm và xử lý được các nguyên nhân re-render thừa phổ biến, áp dụng được `useTransition`/`useDeferredValue` và virtualization cho giao diện nặng, tối ưu được tốc độ tải trang (bundle, ảnh, dữ liệu), và bật được React Compiler.
>
> 🔁 **Kiến thức cần nắm trước:** `memo`/`useMemo`/`useCallback` và Profiler (Bài 9), Selector Zustand + `useShallow` (Bài 9), cache của TanStack Query (Bài 11), `React.lazy` + build production (Bài 12)

### ✨ Đo trước, tối ưu sau - hiệu năng nghĩa là gì với người dùng

#### 🔹Hai kiểu "chậm": tải trang chậm và tương tác bị giật

#### 🔹Core Web Vitals giải thích đơn giản: LCP (tải nội dung chính), INP (phản hồi khi thao tác), CLS (bố cục bị nhảy)

#### 🔹Đo bằng Lighthouse trên bản build production (`pnpm run preview` - Bài 12), không đo trên môi trường dev

### ✨ Tìm re-render thừa với React DevTools Profiler

#### 🔹Ghi phiên Profiler, đọc biểu đồ Flamegraph/Ranked, bật "Record why each component rendered"

#### 🔹Nguyên nhân phổ biến: State đặt quá cao, object/function tạo mới mỗi lần render, `value` của Context thay đổi

#### 🔹Tối ưu không cần memo: đẩy State xuống thấp (move state down), truyền Component qua `children` (Bài 3)

### ✨ Memoization đúng cách và React Compiler

#### 🔹Ôn nhanh `memo`/`useMemo`/`useCallback` (Bài 9) - các trường hợp dùng sai khiến memo không có tác dụng

#### 🔹Tách Context theo tần suất thay đổi (context splitting); Selector Zustand + `useShallow`

#### 🔹React Compiler: cài `babel-plugin-react-compiler` vào Vite để tự động memo, kiểm tra kết quả trong React DevTools, các quy tắc code cần tuân thủ để Compiler hoạt động

### ✨ Giữ giao diện mượt với Concurrent Rendering

#### 🔹`useTransition` - đánh dấu cập nhật không khẩn cấp (ví dụ lọc danh sách lớn) để ô input không bị giật

#### 🔹`useDeferredValue` - hiển thị giá trị "trễ" cho phần render nặng; so sánh với `useDebounce` (Bài 7)

#### 🔹Dùng `isPending` để hiển thị trạng thái đang xử lý

### ✨ Render danh sách lớn với Virtualization

#### 🔹Vì sao render hàng nghìn phần tử vẫn chậm dù đã có Virtual DOM (Bài 5): số lượng node Real DOM quá lớn

#### 🔹Ý tưởng windowing: chỉ render những phần tử đang nằm trong vùng nhìn thấy

#### 🔹Dùng `useVirtualizer` của `@tanstack/react-virtual` ở mức cơ bản

### ✨ Tối ưu tải trang: bundle, ảnh và dữ liệu

#### 🔹Phân tích bundle bằng `rollup-plugin-visualizer`, tìm thư viện nặng, import đúng cách để tree-shaking hoạt động

#### 🔹Code splitting nâng cao: tải trước (preload) route khi người dùng rê chuột lên `Link`; ôn `React.lazy` (Bài 12)

#### 🔹Ảnh: `loading="lazy"`, khai báo sẵn `width`/`height` để tránh CLS, định dạng WebP/AVIF

#### 🔹Dữ liệu: `staleTime` và `prefetchQuery` của TanStack Query (Bài 11) để tránh gọi lại API không cần thiết

### ✨ Ví dụ tổng hợp: Tối ưu một trang danh sách chậm (hướng dẫn từng bước)

#### 🔹Điểm xuất phát: code "trước khi tối ưu" in sẵn trong bài (danh sách 10.000 sản phẩm, State đặt quá cao, Context đặt sai chỗ, bundle lớn)

#### 🔹Đo Lighthouse + Profiler, ghi lại số liệu ban đầu

#### 🔹Áp dụng lần lượt: move state down, virtualization, `useDeferredValue`, lazy route, prefetch - mỗi bước có code "sau khi sửa" và số liệu đo lại để so sánh

#### 🔹Gợi ý tự áp dụng quy trình đo → tối ưu → đo lại cho Capstone Project của chính mình

### 📚 Tài liệu tham khảo

- react.dev: React Compiler, `useTransition`, `useDeferredValue`, React DevTools Profiler
- web.dev: Core Web Vitals (LCP, INP, CLS), Lighthouse
- Tài liệu TanStack Virtual và TanStack Query (prefetching, `staleTime`)
