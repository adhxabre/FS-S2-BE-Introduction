---
sidebar_position: 1
slug : /
---

# 1. Hello World with ExpressJs

import useBaseUrl from '@docusaurus/useBaseUrl';

**ExpressJs** merupakan framework dari `NodeJS` yang didesain secara fleksibel dan sederhana untuk membantu tahap pengembangan aplikasi Back-End.

Kita akan mencoba implementasi ExpressJs yang dapat menampilkan teks `Hello Wolrd`, berikut contoh codenya:

<a class="btn-example-code" href="https://github.com/demo-dumbways/ebook-code-results-stage-2-backend/blob/1-expressjs-fundamental/index.js">
Contoh code
</a>

<br />
<br />

```js title=index.js
const express = require('express');

const app = express();

const port = 5000;

app.get('/', (req, res) => {
  res.send('Hello Express!');
});

app.listen(port, () => console.log(`Listening on port ${port}!`));
```

<img alt="image1-2" src={useBaseUrl('img/docs/image-4-1.png')} width="60%"/>

<br />
<br />

<div>
<a class="btn-demo" href="https://ebook-code-results-stage-2-backend-git-1-e-bef277-demo-dumbways.vercel.app/">
Demo
</a>
</div>
