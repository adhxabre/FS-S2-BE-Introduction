---
sidebar_position: 4
---

# 4. Group Routes

import useBaseUrl from '@docusaurus/useBaseUrl';

**Group Routes** adalah teknik mengelompokkan route API agar lebih teratur dan terdokumentasi dengan baik.

### 4.1 Persiapan Struktur File

Tahap awal, perlu dilakukan persiapan dari sisi folder dan file. Jadi Anda perlu membuat folder baru seperti `src` yang didalamnya terdapat folder `controllers` dan `routes`. Kemudian didalam folder `controllers` terdapat file `todo.js`, kemudian didalam folder `routes` terdapat file `index.js`.

```text {3-7}
express-api-app
 ┣ node_modules
 ┣ src
 ┃ ┣ controllers
 ┃ ┃ ┗ todo.js
 ┃ ┗ routes
 ┃ ┃ ┗ index.js
 ┣ .gitignore
 ┣ index.js
 ┣ package-lock.json
 ┣ package.json
 ┗ vercel.json
```

### 4.2 Controllers

**Controller** berfungsi sebagai handler. Ketika client mengakses `route`, maka selanjutnya system akan membawa ke `controller` yang dituju kemudian akan mengeksekusi code yang terdapat didalam `controller` tersebut.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/4-expressjs-fundamental/src/controllers/todo.js">
Contoh code
</a>

<br />
<br />

```js {1-17,19-34,36-54,56-73,75-102,104-122} title=todo.js
let todos = [
  {
    id: 1,
    title: 'Cuci tangan',
    isDone: true,
  },
  {
    id: 2,
    title: 'Jaga jarak',
    isDone: false,
  },
  {
    id: 3,
    title: 'Gunakan maskaer',
    idDone: true,
  },
];

exports.getTodos = async (req, res) => {
  try {
    res.send({
      status: 'success',
      data: {
        todos,
      },
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: 'failed',
      message: 'Server Error',
    });
  }
};

exports.getTodo = async (req, res) => {
  try {
    const id = req.params.id;

    const data = todos.find((todo) => todo.id == id);
    res.send({
      status: 'success',
      data: {
        todo: data,
      },
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: 'failed',
      message: 'Server Error',
    });
  }
};

exports.addTodo = async (req, res) => {
  try {
    const data = req.body;
    todos.push(data);
    res.send({
      status: 'success',
      data: {
        todos,
      },
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: 'failed',
      message: 'Server Error',
    });
  }
};

exports.updateTodo = async (req, res) => {
  try {
    const { id } = req.params;

    todos = todos.map((todo) => {
      if (todo.id == id) {
        return req.body;
      } else {
        return todo;
      }
    });

    const data = todos.find((todo) => todo.id == id);

    res.send({
      status: 'success',
      data: {
        todo: data,
      },
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: 'failed',
      message: 'Server Error',
    });
  }
};

exports.deleteTodo = async (req, res) => {
  try {
    const { id } = req.params;
    todos = todos.filter((todo) => todo.id != id);

    res.send({
      status: 'success',
      data: {
        todos,
      },
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: 'failed',
      message: 'Server Error',
    });
  }
};
```

### 4.3 Routes

Pada file ini terdapat proses penghubung antara `route` dan `controller`.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/4-expressjs-fundamental/src/routes/index.js">
Contoh code
</a>

<br />
<br />

```js {6-12,15-19} title=index.js
const express = require('express');

const router = express.Router();

// Controller
const {
  getTodos,
  getTodo,
  addTodo,
  updateTodo,
  deleteTodo,
} = require('../controllers/todo');

// Route
router.get('/todos', getTodos);
router.get('/todo/:id', getTodo);
router.post('/todo', addTodo);
router.patch('/todo/:id', updateTodo);
router.delete('/todo/:id', deleteTodo);

module.exports = router;
```

### 4.4 Root file

Pada bagian ini kita melakukan `grouping` berdasarkan `route` yang telah dibuat pada folder `routes`.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/4-expressjs-fundamental/index.js">
Contoh code
</a>

<br />
<br />

```js {4,13} title=index.js
const express = require('express');

// Get routes to the variabel
const router = require('./src/routes');

const app = express();

const port = 5000;

app.use(express.json());

// Add endpoint grouping and router
app.use('/api/v1/', router);

app.listen(port, () => console.log(`Listening on port ${port}!`));
```

### 4.5 Akses API Group route dengan Postman

```link title=baseUrl
https://ebook-code-results-stage-2-backend-git-4-e-a80eda-demo-dumbways.vercel.app
```

Berikut list route yang telah dibuat :

| Method | Group     | Route       |
| ------ | --------- | ----------- |
| GET    | `/api/v1` | `/todos`    |
| GET    | `/api/v1` | `/todo/:id` |
| POST   | `/api/v1` | `/todo`     |
| PATCH  | `/api/v1` | `/todo/:id` |
| DELETE | `/api/v1` | `/todo/:id` |

Untuk menggunakan grouping route, Anda dapat menambahkan `baseUrl` terlebih dahulu kemudian `group`, lalu diikuti `route`, berikut contohnya:

```
https://ebook-code-results-stage-2-backend-git-4-e-a80eda-demo-dumbways.vercel.app/api/v1/todos
```

```

```
