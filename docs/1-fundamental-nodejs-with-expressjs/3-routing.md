---
sidebar_position: 3
---

# 3. Routing

import useBaseUrl from '@docusaurus/useBaseUrl';

**Routing** adalah cara bagaimana aplikasi berkomunikasi / meresponse sebuah permintaan dari `client` ke titik akhir dari perminataan aplikasi tersebut, yang dimana permintaan tersebut seperti `URI (Path)` ataupun `HTTP Request`. Setiap Routing dapat memiliki satu atau lebih fungsi yang dapat di jalankan.

Struktur Routing :

```bash
app.METHOD(PATH, HANDLER)
```

Ket :

- app = contoh dari express
- METHOD = Permintaan dari `HTTP Request`
- PATH = `URL` alamat yang terdapat di server
- HANDLER = Fungsi yang dijalankan ketika routing yang di eksekusi cocok

### 3.1 Express.json()

`Express.json()` adalah fungsi middleware bawaan di Express. Metode ini digunakan untuk menerima permintaan input dengan muatan JSON.

### 3.2 Membuat API

**API** adalah `Application Programming Interface` adalah antarmuka yang dapat menghubungkan satu aplikasi dengan aplikasi lain. Dengan demikian, API bertindak sebagai perantara antara aplikasi yang berbeda, baik dalam aplikasi yang sama platform atau lintas platform.

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/3-expressjs-fundamental/index.js">
Contoh code
</a>

<br />
<br />

Berikut adalah contoh code dari beberapa response API :

```js {21-32,34-39,41-56,58-63} title=index.js
const express = require('express');

const app = express();
const port = 5000;

app.use(express.json());

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
];

// Pengimplementasian API dengan Method GET
app.get('/todos', (req, res) => {
  res.send({ data: todos });
});

app.get('/todo/:id', (req, res) => {
  const id = req.params.id;

  const data = todos.find((todo) => todo.id == id);

  res.send(data);
});

// Pengimplementasian API dengan Method POST
app.post('/todo', (req, res) => {
  const data = req.body;
  todos.push(data);
  res.send({ data: todos });
});

// Pengimplementasian API dengan Method PATCH
app.patch('/todo/:id', (req, res) => {
  const { id } = req.params;

  todos = todos.map((todo) => {
    if (todo.id == id) {
      return req.body;
    } else {
      return todo;
    }
  });

  const data = todos.find((todo) => todo.id == id);

  res.send({ data });
});

// Pengimplementasian API dengan Method DELETE
app.delete('/todo/:id', (req, res) => {
  const { id } = req.params;
  todos = todos.filter((todo) => todo.id != id);
  res.send({ data: todos });
});

app.listen(port, () => console.log(`Listening on port ${port}!`));
```

### 3.3 Akses API dengan Postman

Untuk Mencoba API yang sudah kita buat kita memerlukan sebuah aplikasi yang dapat mengakses dan menyajian data dari sebuah API. untuk itu pastikan kalian sudah memiliki aplikasi yang bernama Postman, jika belum bisa mendownload melalui link dibawah ini

**Postman :[Download](https://www.postman.com/downloads/?utm_source=postman-home)**

**Dokumentasi Penggunaan Lebih lengkap : [Here](https://learning.postman.com/docs/sending-requests/requests/)**

```link title=baseUrl
https://ebook-code-results-stage-2-backend-git-3-e-d5f17e-demo-dumbways.vercel.app
```

Berikut list route yang telah dibuat ...

| Method | Route       |
| ------ | ----------- |
| GET    | `/todos`    |
| GET    | `/todo/:id` |
| POST   | `/todo`     |
| PATCH  | `/todo/:id` |
| DELETE | `/todo/:id` |

- Buka Aplikasi Postman
- Klik +, maka akan muncul tampilan seperti berikut :
  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-1.png')} width="60%"/>
- Pastekan link yang sudah anda copy kesini

  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-2.png')} width="60%"/>

- Tentukan Method dan Route yang tertera pada ebook

  <img alt="image1-2" src={useBaseUrl('img/docs/Postman-3.png')} width="60%"/>
