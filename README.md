# mongoose-learning
Tự học mongoose - Sưu tầm và mò mẫm

## Cài đặt
Mongoose đòi hỏi phải cài MongoDB trước đã.<br>
> First be sure you have MongoDB and Node.js installed.

Đề cài MongoDB thì phải tải từ http://www.mongodb.org/downloads -> chọn phiên bản phù hợp rồi cài.<br>
Để cài đặt mongoose thì dùng lệnh
> $ npm install mongoose

## Kết nối
Kiểu gì trong file js cũng phải có connect đầu tiên.
```js
mongoose = require('mongoose');
const uri = process.env.MONGODB_URI || 'mongodb://localhost:27017/database';
mongoose.connect(uri);
```
hoặc có kiểu hỗ trợ kết nối có Auth
```js
mongoose.connect('mongodb://user:pass@localhost:port/database');

// replica sets
var uri = 'mongodb://user:pass@localhost:port,anotherhost:port,yetanother:port/mydatabase';
mongoose.connect(uri);

// with options
mongoose.connect(uri, options);

// connecting to multiple mongos
var uri = 'mongodb://hostA:27501,hostB:27501';
var opts = { mongos: true };
mongoose.connect(uri, opts);
```


