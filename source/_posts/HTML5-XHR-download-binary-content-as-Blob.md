title: How to download binary content as Blob in JS with Ajax(XHR)
date: 2017-09-01 23:32:13
tags:
  - js
  - web

---

Using `XMLHTTPRequest` (ajax) transporting data between client and server has been popular for a while. Sometimes, we want our browser to retrieve binary data from server (as `ArrayBuffer` or `Blob`) such as pdf, image, and psd files. This post will go through how to achieve it with `XMLHTTPRequest` and `jQuery`.

<!--more-->

# Download Binary using XMLHTTPRequest

For `XMLHTTPRequest`, just simply setup the `responseType` of `XHR` instance to either `arraybuffer` or `blob`. Example:

```js
var xhr=new XMLHTTPRequest();
xhr.open("GET", url, true);
//Now set response type
xhr.responseType = 'arraybuffer';
xhr.addEventListener('load',function(){
  if (xhr.status === 200){
    console.log(xhr.response) // ArrayBuffer
    console.log(new Blob([xhr.response])) // Blob
  }
})
xhr.send();

```


# Download with jQuery Ajax

`$.ajax` does not support either `arraybuffer` or `blob` as its `dataType`. Thus we need write a `beforeSend` handler:

```js
//setup ajax
$.ajaxSetup({
  beforeSend:function(jqXHR,settings){
    if (settings.dataType === 'binary'){
      settings.xhr().responseType='arraybuffer';
      settings.processData=false;
    }
  }
})

//use ajax now
$.ajax({
  url:url,
  dataType:"binary",
  success:function(data){
    console.log(data); //ArrayBuffer
    console.log(new Blob([data])) // Blob
  }
})

```

# About ResponseType
For more information about `responseType`, take a look at [this](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/responseType).

