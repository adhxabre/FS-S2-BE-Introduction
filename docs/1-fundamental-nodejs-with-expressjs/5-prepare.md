---
sidebar_position: 5
---

# 5. Prepare Database

import useBaseUrl from '@docusaurus/useBaseUrl';

## 5.1 Installation

- Sequelize

  ```bash
  npm install sequelize
  ```

- Migration tools

  ```bash
  npm install sequelize-cli
  ```

- PostgreSQL Package

  ```bash
  npm install pg
  ```

- Init Sequelize on your project

  ```bash
  npx sequelize init
  ```

  Setelah menjalankan perintah diatas, maka terdapat tambahan file & folder pada aplikasi `express-api-app` sebagai berikut:

  ```text {2-7}
  express-api-app
  ┣ config
  ┃ ┗ config.json
  ┣ migrations
  ┣ models
  ┃ ┗ index.js
  ┣ seeders
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

## 5.2 Database

- Buat sebuah database terlebih dahulu (Contoh: `course-express`)
- Atur konfigurasi koneksi antara aplikasi `express-api-app` dengan `database`

  ```json {2-8} title=config/config.json
  {
    "development": {
      "username": "root",
      "password": "root",
      "database": "course-express",
      "host": "localhost",
      "port": 5432,
      "dialect": "PostgreSQL"
    },
    "test": {
      "username": "root",
      "password": null,
      "database": "database_test",
      "host": "127.0.0.1",
      "dialect": "mysql"
    },
    "production": {
      "username": "root",
      "password": null,
      "database": "database_production",
      "host": "127.0.0.1",
      "dialect": "mysql"
    }
  }
  ```

- Buat sebuah tabel beserta attribute didalamnya menggunakan `sequelize-cli` seperti berikut:

  ```bash
  npx sequelize-cli model:generate --name user --attributes email:string,password:string,name:string,status:string
  ```

- Lakukan `migration` tabel `user` menggunakan perintah berikut:

  ```bash
  npx sequelize db:migrate
  ```

- Jika anda ingin melakukan `migration` ke database online (heroku), silakan buat file `Procfile` dan tambahkan code berikut:

  ```title=Procfile
  Release: node_modules/.bin/sequelize db:migrate:undo:all; node_modules/.bin/sequelize db:migrate; node_modules/.bin/sequelize db:seed:all;

  web: node index.js
  ```

## 5.3 Database Connection

```js title=src/database/connection.js
const Sequelize = require('sequelize');
const db = {};
const sequelize = new Sequelize('nama_database...', 'user...', 'password...', {
  host: 'host...',
  port: 'port...',
  dialect: 'PostgreSQL',
  logging: console.log,
  freezeTableName: true,

  pool: {
    max: 5,
    min: 0,
    acquire: 30000,
    idle: 10000,
  },
});

db.sequelize = sequelize;

module.exports = db;
```
