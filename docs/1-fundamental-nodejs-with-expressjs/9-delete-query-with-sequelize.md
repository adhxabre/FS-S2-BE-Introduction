---
sidebar_position: 9
---

# 9. Delete Query with Sequelize

import useBaseUrl from '@docusaurus/useBaseUrl';

## 9.1 Controller

Mulai dengan membuat sebuah `controller` yang menghandle proses penghapusan salah satu data seperti berikut:

```js {3,5,7,9-12} title=src/controllers/user.js
exports.deleteUser = async (req, res) => {
  try {
    const { id } = req.params;

    const query = `DELETE FROM users WHERE id = ${id}`;

    await db.sequelize.query(query);

    res.send({
      status: "success",
      message: `Delete user id: ${id} finished`,
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

## 9.2 Route

Tahap akhir yang perlu kita lakukan adalah buat sebuah route untuk menjalakan atau mengakases fungsi pada controller `deleteUser` menggunakan HTTP method `delete`

```js {1,3} title=src/routes/index.js
const { deleteUser } = require("../controllers/user");

router.patch("/user/:id", deleteUser);
```

## 9.3 Penggunaan

Cara menghapus data menggunakan `Postman` sebagai berikut:

- Buat sebuah request baru, yang bernama `user`
- Gunakan HTTP Method: `DELETE`
- Gunakan endpoint: ` `/user/:id`
- Contoh endpoint pengambilan data user berdasarkan `id`:
  ```
  http://localhost:5000/api/v1/user/1
  ```
  \*Angka `1` merupakan `id` dari `user`
- Silakan tekan tombol `Send`, kemudian Anda akan mendapatkan `Response` seperti berikut:

  ```json title=Response
  {
    "status": "success",
    "message": "Delete user id: 1 finished"
  }
  ```

## 9.4 Practice

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
