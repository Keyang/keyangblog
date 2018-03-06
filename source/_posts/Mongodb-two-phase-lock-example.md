title: Mongodb two phase lock example (with mongoose)
date: 2016-03-17 22:34:09
tags:
  - mongodb

---
Mongodb needs extra collection for two phase lock.
```js
var transactionSchema=new Schema({
  lastModified:Date,
  status:{
    type:String,
    default:"new",
    index:true
  },
  data:{}
})
```
The transaction life cycle is `new->pending->applied->done`
<!-- more -->

## New
The transaction is just created and waiting to be started.
Once the transaction is created, the `data` field would contain all information. e.g. source, destination, cash amount.

## Pending
The transaction has started but some collection(s) have not finished its operation.

When process pending transaction, there are always a series of operations on all related collections and documents. For example, apply a promotion code to an account needs change both promotion collection and user account collection. The changes can happen in parallel or sequence which do not matter.

The only important thing is the operation will only affect documents that has not this pending transaction and the operation on the document is done with pushing the pending transaction to the doc.

This will need following design:

* the collection should have a `pendingTransaction` field which record all current pending transactions on a document.
* Developer should use `update` method to update target fields and the pendingTransaction **at the same time**.

Example:
```js
var UserSchema=new Schema({
    money:Number,
    pendingTransaction:[ObjectId]
});

//Process transaction
var transaction;
UserModel.findAndUpdate({ //the query find specified user and gurantee it has not been applied before
  $ne:{
    pendingTransaction:transaction._id
  },
  _id:transaction.data.targetUser
},{ //update to increase the money and push the transaction to pendingTransaction to mark current user has been applied with the transaction so next application will skip this user account.
  $push:{
    pendingTransaction:transaction._id
  },
  $inc:{
    money:transaction.data.amount
  }
})

```

After all operations finished, transaction will be marked as `applied`. If any problems happens during the transaction (e.g. system crashes), the next run through the transaction will 'skip' the documents that already being applied and continue applying other operations until the transaction is marked as `applied`.

## Applied
The transaction has made modifications to all related documents. This stage is to clear the pendingTransaction field of those documents ($pull the transaction id from the field.)

## Done
The transaction has completed

# Roll Back
The lastModified field in transaction can be used to judge if a transaction needs to roll-back. (e.g. the lastModified was 5 mins ago but still not successful)

Only `pending` transactions need to be rolled-back as it means there are unfinished changes and some changes have been applied. The roll-back operation is a reverse operation of application operation. e.g. for user document with transaction id in pendingTransaction, pull transaction id from pendingTransaction and decrease the money amount.
