---
sidebar_position: 2
---

# 2. Nodemon

import useBaseUrl from '@docusaurus/useBaseUrl';

**Nodemon** adalah sebuah library antarmuka berbasis baris-perintah atau biasa yang kita kenal dengan (CLI) dimana `nodemon` berfungsi sebagai pengemas aplikasi `node`, memantau sistem berkas serta memulai ulang / (Re-run) aplikasi `node` jika mengalami perubahan pada code.

### 2.1 Installasi

Nodemon dapat di install dengan dua cara yaitu, `Instal utilitas secara global` maupun secara `lokal pada proyek Anda` menggunakan `npm` atau `yarn`:

#### 2.1.1 Installasi Global

Installasi `Nodemon` menggunakan `npm` :

```bash
npm install nodemon -g
```

atau menggunakan `yarn` :

```bash
yarn global add nodemon
```

#### 2.1.2 Installasi lokal

selain itu kita dapat menginstall `nodemon` secara lokal dengan menginstal spesifik project yang akan kita pasang `nodemon`. ketika kita melakukan installasi `nodemon` secara lokal kita dapat menginstall `nodemon` sebagai depedency `dev` dengan (`--dev`)

```bash
npm install nodemon --dev
```

atau menggunakan `yarn` :

```bash
yarn add nodemon --dev
```

Satu hal yang harus disadari dengan instalasi lokal adalah Anda tidak akan dapat menggunakan perintah nodemon secara langsung dari baris perintah:

```bash
$
Output
command not found: nodemon
```

Namun, Anda dapat menggunakannya sebagai bagian dari skrip npm atau dengan npx.

### 2.2 Menjalankan Nodemon

Pada tahap ini kini sudah dapat menggunakan `nodemon` sebagai perintah untuk menjalankan project `node`. Sebagai contoh kita asumsikan bahwa kita memiliki sebuah project `node` dengan konfiguasi serve pada file `server.js`, kita dapat memulai dan memantau perubahan seperti berikut :

```bash
nodemon server.js
```

selain `node` setiap kali kita melakukan perubahan pada beberapa ekstensi seperti (`.js, .mjs, .json, .coffee, atau .litcoffee`) di dalam direktori maupun subdirektori maka proses akan memulai ulang.

Kita asumsikan kita akan menuliskan suatu pesan pada file `server.js` pesan tersebut akan mengeluarkan `Port Is Connected app listening on port ${port}!`

```bash
nodemon server.js
```

Berikut adalah output dari pesan tersebut :

```bash
Output
[nodemon] 1.17.3
[nodemon] to restart at any time, enter `rs`
[nodemon] watching: *.*
[nodemon] starting  `node server.js`
`Port Is Connected` app listening on port 3000!
```

Sekarang kita asumsikan nodemon masih berjalan lalu kita akan merubah pesan tersebut menjadi `Successed app listening on port ${port}!`

```bash
Output
[nodemon] restarting due to changes...
[nodemon] starting `node server.js`
`Successed` app listening on port 3000!
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
  "main": "index.js",
  "scripts": {
    "start": "nodemon index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.1"
  },
  "devDependencies": {
    "nodemon": "^2.0.9"
  }
}
```

Jalankan nodemon dengan perintah berikut

```
npm start
```
