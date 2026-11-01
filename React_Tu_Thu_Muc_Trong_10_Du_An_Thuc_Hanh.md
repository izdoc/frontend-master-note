# Học React từ thư mục trống — nền tảng và 10 dự án tự gõ code

**Dành cho người bắt đầu • Tiếng Việt • JavaScript + React • Tự tạo toàn bộ file**

Tác giả biên soạn: Jenni, dành cho anh Tim. Ngày đối chiếu tài liệu: 01/11/2026.

## Mục lục

1. Phần I — Hiểu React và chuẩn bị JavaScript.
2. Phần II — Tự tạo dự án từ thư mục trống.
3. Phần III — Kiến thức React cốt lõi và ví dụ nhỏ.
4. Phần IV — 10 dự án hoàn chỉnh, từ dễ đến khó.
5. Phần V — Kiểm thử, sửa lỗi, tổ chức code và bước sang Next.js.
6. Phần VI — Lộ trình học, câu hỏi tự kiểm tra và tài liệu gốc.

## Cách dùng tài liệu

Anh hãy đọc phần I–III, sau đó làm từng dự án ở phần IV. Mỗi dự án là một ứng dụng độc lập. Mục tiêu không phải chép hết code thật nhanh mà là hiểu được: dữ liệu ở đâu, ai thay đổi dữ liệu, và vì sao giao diện thay đổi.

Mỗi buổi nên có ba lượt:

1. **Gõ theo:** tạo folder, tạo file, gõ code đúng đường dẫn; chạy khi hoàn thành một mốc.
2. **Giải thích:** đóng tài liệu, nói lại tác dụng của state, props và từng event handler.
3. **Tự làm:** thay đổi một yêu cầu nhỏ mà không nhìn lời giải; kiểm tra cả trường hợp rỗng và sai dữ liệu.

Tài liệu cung cấp **bản hoàn chỉnh tối thiểu chạy được** của cả 10 dự án. Các mục “Bài tập mở rộng” là yêu cầu anh tự làm thêm, không phải tính năng đã có trong mã nguồn. Không có đoạn `...` thay cho mã còn thiếu trong các file dự án.

“Đầy đủ căn bản” ở đây gồm thiết lập môi trường, JavaScript cần thiết, JSX, component, props/state, event, form, danh sách, Effect, Ref, custom Hook, Context/Reducer, HTTP, lưu trữ, build và cách kiểm tra. Không có nghĩa là bao quát toàn bộ API React, hệ thống backend hay vận hành production.

# Phần I — Hiểu React trước khi viết code

## 1. React giải quyết vấn đề gì?

Giả sử một màn hình có danh sách sách, ô tìm kiếm và số lượng sách đã đọc. Nếu viết DOM thủ công, mỗi thao tác thêm/xóa có thể phải cập nhật nhiều chỗ: thêm dòng HTML, đổi tổng số, cập nhật thông báo rỗng. Càng nhiều trạng thái, càng dễ bỏ sót.

Với React, ta mô tả giao diện tương ứng với dữ liệu hiện tại. Khi dữ liệu thay đổi qua cơ chế state, React chạy lại component và cập nhật DOM cần thiết.

```js
// Tư duy: từ dữ liệu hiện tại tính ra nội dung cần hiển thị.
const books = [{ id: 'b1', title: 'Học React', read: false }];
const total = books.length;
const readCount = books.filter(book => book.read).length;
```

**Component** là một phần giao diện được đóng gói bằng hàm JavaScript. Ví dụ: `BookForm`, `BookCard`, `BookList`. React không tự cung cấp database, đăng nhập, phân quyền hay API server. [R1]

## 2. Phân biệt các công cụ

| Thành phần | Vai trò trong khóa học | Ví dụ |
|---|---|---|
| HTML | Điểm vào trang và phần tử root | `index.html` |
| CSS | Màu, bố cục, khoảng cách | `styles.css` |
| JavaScript | Dữ liệu và logic | `map`, `filter`, hàm xử lý |
| JSX | Cú pháp mô tả giao diện trong JS | `<BookCard />` |
| React | Component, state, cập nhật UI | `useState` |
| React DOM | Gắn React vào DOM trình duyệt | `createRoot` |
| Node.js | Chạy công cụ phát triển trên máy | Vite chạy trong Node |
| npm | Cài thư viện và chạy script | `npm install` |
| Vite | Dev server và build, được cấu hình thủ công | `vite.config.js` |
| Next.js | Framework dựa trên React, có routing và khả năng server | Học sau phần căn bản |

Ở đây **không dùng công cụ sinh dự án**: không chạy `create-react-app`, `create-vite`, `npm create vite`, `create-next-app`, hay lấy template sẵn. Anh tự viết package.json và tất cả file nguồn. `npm install`, `npm run dev`, `npm run build` chỉ cài phụ thuộc/chạy công cụ trên các file anh đã viết. Vite có hỗ trợ cài đặt thủ công. [R2]

Trong .NET, anh có thể liên tưởng package.json với phần khai báo dependency và task của một project. Tuy nhiên React không cần file `.sln`, không có Controller bắt buộc, và cấu trúc component không tương đương nguyên xi kiến trúc backend.

## 3. Cài môi trường

Chuẩn bị Node.js 24 LTS, npm đi kèm, VS Code và trình duyệt Chrome/Edge mới. Bộ bài được xây dựng với React 19.3.1, React DOM 19.3.1, Vite 8.3.5, plugin React 6.1.2. Đây là bộ phiên bản cố định cho bài học, không phải chỉ dẫn luôn dùng bản mới nhất.

Sau khi cài Node, đóng rồi mở lại Terminal:

```bash
node --version
npm --version
```

Node in ra `v24.x.x` là phù hợp với môi trường của tài liệu. Nếu Windows không nhận lệnh, kiểm tra PATH và mở Terminal mới. Nếu PowerShell chặn `npm.ps1`, thử `npm.cmd --version` hoặc dùng Command Prompt; không cần tắt bảo mật toàn hệ thống.

## 4. JavaScript tối thiểu — gõ trong Console trình duyệt

Mở F12 → Console và chạy từng nhóm độc lập. Các tên biến trùng nhau có thể gây lỗi khai báo lại; refresh trang hoặc đổi tên biến khi cần.

### 4.1 Biến, object và destructuring

```js
const learner = { name: 'Tim', target: 20 };
const { name, target } = learner;
console.log(`${name} muốn học ${target} buổi`);

let completed = 3;
completed = completed + 1;
console.log(completed); // 4
```

`const` không cho gán lại biến, nhưng không tự làm object bất biến. `learner.name = 'An'` vẫn thay đổi object; trong React state ta cần tránh kiểu sửa trực tiếp đó.

### 4.2 Hàm và arrow function

```js
function double(n) { return n * 2; }
const triple = n => n * 3;
console.log(double(3)); // 6
console.log(triple(3)); // 9

const createBook = title => ({ title, read: false });
console.log(createBook('React căn bản'));
```

Dấu ngoặc quanh object trong arrow function giúp JavaScript hiểu đó là giá trị trả về. Nếu dùng thân hàm `{ ... }`, hãy viết `return` rõ ràng.

### 4.3 map, filter, find, reduce

```js
const lessons = [
  { id: 1, title: 'JSX', minutes: 20, done: true },
  { id: 2, title: 'State', minutes: 30, done: false }
];
console.log(lessons.map(x => x.title)); // ['JSX', 'State']
console.log(lessons.filter(x => !x.done)); // một phần tử State
console.log(lessons.find(x => x.id === 1)); // object JSX
console.log(lessons.reduce((sum, x) => sum + x.minutes, 0)); // 50
```

`map` biến đổi mỗi phần tử; `filter` chọn phần tử; `find` lấy phần tử đầu tiên phù hợp hoặc undefined; `reduce` gom thành một kết quả.

### 4.4 Spread và cập nhật dữ liệu

```js
const original = { title: 'React', read: false };
const changed = { ...original, read: true };
console.log(original.read, changed.read); // false true

const oldList = [{ id: 1, title: 'React' }];
const newList = [...oldList, { id: 2, title: 'JavaScript' }];
console.log(oldList.length, newList.length); // 1 2
```

Spread chỉ sao chép nông. Nếu object chứa object con, phải sao chép cả cấp con muốn sửa: `{ ...user, address: { ...user.address, city: 'HCM' } }`.

### 4.5 Điều kiện và giá trị rỗng

```js
const score = 7;
console.log(score >= 5 ? 'Đạt' : 'Chưa đạt');
const user = null;
console.log(user?.name ?? 'Khách');
console.log(Number('25')); // 25
console.log(Number('')); // 0: phải kiểm tra đầu vào nếu không muốn coi rỗng là 0
```

`?.` dừng truy cập khi bên trái null/undefined. `??` dùng giá trị thay thế chỉ cho null/undefined, khác `||` còn thay cả 0, false và chuỗi rỗng.

### 4.6 Module

Sau này trong project, tạo `src/math.js`:

```js
export function add(a, b) { return a + b; }
export default function multiply(a, b) { return a * b; }
```

Ở file khác:

```js
import multiply, { add } from './math.js';
console.log(add(2, 3), multiply(2, 3));
```

Named export dùng `{ add }` và đúng tên. Default export không có ngoặc nhọn và có thể đổi tên khi import. Đường dẫn `./` là cùng thư mục, `../` là lên một cấp. Phân biệt chữ hoa/thường để code chạy được trên Linux.

### 4.7 Promise, async/await và lỗi

```js
async function demo() {
  try {
    const message = await Promise.resolve('Đã tải xong');
    console.log(message);
  } catch (error) {
    console.error(error.message);
  }
}
demo();
```

`await` chờ Promise trong hàm async mà không khóa toàn bộ giao diện. Dự án 08 dùng kiến thức này để tải JSON.

**Bài tự luyện JS:** tự viết hàm nhận mảng sách, trả sách chưa đọc; hàm tính tổng số phút; hàm đổi trạng thái một phần tử mà không sửa mảng ban đầu. Nếu chưa làm được, nên luyện trước khi qua React.

# Phần II — Dựng dự án từ thư mục trống

## 5. Tạo folder và file bằng tay

Ví dụ bắt đầu dự án `01-profile`:

1. Mở File Explorer, tạo folder `react-practice`, bên trong tạo `01-profile`.
2. Trong VS Code chọn File → Open Folder → chọn `01-profile`.
3. Trong Explorer của VS Code, chọn New File để tạo `package.json`, `index.html`, `vite.config.js`, `.gitignore`.
4. Chọn New Folder tạo `src`. Trong `src`, tạo `main.jsx`, `App.jsx`, `styles.css`.
5. Không tạo `node_modules`, `dist`, `package-lock.json` bằng tay. Đó là sản phẩm của cài đặt/build.

Anh có thể dùng `mkdir` để tạo folder nếu thích; đó là lệnh hệ điều hành, không phải trình sinh dự án. Tài liệu dùng thao tác Explorer để phù hợp cả Windows/macOS/Linux.

Danh sách file cơ bản:

| File | Tác dụng |
|---|---|
| `package.json` | Tên project, phiên bản thư viện, các lệnh chạy |
| `index.html` | Trang HTML và nơi React gắn giao diện |
| `vite.config.js` | Bật plugin React để xử lý JSX/Fast Refresh |
| `.gitignore` | Bỏ qua dependency, build output, cấu hình cục bộ khi dùng Git |
| `src/main.jsx` | Điểm vào JavaScript, gắn App vào root |
| `src/App.jsx` | Component ứng dụng chính |
| `src/styles.css` | CSS của ứng dụng |

## 6. Gõ các file nền tảng

Các file sau dùng cho **từng dự án độc lập**. Ở dự án tiếp theo, tự tạo thư mục mới rồi gõ lại bộ khung; không có bước bí mật cần chạy trình tạo project. Phần IV ghi rõ các file bổ sung của từng bài.


### File `package.json`

```json
{
  "name": "react-manual-lab",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "19.3.1",
    "react-dom": "19.3.1"
  },
  "devDependencies": {
    "vite": "8.3.5",
    "@vitejs/plugin-react": "6.1.2"
  }
}
```

### File `index.html`

```html
<!doctype html>
<html lang="vi">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>React thực hành</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

### File `vite.config.js`

```js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({ plugins: [react()] });
```

### File `.gitignore`

```text
node_modules/
dist/
.env.local
```

### File `src/main.jsx`

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import App from './App.jsx';
import './styles.css';

createRoot(document.getElementById('root')).render(
  <StrictMode><App /></StrictMode>
);
```

### File `src/styles.css`

```css
* { box-sizing: border-box; }
body { margin: 0; background: #f1f5f9; color: #17233b;
  font-family: system-ui, sans-serif; line-height: 1.6; }
main { max-width: 900px; margin: 32px auto; padding: 24px; }
h1 { line-height: 1.2; }
form, article, section, .card { padding: 20px; margin: 16px 0;
  background: white; border: 1px solid #cbd5e1; border-radius: 12px; }
label { display: block; margin: 10px 0; }
input:not([type="checkbox"]), select, textarea { display: block;
  width: 100%; padding: 10px; font: inherit; border: 1px solid #64748b;
  border-radius: 6px; }
textarea { min-height: 140px; }
button { cursor: pointer; margin: 5px 8px 5px 0; padding: 9px 14px;
  border: 0; border-radius: 6px; background: #164e63; color: white; font: inherit; }
button:disabled { cursor: not-allowed; opacity: .5; }
:focus-visible { outline: 3px solid #ea580c; outline-offset: 3px; }
[role="alert"] { color: #a21b13; }
ul { padding-left: 24px; }
li { margin: 10px 0; }
small { color: #475569; }
.row { display: flex; gap: 16px; flex-wrap: wrap; }
.row > * { flex: 1; min-width: 200px; }
.done { text-decoration: line-through; }
.timer { font-size: 3rem; font-variant-numeric: tabular-nums; }
@media(max-width: 600px) { main { margin: 0; padding: 16px; } }
```


## 7. File App đầu tiên

Tự tạo `src/App.jsx` và gõ:

```jsx
export default function App() {
  return <main><h1>Hello React, anh Tim!</h1><p>Em đã chạy từ thư mục trống.</p></main>;
}
```

Sau đó mở Terminal ở đúng folder chứa package.json:

```bash
npm install
npm run dev
```

`npm install` đọc package.json, tải thư viện về node_modules và tạo package-lock.json. Lần đầu cần mạng truy cập npm registry. `npm run dev` chạy script `dev`, tức Vite đã cài cục bộ.

Mở **đúng URL được Terminal in ra**, thường là `http://localhost:5173`. Nếu cổng bận Vite có thể dùng cổng khác. Không mở index.html bằng double-click và không dùng `file://`. Giữ Terminal đang chạy; Ctrl+C để dừng.

**Mốc 1:** nhìn thấy Hello React. Đổi tiêu đề, lưu file, kiểm tra trang cập nhật. Nếu chưa chạy được bước này, chưa chuyển sang CRUD.

**Mốc 2:** thay App.jsx bằng mã dự án 01, tạo file component còn thiếu; trang có thẻ hồ sơ và bộ đếm.

**Mốc 3:** kiểm tra build:

```bash
npm run build
npm run preview
```

Build tạo folder dist. Preview phục vụ bản build để kiểm tra cục bộ, không phải production server để vận hành lâu dài. Nếu sửa code sau build, phải build lại mới thấy thay đổi trong preview.

Giữ package-lock.json trong Git để người khác cài lại bằng `npm ci`. Khi chưa có lockfile, dùng npm install. Không chép node_modules giữa các hệ điều hành.

## 8. Lần theo đường chạy

Trình duyệt nhận index.html, thấy script `/src/main.jsx`, tải module đã được Vite xử lý. main.jsx import App và CSS; createRoot kết nối React với div có id root. Khi React gọi App, hàm trả về JSX mô tả màn hình.

Không phải trình duyệt tự hiểu JSX thô. Công cụ build chuyển JSX thành lời gọi JavaScript. React quản lý phần DOM bên trong root. Không dùng `document.querySelector(...).innerHTML = ...` để thay giao diện mà React đang sở hữu.

# Phần III — React cốt lõi

## 9. JSX và component

Tên component bắt đầu bằng chữ hoa. Thẻ HTML thường viết chữ thường. Một component trả về một cây JSX; dùng Fragment `<>...</>` khi cần gom các phần tử mà không thêm div.

```jsx
function Greeting({ name }) {
  return <><h2>Chào {name}</h2><p className="hint">Cùng học React nhé.</p></>;
}
```

Trong JSX: `className` thay cho class, `htmlFor` nối label với id input, thẻ tự đóng như `<input />`, biểu thức JavaScript nằm trong `{}`. Không đặt `if` trực tiếp bên trong `{}`; dùng toán tử ba ngôi hoặc tính biến trước return.

```jsx
const message = books.length === 0 ? 'Chưa có sách' : `Có ${books.length} sách`;
// Trong return: <p>{message}</p>
```

Một object không render trực tiếp được bằng `{book}`. Hiển thị `{book.title}`; khi debug có thể dùng `<pre>{JSON.stringify(book, null, 2)}</pre>`.

**Bài tập:** tạo Header nhận title, Footer nhận year; gọi cả hai từ App. Thử thay title tại App để nhìn thấy dòng dữ liệu từ cha xuống con.

## 10. Props: dữ liệu cha truyền xuống

```jsx
function BookCard({ title, author = 'Chưa rõ', onSelect }) {
  return <article><h2>{title}</h2><p>{author}</p>
    <button onClick={onSelect}>Chọn</button></article>;
}
```

Trong App có thể dùng:

```jsx
<BookCard title="React căn bản" author="Tim" onSelect={() => alert('Đã chọn')} />
```

Props có thể là string, number, boolean, array, object, function hoặc JSX. Con không tự sửa props; con gọi callback để cha quyết định thay đổi dữ liệu. Default value chỉ áp dụng khi prop undefined hoặc không truyền, không áp dụng cho null.

`children` là một prop đặc biệt về cách viết: `<Panel><p>Nội dung</p></Panel>` sẽ truyền đoạn p vào children. Cách này hữu ích cho khung, card, modal.

## 11. State: bộ nhớ của component

Ví dụ hoàn chỉnh có thể thay vào App.jsx để thử riêng:

```jsx
import { useState } from 'react';
export default function App() {
  const [count, setCount] = useState(0);
  return <main><p>{count}</p>
    <button onClick={() => setCount(n => n + 1)}>Tăng</button>
  </main>;
}
```

`useState(0)` trả về giá trị hiện tại và hàm yêu cầu cập nhật. Biến cục bộ `let count = 0` trong component sẽ được tạo lại khi render, không thay thế được state.

State của mỗi render là một snapshot. Sau `setCount(...)`, biến count trong handler đang chạy vẫn là giá trị của render cũ; setter yêu cầu render tiếp theo, không sửa biến tại chỗ. Khi tính từ giá trị cũ, dùng updater `n => n + 1`. [R3]

```jsx
// Cộng 3 bằng ba cập nhật nối tiếp:
setCount(n => n + 1);
setCount(n => n + 1);
setCount(n => n + 1);
```

Không gọi setter vô điều kiện trong lúc render. Không viết `onClick={setCount(count+1)}` vì nó gọi hàm ngay khi render; viết `onClick={() => setCount(n => n+1)}`.

| Câu hỏi | Props | State |
|---|---|---|
| Đến từ đâu? | Cha truyền xuống | Component sở hữu |
| Component nhận có được sửa trực tiếp? | Không | Không; gọi setter |
| Ví dụ | Tên sách trong BookCard | Nội dung đang gõ trong form |
| Chia sẻ giữa anh em? | Cha truyền props/callback | Đưa state lên cha chung |

## 12. Quy tắc Hooks

Gọi useState, useEffect, useRef và các Hook thông thường ở đầu component hoặc trong custom Hook. Không gọi trong if, for, callback onClick hay sau một nhánh return có điều kiện. Custom Hook đặt tên bắt đầu bằng `use`.

Các instance gọi cùng custom Hook có state riêng. Muốn hai component chia sẻ cùng dữ liệu, đưa state lên cha hoặc Provider; chỉ import cùng Hook không tự tạo bộ nhớ chung.

## 13. Danh sách, key và tính bất biến

```jsx
{books.map(book => <BookCard key={book.id} title={book.title} />)}
```

Key giúp React nhận ra phần tử qua các render. Không tạo key bằng Math.random mỗi render. Với danh sách thêm/xóa/sắp xếp, dùng id ổn định thay cho index. `key` không được truyền vào props như thuộc tính bình thường; cần id ở con thì truyền `id={book.id}`.

Khi thay đổi mảng trong state, tạo mảng mới; khi sửa object trong mảng, tạo object mới tại phần tử đó. [R4]

```jsx
// Thêm
setBooks(old => [...old, newBook]);
// Xóa
setBooks(old => old.filter(book => book.id !== id));
// Sửa
setBooks(old => old.map(book => book.id === id ? { ...book, read: true } : book));
// Sắp xếp bản sao
const sorted = [...books].sort((a, b) => a.title.localeCompare(b.title));
```

Không dùng `books.push(...)`, `books.sort(...)`, `book.read = true` trực tiếp trên state. Trong dự án 10, `.filter(...)` đã tạo mảng mới trước `.sort(...)`, nên không sửa mảng sessions gốc.

## 14. Form có kiểm soát

```jsx
import { useState } from 'react';
export default function App() {
  const [name, setName] = useState('');
  const [error, setError] = useState('');
  function submit(event) {
    event.preventDefault();
    if (!name.trim()) { setError('Anh nhập tên nhé.'); return; }
    setError('');
    alert(`Chào ${name.trim()}`);
  }
  return <main><form onSubmit={submit}>
    <label>Tên<input value={name} onChange={e => setName(e.target.value)} /></label>
    {error && <p role="alert">{error}</p>}
    <button>Gửi</button>
  </form></main>;
}
```

`value` đến từ state, `onChange` cập nhật state. Với checkbox dùng checked và e.target.checked. Input text nên bắt đầu bằng chuỗi rỗng thay vì undefined. Nút trong form mặc định submit; nút Hủy/Sửa phụ phải có type=button nếu không muốn gửi form.

`required`, `min`, `max` hỗ trợ kiểm tra trên trình duyệt. Logic nghiệp vụ vẫn phải kiểm tra riêng, và backend thực tế phải kiểm tra lại. `trim()` bỏ khoảng trắng ở hai đầu, không xóa khoảng trắng giữa các từ.

## 15. Derived state và nâng state lên cha

Đừng lưu đồng thời todos và remaining nếu remaining tính được từ todos:

```jsx
const remaining = todos.filter(todo => !todo.done).length;
```

Tương tự, tổng tiền, số sách đã đọc, kết quả tìm kiếm thường chỉ là giá trị tính từ state gốc. Giữ ít nguồn dữ liệu cần đồng bộ sẽ giảm lỗi. Không cần Effect chỉ để tính tổng hoặc lọc danh sách. [R5]

Nếu BookForm cần thêm sách và BookList cần hiển thị sách, hãy đặt books ở App. App truyền onSave cho form và books cho list. Sự kiện đi từ con lên cha qua callback; dữ liệu mới đi từ cha xuống qua props.

## 16. useEffect: kết nối với hệ thống bên ngoài

Effect phù hợp khi đồng bộ với timer, mạng, sự kiện trình duyệt hoặc localStorage. Nó chạy sau commit; dependency quyết định khi nào cần đồng bộ lại. Không coi Effect là chỗ mặc định chứa mọi logic. [R6]

```jsx
useEffect(() => {
  const id = setInterval(() => console.log('tick'), 1000);
  return () => clearInterval(id);
}, []);
```

Để thử ví dụ, import useEffect rồi đặt đoạn này trong component ở trước return. Khi component bị gỡ, cleanup dọn interval. Khi dependency thay đổi, cleanup cũ chạy trước setup mới. `[]` không có nghĩa “chỉ chạy đúng một lần trong mọi hoàn cảnh”; development StrictMode có vòng setup/cleanup kiểm tra thêm.

| Dependency | Ý nghĩa |
|---|---|
| Không truyền đối số thứ hai | Chạy sau mỗi commit của component |
| `[]` | Không phụ thuộc giá trị reactive của render |
| `[query]` | Đồng bộ lại khi query thay đổi theo so sánh Object.is |

Nếu Effect đọc một giá trị reactive, phải xem nó có cần trong dependency hay không. Đừng bỏ dependency chỉ để che loop. Nếu object/function được tạo lại mỗi render, xem xét chuyển logic vào Effect hoặc thiết kế lại trước khi dùng memoization.

## 17. useRef, useMemo và useCallback

`useRef` giữ một giá trị qua các render mà việc đổi `.current` không làm render lại. Dùng cho DOM reference, timer id hoặc deadline. State dùng cho dữ liệu cần hiện lên UI.

```jsx
import { useRef } from 'react';
export default function App() {
  const inputRef = useRef(null);
  return <main><label>Tên<input ref={inputRef} /></label>
    <button onClick={() => inputRef.current?.focus()}>Đưa con trỏ vào ô tên</button>
  </main>;
}
```

`useMemo` có thể cache kết quả tính toán và `useCallback` có thể cache identity của hàm theo dependency. Đây là công cụ tối ưu, không làm code tự đúng hơn. Với vài chục sách, filter thường đủ; chỉ tối ưu sau khi đo vấn đề. Không bọc mọi hàm bằng useCallback.

## 18. Context và Reducer

Context cho phép cung cấp giá trị tới component sâu bên dưới mà không phải chuyền qua từng tầng. Reducer tập trung quy tắc cập nhật state vào hàm nhận `(state, action)` và trả state mới. Chúng giải quyết hai vấn đề khác nhau nhưng thường dùng cùng nhau.

Ví dụ action: `{ type: 'add', product }`. Reducer phải thuần: không fetch, không random id, không ghi localStorage trong reducer. Tạo id ở event handler trước dispatch. Bài 09 triển khai đủ Provider, Hook, reducer và component tiêu thụ.

## 19. Lưu trữ trong trình duyệt

localStorage lưu chuỗi theo origin: protocol + hostname + port. Phải JSON.stringify khi ghi object và JSON.parse khi đọc. Đổi cổng dev server có thể khiến anh tưởng dữ liệu bị mất vì đó là origin khác.

Hook trong các bài lưu mảng kiểm tra parse và bắt lỗi ghi, nhưng chưa kiểm tra schema của từng phần tử hoặc migrate phiên bản. Khi làm sản phẩm thật, cần validation đầy đủ, migration, giới hạn dung lượng và chiến lược đồng bộ. Nếu tự sửa dữ liệu bằng DevTools thành object con sai cấu trúc, ứng dụng mẫu vẫn có thể lỗi.

Không lưu mật khẩu, bí mật API hay dữ liệu nhạy cảm vào các ví dụ này. localStorage không phải cơ sở dữ liệu server, không chia sẻ giữa các máy, có thể mất khi xóa dữ liệu trình duyệt và không tự đồng bộ state giữa hai tab trong Hook hiện tại.

## 20. Tải dữ liệu và trạng thái giao diện

Một màn hình tải dữ liệu cần xử lý ít nhất: đang tải, lỗi, thành công rỗng, thành công có dữ liệu. Hãy mô tả các trạng thái này trước khi viết fetch. Bài 08 dùng file JSON tự viết để anh học HTTP mà không phụ thuộc dịch vụ bên ngoài.

Với API thật: kiểm tra HTTP status, xác thực schema, xử lý hủy request/response cũ, phân trang khi dữ liệu lớn và để server thực thi quyền truy cập. Frontend ẩn nút không tương đương phân quyền.

# Phần IV — 10 dự án tự tạo và tự gõ

## Quy tắc thực hành chung

Mỗi bài bắt đầu từ folder trống mang tên bài. Tạo lại 6 file nền tảng ở phần II và App.jsx; tạo các folder bổ sung đúng danh sách của bài. Code của Hook dùng lại cũng được in đầy đủ ở mỗi bài để có thể học từng dự án độc lập.

Ở mỗi dự án:

1. Chạy Hello React trước bằng npm install và npm run dev.
2. Tạo file dữ liệu/Hook/component trước, thay App.jsx sau cùng.
3. Đọc giải thích, thực hiện từng ca kiểm tra bằng tay.
4. Chạy npm run build và npm run preview.
5. Chỉ chuyển bài khi anh giải thích được dữ liệu từ đâu đến và thay đổi ở đâu.

CSS là bộ tối thiểu dùng chung để có giao diện dễ đọc trên desktop/mobile; trọng tâm là hành vi, không phải sao chép giao diện thương mại.

| Bài | Dự án | Trọng tâm |
|---|---|---|
| 01 | Thẻ giới thiệu và bộ đếm mục tiêu | Component, JSX, props, children, event và state số. |
| 02 | Quản lý Todo List | Form có kiểm soát, mảng state, CRUD, lọc, localStorage và custom Hook. |
| 03 | Quản lý sách cá nhân | CRUD có form sửa, validation, tìm kiếm, trạng thái đọc và nâng state lên cha. |
| 04 | Sổ thu chi cá nhân | Input số, chuyển kiểu, reduce, lọc theo tháng và định dạng tiền. |
| 05 | Flashcard từ vựng IELTS | Lật thẻ, state phiên học, dữ liệu mẫu, thêm từ và đánh dấu đã nhớ. |
| 06 | Bài kiểm tra từ vựng tiếng Anh | State machine nhỏ, radio input, nộp bài, chấm điểm và reset. |
| 07 | Đồng hồ Pomodoro học tập | useEffect, cleanup, useRef, timer, pause/resume và vòng đời component. |
| 08 | Thư viện bài đọc tiếng Anh | fetch, async/await, loading/error/empty, AbortController và trang chi tiết. |
| 09 | Giỏ hàng cửa hàng sách | useReducer, Context, custom Hook và tách logic nghiệp vụ thuần. |
| 10 | IELTS Study Planner — dự án tổng hợp | Tách component, form nhiều trường, thống kê, filter, localStorage và luồng hoàn thành. |

## Dự án 01 — Thẻ giới thiệu và bộ đếm mục tiêu

**Folder:** `01-profile`

**Kiến thức:** Component, JSX, props, children, event và state số.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung ở phần II; chạy màn hình Hello React trước.
2. Tạo components/ProfileCard.jsx để nhận dữ liệu từ cha.
3. Tạo App.jsx với state số buổi học; truyền JSX vào children.
4. Bấm tăng, giảm, đặt lại và quan sát React cập nhật giao diện.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/components/ProfileCard.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/components/ProfileCard.jsx`

```jsx
export default function ProfileCard({ name, role, children }) {
  return <article><h2>{name}</h2><p>{role}</p>{children}</article>;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import ProfileCard from './components/ProfileCard.jsx';

export default function App() {
  const [sessions, setSessions] = useState(0);
  return <main>
    <h1>Hồ sơ học React</h1>
    <ProfileCard name="Tim" role="Người học React">
      <p aria-live="polite">Đã học {sessions} buổi</p>
      <button onClick={() => setSessions(n => n + 1)}>+ Một buổi</button>
      <button disabled={sessions === 0}
        onClick={() => setSessions(n => Math.max(0, n - 1))}>Giảm</button>
      <button onClick={() => setSessions(0)}>Đặt lại</button>
    </ProfileCard>
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `01-profile`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

App sở hữu sessions. ProfileCard nhận name và role qua props, không sửa chúng. children là nội dung nằm giữa cặp thẻ ProfileCard. Hàm cập nhật nhận n cũ để tính n mới; nút giảm bị khóa khi bằng 0.

### Checklist tự kiểm tra

- [ ] Ban đầu hiển thị Tim và 0 buổi.
- [ ] Bấm + hai lần được 2; giảm về 0 thì không giảm tiếp.
- [ ] Đặt lại đưa số buổi về 0; tải lại cũng về 0 vì chưa lưu dữ liệu.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Bổ sung mục tiêu 20 buổi và thanh progress; tính phần trăm trực tiếp từ sessions.


## Dự án 02 — Quản lý Todo List

**Folder:** `02-todo`

**Kiến thức:** Form có kiểm soát, mảng state, CRUD, lọc, localStorage và custom Hook.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung, rồi tạo hooks/useStoredState.js.
2. Tạo TodoItem để một dòng công việc nhận callback từ cha.
3. Trong App, khai báo danh sách, ô nhập và bộ lọc.
4. Viết add, edit, toggle, remove; cuối cùng ghép form và danh sách.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/hooks/useStoredState.js` | Gõ nội dung đầy đủ bên dưới |
| `src/components/TodoItem.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/hooks/useStoredState.js`

```js
import { useEffect, useState } from 'react';

// key phải cố định trong vòng đời component; initialValue là JSON serializable.
export function useStoredState(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      if (raw === null) return initialValue;
      const parsed = JSON.parse(raw);
      // Các bài này chỉ lưu mảng. Nếu dữ liệu bị sửa sai kiểu thì dùng mặc định.
      return Array.isArray(parsed) ? parsed : initialValue;
    } catch { return initialValue; }
  });
  const [storageError, setStorageError] = useState('');
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      setStorageError('');
    } catch {
      setStorageError('Không lưu được vào trình duyệt. Dữ liệu hiện tại chỉ còn trong bộ nhớ.');
    }
  }, [key, value]);
  return [value, setValue, storageError];
}
```

### File `src/components/TodoItem.jsx`

```jsx
export default function TodoItem({ todo, onToggle, onEdit, onRemove }) {
  return <li>
    <label className={todo.done ? 'done' : ''}>
      <input type="checkbox" checked={todo.done} onChange={() => onToggle(todo.id)} />
      {' '}{todo.title}
    </label>
    <button onClick={() => onEdit(todo)}>Sửa</button>
    <button onClick={() => onRemove(todo.id)}>Xóa</button>
  </li>;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { useStoredState } from './hooks/useStoredState.js';
import TodoItem from './components/TodoItem.jsx';

export default function App() {
  const [todos, setTodos, storageError] = useStoredState('manual-todos-v1', []);
  const [title, setTitle] = useState('');
  const [filter, setFilter] = useState('all');
  const visible = todos.filter(t => filter === 'all' ||
    (filter === 'done' ? t.done : !t.done));
  function add(event) {
    event.preventDefault();
    if (!title.trim()) return;
    const todo = { id: crypto.randomUUID(), title: title.trim(), done: false };
    setTodos(old => [...old, todo]);
    setTitle('');
  }
  function edit(todo) {
    const next = window.prompt('Tên mới', todo.title);
    if (!next?.trim()) return;
    setTodos(old => old.map(t => t.id === todo.id ? { ...t, title: next.trim() } : t));
  }
  function toggle(id) {
    setTodos(old => old.map(t => t.id === id ? { ...t, done: !t.done } : t));
  }
  return <main>
    <h1>Todo List</h1>
    {storageError && <p role="alert">{storageError}</p>}
    <form onSubmit={add}>
      <label>Công việc<input value={title} onChange={e => setTitle(e.target.value)} required /></label>
      <button>Thêm</button>
    </form>
    <label>Bộ lọc<select value={filter} onChange={e => setFilter(e.target.value)}>
      <option value="all">Tất cả</option><option value="active">Chưa xong</option>
      <option value="done">Đã xong</option>
    </select></label>
    <p>Còn {todos.filter(t => !t.done).length} việc chưa xong</p>
    {visible.length === 0 ? <p>Không có công việc phù hợp.</p> :
      <ul>{visible.map(todo => <TodoItem key={todo.id} todo={todo}
        onToggle={toggle} onEdit={edit}
        onRemove={id => setTodos(old => old.filter(t => t.id !== id))} />)}</ul>}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `02-todo`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Form gọi preventDefault để trình duyệt không tải lại trang. Mỗi todo có id ổn định. map thay đổi một phần tử bằng object mới, filter loại phần tử. visible và remaining được tính từ todos, không cần thêm state hoặc Effect. prompt chỉ dùng cho thao tác sửa nhỏ ở bài nhập môn; bài sách sẽ dùng form sửa thật.

### Checklist tự kiểm tra

- [ ] Chuỗi chỉ có dấu cách không tạo công việc.
- [ ] Thêm hai việc; sửa tên một việc; đánh dấu hoàn thành rồi lọc.
- [ ] Xóa một việc không làm thay đổi việc còn lại.
- [ ] F5 vẫn giữ dữ liệu trên cùng địa chỉ và cổng.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thay prompt bằng form sửa inline; thêm hạn hoàn thành và bộ lọc quá hạn.


## Dự án 03 — Quản lý sách cá nhân

**Folder:** `03-books`

**Kiến thức:** CRUD có form sửa, validation, tìm kiếm, trạng thái đọc và nâng state lên cha.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung và Hook lưu trữ.
2. Tạo BookForm.jsx với state draft riêng.
3. Tạo App.jsx sở hữu books, editing và query.
4. Dùng key để khởi tạo lại form khi chọn sách khác; thêm tìm kiếm sau khi CRUD chạy.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/hooks/useStoredState.js` | Gõ nội dung đầy đủ bên dưới |
| `src/components/BookForm.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/hooks/useStoredState.js`

```js
import { useEffect, useState } from 'react';

// key phải cố định trong vòng đời component; initialValue là JSON serializable.
export function useStoredState(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      if (raw === null) return initialValue;
      const parsed = JSON.parse(raw);
      // Các bài này chỉ lưu mảng. Nếu dữ liệu bị sửa sai kiểu thì dùng mặc định.
      return Array.isArray(parsed) ? parsed : initialValue;
    } catch { return initialValue; }
  });
  const [storageError, setStorageError] = useState('');
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      setStorageError('');
    } catch {
      setStorageError('Không lưu được vào trình duyệt. Dữ liệu hiện tại chỉ còn trong bộ nhớ.');
    }
  }, [key, value]);
  return [value, setValue, storageError];
}
```

### File `src/components/BookForm.jsx`

```jsx
import { useState } from 'react';

export default function BookForm({ book, onSave, onCancel }) {
  const [draft, setDraft] = useState(book ?? { title: '', author: '', read: false });
  const [error, setError] = useState('');
  function submit(event) {
    event.preventDefault();
    if (!draft.title.trim() || !draft.author.trim()) {
      setError('Nhập tên sách và tác giả.'); return;
    }
    onSave({ ...draft, title: draft.title.trim(), author: draft.author.trim() });
    if (!book) setDraft({ title: '', author: '', read: false });
    setError('');
  }
  return <form onSubmit={submit}>
    <h2>{book ? 'Sửa sách' : 'Thêm sách'}</h2>
    <label>Tên sách<input value={draft.title}
      onChange={e => setDraft(d => ({ ...d, title: e.target.value }))} required /></label>
    <label>Tác giả<input value={draft.author}
      onChange={e => setDraft(d => ({ ...d, author: e.target.value }))} required /></label>
    <label><input type="checkbox" checked={draft.read}
      onChange={e => setDraft(d => ({ ...d, read: e.target.checked }))} /> Đã đọc</label>
    {error && <p role="alert">{error}</p>}
    <button>Lưu sách</button>
    {book && <button type="button" onClick={onCancel}>Hủy</button>}
  </form>;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import BookForm from './components/BookForm.jsx';
import { useStoredState } from './hooks/useStoredState.js';

export default function App() {
  const [books, setBooks, storageError] = useStoredState('manual-books-v1', []);
  const [editing, setEditing] = useState(null);
  const [query, setQuery] = useState('');
  const visible = books.filter(b => `${b.title} ${b.author}`.toLocaleLowerCase()
    .includes(query.trim().toLocaleLowerCase()));
  function save(draft) {
    const book = { ...draft, id: draft.id ?? crypto.randomUUID() };
    setBooks(old => draft.id ? old.map(b => b.id === draft.id ? book : b) : [...old, book]);
    setEditing(null);
  }
  function remove(id) {
    setBooks(old => old.filter(b => b.id !== id));
    if (editing?.id === id) setEditing(null);
  }
  return <main>
    <h1>Tủ sách của anh</h1>
    {storageError && <p role="alert">{storageError}</p>}
    <BookForm key={editing?.id ?? 'new'} book={editing} onSave={save}
      onCancel={() => setEditing(null)} />
    <label>Tìm sách<input value={query} onChange={e => setQuery(e.target.value)} /></label>
    <p>{books.length} sách · {books.filter(b => b.read).length} đã đọc</p>
    {!visible.length && <p>Chưa có sách phù hợp.</p>}
    {visible.map(book => <article key={book.id}>
      <h2>{book.title}</h2><p>{book.author} · {book.read ? 'Đã đọc' : 'Chưa đọc'}</p>
      <button onClick={() => setEditing(book)}>Sửa</button>
      <button onClick={() => remove(book.id)}>Xóa</button>
    </article>)}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `03-books`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

BookForm quản lý dữ liệu đang gõ, App quản lý dữ liệu đã lưu. Khi nhấn Sửa, editing giữ sách được chọn. key đổi từ new sang id khiến React tạo form mới và useState nhận dữ liệu sách đó. Lưu xong đặt editing về null. Cùng một form phục vụ cả thêm và sửa; draft không trực tiếp thay đổi sách trong danh sách.

### Checklist tự kiểm tra

- [ ] Thêm sách có tên và tác giả; không nhận trường chỉ có khoảng trắng.
- [ ] Sửa một sách rồi Hủy: dữ liệu cũ không đổi.
- [ ] Lưu sửa và chuyển trạng thái Đã đọc; tìm theo tên hoặc tác giả.
- [ ] Xóa sách đang sửa đưa form về chế độ thêm; F5 giữ danh sách.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm phân trang 5 sách/trang; khi query thay đổi phải đưa trang về 1.


## Dự án 04 — Sổ thu chi cá nhân

**Folder:** `04-expenses`

**Kiến thức:** Input số, chuyển kiểu, reduce, lọc theo tháng và định dạng tiền.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung và Hook.
2. Khai báo form gồm nội dung, số tiền, loại và ngày.
3. Viết submit kiểm tra số nguyên dương; lưu amount dưới dạng number.
4. Tính danh sách tháng và tổng thu/chi từ dữ liệu đó.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/hooks/useStoredState.js` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/hooks/useStoredState.js`

```js
import { useEffect, useState } from 'react';

// key phải cố định trong vòng đời component; initialValue là JSON serializable.
export function useStoredState(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      if (raw === null) return initialValue;
      const parsed = JSON.parse(raw);
      // Các bài này chỉ lưu mảng. Nếu dữ liệu bị sửa sai kiểu thì dùng mặc định.
      return Array.isArray(parsed) ? parsed : initialValue;
    } catch { return initialValue; }
  });
  const [storageError, setStorageError] = useState('');
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      setStorageError('');
    } catch {
      setStorageError('Không lưu được vào trình duyệt. Dữ liệu hiện tại chỉ còn trong bộ nhớ.');
    }
  }, [key, value]);
  return [value, setValue, storageError];
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { useStoredState } from './hooks/useStoredState.js';
const money = n => new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(n);
function today() {
  const d = new Date();
  return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
}
export default function App() {
  const [items, setItems, storageError] = useStoredState('manual-expenses-v1', []);
  const [title, setTitle] = useState('');
  const [amount, setAmount] = useState('');
  const [type, setType] = useState('expense');
  const [date, setDate] = useState(today);
  const [month, setMonth] = useState(() => today().slice(0,7));
  const [error, setError] = useState('');
  const visible = items.filter(i => i.date.startsWith(month));
  const sum = type => visible.filter(i => i.type === type).reduce((s,i) => s+i.amount,0);
  function submit(e) {
    e.preventDefault();
    const n = Number(amount);
    if (!title.trim() || !date || !Number.isSafeInteger(n) || n <= 0) {
      setError('Nhập nội dung, ngày và số tiền nguyên dương.'); return;
    }
    const item = { id: crypto.randomUUID(), title: title.trim(), amount: n, type, date };
    setItems(old => [...old, item]); setTitle(''); setAmount(''); setError('');
  }
  return <main><h1>Sổ thu chi</h1>
    {storageError && <p role="alert">{storageError}</p>}
    <form onSubmit={submit}>
      <label>Nội dung<input value={title} onChange={e => setTitle(e.target.value)} required /></label>
      <label>Số tiền VND<input type="number" min="1" step="1" value={amount}
        onChange={e => setAmount(e.target.value)} required /></label>
      <label>Loại<select value={type} onChange={e => setType(e.target.value)}>
        <option value="expense">Chi</option><option value="income">Thu</option></select></label>
      <label>Ngày<input type="date" value={date} onChange={e => setDate(e.target.value)} required /></label>
      {error && <p role="alert">{error}</p>}<button>Thêm giao dịch</button>
    </form>
    <label>Tháng<input type="month" value={month} onChange={e => setMonth(e.target.value)} /></label>
    <p>Thu: {money(sum('income'))} · Chi: {money(sum('expense'))}</p>
    <strong>Số dư: {money(sum('income') - sum('expense'))}</strong>
    {!visible.length && <p>Không có giao dịch.</p>}
    {visible.map(i => <article key={i.id}>
      {i.date} · {i.title} · {i.type === 'income' ? '+' : '-'}{money(i.amount)}
      <button onClick={() => setItems(old => old.filter(x => x.id !== i.id))}>Xóa</button>
    </article>)}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `04-expenses`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Giá trị input luôn là chuỗi dù type=number. Number chuyển sang số, Number.isSafeInteger tránh chấp nhận số không an toàn. Tiền VND ở bài này dùng số nguyên. YYYY-MM-DD là chuỗi ngày lịch địa phương, lọc tháng bằng startsWith; không dùng toISOString vì UTC có thể lệch ngày.

### Checklist tự kiểm tra

- [ ] Nhập thu 1000000 và chi 200000 cùng tháng: số dư 800000.
- [ ] Chọn tháng khác không có giao dịch: tổng bằng 0.
- [ ] Số âm, số thập phân hoặc chuỗi trắng không tạo giao dịch.
- [ ] Xóa khoản chi: số dư tăng tương ứng; F5 giữ dữ liệu.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm hạn mức chi theo tháng và cảnh báo khi vượt 80%; không lưu tổng vào state.


## Dự án 05 — Flashcard từ vựng IELTS

**Folder:** `05-flashcards`

**Kiến thức:** Lật thẻ, state phiên học, dữ liệu mẫu, thêm từ và đánh dấu đã nhớ.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung, Hook và data/cards.js.
2. Tạo Flashcard.jsx hiển thị mặt trước hoặc mặt sau.
3. App quản lý vị trí, trạng thái lật và bộ lọc chưa nhớ.
4. Thêm form nhập từ; reset vị trí khi đổi bộ lọc hoặc đánh dấu.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/hooks/useStoredState.js` | Gõ nội dung đầy đủ bên dưới |
| `src/data/cards.js` | Gõ nội dung đầy đủ bên dưới |
| `src/components/Flashcard.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/hooks/useStoredState.js`

```js
import { useEffect, useState } from 'react';

// key phải cố định trong vòng đời component; initialValue là JSON serializable.
export function useStoredState(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      if (raw === null) return initialValue;
      const parsed = JSON.parse(raw);
      // Các bài này chỉ lưu mảng. Nếu dữ liệu bị sửa sai kiểu thì dùng mặc định.
      return Array.isArray(parsed) ? parsed : initialValue;
    } catch { return initialValue; }
  });
  const [storageError, setStorageError] = useState('');
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      setStorageError('');
    } catch {
      setStorageError('Không lưu được vào trình duyệt. Dữ liệu hiện tại chỉ còn trong bộ nhớ.');
    }
  }, [key, value]);
  return [value, setValue, storageError];
}
```

### File `src/data/cards.js`

```js
export const seedCards = [
  { id: 'c1', word: 'commute', meaning: 'đi lại giữa nhà và nơi làm việc',
    example: 'I commute by bus to save money.', known: false },
  { id: 'c2', word: 'reliable', meaning: 'đáng tin cậy',
    example: 'A reliable transport system helps everyone.', known: false },
  { id: 'c3', word: 'affordable', meaning: 'có giá vừa túi tiền',
    example: 'Students need affordable housing.', known: false }
];
```

### File `src/components/Flashcard.jsx`

```jsx
export default function Flashcard({ card, flipped, onFlip }) {
  return <article>
    <h2>{card.word}</h2>
    {flipped && <div><p>{card.meaning}</p><p lang="en">{card.example}</p></div>}
    <button onClick={onFlip} aria-expanded={flipped}>{flipped ? 'Ẩn nghĩa' : 'Lật thẻ'}</button>
  </article>;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { useStoredState } from './hooks/useStoredState.js';
import { seedCards } from './data/cards.js';
import Flashcard from './components/Flashcard.jsx';
export default function App() {
  const [cards, setCards, storageError] = useStoredState('manual-flashcards-v1', seedCards);
  const [index, setIndex] = useState(0);
  const [flipped, setFlipped] = useState(false);
  const [onlyNew, setOnlyNew] = useState(false);
  const [draft, setDraft] = useState({ word: '', meaning: '', example: '' });
  const deck = onlyNew ? cards.filter(c => !c.known) : cards;
  const current = deck[index % Math.max(1, deck.length)];
  function mark() {
    setCards(old => old.map(c => c.id === current.id ? { ...c, known: !c.known } : c));
    setIndex(0); setFlipped(false);
  }
  function add(e) {
    e.preventDefault();
    if (!draft.word.trim() || !draft.meaning.trim() || !draft.example.trim()) return;
    const card = { id: crypto.randomUUID(), word: draft.word.trim(),
      meaning: draft.meaning.trim(), example: draft.example.trim(), known: false };
    setCards(old => [...old, card]); setDraft({ word: '', meaning: '', example: '' });
  }
  return <main><h1>IELTS Flashcards</h1>
    {storageError && <p role="alert">{storageError}</p>}
    <p>Đã nhớ {cards.filter(c => c.known).length}/{cards.length} từ</p>
    <label><input type="checkbox" checked={onlyNew} onChange={e => {
      setOnlyNew(e.target.checked); setIndex(0); setFlipped(false);
    }} /> Chỉ học từ chưa nhớ</label>
    {current ? <>
      <Flashcard card={current} flipped={flipped} onFlip={() => setFlipped(v => !v)} />
      <button onClick={mark}>{current.known ? 'Cần ôn lại' : 'Đã nhớ'}</button>
      <button onClick={() => { setIndex(i => (i+1)%deck.length); setFlipped(false); }}>Tiếp</button>
    </> : <p>Đã học hết các thẻ trong bộ lọc này.</p>}
    <form onSubmit={add}><h2>Thêm từ</h2>
      {Object.entries({ word: 'Từ tiếng Anh', meaning: 'Nghĩa', example: 'Câu ví dụ' }).map(([key,label]) =>
        <label key={key}>{label}<input required value={draft[key]}
          onChange={e => setDraft(d => ({ ...d, [key]: e.target.value }))} /></label>)}
      <button>Thêm thẻ</button>
    </form>
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `05-flashcards`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

cards là dữ liệu dài hạn; index và flipped chỉ là trạng thái phiên học. deck được lọc từ cards. Khi đánh dấu nhớ trong bộ lọc Chưa nhớ, thẻ biến mất khỏi deck nên phải đưa index về 0. Không truy cập current.word khi deck rỗng. Câu ví dụ là nội dung luyện tập tự soạn, không phải đề IELTS chính thức hay thuật toán spaced repetition.

### Checklist tự kiểm tra

- [ ] Có ba thẻ mẫu; Lật thẻ hiện nghĩa và ví dụ.
- [ ] Bấm Tiếp ở thẻ cuối quay về thẻ đầu.
- [ ] Chọn Chưa nhớ, đánh dấu hết: hiển thị hết thẻ, không crash.
- [ ] Thêm từ mới và F5: vẫn còn từ và trạng thái nhớ.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm dueDate và lịch ôn 1–3–7 ngày; định nghĩa rõ khi nào tăng hoặc reset lịch.


## Dự án 06 — Bài kiểm tra từ vựng tiếng Anh

**Folder:** `06-quiz`

**Kiến thức:** State machine nhỏ, radio input, nộp bài, chấm điểm và reset.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung và data/questions.js.
2. Lưu đáp án theo id câu hỏi trong object answers.
3. Render các lựa chọn bằng fieldset và radio có cùng name.
4. Nộp bài chuyển sang màn hình kết quả; làm lại xóa answers.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/data/questions.js` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/data/questions.js`

```js
export const questions = [
  { id: 'q1', text: 'Affordable means ...', options: ['very expensive', 'not too expensive', 'far away'],
    correct: 1, explanation: 'Affordable: có giá vừa túi tiền.' },
  { id: 'q2', text: 'I ___ to work by train every day.', options: ['commute', 'protect', 'reduce'],
    correct: 0, explanation: 'Commute: đi lại thường xuyên giữa nhà và nơi làm việc.' },
  { id: 'q3', text: 'A reliable person is someone you can ___.', options: ['forget', 'avoid', 'trust'],
    correct: 2, explanation: 'Reliable: đáng tin cậy; trust: tin tưởng.' }
];
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { questions } from './data/questions.js';
export default function App() {
  const [answers, setAnswers] = useState({});
  const [submitted, setSubmitted] = useState(false);
  const complete = questions.every(q => answers[q.id] !== undefined);
  const score = questions.filter(q => answers[q.id] === q.correct).length;
  return <main><h1>English Vocabulary Quiz</h1>
    <form onSubmit={e => { e.preventDefault(); if (complete) setSubmitted(true); }}>
      {questions.map(q => <fieldset key={q.id} disabled={submitted}>
        <legend>{q.text}</legend>
        {q.options.map((option,i) => <label key={option}>
          <input type="radio" name={q.id} checked={answers[q.id] === i}
            onChange={() => setAnswers(old => ({ ...old, [q.id]: i }))} /> {option}
        </label>)}
        {submitted && <p>{answers[q.id] === q.correct ? 'Đúng' : 'Sai'} — {q.explanation}</p>}
      </fieldset>)}
      {!submitted && <button disabled={!complete}>Nộp bài</button>}
    </form>
    {submitted && <section aria-live="polite"><h2>Kết quả: {score}/{questions.length}</h2>
      <button onClick={() => { setAnswers({}); setSubmitted(false); }}>Làm lại</button>
    </section>}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `06-quiz`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Mỗi radio nhận checked từ answers[q.id]. Không dùng nhãn lựa chọn làm đáp án: lưu chỉ số và so với correct. Điểm được suy ra từ answers khi render. submitted điều khiển cả việc khóa input và hiện giải thích. Đây là bài tự luyện từ vựng, không quy đổi sang IELTS band.

### Checklist tự kiểm tra

- [ ] Chưa trả lời hết thì nút Nộp bị khóa.
- [ ] Chọn đúng cả ba câu: kết quả 3/3.
- [ ] Sau khi nộp không đổi được đáp án.
- [ ] Làm lại xóa đáp án và điểm; chọn sai hiện giải thích đúng.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm chế độ mỗi trang một câu nhưng vẫn giữ đáp án khi quay lại.


## Dự án 07 — Đồng hồ Pomodoro học tập

**Folder:** `07-pomodoro`

**Kiến thức:** useEffect, cleanup, useRef, timer, pause/resume và vòng đời component.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung; khai báo remaining, running, minutes và deadlineRef.
2. Start ghi thời điểm kết thúc vào ref; Effect cập nhật thời gian còn lại.
3. Cleanup clearInterval khi dừng hoặc unmount.
4. Thêm Tạm dừng, Tiếp tục và Đặt lại; kiểm tra với 1 phút.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/App.jsx`

```jsx
import { useEffect, useRef, useState } from 'react';
export default function App() {
  const [minutes, setMinutes] = useState('25');
  const [remaining, setRemaining] = useState(25*60);
  const [running, setRunning] = useState(false);
  const [error, setError] = useState('');
  const deadlineRef = useRef(null);
  useEffect(() => {
    if (!running) return;
    const tick = () => {
      const seconds = Math.max(0, Math.ceil((deadlineRef.current-Date.now())/1000));
      setRemaining(seconds);
      if (seconds === 0) setRunning(false);
    };
    tick();
    const id = setInterval(tick, 250);
    return () => clearInterval(id);
  }, [running]);
  function start() {
    if (remaining <= 0) return;
    deadlineRef.current = Date.now() + remaining*1000;
    setRunning(true);
  }
  function pause() {
    setRemaining(Math.max(0, Math.ceil((deadlineRef.current-Date.now())/1000)));
    setRunning(false);
  }
  function reset() {
    const n = Number(minutes);
    if (!Number.isInteger(n) || n < 1 || n > 180) {
      setError('Số phút phải là số nguyên từ 1 đến 180.'); return;
    }
    setRunning(false); setRemaining(n*60); setError('');
  }
  const time = `${String(Math.floor(remaining/60)).padStart(2,'0')}:${String(remaining%60).padStart(2,'0')}`;
  return <main><h1>Pomodoro</h1>
    <label>Số phút<input type="number" min="1" max="180" value={minutes}
      onChange={e => setMinutes(e.target.value)} /></label>
    <p>Đổi số phút rồi bấm Áp dụng / Đặt lại.</p>
    {error && <p role="alert">{error}</p>}
    <p className="timer" role="timer" aria-label="Thời gian còn lại">{time}</p>
    <button disabled={running || remaining === 0} onClick={start}>Bắt đầu / Tiếp tục</button>
    <button disabled={!running} onClick={pause}>Tạm dừng</button>
    <button onClick={reset}>Áp dụng / Đặt lại</button>
    {remaining === 0 && <p role="status">Hết giờ, anh nghỉ một chút nhé!</p>}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `07-pomodoro`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Không giảm một giây mỗi tick vì tab nền có thể làm interval chạy chậm. Mỗi tick lấy deadline trừ Date.now. Ref giữ deadline mà không tự gây render; remaining là state để giao diện thay đổi. Khi pause, lưu số giây còn lại; resume tạo deadline mới. StrictMode chạy thử setup/cleanup thêm một lần trong development nên cleanup là bắt buộc.

### Checklist tự kiểm tra

- [ ] Đặt 1 phút, Start: hiển thị gần 01:00 rồi giảm.
- [ ] Tạm dừng giữ số giây; Tiếp tục chạy từ số giây đang giữ.
- [ ] Đặt lại khi đang chạy: timer dừng và trở về số phút đã nhập.
- [ ] Đổi tab rồi quay lại: thời gian theo đồng hồ thực; hết giờ không âm.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm chế độ nghỉ 5 phút và số phiên hoàn thành; chỉ tăng số phiên một lần khi hết giờ.


## Dự án 08 — Thư viện bài đọc tiếng Anh

**Folder:** `08-reading`

**Kiến thức:** fetch, async/await, loading/error/empty, AbortController và trang chi tiết.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung rồi tự tạo public/readings.json.
2. Viết useReadings để tải JSON bằng HTTP từ cùng dev server.
3. App hiển thị loading, lỗi và nút thử lại trước khi hiện danh sách.
4. Chọn bài bằng selectedId; hiển thị chi tiết và nút quay lại.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `public/readings.json` | Gõ nội dung đầy đủ bên dưới |
| `src/hooks/useReadings.js` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `public/readings.json`

```json
[
  {
    "id": "r1",
    "title": "A greener commute",
    "level": "B1",
    "body": "Many people drive to work every day. Taking a bus can reduce traffic and save money. Walking short distances is also good for our health. Small changes in our daily habits can help our city become a better place to live."
  },
  {
    "id": "r2",
    "title": "Learning in small steps",
    "level": "A2",
    "body": "I study English for twenty minutes each day. First I read a short story. Then I write five new words in my notebook. I use each word in a sentence. On Sunday I review the words with a friend."
  }
]
```

### File `src/hooks/useReadings.js`

```js
import { useEffect, useState } from 'react';
export function useReadings(reloadKey) {
  const [state, setState] = useState({ data: [], loading: true, error: '' });
  useEffect(() => {
    const controller = new AbortController();
    let active = true;
    setState({ data: [], loading: true, error: '' });
    async function load() {
      try {
        const response = await fetch('/readings.json', { signal: controller.signal, cache: 'no-store' });
        if (!response.ok) throw new Error(`HTTP ${response.status}`);
        const data = await response.json();
        if (!Array.isArray(data) || !data.every(x => x && typeof x.id === 'string' &&
          typeof x.title === 'string' && typeof x.body === 'string' && typeof x.level === 'string')) {
          throw new Error('Dữ liệu bài đọc không đúng cấu trúc');
        }
        if (active) setState({ data, loading: false, error: '' });
      } catch (error) {
        if (active && error.name !== 'AbortError')
          setState({ data: [], loading: false, error: error.message });
      }
    }
    load();
    return () => { active = false; controller.abort(); };
  }, [reloadKey]);
  return state;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { useReadings } from './hooks/useReadings.js';
export default function App() {
  const [reloadKey, setReloadKey] = useState(0);
  const [selectedId, setSelectedId] = useState(null);
  const { data, loading, error } = useReadings(reloadKey);
  const selected = data.find(x => x.id === selectedId);
  if (loading) return <main><p role="status">Đang tải bài đọc...</p></main>;
  if (error) return <main><p role="alert">Không tải được: {error}</p>
    <button onClick={() => setReloadKey(n => n+1)}>Thử lại</button></main>;
  return <main><h1>English Reading Library</h1>
    {selected ? <article>
      <button onClick={() => setSelectedId(null)}>Quay lại</button>
      <h2>{selected.title}</h2><p lang="en">{selected.body}</p>
      <small>{selected.body.trim().split(/\s+/).filter(Boolean).length} từ · {selected.level}</small>
    </article> : <>
      {!data.length && <p>Chưa có bài đọc.</p>}
      {data.map(item => <article key={item.id}><h2>{item.title}</h2>
        <p>{item.level}</p><button onClick={() => setSelectedId(item.id)}>Đọc bài</button>
      </article>)}
    </>}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `08-reading`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

public/readings.json được phục vụ tại /readings.json. Đây là file tĩnh qua HTTP, không phải backend CRUD. fetch không tự ném lỗi khi HTTP 404 nên phải kiểm tra response.ok. Cleanup abort request; cờ active ngăn response cũ ghi đè state. Chi tiết dùng state tại chỗ, chưa phải URL routing: F5 đưa về danh sách.

### Checklist tự kiểm tra

- [ ] Network hiển thị GET readings.json, sau đó danh sách hai bài.
- [ ] Chọn bài thấy nội dung và đếm từ; quay lại thấy danh sách.
- [ ] Đổi tên file JSON, tải lại: báo lỗi, không treo loading; trả tên và Thử lại.
- [ ] Thay nội dung JSON bằng []: hiện danh sách rỗng; JSON sai cú pháp: hiện lỗi.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm query tìm theo tiêu đề; sau đó chuyển selectedId sang URL để liên kết mở trực tiếp được.


## Dự án 09 — Giỏ hàng cửa hàng sách

**Folder:** `09-cart`

**Kiến thức:** useReducer, Context, custom Hook và tách logic nghiệp vụ thuần.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung và dữ liệu products.js.
2. Viết cartReducer.js xử lý add, remove, clear; reducer không gọi API.
3. Tạo CartContext.jsx chứa Provider và useCart.
4. Tạo Catalog, Cart rồi bọc chúng bằng CartProvider trong App.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/data/products.js` | Gõ nội dung đầy đủ bên dưới |
| `src/state/cartReducer.js` | Gõ nội dung đầy đủ bên dưới |
| `src/state/CartContext.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/components/Catalog.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/components/Cart.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/data/products.js`

```js
export const products = [
  { id: 'p1', title: 'Sổ luyện từ vựng', price: 50000 },
  { id: 'p2', title: 'Bài đọc tiếng Anh', price: 80000 },
  { id: 'p3', title: 'Nhật ký học tập', price: 40000 }
];
```

### File `src/state/cartReducer.js`

```js
export function cartReducer(state, action) {
  switch (action.type) {
    case 'add': {
      const found = state.find(x => x.id === action.product.id);
      return found
        ? state.map(x => x.id === action.product.id ? { ...x, qty: x.qty+1 } : x)
        : [...state, { ...action.product, qty: 1 }];
    }
    case 'remove': return state.filter(x => x.id !== action.id);
    case 'clear': return [];
    default: throw new Error(`Action không hợp lệ: ${action.type}`);
  }
}
```

### File `src/state/CartContext.jsx`

```jsx
import { createContext, useContext, useReducer } from 'react';
import { cartReducer } from './cartReducer.js';
const CartContext = createContext(null);
export function CartProvider({ children }) {
  const [items, dispatch] = useReducer(cartReducer, []);
  return <CartContext.Provider value={{ items, dispatch }}>{children}</CartContext.Provider>;
}
export function useCart() {
  const context = useContext(CartContext);
  if (!context) throw new Error('useCart phải ở trong CartProvider');
  return context;
}
```

### File `src/components/Catalog.jsx`

```jsx
import { products } from '../data/products.js';
import { useCart } from '../state/CartContext.jsx';
export default function Catalog() {
  const { dispatch } = useCart();
  return <section><h2>Sách đang bán</h2>{products.map(product => <article key={product.id}>
    <h3>{product.title}</h3><p>{product.price.toLocaleString('vi-VN')} đ</p>
    <button onClick={() => dispatch({ type: 'add', product })}>Thêm vào giỏ</button>
  </article>)}</section>;
}
```

### File `src/components/Cart.jsx`

```jsx
import { useCart } from '../state/CartContext.jsx';
export default function Cart() {
  const { items, dispatch } = useCart();
  const total = items.reduce((sum, item) => sum + item.price*item.qty, 0);
  return <section><h2>Giỏ hàng</h2>
    {!items.length && <p>Giỏ hàng trống.</p>}
    {items.map(item => <article key={item.id}>
      <h3>{item.title}</h3><p>Số lượng: {item.qty}</p>
      <button onClick={() => dispatch({ type: 'remove', id: item.id })}>Xóa dòng</button>
    </article>)}
    <p>Tổng: {total.toLocaleString('vi-VN')} đ</p>
    <button disabled={!items.length} onClick={() => dispatch({ type: 'clear' })}>Xóa hết</button>
  </section>;
}
```

### File `src/App.jsx`

```jsx
import { CartProvider } from './state/CartContext.jsx';
import Catalog from './components/Catalog.jsx';
import Cart from './components/Cart.jsx';
export default function App() {
  return <CartProvider><main><h1>Book Shop</h1>
    <div className="row"><Catalog /><Cart /></div>
  </main></CartProvider>;
}
```

### Chạy và quan sát

Mở Terminal tại `09-cart`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Catalog và Cart là hai component ngang hàng dùng chung Context. dispatch mô tả hành động, reducer trả state mới. Thêm cùng id tăng qty, không tạo dòng trùng. Đây là giỏ hàng học tập: giá nằm ở frontend, chưa thanh toán; backend thực tế phải xác nhận lại giá, tồn kho và quyền thao tác. Không dùng Context cho mọi ô input.

### Checklist tự kiểm tra

- [ ] Thêm một sách hai lần tạo một dòng qty=2.
- [ ] Thêm sách khác: tổng bằng giá nhân số lượng của từng dòng.
- [ ] Xóa một dòng và Xóa hết cập nhật cả tổng và danh sách.
- [ ] F5 làm trống giỏ vì bài này cố ý dùng bộ nhớ; không nhầm với lỗi lưu trữ.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Thêm action setQuantity và unit test qty phải là số nguyên dương; tiếp theo mới lưu giỏ.


## Dự án 10 — IELTS Study Planner — dự án tổng hợp

**Folder:** `10-ielts-planner`

**Kiến thức:** Tách component, form nhiều trường, thống kê, filter, localStorage và luồng hoàn thành.

### Thành quả và thứ tự thực hiện

1. Tạo bộ khung và Hook lưu trữ; tạo data/skills.js làm nguồn lựa chọn chung.
2. Tạo SessionForm để nhập kỹ năng, nội dung, ngày và số phút.
3. Tạo Summary nhận sessions qua props và tính thống kê theo kỹ năng.
4. App nối form, bộ lọc, danh sách, hoàn thành và xóa; tự kiểm tra toàn luồng.

### Cấu trúc file cần tự tạo

| File | Cách thực hiện |
|---|---|
| `package.json` | Gõ nguyên nội dung file nền tảng ở phần II |
| `index.html` | Gõ nguyên nội dung file nền tảng ở phần II |
| `vite.config.js` | Gõ nguyên nội dung file nền tảng ở phần II |
| `.gitignore` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/main.jsx` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/styles.css` | Gõ nguyên nội dung file nền tảng ở phần II |
| `src/hooks/useStoredState.js` | Gõ nội dung đầy đủ bên dưới |
| `src/data/skills.js` | Gõ nội dung đầy đủ bên dưới |
| `src/components/SessionForm.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/components/Summary.jsx` | Gõ nội dung đầy đủ bên dưới |
| `src/App.jsx` | Gõ nội dung đầy đủ bên dưới |

Không cần tạo folder rỗng không được liệt kê. File chứa JSX phải có đuôi `.jsx`.

### File `src/hooks/useStoredState.js`

```js
import { useEffect, useState } from 'react';

// key phải cố định trong vòng đời component; initialValue là JSON serializable.
export function useStoredState(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const raw = localStorage.getItem(key);
      if (raw === null) return initialValue;
      const parsed = JSON.parse(raw);
      // Các bài này chỉ lưu mảng. Nếu dữ liệu bị sửa sai kiểu thì dùng mặc định.
      return Array.isArray(parsed) ? parsed : initialValue;
    } catch { return initialValue; }
  });
  const [storageError, setStorageError] = useState('');
  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
      setStorageError('');
    } catch {
      setStorageError('Không lưu được vào trình duyệt. Dữ liệu hiện tại chỉ còn trong bộ nhớ.');
    }
  }, [key, value]);
  return [value, setValue, storageError];
}
```

### File `src/data/skills.js`

```js
export const skills = ['Listening', 'Reading', 'Writing', 'Speaking'];
```

### File `src/components/SessionForm.jsx`

```jsx
import { useState } from 'react';
import { skills } from '../data/skills.js';
export default function SessionForm({ onAdd }) {
  const [draft, setDraft] = useState({ skill: 'Reading', task: '', date: '', minutes: '30' });
  const [error, setError] = useState('');
  const change = e => setDraft(d => ({ ...d, [e.target.name]: e.target.value }));
  function submit(e) {
    e.preventDefault();
    const minutes = Number(draft.minutes);
    if (!draft.task.trim() || !draft.date || !Number.isInteger(minutes) || minutes < 1 || minutes > 480) {
      setError('Nhập nội dung, ngày và thời lượng từ 1 đến 480 phút.'); return;
    }
    onAdd({ ...draft, task: draft.task.trim(), minutes, id: crypto.randomUUID(), done: false });
    setDraft(d => ({ ...d, task: '' })); setError('');
  }
  return <form onSubmit={submit}><h2>Lên lịch học</h2>
    <label>Kỹ năng<select name="skill" value={draft.skill} onChange={change}>
      {skills.map(s => <option key={s}>{s}</option>)}</select></label>
    <label>Nội dung<input name="task" value={draft.task} onChange={change} required /></label>
    <label>Ngày<input type="date" name="date" value={draft.date} onChange={change} required /></label>
    <label>Số phút<input type="number" name="minutes" min="1" max="480"
      value={draft.minutes} onChange={change} required /></label>
    {error && <p role="alert">{error}</p>}<button>Thêm buổi học</button>
  </form>;
}
```

### File `src/components/Summary.jsx`

```jsx
import { skills } from '../data/skills.js';
export default function Summary({ sessions }) {
  const planned = sessions.reduce((n,s) => n+s.minutes,0);
  const completed = sessions.filter(s => s.done).reduce((n,s) => n+s.minutes,0);
  return <section><h2>Toàn bộ kế hoạch</h2>
    <p>Đã học {completed}/{planned} phút</p>
    <progress aria-label="Tiến độ theo phút" value={completed} max={planned || 1} />
    <ul>{skills.map(skill => <li key={skill}>{skill}: {
      sessions.filter(s => s.skill === skill && s.done).reduce((n,s) => n+s.minutes,0)
    } phút đã học</li>)}</ul>
  </section>;
}
```

### File `src/App.jsx`

```jsx
import { useState } from 'react';
import { useStoredState } from './hooks/useStoredState.js';
import { skills } from './data/skills.js';
import SessionForm from './components/SessionForm.jsx';
import Summary from './components/Summary.jsx';
export default function App() {
  const [sessions, setSessions, storageError] = useStoredState('manual-ielts-plan-v1', []);
  const [filter, setFilter] = useState('all');
  const visible = sessions.filter(s => filter === 'all' || s.skill === filter)
    .sort((a,b) => a.date.localeCompare(b.date));
  function toggle(id) {
    setSessions(old => old.map(s => s.id === id ? { ...s, done: !s.done } : s));
  }
  return <main><h1>IELTS Study Planner</h1>
    {storageError && <p role="alert">{storageError}</p>}
    <SessionForm onAdd={session => setSessions(old => [...old, session])} />
    <Summary sessions={sessions} />
    <label>Lọc kỹ năng<select value={filter} onChange={e => setFilter(e.target.value)}>
      <option value="all">Tất cả</option>{skills.map(s => <option key={s}>{s}</option>)}
    </select></label>
    {!visible.length && <p>Chưa có buổi học phù hợp.</p>}
    {visible.map(s => <article key={s.id}><h2>{s.task}</h2>
      <p>{s.date} · {s.skill} · {s.minutes} phút</p>
      <label><input type="checkbox" checked={s.done} onChange={() => toggle(s.id)} /> Hoàn thành</label>
      <button onClick={() => setSessions(old => old.filter(x => x.id !== s.id))}>Xóa buổi học</button>
    </article>)}
  </main>;
}
```

### Chạy và quan sát

Mở Terminal tại `10-ielts-planner`. Nếu đã cài thư viện ở mốc Hello React thì không cần cài lại.

```bash
npm install
npm run dev
```

Mở URL Terminal cung cấp. Các thao tác thành công mong đợi được ghi trong checklist bên dưới.

### Giải thích luồng dữ liệu

Đây là kế hoạch học cá nhân, không phải hệ thống chấm IELTS. Một session có skill, task, date, minutes và done. Summary tính trên toàn bộ kế hoạch; bộ lọc chỉ ảnh hưởng danh sách bên dưới. Dữ liệu lưu cùng trình duyệt, không đồng bộ nhiều máy. Cố ý chưa thêm đăng nhập hay API để người mới tập trung vào React.

### Checklist tự kiểm tra

- [ ] Thêm Reading 30 phút và Speaking 20 phút: kế hoạch tổng 50 phút.
- [ ] Hoàn thành Reading: thống kê đã học tăng 30, Speaking vẫn 0.
- [ ] Lọc Speaking: chỉ hiện Speaking, thống kê toàn kế hoạch vẫn giữ nguyên.
- [ ] Bỏ hoàn thành rồi F5: trạng thái và tổng khớp; xóa hết hiện màn hình trống.

Sau khi thử tương tác, chạy `npm run build` rồi `npm run preview` để kiểm tra bản đóng gói.

### Bài tập mở rộng — anh tự viết thêm

Tạo trang theo tuần, form sửa session và xuất/nhập JSON có kiểm tra schema; sau đó nối ASP.NET Core API.


# Phần V — Từ chạy được đến hiểu và kiểm tra được

## 21. Cách tự kiểm tra mỗi dự án

Build thành công chỉ chứng minh công cụ đóng gói được source và giải quyết được import; chưa chứng minh mọi hành vi đúng. Với từng bài, chạy các ca kiểm tra ghi ở cuối bài và quan sát Console.

| Nhóm | Cần thử |
|---|---|
| Dữ liệu hợp lệ | Thêm, xem, sửa, xóa đúng đối tượng |
| Dữ liệu không hợp lệ | Chuỗi rỗng, chỉ dấu cách, số âm, số không nguyên |
| Danh sách rỗng | Không crash; thông báo rõ ràng |
| Dữ liệu đã có | Tải lại trang trên cùng origin |
| Chuyển trạng thái | Lọc rồi sửa/xóa; hoàn thành rồi bỏ hoàn thành |
| Tương tác nhanh | Bấm liên tục; không mất các cập nhật state |
| Mạng | Loading, 404, JSON sai, retry |
| Timer | Pause/resume, reset, đổi tab |
| Bàn phím | Tab tới input/nút; Enter gửi form; focus nhìn thấy được |
| Mobile | Khoảng 375px; không tràn ngang; label rõ ràng |

### Unit test đầu tiên: reducer không cần trình duyệt

Trong bài 09, tự tạo folder `tests`, file `tests/cartReducer.test.js`:

```js
import test from 'node:test';
import assert from 'node:assert/strict';
import { cartReducer } from '../src/state/cartReducer.js';

test('thêm cùng sách tăng số lượng và không sửa state cũ', () => {
  const product = { id: 'p1', title: 'Sổ từ vựng', price: 50000 };
  const before = [{ ...product, qty: 1 }];
  const after = cartReducer(before, { type: 'add', product });
  assert.equal(after.length, 1);
  assert.equal(after[0].qty, 2);
  assert.equal(before[0].qty, 1);
  assert.notEqual(after, before);
});

test('xóa một dòng không xóa dòng còn lại', () => {
  const before = [{ id: 'p1', qty: 1 }, { id: 'p2', qty: 2 }];
  const after = cartReducer(before, { type: 'remove', id: 'p1' });
  assert.deepEqual(after, [{ id: 'p2', qty: 2 }]);
});
```

Chạy trong folder dự án 09:

```bash
node --test
```

Bài này chỉ dùng test runner của Node, không cần tạo project test bằng lệnh sinh mã. Sau khi quen, có thể học React Testing Library để kiểm tra giao diện theo hành vi người dùng và Playwright để kiểm tra toàn luồng trong browser.

## 22. Debug bằng DevTools

1. Mở F12 → Console. Đọc lỗi đầu tiên trước, không đoán từ màn hình trắng.
2. Mở Network khi làm bài 08: xem URL, status, response JSON.
3. Mở Application → Local Storage: chọn đúng origin, xem key của bài.
4. Đặt breakpoint trong handler add/save; xem giá trị trước khi gọi setter.
5. Dùng React DevTools nếu có để xem component, props và state.

Nếu localStorage của **bài luyện** bị hỏng, xóa đúng key, không xóa toàn bộ storage của website khác:

```js
localStorage.removeItem('manual-todos-v1');
location.reload();
```

Đây là thao tác xóa dữ liệu todo mẫu trên origin hiện tại. Không dùng để “sửa” dữ liệu thật chưa sao lưu.

## 23. Những lỗi thường gặp

| Hiện tượng | Nguyên nhân thường gặp | Cách xử lý |
|---|---|---|
| Không tìm thấy package.json | Terminal ở sai folder | Mở Terminal tại root dự án |
| `Missing script: dev` | Chưa khai báo scripts | So lại package.json |
| JSON parse error | Dấu phẩy cuối hoặc comment trong JSON | JSON không có comment; kiểm tra dấu ngoặc |
| Không resolve import | Sai đường dẫn/chữ hoa/thường | Đối chiếu danh sách file |
| Màn hình trắng | Lỗi runtime hoặc root sai id | Console; kiểm tra root và main.jsx |
| `Too many re-renders` | Gọi setter trong render | Truyền callback vào event |
| Input không gõ được | Có value nhưng thiếu onChange | Thêm handler cập nhật state |
| Warning uncontrolled/controlled | Ban đầu undefined, sau là string | Khởi tạo chuỗi rỗng |
| Key warning | Thiếu key ổn định trong map | Dùng id của dữ liệu |
| Thêm rồi UI không đổi | Sửa mảng/object cũ trực tiếp | Trả mảng/object mới |
| Effect chạy thêm ở dev | StrictMode kiểm tra lifecycle | Viết cleanup, không xóa StrictMode để che lỗi |
| Dữ liệu “mất” khi đổi port | Origin khác | Dùng đúng URL cũ |
| Fetch nhận HTML thay JSON | Sai URL hoặc fallback HTML | Xem Network response và file public |
| Không đọc được API khác origin | CORS server chưa cấu hình | Cấu hình backend/proxy đúng; không tắt bảo mật browser |
| Preview không có code mới | Chưa build lại | npm run build rồi preview |
| `crypto.randomUUID` không có | Browser cũ hoặc ngữ cảnh không an toàn | Dùng localhost/HTTPS và browser phù hợp |
| Build quá chậm/lỗi Node engine | Node không tương thích | Dùng Node 24 đã nêu; cài lại dependency đúng môi trường |

## 24. Tổ chức source khi dự án lớn hơn

Các bài đầu ưu tiên ít file, dễ lần theo luồng. Khi một App có quá nhiều trách nhiệm, tách theo hành vi: form, danh sách, thống kê, Hook tải dữ liệu. Không tách mỗi thẻ HTML thành một component chỉ để tăng số file.

Ví dụ khi nâng cấp quản lý sách:

| Đường dẫn | Trách nhiệm |
|---|---|
| `src/features/books/BookPage.jsx` | Ghép các phần của màn hình sách |
| `src/features/books/BookForm.jsx` | Nhập và kiểm tra draft |
| `src/features/books/BookList.jsx` | Hiển thị danh sách |
| `src/features/books/useBooks.js` | Logic state/load của tính năng |
| `src/features/books/bookApi.js` | Giao tiếp HTTP |
| `src/components/Button.jsx` | UI dùng chung khi thật sự có nhu cầu |
| `src/App.jsx` | Ghép ứng dụng/routing |

Đặt state ở phạm vi nhỏ nhất đủ phục vụ người dùng dữ liệu. Logic thuần như tính tổng, lọc, validation có thể đưa ra module và test độc lập. Đừng áp Clean Architecture backend bằng cách tạo nhiều lớp interface/repository cho một ứng dụng học vài chục dòng.

## 25. Khi nối với ASP.NET Core

Các bài hiện tại chưa có backend. Để chuyển quản lý sách thành ứng dụng full-stack, có thể thiết kế:

| Hành động | HTTP | Ví dụ |
|---|---|---|
| Danh sách | GET | `/api/books?page=1&pageSize=20` |
| Xem một sách | GET | `/api/books/{id}` |
| Thêm | POST | `/api/books` |
| Cập nhật | PUT hoặc PATCH theo hợp đồng | `/api/books/{id}` |
| Xóa | DELETE | `/api/books/{id}` |

Frontend giữ draft, loading, error và dữ liệu hiển thị; server chịu trách nhiệm lưu lâu dài, validation, authentication/authorization, concurrency và quy tắc nghiệp vụ. Không giả định API luôn trả JSON cho mọi lỗi. Khi lưu thất bại, giữ draft để người dùng không mất nội dung đang gõ.

Biến có tiền tố `VITE_` được đưa vào mã client khi build; người dùng có thể đọc được. Chỉ đưa base URL/cấu hình công khai vào đó. API secret và database connection string thuộc server.

## 26. Học Next.js sau nền tảng này

React là thư viện UI; Next.js bổ sung quy ước ứng dụng và khả năng chạy phía server. Không đổi tên App.jsx thành page.jsx rồi coi như đã chuyển xong. Cần hiểu routing theo file, layout, server/client boundary và cơ chế tải dữ liệu.

Lộ trình tiếp nối:

1. Chuyển trang quản lý sách thành route `/books`.
2. Tạo layout dùng chung và trang chi tiết `/books/[id]`.
3. Đọc dữ liệu ở phía server khi phù hợp.
4. Tách form tương tác thành Client Component.
5. Thêm loading/error UI, xử lý mutation và xác thực server.

Next.js hỗ trợ thiết lập thủ công mà không cần create-next-app. Nếu học tiếp, anh tự tạo package.json, cài dependency Next/React, tự tạo `app/layout.jsx` và `app/page.jsx` theo tài liệu phiên bản đang dùng. [R7] Phần này là định hướng tiếp nối, **không phải dự án thứ 11 có mã nguồn hoàn chỉnh**.

Trong App Router, component dùng state/event tương tác cần nằm trong client boundary phù hợp, thường mở bằng `'use client'`. Đừng đọc window/localStorage trong server rendering. Client Component vẫn có thể được pre-render trên server, nên chỉ thêm `'use client'` không tự làm mọi truy cập browser API trong render trở nên an toàn. Hook localStorage trong bộ bài này được viết cho ứng dụng Vite chạy client; không bê nguyên sang SSR mà bỏ qua khác biệt đó.

# Phần VI — Lộ trình học và tự đánh giá

## 27. Gợi ý 6 tuần

Đây là lịch tham khảo; anh có thể đi chậm hơn nếu đang mới học JavaScript.

| Tuần | Nội dung | Thành quả |
|---|---|---|
| 1 | JavaScript, dựng thủ công, JSX/props/state | Bài 01 và hiểu đường chạy |
| 2 | Form, mảng, bất biến, localStorage | Todo và quản lý sách |
| 3 | Derived data, input số, trạng thái phiên | Thu chi và flashcard |
| 4 | Điều kiện UI, timer, Effect/cleanup | Quiz và Pomodoro |
| 5 | HTTP, loading/error, Reducer/Context | Bài đọc và giỏ hàng |
| 6 | Tổng hợp, debug, build, test | IELTS Planner và tự làm lại một bài |

Mỗi buổi khoảng 60–90 phút: 15 phút đọc, 35–50 phút gõ code, 15 phút kiểm tra và ghi lại lỗi. Không cần học cùng lúc Redux, TypeScript, Tailwind, Next.js và một thư viện form trước khi nắm state/props.

## 28. Câu hỏi tự kiểm tra có đáp án ngắn

1. **Tại sao không mở index.html bằng double-click?** Vì bài sử dụng module, npm dependency và JSX cần dev server/build tool xử lý.
2. **Npm install có tự tạo component không?** Không. Nó cài phụ thuộc và tạo lockfile/node_modules.
3. **Component nhận props có sửa trực tiếp được không?** Không; gửi ý định thay đổi qua callback.
4. **Tại sao dùng setCount(n => n+1)?** Để tính dựa trên state trước đó trong hàng đợi cập nhật.
5. **Tại sao books.push không phải cách cập nhật state đúng?** Nó sửa mảng cũ; cần tạo reference mới bằng setter.
6. **Tổng tiền có cần state riêng không?** Thường không, tính từ dòng hàng.
7. **Effect có phải nơi gửi mọi thao tác lưu form không?** Không; thao tác xuất phát từ submit thường nằm trong handler.
8. **Ref khác state thế nào?** Đổi ref.current không tự gây render.
9. **Custom Hook có chia sẻ state giữa hai component không?** Không tự động; mỗi lần gọi có state riêng.
10. **Context có thay thế reducer không?** Không, một bên truyền dữ liệu, một bên mô tả cập nhật.
11. **Fetch HTTP 404 có luôn nhảy catch không?** Không; cần kiểm tra response.ok.
12. **Có cần lưu key ổn định?** Có, nhất là khi danh sách thay đổi thứ tự/thêm/xóa.
13. **Có thể dùng localStorage làm database nhiều người dùng?** Không.
14. **Build pass có nghĩa ứng dụng hết lỗi?** Không, cần kiểm tra hành vi và tình huống biên.
15. **Chặn nút trên frontend có đủ phân quyền không?** Không, server phải xác thực và kiểm tra quyền.

## 29. Thử thách cuối khóa

Không mở mã mẫu, tự dựng lại quản lý sách từ folder trống trong nhiều buổi nếu cần. Bản tự làm phải thêm/sửa/xóa, tìm kiếm, đánh dấu đã đọc, giữ dữ liệu sau F5 và có trạng thái trống. Sau đó giải thích cho một người khác: component nào giữ state, component nào nhận props, đoạn nào cần Effect và đoạn nào chỉ là phép tính.

Khi anh tự làm được việc đó, anh đã có nền tảng đủ vững để tiếp tục TypeScript, routing, API thật và Next.js.

## 30. Tài liệu gốc để đọc thêm

Nội dung và ví dụ dự án được biên soạn riêng cho khóa học. Các nguồn dưới đây dùng để đối chiếu cơ chế và cách thiết lập, không phải nguồn mã của 10 ứng dụng.

- [R1 — React Quick Start](https://react.dev/learn): component, JSX và tư duy UI.
- [R2 — Vite Getting Started, Manual Installation](https://vite.dev/guide/): cài đặt và chạy thủ công.
- [R3 — State as a Snapshot](https://react.dev/learn/state-as-a-snapshot): state của từng render.
- [R4 — Updating Arrays in State](https://react.dev/learn/updating-arrays-in-state): cập nhật mảng bất biến.
- [R5 — You Might Not Need an Effect](https://react.dev/learn/you-might-not-need-an-effect): tránh state/Effect dư thừa.
- [R6 — useEffect Reference](https://react.dev/reference/react/useEffect): dependency và cleanup.
- [R7 — Next.js Manual Installation](https://nextjs.org/docs/app/getting-started/installation): bước học tiếp.
- [React — Build an app from scratch](https://react.dev/learn/build-a-react-app-from-scratch): bối cảnh dùng build tool để học từ đầu.

## 31. Phạm vi kiểm chứng của bản tài liệu

Cả 10 dự án đã chạy `npm run build` thành công với Node 24.22.1 và bộ dependency nêu trong package.json. Hai unit test của cartReducer ở phần 21 đã chạy đạt bằng Node test runner. Chưa kiểm thử tự động các luồng tương tác trong trình duyệt: môi trường kiểm tra không tải được Chromium. Các checklist UI trong từng bài là hướng dẫn để anh chạy thực tế, không phải báo cáo đã pass.

Các ca kiểm tra bằng tay và bài tập mở rộng được ghi để người học tự thực hiện. Chúng không đồng nghĩa với việc đã có bộ kiểm thử đầy đủ cho production. Các ứng dụng là bài học độc lập, chưa có tài khoản, backend, đồng bộ đa thiết bị hoặc hệ thống chấm IELTS.
