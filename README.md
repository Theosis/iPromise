iPromise
========

#Small powerful and no dependencies promises

```javascript

Promise('do something').done(function(){
  ...
}).fail(function(){
  ...
}).then(function(data){
  ...
},function(error){
  ...
}).always(function(value){
  ...
}).setTimeout(1000, 'the promise has been rejected.');


/// somewhere else in the code...
///since we gave the Promise an id we can get it any where.
Promise('do something').resolve('the promise has been successfully resolved. we are done!');

```

##Things to know before you start.
```javascript
  //create a new promise factory
  var PromiseA = IPromise();
  var PromiseB = IPromise();
  
  //promise can have an id.
  //when you call the promise constructor with a string it will return the same promise.
  PromiseA('my promise') === PromiseA('my promise') // true
  
  //promises don't share the same namespace.
  PromiseA('my promise') === PromiseB('my promise') // false
  
  //if you don't pass an id a new promise will be created each time.
  PromiseA() === PromiseA() // false

```


##Promising fetch resources with ajax demo.

```javascript
var Promise = IPromise();
  
//very simple ajax request function
function ajax_get(url){
  //create a promise with the url as an id
  var promise = Promise(url);
  var xhr = new XMLHttpRequest();
  xhr.open('GET', url, true);
  xhr.onload = function(e){
    //fulfill the promise with the data
    promise.resolve(xhr.responseText);
  }    
  xhr.error = function(e){
     //break the promise with the error
     promise.reject(e);
  }
  xhr.send();
  //in this library you dont have to return your promises you can reffer to them by their id`s 
  return promise;
}

//using Promise to fetch resources.

Promise.when('resources', [
  ajax_get('url/data.json'),
  ajax_get('url/lang-en.json'),
  ajax_get('url/template.html')
]);


/// somewhere else in the code...

Promise('resources').done(function(data, lang, template){
  ...
}).fail(function(error){
  ...
});


```

##the same resources example but different

```javascript

var Promise = IPromise();

function ajax_get(url){
  var xhr = new XMLHttpRequest();
  xhr.open('GET', url, true);
  xhr.onload = function(e){
    Promise(url).resolve(xhr.responseText);
  }    
  xhr.error = function(e){
    Promise(url).reject(e);
  }
  xhr.send();  
}

//dummy render non blocking ui function
function renderNonBlocking(data, lang, template){
  var renderPromise = Promise();
  setTimeout(function(){
    renderPromise.resolve('done after 1s')
  },1000);
  return renderPromise; 
}

ajax_get('url/data.json');
ajax_get('url/lang-en.json');
ajax_get('url/template.html');
ajax_get('url/hugeFile.json');


Promise.when('resources', [
  Promise('url/data.json'),
  Promise('url/lang-en.json'),
  Promise('url/template.html')
]).done(function(data, lang, template){
  return renderNonBlocking(data, lang, template);
}).done(function(msg){
  console.log(msg) // 'done after 1s'
});


//get the data any time you want!
Promise('url/data.json').always(function(value){
  if(value && value.status === 'rejected'){...}
  ...
});


```
