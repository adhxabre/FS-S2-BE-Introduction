---
sidebar_position: 2
---

# 2. Nodemon

import useBaseUrl from '@docusaurus/useBaseUrl';

**Nodemon** adalah sebuah library antarmuka berbasis baris-perintah atau biasa yang kita kenal dengan (CLI) dimana `nodemon` berfungsi sebagai pengemas aplikasi `node`, memantau sistem berkas serta memulai ulang / (Re-run) aplikasi `node` jika mengalami perubahan pada code.

Walaupun nodemon seringkali digunakan untuk **node**, namun sebenarnya nodemon juga bisa digunakan untuk bahasa pemrograman lain seperti **Golang** yang akan kita gunakan pada e-Book ini.

### 2.1 Installasi

Nodemon dapat di install dengan cara `Instal utilitas secara global` menggunakan `npm` atau `yarn`:

Installasi `Nodemon` menggunakan `npm` :

```bash
npm install nodemon -g
```

atau menggunakan `yarn` :

```bash
yarn global add nodemon
```

### 2.2 Menjalankan Nodemon

Pada tahap ini kita sudah dapat menggunakan `nodemon` sebagai perintah untuk menjalankan project `golang`. Sebagai contoh kita asumsikan bahwa kita memiliki sebuah project `golang` dengan konfigurasi server pada file `main.go`, kita dapat memulai dan memantau perubahan seperti berikut :

```bash
nodemon --exec go run main.go
```

selain `golang` setiap kali kita melakukan perubahan pada ekstensi seperti (`.go dan .json`) di dalam direktori maupun subdirektori maka proses akan memulai ulang.

Kita asumsikan kita akan menuliskan suatu pesan pada file `main.go` pesan tersebut akan menampilkan teks `server running localhost:5000`

```bash
nodemon --exec go run main.go
```

Berikut adalah output dari pesan tersebut :

```bash
Output
[nodemon] 2.0.19
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): *.*
[nodemon] watching extensions: go,json
[nodemon] starting `go run main.go`
`server` running localhost:5000
```

Sekarang kita asumsikan nodemon masih berjalan lalu kita akan merubah pesan tersebut menjadi `Successed running app on port 5000!`

```bash
Output
[nodemon] restarting due to changes...
[nodemon] starting `go run main.go`
`Successed` running app on port 5000!
```

untuk menjalankan `Nodemon` juga dapat di setup seperti file berikut :

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/2-expressjs-fundamental/package.json">
Contoh code
</a>

<br />
<br />

untuk menjalankan `Nodemon` kita juga dapat melakukan setup seperti file berikut :

```json {7} title=package.json
{
  "name": "backend-concept",
  "version": "1.0.0",
  "description": "",
  "main": "main.go",
  "scripts": {
    "start": "go run main.go",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "nodemon": "^2.0.9"
  }
}
```

Jalankan nodemon dengan perintah berikut

```
npm start
```
