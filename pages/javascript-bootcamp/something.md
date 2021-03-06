### **5.3 AJAX**

`A` = Asynchronous  
`J` = JavaScript  
`A` = And  
`X` = XML  
Ajax হলো একটা টেকনিক যার মাধ্যমে একটা ওয়েব পেইজ থেকে সহজেই ওয়েব সার্ভার এক্সেস করা যায়। Ajax এর মাধ্যমে behind the scene ওয়েব সার্ভার এর সাথে asynchronously ডাটা বিনিময় করে ওয়েব পেইজ আপডেট করা যায়। যার ফলে পুরো পেইজ reload না করেই পেইজের একটা অংশ আপডেট করা যায়। Ajax ব্যসিক্যালি দুটো কাজের কম্বিনেশন - XMLHttpRequest অবজেক্ট ব্যবহার করে সার্ভারে ডাটা রিকোয়েস্ট করে এবং জাভাস্ক্রিপ্ট ও HTML DOM দিয়ে ডাটাটা ব্যবহার করে।

এখনকার মর্ডান ব্রাউজার HTTP রিকোয়েস্টের জন্য XMLHttpRequest অবজেক্টের বদলে Fetch API ব্যবহার করে যার মাধ্যমে একই কাজ আরো সহজে করা যায়।

#### **XMLHttpRequest কি ?**

ওয়েব সাইট এবং সার্ভারের মাঝে ডাটা আদানপ্রদান করার জন্য XMLHttpRequest ব্যবহার করে HTTP requests পাঠানো যায়। এটি একটি ব্রাউজার অবজেক্ট। এর জন্য কিছু ধাপ আছে - প্রথমে একটা XMLHttpRequest object তৈরি করতে হবে, তারপর একটা URL ওপেন করতে হবে এবং তারপর request পাঠাতে হবে। Transaction শেষ হওয়ার পর অবজেক্টটিতে রেজাল্ট হিসেবে রেস্পন্স বডি আর HTTP স্ট্যাটাস পাওয়া যাবে। `onload`, `onprogress` আর `onerror` হচ্ছে সবচেয়ে বেশি ব্যবহৃত ইভেন্ট। বর্তমানে `fetch API` দিয়ে আরো সহজে মোটামুটি প্রায় সব কাজই করা যায় যা XMLHttpRequest দিয়ে করা যায়। চলুন কযেকটি উদাহরণ যাক।

**উদাহরণ ১:**

```js
let request = new XMLHttpRequest();

request.open("GET", “/my/url”, async=false);
// async=false দিয়ে বোঝায় request টি syncronous হবে

request.responseType = 'json';
// এভাবে রেস্পন্সটাইপ সেট করে দেওয়া যায়

request.send();

request.onload = function() {
   if (request.status != 200) {
     alert(`Error ${request.status}: ${request.statusText}`);
   } else {
     alert(`Done, got ${request.response.length} bytes`);
   }
};

request.onprogress = function(event) {
   if (event.lengthComputable) {
     alert(`Received ${event.loaded} of ${event.total} bytes`);
   } else {
     alert(`Received ${event.loaded} bytes`);
   }
};

request.onerror = function() {
   alert("Request failed");
};
```

**উদাহরণ ২:**

```js
let formData = new FormData();
formData.append("Name", "Lee");

let request = new XMLHttpRequest();
request.open("POST", "/article/post");
request.setRequestHeader('Content-Type', 'multipart/form-data');
request.send(formData);
request.onload = () =&gt; alert(request.response);
```

`উদাহরণ ৩:`

```js
let json = JSON.stringify({
  name: 'John',
  surname: 'Smith',
});

let request = new XMLHttpRequest();
request.open('POST', '/article/submit');
request.setRequestHeader('Content-type', 'application/json; charset=utf-8');
request.send(json);
```

#### **Send GET, POST, PATCH, PUT and DELETE requests:**

বোঝার সুবিধার্তে আমরা fetch API দিয়ে এই HTTP request method গুলো বোঝানোর চেষ্টা করবো। fetch API সবর্দা promise return করে বিধায় আমাদেরকে ডাটা process করার জন্য .then() ব্যবহার করতে হয়।

`Get` - এই মেথডটি দিয়ে সার্ভার থেকে ডাটা আনা বা read করা হয়।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos’, {
   method: ‘GET’
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

`Post` - এই মেথড দিয়ে সার্ভারে ডাটা পাঠানো যায়। ডাটাটা বিভিন্ন রকমের হতে পারে - অবজেক্ট বা ফাইল।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos’, {
   method: ‘POST’,
   headers: {‘content-type’: ‘application/json’},
   body: JSON.stringify({
     name: ‘Random’,
     age: 28,
     married: false
   })
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

`Put` - এই মেথডটি দিয়ে ডাটা আপডেটের কাজ করা হয়। এর mechanism হচ্ছে যে প্রথমে চেক করা হবে যে যেই রিসোর্সটি আপডেটের চেষ্টা করা হচ্ছে, তা আদৌ আছে নাকি। যদি থাকে, তাহলে সেটাকে আপডেট করা হবে। অন্যথায় নতুন রিসোর্স তৈরি করা হবে।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos/2’, {
   method: ‘PUT’,
   headers: {‘content-type’: ‘application/json’},
   body: JSON.stringify({
     name: ‘Random’,
     age: 25,
     married: true
   })
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

`Patch` - এই মেথডটি দিয়ে ডাটা আপডেটের কাজ করা হয়। মেথডটি তখনই ব্যবহার করা হয় যখন ডাটা শুধুমাত্র আপডেটের প্রয়োজন, নতুন তৈরির প্রয়োজন নেই।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos/2’, {
   method: ‘PATCH’,
   headers: {‘content-type’: ‘application/json’},
   body: JSON.stringify({
     name: ‘Random Guy’,
     age: 35,
     married: true
   })
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

`Delete` - এই মেথডটি দিয়ে স্পেসিফিক রিসোর্স delete করা হয়।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos/1’, {
   method: ‘DELETE’
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

Put, Patch এবং Delete মেথডে রিসোর্স যেটা মডিফাই করা হবে, সেটার identification value URL দিয়ে সাধারণত pass করা হয়।

### **উদাহরণ**

XMLHttpRequest দিয়ে আমরা একটি GET কল করে ডাটা নিয়ে আসার চেষ্টা করি।

```js
let request = new XMLHttpRequest();

request.open("GET", “/my/url”, async=false);
// async=false দিয়ে বোঝায় request টি syncronous হবে

request.responseType = 'json';
// এভাবে রেস্পন্সটাইপ সেট করে দেওয়া যায়

request.send();

request.onload = function() {
   if (request.status != 200) {
     alert(`Error ${request.status}: ${request.statusText}`);
   } else {
     alert(`Done, got ${request.response.length} bytes`);
   }
};

request.onprogress = function(event) {
   if (event.lengthComputable) {
     alert(`Received ${event.loaded} of ${event.total} bytes`);
   } else {
     alert(`Received ${event.loaded} bytes`);
   }
};

request.onerror = function() {
   alert("Request failed");
};
```

এখন আমরা XMLHttpRequest দিয়ে আমরা একটি POST কল এর উদাহরণ দেখবো

```js
let formData = new FormData();
formData.append("Name", "Lee");

let request = new XMLHttpRequest();
request.open("POST", "/article/post");
request.setRequestHeader('Content-Type', 'multipart/form-data');
request.send(formData);
request.onload = () =&gt; alert(request.response);
```

fetch api ব্যবহার করে সার্ভার থেকে ডাটা GET করার আমাদের সবসময় promise return করে এইজন্য আমাদেরকে ডাটা process করার জন্য .then() ব্যবহার করতে হয়।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos’, {
   method: ‘GET’
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

fetch api এর Post মেথড দিয়ে সার্ভারে ডাটা পাঠানো যায়। এখানেও রেসপন্স promise return করে। এই জন্য then ব্যবহার করতে হয়

```js
fetch(‘https://jsonplaceholder.typicode.com/todos’, {
   method: ‘POST’,
   headers: {‘content-type’: ‘application/json’},
   body: JSON.stringify({
     name: ‘Random’,
     age: 28,
     married: false
   })
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

### **এসো নিজে করি**

<ul>
	<li>fetch api দিয়ে একটা PUT রিকোয়েস্ট করতে হবে।</li>
</ul>

> URL: https://jsonplaceholder.typicode.com/todos/2/

> Payload: {
> name: ‘Random’,
> age: 25,
> married: true
> }

- fetch api দিয়ে একটা DELETE রিকোয়েস্ট করতে হবে।

> URL: https://jsonplaceholder.typicode.com/todos/1

### **সমাধান**

- fetch api দিয়ে একটা PUT রিকোয়েস্ট করতে হবে।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos/2’, {
   method: ‘PUT’,
   headers: {‘content-type’: ‘application/json’},
   body: JSON.stringify({
     name: ‘Random’,
     age: 25,
     married: true
   })
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```

- fetch api দিয়ে একটা DELETE রিকোয়েস্ট করতে হবে।

```js
fetch(‘https://jsonplaceholder.typicode.com/todos/1’, {
   method: ‘DELETE’
})
.then(response =&gt; response.json())
.then(data =&gt; console.log(data))
```