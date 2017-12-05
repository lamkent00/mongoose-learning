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
mongoose.connect(uri, function(ex, res){
	if(ex) return console.error(`Connection to ${uri} Error `);
	console.log('\nCONNECTION SUCCESS');
});
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
## Schema
Mongoose hỗ trợ thiết kế schema cho csdl để dễ dàng quản lý hơn.
Schema quy định các {key: value Type} http://mongoosejs.com/docs/guide.html
Định nghĩa 1 Schema như sau:
```js
var blogSchema = mongoose.Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});
```
Type gồm các dạng sau:
String
Number
Date
Buffer
Boolean
Mixed
Objectid
Array
// Xem thêm tại http://mongoosejs.com/docs/schematypes.html

> Schema cũng có option khi khởi tạo. Tuy nhiên opt để làm gì thì chưa biết. Ví dụ lụm được
```js
const schema = new Schema(
  { name: String},
  { timestamps: true });

// Hoặc option của từng Field
var schemaStudent = new mongoose.Schema({
    firstName: { type: String, required: true },
    lastName: { type: String, required: true },
    studentId: { type: String, require: true}
});
// List các Option:
autoIndex
bufferCommands
capped
collection
emitIndexErrors
id
[id](#id)
minimize
read
safe
shardKey
strict
toJSON
toObject
typeKey
validateBeforeSave
versionKey
collation
skipVersioning
timestamps
retainKeyOrder

```
## RelationShip trong Schema
Ví dụ ta có 1 Schema là link kiện có cấu trúc dạng
```js
{
    _id : ObjectID('AAAA'),
    partno : '123-aff-456',
    name : '#4 grommet',
    qty: 94,
    cost: 0.94,
    price: 3.99
}
```
Và 1 Schema là Điện thoại (được cấu thành từ N link kiện)
```js 
    name : 'left-handed smoke shifter',
    manufacturer : 'Acme Corp',
    catalog_number: 1234,
    parts : [     // array of references to Part documents
        ObjectID('AAAA'),    
        ObjectID('F17C'),   
        ObjectID('D2AA'),
        // etc
    ]
```
Thì cách để khai báo RelationShip giữa các Schema như sau:

```js
const linkkienSchema = mongoose.Schema({
    _id : mongoose.Schema.Types.ObjectId,
    partno : String,
    name : String,
    qty: Number,
    cost: Number,
    price: Number
});
const linkkienModel = mongoose.model('linkkien', linkkienSchema);
// AND
const dienthoaiSchema = mongoose.Schema({
    name: String,
    catalog_number: 1234,
    parts : [
      {
       type: mongoose.Schema.Types.ObjectId,
        ref: 'linkkienModel' 
      }
    ]
});
const dienthoaiModel = mongoose.model('dienthoai', dienthoaiSchema);
var linkkien1 = new linkkienModel ({
    partno : 'abc',
    name : 'Head phone',
    qty: 11,
    cost: 200000,
    price: 300000
});
linkkien1.save(function(ex,linkkien){
if(ex) console.log('loi: '+ex);
//Tim kiem _Id cua link kien de gan vao dien thoai
linkkienModel.find({name:'Head phone'}).select('_id'').lean().then(id=>{
    console.log('Dung _id cua linkkien de gan vao dien thoai'+ id[0]._id);
    var dienthoai1 = new dienthoaiModel({
      name: "iPhone",
      catalog_number: 1234,
      parts : id[0]._id // => sau nay no tu ref toi ID cua Linkkien
    });
  })
})
```
## Model
Sau khi khởi tạo Schema thì kiểu như là phải gán cái Schema đó cho 1 thằng Collection (bảng) để áp đặt cái Schema đó cho bảng mới đó.
Và cái Collection này là Collection MỚI (được tự động tạo nếu chưa tồn tại, hoặc được sử dụng lại nếu tồn tại tên)
```js
const blogs = mongoose.model('Ten Collection', Ten_Schema);
const blogs = mongoose.model('blogs', blogSchema);
```



