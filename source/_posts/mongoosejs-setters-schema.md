title: 'Using setter in Mongoose'
date: 2016-03-20 18:16:31
tags:
  - node.js
  - mongoose.js

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
<!--more-->

The `hash` will be called mainly on following scenarios:

* When a new doc being created.
```js
model.create({password:"12345"}) //password will be hashed
```
* When set a value to a doc.
```js
doc.password="22222" // 22222 will be hashed
```

However, this will not work for `update` query:
```js
model.update({_id:<id>},{$set:{password:"12345"}}) // password will not be hashed
model.findOneAndUpdate
model.findAndUpdate
```

For password, it is able to write beforeUpdateHook
```js
schema.methods.beforeUpdateHook=function(data){
  if (!data || this.password === data.password){
    return ;
  }
  if (data.password){
    data.password=hash(data.password);
  }
}
```