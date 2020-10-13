# Node.js 혼자 깨부수기 2일차

## 2. 알아두어야 할 JS

#### 2.1 ES2015+

> ES2015 (ES6)의 등장으로 JS 문법에 큰 변화가 왔다.

2015년을 기점으로 다른 언어의 장점을 본딴 편리한 기능이 많이 추가되었다.



---

###### 2.1.1 const, let

> **`var`**은 이제 **`const`**와 **`let`**으로 대체한다.

먼저 const와 let이 공통으로 가지는 특징인 블록 스코프에 대해 알아보자

```js
//크롬 콘솔 창
if(true){
	var x = 3;
}
console.log(x); //3

if(true){
	const y = 3;
}
console.log(y); //Uncaught ReferenceError: y is not defined
```

var은 **함수 스코프**를 가지므로 if문의 블록과 관계없이 접근 가능.
const와 let은 **블록 스코프**를 가지므로 블록 밖에서는 접근 불가능.

블록 스코프를 사용함으로써 호이스팅 같은 문제도 해결되고 코드 관리도 수월해졌다.

const, let과 var의 차이점? 어떤 스코프를 취하는지이다.

- **const** : 한 번 대입하면 다른 값을 대입할 수 없다. 초기화시 대입하지 않으면 에러 뜬다.
- **let** : 다른 값을 대입할 수 있다.

> > 자바스크립트를 사용할 때 한 번 초기화했던 변수에 다른 값을 대입하는 경우는 의외로 적다.
> > 기본적으로 변수 선언시 const를 사용하고 다른 값을 대입해야하는 경우가 생기면 let을 쓰자.



---

###### 2.1.2 템플릿 문자열

> ES2015 문법에 새로운 문자열이 생겼다. 

('), (") 이 아닌 백틱(`)이다.
특이하게 문자열 안에 변수를 넣을 수 있다.

```js
var num1 = 1;
var num2 = 2;
var result1 = 3;
var string1 = num1 + ' 더하기 ' + num2 + '는 \' ' + result + '\'';
console.log(string1);
```

문자열 string1의 가독성이 너무 안좋음... 심지어 (')은 \으로 이스케이프까지 해줘야 함.

```js
const num3 = 3;
const num4 = 4;
const result2 = 7;
const string2 = `${num3} 더하기 ${num4}는 '${result2}'`;
```

개 깔끔... ${변수} 형식으로 +기호 없이 문자열을 넣을 수 있다. ('), (")도 사용 가능.



---

###### 2.1.3 객체 리터럴

```js
var sayNode = function(){
	console.log('Node');
};
var es = 'ES';
var oldObject = {
	sayJS: function(){
		console.log('JS');
	},
	sayNode: sayNode,
};
oldObject[es + 6] = 'Fantastic';

oldObject.sayNode(); //Node
oldObject.sayJS(); //JS
console.log(oldObject.ES6); //Fantastic;
```

oldObject 객체에 동적으로 속성을 추가하고 있다. 위의 코드를 다음과 같이 다시 쓸 수 있다.

```js
const newObject = {
    sayJS(){
        console.log('JS');
    },
    sayNode,
    [es + 6]: 'Fantastic',
};
newObject.sayNode(); //Node
newObject.sayJS(); //JS
console.log(newObject.ES6); //Fantastic
```

`sayJS` 같은 객체의 메서드에 함수를 연결할 때 (:)을 붙이지 않아도 된다.

`sayNode` 처럼 속성명과 변수명이 같을 경우 한 번만 쓸 수 있게 되었다.
`{name: name, age: age} //ES5`
`{name, age} 		   //ES2015`

객체의 속성명을 동적으로 생성할 수 있다. newObject 안에서 [es + 6]가 속성명으로 바로 사용되고 있다.

> > 예전 문법에서는 ES6라는 속성명을 만들려면 객체 리터럴 바깥에서 [es + 6]를 해야했다.



---

###### 2.1.4 화살표 함수

> => 

```js
function add1(x, y){
	return x + y;
}

const add2 = (x, y) => {
    return x + y;
}

const add3 = (x, y) => x + y;

const add4 = (x, y) => (x + y);

function not1(x){
    return !x;
}

const not2 = x => !x;
```



기존의 function(){}도 그대로 사용 가능. 다른 점은 this 바인드 방식.

```js
var relationship1 = {
    name: 'zero',
    friends: ['nero', 'hero', 'xero'],
    logFriends: function(){
        var that = this; //relationship1을 가리키는 this를 that에 저장
        this.friends.forEach(function(friend){
            console.log(that.name, friend);
        });
    },
};
relationship1.logFriends();

const relationship2 = {
    name: 'zero',
    friends: ['nero', 'hero', 'xero'],
    logFriends(){
        this.friends.forEach(friend => {
            console.log(this.name, friend);
        });
    },
};
relationship2.logFriends();
```

> > relationship1.logFriends() 안의 forEach문에서는 function 선언문을 사용.
> > 각자 다른 함수 스코프의 this를 가지므로 that이라는 변수를 사용해서 relationship1에 간접적으로 접근하고 있다.
> >
> > relationship2.logFriends()안의 forEach문에서는 화살표 함수를 사용.
> > 바깥 스코프인 logFriends()의 this를 그대로 사용 가능. 상위 스코프의 this를 그대로 물려받은 것.
> >
> > 기본적으로 화살표를 쓰되, this를 사용해야할 경우 화살표와 function문 둘 중에 하나를 고르면 됨.



---

###### 2.1.5 비구조화 할당

> 객체와 배열로부터 속성이나 요소를 쉽게 꺼낼 수 있다.

```js
var candyMachine = {
    status: {
        name: 'node',
        count: 5,
    },
    getCandy: function(){
        this.status.count--;
        return this.status.count;
    }
};
var getCandy = candyMachine.getCandy;
var count = candyMachine.status.count;

//객체의 속성을 같은 이름의 변수에 대입하는 코드. 다음과 같이 바꿀 수 있다.

const candyMachine = {
    status: {
        name: 'node',
        count: 5,
    },
    getCandy(){
        this.status.count--;
        return this.status.count;
    }
};
const { getCandy, status: { count } } = candyMachine;
//candyMachine 객체 안의 속성을 찾아서 변수와 매칭해준다. count처럼 여러 단계 안의 속성도 찾을 수 있다.
```



배열도 비구조화가 가능하다.

```js
var array = ['nodejs', {}, 10, true];
var node = array[0];
var obj = array[1];
var bool = array[3];

//다음과 같이 변경 가능

const array = ['node.js', {}, 10, true];
const [node, obj, , bool] = array;
```



---

###### 2.1.6 프로미스

> ES2015부터는 JS와 노드의 API들이 콜백 대신 프로미스 기반으로 재구성된다.
> ~~악명높은 콜백 헬을 극복했다는 평가를 받고있다.~~

JS와 노드에서는 주로 비동기 프로그래밍을 한다. 특히 이벤트 주도 방식 때문에 콜백 함수를 자주 사용한다.

```js
const condition = true; //true면 resolve, false면 rejectc
const promise = new Promise((resolve, reject) => {
    if(condition){
        resolve('성공');
    }
    else{
        reject('실패');
    }
});

promise
.then((message) => {
    console.log(message); //성공(resolve)한 경우 실행
})
.catch((error) => {
    console.error(error); //실패(reject)한 경우 실행
});
```

> > 프로미스 내부에서 resolve가 호출되면 then이 실행되고, reject가 호출되면 catch가 실행된다.
> > resolve와 reject에 넣어준 인자는 각각 then과 catch의 매개변수에서 받을 수 있다.
> > 즉, '성공'은 message에 '실패'는 error에 들어간다.

then과 catch에서 다시 다른 then, catch를 붙일 수 있다. 이전 then의 return 값을 다음 then의 매개변수로 넘기는식인다.
프로미스를 return한 경우 프로미스가 수행된 후 다음 then이나 catch가 호출 된다.

```js
promise
.then((message) => {
	return new Promise((resolve, reject) => {
        resolve(message);
    });
})
.then((message2) => {
    return new Promise((resolve, reject) => {
        resolve(message2);
    });
})
.then((message3) => {
    console.log(message3);
})
.catch((error) => {
    console.log(error);
});
```

> > 처음 then에서 message를 resolve하면 다음 then에서 받을 수 있다.
> > 여기서 다시 message2를 resolve했으므로 다음 then에서 message3를 받았다.



이것을 활용해서 콜백을 프로미스로 바꿀 수 있다.

```js
function findAndSaveUser(Users){
    Users.findOne({}, (err, user) => { //첫 번째 콜백
        if(err){
            return console.error(err);
        }
        user.name = 'zero';
        user.save((err) => { //두 번째 콜백
            if(err){
                return console.error(err);
            }
            Users.findOne({gender:'m'}, (err, user) => { //세 번째 콜백
               //생략 
            });
        });
    });
}

//콜백 함수가 3번 중첩되어있다. 
//콜백 함수가 나올 때마다 코드의 깊이가 깊어진다. 
//각 콜백 마다 에러도 따로 처리해야 한다. 
//콜백 함수를 프로미스로 바꾸면 다음과 같다.

function findAndSaveUser(Users){
    Users.findOne({})
    .then((user) => {
        user.name = 'zero';
        return user.save();
    })
    .then((user) => {
        return user.findOne({gender:'m'});
    })
    .then((user) => {
        //생략
    })
    .catch(err => {
        console.error(err);
    });
}

//코드의 깊이가 더 이상 깊어지지 않음.
//then 메서드들은 순차적으로 실행 됨.
//에러도 마지막 catch에서 한번에 처리 가능.
//하지만 모든 콜백 함수를 위와 같이 바꿀 수 있는 것은 아니다. 메서드가 프로미스 방식을 지원해야 한다.
//예제의 코드는 findOne과 save 메서드가 내부적으로 프로미스 객체를 가지고 있어서 가능한 것.
```



프로미스 여러 개를 한번에 실행할 수 있는 방법이 있다. `Promise.all`을 사용하면 된다.

```js
const promise1 = Promise.resolve('성공1');
const promise2 = Promise.resolve('성공2');
Promise.all([promise1, promise2])
.then((result) => {
    console.log(result); //['성공1', '성공2']
})
.catch((error) => {
    console.error(error);
});

//Promise.resolve는 즉시 resolve하는 프로미스를 만드는 방법이다. Promise.reject도 있다.
//프로미스가 여러개일 때 Promise.all에 넣으면 모두 resolve될 때까지 기다렸다가 then으로 넘어감.
//프로미스 중 하나라도 reject가 되면 catch로 넘어간다.
```



---

###### 2.1.7 async/await

> async/await 문법은 프로미스를 사용한 코드를 한 번 더 깔끔하게 줄여준다. (노드 버전 7.6부터 지원)

```js
async function findAndSaveUser(Users){
    let user = await Users.findOne({});
    user.name = 'zero';
    user = await user.save();
    user = await Users.findOne({gender:'m'});
    //생략
}

//일반 함수 대신 async function으로 교체, 프로미스 앞에 await를 붙임.
//이제 함수는 해당 프로미스가 resolve될 때까지 기다린 뒤 다음 로직으로 넘어간다.

//에러 처리 부분이 없으므로 위의 코드를 try로 묶고 catch를 통해 에러를 처리한다.
async function findAndSaveUser(Users){
    try{
        let user = await Users.findOne({});
        user.name = 'zero';
        user = await user.save();
        user = await Users.findOne({gender:'m'});
        //생략
    }
    catch(error){
        console.error(error);
    }
}

//화살표 함수도 async와 같이 사용 가능하다.
const findAndSaveUser = async (Users) => {
    try{
        let user = await Users.findOne({});
        user.name = 'zero';
        user = await user.save();
        user = await Users.findOne({gender:'m'});
        //생략
    }
    catch(error){
        console.error(error);
    }
}

//for문을 async/await을 같이 써서 Promise.all을 대체할 수 있다. (노드 버전 10부터 지원)
const promise1 = Promise.resolve('성공1');
const promise2 = Promist.resolve('성공2');
(async () => {
    for await (promise of [promise1, promise2]){
        console.log(promise);
    }
})();
```



---

#### 2.2 프런트엔드 JS

> HTML에서 script 태그 안에 쓰인다.

###### 2.2.1 AJAX

> 비동기적 웹 서비스를 개발하기 위한 기법. 
> 쉽게 말해 페이지 이동 없이 서버에 요청을 보내고 응답받는 기술.

이름에 XML이 들어가 있지만 꼭 XML을 사용해야 하는 건 아니다.
**<u>요즘은 JSON을 많이 사용한다.</u>**

보통 AJAX 요청은 jQuery나 axios 같은 라이브러리를 이용한다.

```html
<script>
	var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function(){ //요청에 대한 콜백
        if(xhr.readyState === xhr.DONE){ //요청이 완료되면
            if(xhr.status === 200 || xhr.status === 201){ //응답 코드가 200이나 201이면
                console.log(xhr.responseText); //서버에 보내주는 값
            }
            else{
                console.error(xhr.responseText);
            }
        }
    };
    xhr.open('GET', 'https://www.zerocho.com/api/get'); //메서드와 주소 설정
    xhr.send(); //요청 전송
</script>
```



`onreadystatechange` 대신 `onload`와 `onerror`로 성공과 실패를 구별해도 된다.

```html
<script>
	var xhr = new XMLHttpRequest();
    xhr.onload = function(){
        if(xhr.status === 200 || xhr.status === 201){
            console.log(xhr.responseText);
        }
    };
    xhr.onerror = function(){
        console.error(xhr.responseText);
    };
    xhr.open('GET', 'https://www.zerocho.com/api/get'); //메서드와 주소 설정
    xhr.send(); //요청 전송
</script>
```



POST요청의 경우 JSON 데이터를 보낸다.

```html
<script>
	var xhr = new XMLHttpRequest();
    var data = {
        name: 'zerocho',
        birth: 1994,
    };
    xhr.onreadystatechange = function(){
        if(xhr.readyState === xhr.DONE){
            if(xhr.status === 200 || xhr.status === 201){
                console.log(xhr.responseText);
            }
            else{
                console.error(xhr.responseText);
            }
        }
    };
    xhr.open('POST', 'https://www.zerocho.com/api/post/json');
    xhr.setRequestHeader('Content-Type', 'application/json'); //콘텐츠 타입을 json으로
    xhr.send(JSON.stringify(data)); //데이터를 동봉해 전송
</script>
    
```

전체 구조는 비슷한데, `xhr.send` 메서드에 데이터를 넣어 보내는 것이 다르다.
`xhr.setRequestHeader` 메서드를 통해 서버로 보내는 컨텐츠가 JSON 형식임을 알릴 수 있다.



---

###### 2.2.2 FormData

> HTML <form> 태그의 데이터를 동적으로 제어할 수 있는 기능.

```html
<script>
	var formData = new FormDate(); // FormData 객체 생성
    formData.append('name', 'zerocho');
    formData.append('item', 'orange');
    formData.append('item', 'melon');
    
    formData.has('item'); //true
    formData.has('money'); //false
    
    formData.get('item'); //orange
    formData.getAll('item') //['orange', 'melon']
    
    formData.append('test', ['hi', 'zero']);
    formData.get('test'); //hi, zero
    formData.delete('test');
    formData.get('test'); //null
    
    formData.set('item', 'apple');
    formData.getAll('item'); //['apple']
</script>
```

- **.append** : 키-값 형식의 데이터를 저장할 수 있다. 여러 번 사용해서 키 하나에 여러 개의 값을 추가해도 된다.
- **.has** : 주어진 키에 해당하는 값이 있는지 여부를 알려준다.
- **.get** : 주어진 키에 해당하는 값을 가져온다. 
- **.delete** : 현재 키를 제거한다.
- **.set** : 현재 키를 수정한다.



AJAX로 FormData를 서버에 보내는거까지 해보자.

```html
<script>
	var xhr = new XMLHttpRequest();
    var formData = new FormData();
    formData.append('name', 'zerocho');
    formData.append('birth', 1994);
    xhr.onreadystatuchange = function(){
        if(xhr.readyState === xhr.DONE){
            if(xhr.status == 200 || xhr.status === 201){
                console.log(xhr.responseText);
            }
            else{
                console.error(xhr.responseText);
            }
        }
    };
    xhr.open('POST', 'https://www.zerocho.com/api/post/formdata');
    xhr.send(formData); //폼 데이터 객체 전송
</script>
```



> > ==, === 의 차이
> > == : 값 비교
> > === : 값 + 데이터 타입 비교



---

###### 2.2.3 encodeURIComponent, decodeURIComponent

> 한글 처리용 인코더, 디코더
> AJAX 요청 시, 서버가 한글 주소를 이해하지 못하는 경우를 해결해준다.

```html
<script>
	var xhr = new XMLHttpRequest();
    //
    //생략
    //
    xhr.open('GET', 'https://www.zerocho.com/api/serch/' + encodeURIComponent('노드'));
    //한글 주소 인코딩 후 전송, 한글 부분만 encodeURIComponent메서드로 감싸주면 된다.
    //서버라면 decodeURIComponent('인코딩된 한글')을 사용하면 다시 한글로 바뀐다.
    xhr.send();
</script>
```



---

###### 2.2.4 data attribute와 dataset

> HTML과 관련된 데이터를 저장하는 방법.

노드를 웹 서버로 사용하는 경우, 클라이언트(프론트엔드)와 빈번하게 데이터를 주고받게 된다.
이때 서버에서 보내준 데이터를 프론트엔드 어디에 넣어야 할지 고민된다.

**프론트엔드에 데이터를 내려보낼 때 첫 번째로 고려해야 할점은 <u>보안</u>이다.**
**'클라이언트를 믿지 말라'**는 말이 있을 정도로 프론트엔드에 민감한 데이터를 내려보내는 것을 실수이다.

보안과 관련없는 데이터들은 자유롭게 프론트엔드로 내려보내도 된다.
JS 변수에 저장해도 되지만... HTML5에도 데이터를 저장하는 공식적인 방법이 `data attribute`이다.

```html
<ul>
    <li data-id="1" data-user-job="programmer">Zero</li>
    <li data-id="2" data-user-job="designer">Nero</li>
</ul>
<script>
	console.log(document.querySelector('li').dataset);
    //{id: '1', userJob: 'programmer'}
</script>
```

HTML 태그의 속성으로 data- 접두어를 넣어준다. 얘네들이 `data attribute`이다.

`data attributr`의 장점은 **JS로 쉽게 접근할 수 있다**는 것!!

반대로 `dataset`에 데이터를 넣어도 HTML 태그에 반영된다.

```html
<script>
    dataset.monthSalary = 1000; //data-month-salary="1000" 속성이 생긴다.
</script>
```





# 끝.

