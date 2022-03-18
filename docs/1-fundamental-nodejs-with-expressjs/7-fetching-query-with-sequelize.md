---
sidebar_position: 7
---

# 7. Fetching Query with Sequelize

import useBaseUrl from '@docusaurus/useBaseUrl';

## 7.1 Controller

Mulai dengan membuat sebuah `controller` yang menghandle proses `pengambilan data` dari tabel seperti berikut:

- Mengambil semua data `user`

  ```js {1-25} title=src/controllers/user.js
  exports.getUsers = async (req, res) => {
    try {
      const query = "SELECT * FROM users";
      const data = await db.sequelize.query(query, { type: QueryTypes.SELECT });

      res.send({
        status: "success",
        data,
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

- Mengambil data `user` berdasarkan `id`

  ```js {1-25} title=src/controllers/user.js
  exports.getUser = async (req, res) => {
    try {
      const { id } = req.params;

      const data = await db.sequelize.query(
        `SELECT * FROM users WHERE id = ${id}`,
        { type: QueryTypes.SELECT }
      );

      res.send({
        status: "success",
        data,
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

## 7.2 Route

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada controller `getUsers` dan `getUser` menggunakan HTTP method `get`

```js {1,3-4} title=src/routes/index.js
const { getUsers, getUser } = require("../controllers/user");

router.get("/users", getUsers);
router.get("/user/:id", getUser);
```

## 7.3 Penggunaan

Cara mengambil data menggunakan `Postman` sebagai berikut:

- Buat dua request baru, yang bernama `user` dan `user by id`
- Gunakan HTTP Method: `GET`
- Gunakan endpoint: `/user` dan `/user/:id`
- Contoh endpoint pengambilan data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Silakan tekan tombol `Send` dan pastikan response yang Anda terima sesuai dengan data yang tersimpan pada tabel `user`

## 7.4 Practice

Sesuaikan code Anda dengan contoh code berikut:
<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/tree/5-expressjs-fundamental/src">
Contoh code
</a>

<br />
<br />

Berikut contoh endpoint yang dapat Anda gunakan:

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/users
```

```
https://ebook-code-results-stage-2-be.herokuapp.com/fundamental/api/v1/user/3
```
