title: 'mongoosejs: setters schema'
date: 2016-03-20 18:16:31
tags:
  - node.js
  - mongoose.js
categories:
  - tech
---
According to mongoose.js doc, it is able to set setters to a field on schema.
However, the [doc](http://mongoosejs.com/docs/2.7.x/docs/getters-setters.html) is not quite detailed and obselete.
Some example:
```js
var schema=new Schema({
  password:{
    type:String,
    required:true,
    set:hash
  }
})

function hash(plainPwd){
  return require("crypto").createHash("sha1").update(plainPwd).update(secret).digest("hex");
}
```

The `hash` will be called mainly on following scenarios:

* When a new doc being created.
```js
model.create({password:"12345"}) //password will be hashed
```
* When set a value to a doc.
```js
doc.password="22222" // 22222 will be hashed
```
