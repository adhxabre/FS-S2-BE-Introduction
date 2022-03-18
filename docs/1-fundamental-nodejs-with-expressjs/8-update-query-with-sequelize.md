---
sidebar_position: 8
---

# 8. Update Query with Sequelize

import useBaseUrl from '@docusaurus/useBaseUrl';

## 8.1 Controller

Mulai dengan membuat sebuah `controller` yang menghandle proses pengubahan salah satu data seperti berikut:

```js {3,7-9,11,13-16} title=src/controllers/user.js
exports.updateUser = async (req, res) => {
  try {
    const { id } = req.params;

    const { email, password, name, status } = req.body;

    const query = `UPDATE users 
                      SET email = '${email}', password = '${password}', name = '${name}', status = '${status}'
                      WHERE id = ${id}`;

    await db.sequelize.query(query);

    res.send({
      status: "success",
      message: `Update user id: ${id} finished`,
      data: req.body,
    });
  } catch (error) {
    console.log(error);
    res.send({
      status: "failed",
      message: "Server Error",
    });
  }
};
```

<!-- Untuk mengubah data, kita membutuhkan sebuah `id` yang akan digunakan untuk menentukan data mana yang ingin diubah. Kemudian kita siapkan `query` untuk mengubah data beserta `data terbaru` dan `id` sebagai kondisinya. Lalu eksekusi `query` tersebut menggunakan `sequelize`. -->

## 8.2 Route

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada controller `updateUser` menggunakan HTTP method `patch`

```js {1,3} title=src/routes/index.js
const { updateUser } = require("../controllers/user");

router.patch("/user/:id", updateUser);
```

## 8.3 Penggunaan

Cara mengubah data menggunakan `Postman` sebagai berikut:

- Buat sebuah request baru, yang bernama `user`
- Gunakan HTTP Method: `PATCH`
- Gunakan endpoint: `/user/:id`
- Contoh endpoint mengubah data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Pilih `Body` &rarr; `raw` &rarr; ubah `Text` menjadi `JSON`
- Ketik pada bagian `Request Body` seperti berikut:

  ```json {4} title=Request
  {
    "email": "user1@mail.com",
    "password": "123456",
    "name": "user 01",
    "status": "customer"
  }
  ```

- Silakan tekan tombol `Send`, kemudian Anda akan mendapatkan `Response` seperti berikut:

  ```json title=Response
  {
    "status": "success",
    "message": "Update user id: 1 finished",
    "data": {
      "email": "user1@mail.com",
      "password": "123456",
      "name": "user 01",
      "status": "customer"
    }
  }
  ```

## 8.4 Practice

Sesuaikan code Anda dengan contoh code berikut:
<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/tree/5-expressjs-fundamental/src">
Contoh code
</a>

<br />
<br />

Berikut contoh endpoint yang dapat Anda gunakan:

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/user/3
```
