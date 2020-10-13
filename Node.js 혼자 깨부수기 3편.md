# Node.js 혼자 깨부수기 3일차

## 3. 노드 기능 알아보기

#### 3.1 REPL 사용하기

> Read Eval Print Loop
> 읽고 해석하고 결과물을 반환하고 종료할 때까지 반복한다.

JS는 스크립트 언어이므로 미리 컴파일을 하지 않아도 즉석에서 코드를 실행할 수 있다.

> > VS Code에서 ctrl + (`) 하면 터미널을 켤 수 있다.
> > 터미널에 node 입력후 JS코드를 입력하자

![image-20191213100309715](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213100309715.png)

REPL이 동작한 것을 볼 수 있다.
REPL을 종료하려면 `ctrl + c`를 두 번 누르거나, REPL 창에 `.exit`를 입력하면 된다.



#### 3.2 JS 파일 실행하기

![image-20191213101914416](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213101914416.png)



---

#### 3.3 모듈로 만들기

> 노드는 코드를 모듈로 만들 수 있다는 점에서 브라우저의 JS와는 다르다.

- **모듈** : 특정한 기능을 하는 함수나 변수들의 집합.

보통 파일 하나가 모듈 하나가 된다. 파일별로 코드를 모듈화할 수 있어 관리하기 편한다.

> > **브라우저와 모듈**
> >
> > 2015년 JS에도 import/export라는 모듈 개념이 도입되었다. 하지만 브라우저에는 구현되지 않아서 사용할 수 없었다. 
> > 크롬60버전부터 브라우저에서도 모듈을 사용할 수 있게 되었다. 앞으로 더 많은 브라우저가 모듈 시스템을 지원할 것으로 보인다.



```js
//var.js
const odd = '홀수';
const even = '짝수';

module.exports = {
    odd,
    even,
};
```

변수 두개 선언 후 `module.exports`에 변수들을 담은 객체를 대입했다.
다른 파일에서 이 파일을 불러오면 `module.exports`에 대입된 값을 사용할 수 있다.



```js
//func.js
const{odd, even} = require('./var'); 

function checkOddOrEven(num){
    if(num % 2){
        return odd;
    }
    else{
        return even;
    }
}

module.exports = checkOddOrEven; 
```

`require`함수 안에 불러올 모듈의 경로를 적어준다. 다른 폴더의 모듈도 사용할 수 있다.
`module.exports`에는 객체뿐만 아니라 함수나 변수를 대입해도 된다.



```js
//index.js
const {odd, even} = require('./var');
const checkNumber = require('./func');

function checkStringOddOrEven(str){
    if(str.length % 2){
        return odd;
    }
    return even;
}

console.log(checkNumber(10));
console.log(checkStringOddOrEven('hello'));
```

![image-20191213103731373](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213103731373.png)

노드에서는 대부분의 파일을 모듈로 사용하고 있으므로, 모듈 사용법을 꼭 알고가자.



> >**ES2015 모듈**
> >
> >ES2015가 도입되면서 JS도 자체 모듈 시스템 문법이 생겼다.
> >func.js를 ES2015 모듈 스타일로 바꾸면
> >
> >```js
> >//func.mjs
> >import {odd, even} from './var';
> >
> >function checkOddOrEven(num){
> >    //중략
> >}
> >
> >export default checkOddOrEven;
> >```
> >
> >이런식인데...
> >
> >`require` -> `import`
> >`module.exports` -> `export default` 로 바뀌고
> >확장자도 `.js` -> `.mjs`로 바뀌었다.
> >노드 버전9 부터 ES2015의 모듈 시스템을 사용할 수 있다.
> >
> >하지만, 실행 시 `node --experimental-modules[파일명]`처럼 특별한 옵션을 붙여주어야 하는 제한이 있다.



---

#### 3.4 노드 내장 객체

> 노드는 기본적인 내장 객체와 내장 모듈을 제공한다.

뭐... 그렇다. 브라우저의 `window`객체와 비슷한거라 생각하면 된다.
`require`함수, `module`객체도 다 내장 객체이다.



###### 3.4.1 global

> 브라우저의 `window`같은 **전역 객체**

전역 객체이므로 모든 파일에서 접근 가능하다.
사실 생략가능하다. `require`도 원래는`global.require`이다. `console`도 마찬가지.



> > **노드의 window, document객체**
> >
> > 노드에는 DOM이나 BOM이 없어 `window`와 `document` 객체를 사용할 수 없다. 사용하면 에러난다.



`global` 객체 내부에는 매우 많은 속성이 들어있다. 내부를 보려면 REPL을 이용해야한다**.**
노드 버전에 따라 콘솔 내용이 다를 수 있다.

전역객체라는 점을 이용하여 간단한 데이터를 파일끼리 공유할 때 사용하기도 한다.

```js
//globalA.js
module.exports = () => global.message;

//globalB.js
const A = require('./globalA');

global.message = '안녕';
console.log(A());
```

![image-20191213111556422](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213111556422.png)



> > **global객체의 남용**
> >
> > `global`객체를 이용한 파일간 데이터 공유를 너무 남용하진 말자.
> > 프로그램의 규모가 커질수록 유지보수의 어려움을 겪게 된다. (어떤 파일에서 global 객체에 값을 대입했는지 찾기 힘듬)
> > 모듈 형식으로 만들어서 명시적으로 값을 불러와 사용하는 것이 좋다.



---

###### 3.4.2 console

> 노드에서는 `window`대신 `global` 객체 안에 들어있다.
> 브라우저의 `console`과 거의 비슷하다.
> ~~최고의 디버거~~

```js
//console.js
const string = 'abc';
const number = 1;
const boolean = true;
const obj = {
    outside: {
        inside: {
            key: 'value',
        },
    },
};

console.time('전체 시간');
console.log('평범한 로그');
console.log(string, number, boolean);
console.error('에러 메세지는 console.error에 담자');

console.dir(obj, {colors: false, depth: 2});
console.dir(obj, {colors: true, depth: 1});

console.time('시간 측정');
for(let i = 0; i < 100000; i++){
    continue;
}
console.timeEnd('시간 측정');

function b(){
    console.trace('에러 위치 추적');
}
function a(){
    b();
}
a();

console.timeEnd('전체 시간');
```

![image-20191213112902358](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213112902358.png)

- `console.time(레이블)` : `console.timeEnd(레이블)`과 대응되어 같은 레이블을 가진 time과 timeEnd사이의 시간 측정.
- `console.log(내용)` : 평범한 로그를 콘솔에 표시한다. 여러 내용을 동시에 표시할 수도 있다.
- `console.error(에러 내용)` : 에러를 콘솔에 표시한다.
- `console.dir(객체, 옵션)` : 객체를 콘솔에 표시할 때 사용한다. 첫 번째 인자로 표시할 객체를 넣고, 두 번째 인자로 옵션을 넣는다. 옵션의 `colors`를 `true`로 하면 콘솔에 색이 추가 되어 보기 편해진다. `depth`는 객체 안의 객체를 몇 단계까지 보여줄지를 결정한다. 기본값은 2이다.
- `console.trace(레이블)` : 에러가 어디서 발생했는지 추적할 수 있게 해준다. 보통은 에러 발생 시 에러 위치를 알려주므로 자주 사용하지는 않지만, 위치가 나오지 않는다면 사용할만하다.

실제로는 더 많은 메소드가 있다.



---

###### 3.4.3 타이머

> 얘도 마찬가지로 `window` 대신 `global` 객체에 있다.

- `setTimeout(콜백 함수, 밀리초)` : 주어진 ms 이후의 콜백 함수를 실행한다.
- `setInterval(콜백 함수, 밀리초)` : 주어진 ms마다 콜백 함수를 반복 실행한다.
- `setImmediate(콜백 함수)` : 콜백 함수를 즉시 실행합니다.

이 타이머 함수들은 모두 아이디를 반환한다. 아이디를 사용하여 타이머를 취소할 수 있다.

- `clearTimeout(아이디)` : `setTimeout`을 취소한다.
- `clearInterval(아이디)` : `setInterval`을 취소한다.
- `clearImmediate(아이디)` : `setImmediate`를 취소한다.



```js
//timer.js
const timeout = setTimeout(() => {
    console.log('1.5초 후 실행');
}, 1500);

const interval = setInterval(() => {
    console.log('1초마다 실행');
}, 1000);

const timeout2 = setTimeout(() => {
    console.log('실행되지 않습니다.');
}, 3000);

setTimeout(() => {
    clearTimeout(timeout2);
    clearInterval(interval);
}, 2500);

const immediate = setImmediate(() => {
    console.log('즉시 실행');
});

const immediate2 = setimmediate(() => {
    console.log('실행되지 않습니다.');
});

clearImmediate(immediate2);
```

![image-20191213130007373](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213130007373.png)



> > **setImmediate(콜백)과 setTimeout(콜백, 0)**
> >
> > 콜백 함수는 이벤트 루프를 거친 뒤 즉시 실행된다.
> >
> > 파일 시스템 접근, 네트워킹 같은 I/O 작업의 콜백 함수 안에서 타이머를 호출하는 경우 `setImmediate`는 `setTimeout(콜백, 0)`보다 먼저 실행된다.
> >
> > 하지만, `setImmediate`가 항상 먼저 실행되는 것은 아니니 헷갈리지 않도록 `setTimeout(콜백, 0)`은 사용하지 말자.



---

###### 3.4.4 \_\_filename, \_\_dirname

> 노드는 `__filename`, `__dirname`이라는 키워드로 경로에 대한 정보를 제공한다.

노드에서는 파일 사이에 모듈관계가 있는 경우가 많아 현재파일의 경로나 파일명을 알아야 한다.
파일에 `__filename`과 `__dirname`을 넣어두면 실행 시 현재 파일명과 파일 경로로 바뀐다.

```js
//filename.js
console.log(__filename);
console.log(__dirname);
```

![image-20191213131038150](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213131038150.png)



---

###### 3.4.5 module, exports

> `module` 객체 말고, `exports` 객체로도 모듈을 만들 수 있다.

```js
//var.js
exports.odd = '홀수';
exports.even = '짝수';
```

`module.exports`로 한 번에 대입하는 대신, 각각의 변수를 `exports` 객체에 하나씩 넣었다.
동일하게 동작하는 이유는 같은 객체를 참조하기 때문.
실제로 `console.log(module.exports === exports)`하면 `true`가 나옴.



> > > **`exports` 객체 사용 시**
> > >
> > > `module.exports`와의 참조 관계가 깨지지 않도록 주의해야 한다.
> > > `module.exports`에는 어떤 값이든 대입해도 되지만, `exports`에는 반드시 객체처럼 속성명과 속성값을 대입해야 한다.
> > > `exports`에 다른 값을 대입하면 객체의 참조 관계가 끊겨 더 이상 모듈로 기능하지 않는다.
> > >
> > > ---
> > >
> > > `exports`를 사용할 때는 객체만 사용할 수 있다.
> > > 따라서 `module.exports`에 함수를 대입한 경우 `exports`로 바꿀 수 없다.
> > >
> > > ---
> > >
> > > 참조 관계가 있으므로, 한 모듈에 `exports`와 `module.exports`를 동시에 사용하지 않는 것이 좋다.



---

###### 3.4.6 process

> `process` 객체는 현재 실행되고 있는 노드 프로세스에 대한 정보를 담고 있다.

`process` 객체를 사용할 일은 많지 않다.
일반적으로 운영체제나 실행 환경별로 다른 동작을 하고 싶을 때 사용한다.



- **process.env**

  > REPL에 입력하면 매우 많은 정보가 출력되는데, 이 정보들은 시스템의 환경 변수임을 알 수 있다.

  서비스의 중요한 키를 저장하는 공간으로도 사용된다.
  서버나 DB의 비밀번호와 API키를 코드에 직접 입력하는 것은 위험하다.
  혹여나 서비스가 해킹당해 코드가 유출되었을 때 비밀번호가 코드에 남아 있어 추가 피해가 발생할 수 있다.

  따라서 중요한 비밀번호는 다음과 같이 `process.env`의 속성으로 대체한다.
  `const secretId = process.env.SECRET_ID;`
  `const secretCode = process.env.SECRET_CODE;`
  그리고 `process.env`에 직접 `SECRET_ID`와 `SECRET_CODE`를 넣어주면 된다.

  

- **process.nextTick(콜백)**

  > 이벤트 루프가 다른 콜백 함수들보다 `nextTick`의 콜백 함수를 우선으로 처리하도록 만든다.

  ```js
  setImmediate(() => {
      console.log('immediate');
  });
  process.nextTick(() => {
      console.log('nextTick');
  });
  setTimeout(() => {
      console.log('timeout');
  }, 0);
  Promise.resolve().then(() => console.log('promise'));
  ```

  ![image-20191213133451056](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213133451056.png)

  

  `process.nextTick`은 `setImmediate`, `setTimeout`보다 먼저 실행된다.
  코드 맨 밑에 `Promise`를 넣은 것은 `resolve`된 `Promise`도 `nextTick`처럼 다른 콜백들보다 우선시되기 때문.
  따라서, `process.nextTick`과 `Promise`를 **마이크로태스크**라고 따로 구분지어 부른다.

  

  > > > **마이크로태스크의 재귀 호출**
  > > >
  > > > `process.nextTick`으로 받은 콜백 함수나 `resolve`된 `Promise`는 다른 이벤트 루프에서 대기하는 콜백 함수보다도 먼저 실행된다.
  > > >
  > > > 그래서 비동기 처리를 할 때 `setImmediate`보다 `process.nextTick`을 더 선호하는 개발자도 있다.
  > > >
  > > > 하지만, 이런 마이크로태스크를 재귀 호출하게 되면 이벤트 루프는 다른 콜백 함수보다 마이크로태스크를 우선하여 처리하므로 콜백 함수들이 실행되지 않을 수도 있다.

  

- **process.exit(코드)**

  > 실행 중인 노드 프로세스를 종료한다.

  서버에 이 함수를 사용하면 서버가 멈춘다. 따라서 서버에서는 사용하지 않는다.

  하지만, 서버 외의 독립 프로그램에서는 수동으로 노드를 멈추게 하기위해 사용한다.

  ```js
  //exit.js
  let i = 1;
  setInterval(() => {
      if(i === 5){
          console.log('종료!');
          process.exit();
      }
      console.log(i);
      i += 1;
  }, 1000);
  ```

  ![image-20191213171725015](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213171725015.png)

  

  `process.exit`메서드는 인자로 코드 번호를 줄 수 있다. 

  - 인자를 주지 않거나 0이면 정상 종료를 뜻하고, 1을 주면 비정상 종료를 뜻한다.
    만약 에러가 발생해서 종료하는 경우에는 1을 넣어주면 된다.



---

#### 3.5 노드 내장 모듈 사용하기

> 노드는 웹 브라우저에서 사용되는 JS보다 더 많은 기능을 제공한다.

노드의 모듈들은 노드 버전마다 차이가 있다.
따라서 버전과 상관없이 안정적이고, 유용한 기능을 지닌 모듈들 위주로 설명하겠다.



###### 3.5.1 os

> 웹 브라우저에 사용되는 JS는 운영체제의 정보를 가져올 수 없다. 노드는 가능하다.

```js
//os.js
const os = require('os');

console.log('운영체제 정보---------------------------------');
console.log('os.arch():', os.arch());
console.log('os.platform():', os.platform());
console.log('os.type():', os.type());
console.log('os.uptime():', os.uptime());
console.log('os.hostname():', os.hostname());
console.log('os.release():', os.release());

console.log('경로-----------------------------------------');
console.log('os.homedir():', os.homedir());
console.log('os.tmpdir():', os.tmpdir());

console.log('cpu 정보-------------------------------------');
console.log('os.cpus():', os.cpus());
console.log('os.cpus().length:', os.cpus().length);

console.log('메모리 정보----------------------------------');
console.log('os.freemem():', os.freemem());
console.log('os.totalmem():', os.totalmem());
```

![image-20191213173247237](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191213173247237.png)



- `os.arch()` : `process.arch`와 동일.
- `os.platform() ` : `process.platform`과 동일.
- `os.type()` : 운영체제의 종류를 보여준다.
- `os.uptime()` : 운영체제 부팅 이후 흐른 시간(초) 보여준다. `process.uptime()`은 노드의 실행 시간이다.
- `os.hostname()` : 컴퓨터의 이름을 보여준다.
- `os.release()` : 운영체제의 버전을 보여준다.
- `os.homedir()` : 홈 디렉터리 경로를 보여준다.
- `os.tmpdir()` : 임시 파일 저장 경로를 보여준다.
- `os.cpus()` : 컴퓨터의 코어 정보를 보여준다.
- `os.freemem()` : 사용 가능한 메모리(RAM)을 보여준다.
- `os.totalmem()` : 전체 메모리 용량을 보여준다.



> > > **코어 개수 확인하기**
> > >
> > > `os.cpus().length`를 하면 코어의 개수가 숫자로 나온다.
> > > 하지만 노드는 싱글 스레드라 코어가 몇 개이든 대부분의 경우 코어를 하나밖에 사용하지 않는다.
> > >
> > > `cluster`모듈을 사용하는 경우 코어 개수에 맞춰서 프로세스를 늘릴 수 있다.
> > > 이때 `cpus()` 메서드를 사용할 것.



`os` 모듈은 주로 컴퓨터 내부 자원에 빈번하게 접근하는 경우 사용된다.
즉, 일반적인 웹 서비스를 제작할 때는 사용 빈도가 높지 않다.
하지만, 운영체제별로 다른 서비스를 제공하고 싶을 때 `os`모듈이 유용하다.



---

###### 3.5.2 path

> 폴더와 파일의 경로를 쉽게 조작하도록 도와주는 모듈

`path` 모듈이 필요한 이유 중하나는 운영체제별로 경로 구분자가 다르기 때문이다.
Window 타입은 \, POSIX 타입은 /

```js
//path.js
const path = require('path');

const string = __filename;

console.log('path.sep:', path.sep);
console.log('path.delimiter:', path.delimiter);
console.log('-------------------------------------------------------');
console.log('path.dirname():', path.dirname(string));
console.log('path.extname():', path.extname(string));
console.log('path.basename():', path.basename(string));
console.log('path.basename():', path.basename(string, path.extname(string)));
console.log('-------------------------------------------------------');
console.log('path.parse():', path.parse(string));
console.log('path.format():', path.format({
    dir: 'C:\\users\\zerocho',
    name: 'path',
    ext: '.js',
}));
console.log('path.normalize():', path.normalize('C://users\\\zerocho\\\path.js'));
console.log('-------------------------------------------------------');
console.log('path.isAbsolute():', path.isAbsolute('C:\\'));
console.log('path.isAbsolute():', path.isAbsolute('./home'));
console.log('-------------------------------------------------------');
console.log('path.relative():', path.relative('C:\\users\\zerocho\\path.js', 'C:\\'));
console.log('path.join():', path.join(__dirname, '..', '..', '/users', '.', '/zerocho'));
console.log('path.resolve():', path.resolve(__dirname, '..', 'users', '.', '/zerocho'));
```

![image-20191215162820131](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215162820131.png)

- `path.sep` : 경로의 구분자. Windows는 /, POSIX는 \
- `path.delimiter` : 환경변수의 구분자. `path.env.PATH`를 입력하면 여러 개의 경로가 이 구분자로 구분 됨. Windows는 ; 이고 POSIX 는 :
- `path.dirname(경로)` : 파일이 위치한 폴더 경로를 보여준다.
- `path.extname(경로)` : 파일의 확장자를 보여준다.
- `path.basename(경로, 확장자)` : 파일의 이름(확장자 포함)을 보여준다.
  파일의 이름만 보고 싶다면 `basename`의 두 번째 인자로 파일의 확장자를 넣어주면 된다.
- `path.parse(경로)` : 파일 경로를 root, dir, base, ext, name으로 분리한다.
- `path.format(경로)` : `path.parse()`한 객체를 파일 경로로 합친다.
- `path.normalize(경로)` : /나 \를 실수로 여러 번 사용했거나 혼용했을 때 정상적인 경로로 변환해준다.
- `path.isAbsolute(경로)` : 파일의 경로가 절대경로인지 상대경로인지 true, false로 알려준다.
- `path.relative(기준경로, 비교경로)` : 경로를 두 개 넣으면 첫 번째 경로에서 두 번째 경로로 가는 방법을 알려준다.
- `path.join(경로, ...)` : 여러 인자를 넣으면 하나의 경로로 합쳐준다. 상대경로인 ..(부모 dir), .(현 위치)도 알아서 처리해준다.
- `path.resolve(경로, ...)` : `path.join()`과 비슷하지만 차이가 있다.

> > > **`join`과 `resolve`의 차이**
> > >
> > > 경로 이후에 나오는 인자에서 /를 만나면
> > > `path.join`은 상대경로로 처리하고, `path.resolve`는 절대경로로 인식해서 앞의 경로를 무시한다.
> > >
> > > `path.join('/a', '/b', '/c');`	결과: /a/b/c
> > > `path.resolve('/a', 'b', '/c');` 결과 : /c

> > >**상대경로, 절대경로**
> > >
> > >절대경로는 루트 폴더(C:\\)나 노드 프로세스가 실행되는 위치가 기준이 된다.
> > >
> > >상대경로는 현재 파일 기준이 된다. 
> > >현재 파일과 같은 경로면 (.)을, 현재 파일보다 한 단계 상위 경로면 (..)을 사용해 표현한다.
> > >
> > >C:\users\zerocho\path.js에서 C:\로 가고 싶다면
> > >절대경로는 그냥 C:\을 입력하면 된다.
> > >상대경로는 ..\\..을 해야 두 디렉터리 위로 올라가 C:\가 된다.

> > >**\\, \\\\ 의 차이**
> > >
> > >기본적으로 \ 하나를 사용한다.
> > >하지만, JS 문자열에서는 \가 특수문자이므로 \\\를 사용하여 표시해야한다.
> > >
> > >ex) \n은 개행문자인데 C:\node라고 쓰면 의도치 않게 오류가 발생한다. 따라서 C:\\\node로 표시해야한다.
> > >
> > >`path`모듈은 위와 같은 문제를 알아서 처리해 준다. Windows에서 `path`모듈이 꼭 필요한 이유이다.



---

###### 3.5.3 url

> 인터넷 주소를 쉽게 조작하도록 도와주는 모듈.

url 처리 방식에는 크게 두 가지가 있다.

- **WHATWG url** : 노드 버전 7에서 추가된 방식.
- **노드 url** 

![image-20191216105714238](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216105714238.png)



```js
//url.js
const url = require('url');

const URL = url.URL;
const myURL = new URL('http://www.gilbut.co.kr/book/bookList.aspx?sercate1=001001000#anchor');
console.log('new URL():', myURL);
console.log('url.format():', url.format(myURL));
console.log('--------------------------------------------------');

const parseUrl = url.parse('http://www.gilbut.co.kr/book/bookList.aspx?secate1=001001000#anchor');
console.log('url.parse():', parseUrl);
console.log('url.format():', url.format(parseUrl));

//url모듈 안에 URL 생성자가 있다. 이 생성자에 주소를 넣어 객체로 만들면 주소가 부분별로 정리된다.
//이 방식이 WHATWG의 url이다. WHATWG에만 있는 username, password, origin, searchParams 속성이 존재한다.
```

![image-20191215171623412](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215171623412.png)

- `url.parse(주소)` : 주소를 분해한다. 
  WHATWG 방식과 비교하면
  username, password 대신 auth 속성이 있고, 
  searchParams 대신 query가 있다.
- `url.format(객체)` : WHATWG 방식의 url과 기존 노드의 url모두 사용할 수 있다. 분해되었던 url객체를 다시 원래 상태로 조립한다.



WHATWG와 노드의 url은 보통 취향 따라 사용한다.

하지만, 주소에 host 부분없이 pathname 부분만 오는 경우(/book/bookList.apsx) WHATWG 방식은 처리 못한다. 



WHATWG 방식은 search 부분을 `searchParams`라는 특수한 객체로 반환하므로 유용하다.
`searchParams` 객체는 search 부분을 조작하는 다양한 메서드를 지원한다.

```js
//searchParams.js
const {URL} = require('url');

const myURL = new URL('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript');
console.log('searchParams:', myURL.searchParams);
console.log('searchParams.getAll():', myURL.searchParams.getAll('category'));
console.log('searchParams.get():', myURL.searchParams.get('limit'));
console.log('searchParams.has():', myURL.searchParams.has('page'));

console.log('searchParams.keys():', myURL.searchParams.keys());
console.log('searchParams.values():', myURL.searchParams.values());

myURL.searchParams.append('filter', 'es3');
myURL.searchParams.append('filter', 'es5');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.set('filter', 'es6');
console.log(myURL.searchParams.getAll('filter'));

myURL.searchParams.delete('filter');
console.log(myURL.searchParams.getAll('filter'));

console.log('searchParams.toString():', myURL.searchParams.toString());
myURL.search = myURL.searchParams.toString();
```

![image-20191215173954643](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215173954643.png)

- `getAll(키)` : 키에 해당하는 모든 값들을 가져온다.
- `get(키)` : 키에 해당하는 첫 번째 값만 가져온다.
- `has(키)` : 해당 키가 있는 없는지 검사한다.
- `keys()` : `searchParams`의 모든 키를 반복기(iterator, ES2015 문법) 객체로 가져온다.
- `valuse()` : `searchParams`의 모든 값을 반복기 객체로 가져온다.
- `append(키, 값)` : 해당 키를 추가한다. 같은 키의 값이 있다면 유지하고 하나 더 추가한다.
- `set(키, 값)` : 같은 키의 값들을 모두 지우고 새로 추가한다.
- `delete(키)` : 해당 키를 제거한다.
- `toString()` : 조작한 `searchParams` 객체를 다시 문자열로 만든다. 
  이 문자열을 `search`에 대입하면 주소 객체에 반영된다.



---

###### 3.5.4 querystring

> WHATWG 방식은 `searchParams`, 기존 노드 방식은 `querystring`

기존 노드 방식에서 search 부분을 사용하기 쉽게 객체로 만드는 모듈이다.

```js
//querystring.js
const url = require('url');
const querystring = require('querystring');

const parsedUrl = url.parse('http://www.gilbut.co.kr/?page=3&limit=10&category=nodejs&category=javascript');
const query = querystring.parse(parsedUrl.query);
console.log('querystring.parse():', query);
console.log('querystring.stringify():', querystring.stringify(query));
```

![image-20191215180654327](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215180654327.png)

- `querystring.parse(쿼리) `: url의 query 부분을 JS객체로 분해해준다.
- `querystring.stringify(객체)` : 분해된 query 객체를 문자열로 다시 조립해준다.



> >query 문자열 보다, `searchParams`가 유용한 이유는 
> >query의 경우 `querystring` 모듈을 한 번 더 사용해야하기 때문이다.



---

###### 3.5.5 crypto

> 다양한 방식의 암호화를 도와주는 모듈.

- **단방향 암호화**

  > 복호화할 수 없는 암호화 방식

  비번은 보통 단방향 암호화 알고리즘을 사용해서 암호화한다.
  한 번 암호화하면 원래 문자열을 찾을 수 없다.

  단방향 암호화 알고리즘은 보통 **해시 기법**을 사용한다.
  **해시 기법**이란 어떠한 문자열을 고정된 길이의 다른 문자열로 바꿔버리는 방식이다.

  ```js
  //hash.js
  const crypto = require('crypto');
  
  console.log('base64:', crypto.createHash('sha512').update('비밀번호').digest('base64'));
  console.log('hex:', crypto.createHash('sha512').update('비밀번호').digest('hex'));
  console.log('base64:', crypto.createHash('sha512').update('다른 비밀번호').digest('base64'));
  
  ```

  ![image-20191215182142458](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215182142458.png)

  - `createHash(알고리즘)` : 사용할 해시 알고리즘을 넣어준다.
    `md5`, `sha1`, `sha256`, `sha512` 등이 있지만, `md5`와 `sha1`은 이미 취약점이 발견되었다.
    현재는 `sha512` 정도로 충분하지만, 나중에 `sha512` 마저 취약해지면 다 강화된 알고리즘으로 바꿔야 함.
  - `udpate(문자열)` : 변환할 문자열을 넣어준다.
  - `digest(인코딩)` : 인코딩할 알고리즘을 넣어준다. 
    `base64`, `hex`, `latin1`이 주로 사용된다. `base64`가 결과 문자열이 가장 짧아서 애용된다.
    문자열로 리턴해준다.



![image-20191216110228912](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216110228912.png)

  현재는 주로 `pbkdf2`나 `bcrypt`, `scrypt`라는 알고리즘으로 비밀번호를 암호화 한다.
  이 중에 노드에서 지원하는 `pbkdf2`에 대해 알아보자.
  `pbkdf2`는 기존 문자열에 salt라고 불리는 문자열을 붙인 후 해시 알고리즘을 반복해서 적용하는 것이다.

  ```js
  //pbkdf2.js
  const crypto = require('crypto');
  
  crypto.randomBytes(64, (err, buf) => {
      const salt = buf.toString('base64');
      console.log('salt:', salt);
      crypto.pbkdf2('비밀번호', salt, 100000, 64, 'sha512', (err, key) => {
          console.log('password:', key.toString('base64'));
      });
  });
  ```

  ![image-20191215183509805](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215183509805.png)


  먼저 `randomBytes()` 메서드로 64byte 길이의 문자열을 만들어준다. 이게 salt가 된다.

  `pbkdf2(비번, salt, 반복 횟수, 출력 바이트, 해시 알고리즘)`
  `sha512`로 변환된 결과값을 다시 `sha512`로 변환하는 과정을 10만 번 반복하는 것이다

  `pbkdf2`는 간단하지만, `bcrypt`나 `scrypt`보다 취약하다.

  

- **양방향 암호화**

  > 암호화된 문자열을 복호화할 수 있다.

  '키'라는 것을 사용한다. 암호를 복호화하려면 암호화할 때 사용한 키와 같은 키를 사용해야 한다.

  ```js
  //cipher.js
  const crypto = require('crypto');
  
  const cipher = crypto.createCipher('aes-256-cbc', '열쇠');
  let result = cipher.update('암호화할 문장', 'utf8', 'base64');
  result += cipher.final('base64');
  console.log('암호화:', result);
  
  const decipher = crypto.createDecipher('aes-256-cbc', '열쇠');
  let result2 = decipher.update(result, 'base64', 'utf8');
  result2 += decipher.final('utf8');
  console.log('복호화:', result2);
  ```

  ![image-20191215184817768](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215184817768.png)

  

  - `crpyto.createCipher(알고리즘, 키)` : 암호화 알고리즘과 키를 넣어준다. 
    사용 가능한 암호화 알고리즘 목록은 `crpyto.getCipher()`을 하면 볼 수 있다.
  - `cipher.update(문자열, 인코딩, 출력 인코딩)` : 암호화할 대상과 대상의 인코딩, 출력 결과물의 인코딩을 넣어준다. 보통 문자열은 `utf8` 인코딩을 , 암호는 `base64`를 많이 사용한다.
  - `cipher.final(출력 인코딩)` : 출력 결과물의 인코딩을 넣어주면 암호화가 완료된다.
  - `crypto.createDecipher(알고리즘, 키)` : 복호화시 사용한다. 암호화할 때 사용했던 알고리즘과 키를 그대로 넣어주어야 한다.
  - `decipher.update(문자열, 인코딩, 출력 인코딩)` : 암호화된 문자열, 그 문자열의 인코딩, 복호화할 인코딩을 넣어준다. 암호화 할 때, `update()`에서 `utf8`, `base64`순서로 넣었다면, 복호화할 때는 반대로 넣어준다.
  - `decipher.final(출력 인코딩)` : 복호화 결과물의 인코딩을 넣어준다.



---

###### 3.5.6 util

> 각종 편의 기능을 모아둔 모듈. ~~닉값~~

계속해서 API가 추가되고 있고, 가끔 deprecated되어 사라지는 경우도 있다.

> > **deprecated**
> >
> > 프로그래밍 용어, '중요도가 떨어져 더 이상 사용되지 않고 앞으로는 사라지게 될' 것이라는 뜻.
> > 새로운 기능이 나와서 기존 기능보다 더 좋을 때, 기존 기능을 dprecated 처리하곤 한다.
> > 이전 기능을 제거하진 않지만, 곧 없앨 예정이므로 더 이상 사용하지 말라는 의미.

```js
//util.js
const util = require('util');
const crypto = require('crypto');

const dontUseMe = util.deprecate((x, y) => {
    console.log(x + y);
}, 'dontUseMe 함수는 deprecated되었으니 더 이상 사용하지 마셈');
dontUseMe(1, 2);

const randomBytesPromise = util.promisify(crypto.randomBytes);
randomBytesPromise(64)
.then((buf) => {
    console.log(buf.toString('base64'));
})
.catch((error) => {
    console.error(error);
});
```

![image-20191215200605955](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215200605955.png)

- `util.deprecate` : 함수가 deprecated 처리되어음을 알려준다.
  첫 번째 인자로 넣은 함수를 사용했을 때 경고 메시지가 출력된다.
  두 번째 인자로 경고 메시지 내용을 넣으면 된다.
  함수가 조만간 사라지거나 변경될 때 알려줄 수 있어 유용하다.
- `util.promisify` : 콜백 패턴을 프로미스 패턴으로 바꿔준다. 바꿀 함수를 인자로 넘기면 된다.
  이렇게 바꾸면 async/await 패턴까지 사용할 수 있어 좋다.
  `util.callbackify`도 있지만 자주 사용되지는 않는다.



---

#### 3.6 파일 시스템 접근하기

> `fs`모듈은 파일 시스템이 접근하는 모듈이다.

파일 생성, 삭제, 읽기, 쓰기를 할 수 있다. 폴더도 만들었다 지웠다 할 수 있다.
웹 브라우저의 JS는 파일 다운로드와 시스템 접근이 금지되어 있다. 노드는 `fs`쓰면 된다.

```txt
//readme.txt
나 좀 읽어주셈.
```

```js
//readFile.js
const fs = require('fs');

fs.readFile('./3ch/3.5/readme.txt', (err, data) => {
    if(err){
        throw err;
    }
    console.log(data);
    console.log(data.toString());
});
```

![image-20191215202655940](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215202655940.png)

- `fs.readFile(경로, 콜백)` : `fs`모듈을 불러온 뒤 읽을 파일의 경로를 지정한다.
  유의할 점은 콜백 함수도 `readFile` 메서드의 인자로 같이 넣어주어야 한다.

`console.log(data)` 했더니 Buffer가 출력 된다. `data.toString()`을 해야 제대로 된 문자열이 출력된다.
`readFile`의 결과물은 버퍼라는 형식으로 제공된다. 



```js
//writeFile.js
const fs = require('fs');

fs.writeFile('./3ch/3.5/writeme.txt', '글이 입력된다.', (err) => {
    if(err){
        throw err;
    }
    fs.readFile('./3ch/3.5/writeme.txt', (err, data) => {
        if(err){
            throw err;
        }
        console.log(data.toString());
    });
});
```

![image-20191215203946123](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215203946123.png)

- `writeFile(경로, 내용, 콜백)` : 메서드에 생성될 파일의 경로와 내용을 입력해주자.



---

###### 3.6.1 동기 메서드와 비동기 메서드

> 노드는 대부분의 메서드를 비동기 방식으로 처리한다.
> 하지만 몇몇 메서드는 동기 방식으로도 사용할 수 있다.
> 특히 `fs`모듈이 그러한 메서드를 많이 가지고 있다.

```txt
//readme2.txt
여러 번 읽어봐.
```

```js
//async.js
const fs = require('fs');

console.log('시작');
fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
    if(err){
        throw err;
    }
    console.log('1번 ', data.toString());
});

fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
    if(err){
        throw err;
    }
    console.log('2번 ', data.toString());
});

fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
    if(err){
        throw err;
    }
    console.log('3번 ', data.toString());
});

console.log('끝');
```

![image-20191215204840657](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215204840657.png)

순서가 제멋대로이다. 
비동기 메서드들은 백그라운드에 해당 파일을 읽으라고만 요청하고 다음 작업으로 넘어간다. 
따라서 파일 읽기 요청만 세 번을 보내고 `console.log('끝')`을 찍는다.
나중에 읽기가 완료되면 백그라운드가 다시 메인 스레드에 알림을 준다.
메인 스레드는 그제서야 등록된 콜백 함수(여기서는 `readFile`)를 실행한다.

비동기 방식은 상당히 좋다.
수백 개의 I/O 요청이 들어와도 메인 스레드는 백그라운드에 요청 처리를 위임한다.
그 후로도 얼마든지 요청을 더 받을 수 있다.
나중에 백그라운드가 각각의 요청 처리가 완료되었다고 알리면 그때 콜백함수를 처리하면 된다.



> > > **동기와 비동기, 블로킹과 논블로킹**
> > >
> > > - 동기와 비동기 : 함수가 바고 return되는지 여부
> > > - 블로킹과 논블로킹 : 백그라운드 작업 완료 여부
> > >
> > > 노드는 동기-블로킹, 비동기-논블로킹 방식이 대부분이다.
> > >
> > > - 동기-블로킹 : 백그라운드 작업 완료 여부를 계속 확인하며, 호출한 함수가 바로 return되지 않고 백그라운드 작업이 끝나야 return된다.
> > > - 비동기-논블로킹 : 호출한 함수가 바로 return되어 다음 작업으로 넘어가고, 백그라운 작업 완료 여부는 신경쓰지 않고 나중에 백그라운드가 알림을 줄 때 처리한다.



순서대로 출력하고 싶으면 밑에 처럼 짜면된다.

```js
//sync.js
const fs = require('fs');

console.log('시작');
let data = fs.readFileSync('./3ch/3.5/readme2.txt');
console.log('1번', data.toString());
data = fs.readFileSync('./3ch/3.5/readme2.txt');
console.log('2번', data.toString());
data = fs.readFileSync('./3ch/3.5/readme2.txt');
console.log('3번', data.toString());
console.log('끝');
```

![image-20191215211909719](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215211909719.png)

`readFile()` 대신 `readFileSync()`라는 메서드를 사용했다.
그런데 콜백 함수를 넣는 대신 직접 return 값을 받아온다.

코드는 훨씬 쉽지만, **<u>치명적인 단점</u>**이 존재한다.
`readFileSync` 메서드를 사용하면 요청이 수백 개 이상 들어왔을 때 성능에 문제가 생긴다.
Sync 메서드를 사용할 때는 이전 작업이 완료되어야 다음 작업을 진행 할 수 있다.
즉, 백그라운드가 작업하는 동안 메인 스레드는 아무것도 못하고 대기하고 있어야 한다.
메인 스레드가 일 하지 않고 노는 시간이 생기기 때문에 비효율적이다.

비동기 메서드는 백그라운드가 작업을 하는 와중에도 다음 작업을 처리할 수 있다.

동기 메서드들은 이름 뒤에 Sync가 붙어 구분하기 쉽다. 다만 사용하는 경우는 극히 드물다. 비동기가 훨씬 효율적이기 때문이다.



비동기 방식으로 하되 순서를 유지하고 싶다면 다음과 같이 하면 된다.

```js
//asyncOrder.js
const fs = require('fs');

console.log('시작');
fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
    if(err){
        throw err;
    }
    console.log('1번', data.toString());
    fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
        if(err){
            throw err;
        }
        console.log('2번', data.toString());
        fs.readFile('./3ch/3.5/readme2.txt', (err, data) => {
            if(err){
                throw err;
            }
            console.log('3번', data.toString());
        });
    });
});
console.log('끝');
```

![image-20191215214114217](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191215214114217.png)

 `readFile()`의 콜백에 다음 `readFile()`을 넣어주면 된다.
콜백 지옥이 펼쳐지지만 적어도 순서가 어긋나는 일은 없다.
콜백 지옥은 `Promise`나 `async/await`로 어느 정도 해결할 수 있다.



---

###### 3.6.2 버퍼와 스트림 이해하기

> 파일을 읽거나 쓰는 방식은 크게 두 가지.
> 버퍼와 스트림.

영상을 로딩 중일 때는 **버퍼링**한다고 하고, 영상을 실시간으로 송출할 때는 **스트리밍**한다고 한다.
**버퍼링**은 영상을 재생할 수 있을 때가지 데이터를 모으는 동작.
**스트리밍**은 방송인의 컴퓨터에서 시청자의 컴퓨터로 영상 데이터를 조금씩 전송하는 동작.

노드도 비슷한 개념이다.
`readFile` 메서드 사용 시 읽었던 파일이 버퍼 형식으로 출력되었다.
노드는 파일을 읽을 때 메모리에 파일 크기만큼 공간을 마련해두며, 파일 데이터를 메모리에 저장한 뒤 사용자가 조작할 수 있도록 해준다.
<u>메모리에 저장된 데이터가 바로 **버퍼**이다.</u>

![image-20191216112955201](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216112955201.png)



버퍼를 직접 다룰 수 있는 클래스가 있다. `Buffer`이다.

```js
//buffer.js
const buffer = Buffer.from('나를 버퍼로 바꿔줘');
console.log('from():', buffer);
console.log('length:', buffer.length);
console.log('toString():', buffer.toString());

const array = [Buffer.from('띄엄 '), Buffer.from('띄엄 '), Buffer.from('띄어쓰기')];
const buffer2 = Buffer.concat(array);
console.log('concat():', buffer2.toString());

const buffer3 = Buffer.alloc(5);
console.log('alloc():', buffer3);
```

![image-20191216113443881](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216113443881.png)

- `from(문자열)` : 문자열을 버퍼로 바꿀 수 있다. `length` 속성은 버퍼의 크기를 알려준다(Byte단위).
- `toString(버퍼)` : 버퍼를 다시 문자열로 바꿀 수 있다.
  이때 `base64`나 `hex`를 인자로 넣으면 해당 인코딩으로도 변환할 수 있다.
- `concat(배열)` : 배열 안에 든 버퍼들을 하나로 합친다.
- `alloc(바이트)` : 빈 버퍼를 생성한다. Byte를 인자로 지정해주면 해당 크기의 버퍼가 생성된다.



`readFile()` 방식의 버퍼가 편리하기는 하지만 <u>문제점</u>도 있다.

> > 만약 용량이 100MB인 파일이 있으면 읽을 때 메모리에 100MB의 버퍼를 만들어야 하는데,
> > 이 작업을 동시에 10번만해도 1GB에 달하는 메모리가 사용된다.
> > 특히 서버 같이 몇 명이 이용할지 모르는 환경에서는 **메모리 문제**가 발생할 수 있다.
> >
> > ---
> >
> > 또한, 모든 내용을 버퍼에 다 쓴후에야 다음 동작으로 넘어가므로 파일 읽기, 압축, 쓰기 등의 조작을 연달아 할 때 매번 전체 용량을 버퍼로 처리해야 다음 단계로 넘어갈 수 있다.

그래서 버퍼의 크기를 <u>작게 만들어서 여러 번에 나눠서 보내는 방식</u>이 등장했다.

> > 예를 들어, 버퍼 1MB를 만든후 100MB 파일을 백 번에 걸쳐 보내는 것이다.

이를 편리하게 만든 것이 **<u>스트림</u>**이다.

![image-20191216123827419](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216123827419.png)



파일을 읽는 스트림 메서드로는 createReadStream이 있다.

```js
//createReadStream.js
const fs = require('fs');

const readStream = fs.createReadStream('./3ch/3.5/readme3.txt', {highWaterMark: 16});
const data = [];

readStream.on('data', (chunk) => {
    data.push(chunk);
    console.log('data:', chunk, chunk.length);
});

readStream.on('end', () => {
    console.log('end:', Buffer.concat(data).toString());
});

readStream.on('error', (err) => {
    console.log('error:', err)
});
```

![image-20191216131925955](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216131925955.png)

- `createReadStream(경로, 옵션)` : 읽기 스트림을 만들어 준다. 
  첫 번째 인자는 파일 경로, 두 번째 인자는 옵션 객체이다.
  `highWaterMark`라는 옵션은 버퍼의 크기(byte 단위)를 정할 수 있는 옵션. 기본값은 64KB이다.
  여러번 나눠서 보내는 모습을 보여주기 위해 16B로 낮췄다.
- `readStream` : 이벤트 리스너를 붙여서 사용한다. 보통 `data`, `end`, `error` 이벤트를 사용한다.
  파일을 읽는 도중에 에러가 발생하면 `error` 이벤트가 호출되고,
  파일 읽기가 시작되면 `data` 이벤트가 발생한다. 16B씩 읽도록 설정했으므로 크기가 16B보다 크면 여러 번 발생할 수도 있다.
  파일을 다 읽으면 `end` 이벤트가 발생한다.



이번에는 파일을 써보자.

```js
//createWriteStream.js
const fs = require('fs');

const writeStream = fs.createWriteStream('./3ch/3.5/writeme2.txt');
writeStream.on('finish', () => {
    console.log('파일 쓰기 완료');
});

writeStream.write('이 글을 쓴다.\n');
writeStream.write('한 번 더 쓴다.');
writeStream.end();
```

![image-20191216133145564](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216133145564.png)

![image-20191216133204748](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216133204748.png)

- `createWriteStream(경로)` : 쓰기 스트림을 만들어 준다.
  첫 번째 인자는 파일 경로이고, 두 번째 인자는 옵션이다.
- `writeStream` : `finish` 이벤트 리스너는 파일 쓰기가 종료되면 콜백 함수가 호출된다.
  `write()` 메서드로 넣을 데이터를 쓴다. 여러번 호출 가능하다.
  `end()` 메서드로 종료를 알려준다. 이때 `finish` 이벤트가 발생한다.



스트림끼리 연결하는 것을 '파이핑한다.'고 표현한다.
`pipe` 메서드를 사용해보자.

```js
//pipe.js
const fs = require('fs');

const readStream = fs.createReadStream('./3ch/3.5/readme4.txt');
const writeStream = fs.createWriteStream('./3ch/3.5/writeme3.txt');
readStream.pipe(writeStream);   
```

![image-20191216141540819](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216141540819.png)

![image-20191216141552737](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216141552737.png)

readme4.txt 와 똑같은 내용의 writeme3.txt 가 생성되었다.
미리, 읽기 스트림과 쓰기 스트림을 만들어둔 후 두 개의 스트림 사이를 `pipe` 메서드로 연결해주면 저절로 데이터가 `writeStream`으로 넘어간다.
따로 `.on('data')`나 `writeStream.write()`를 하지 않아도 알아서 전달되므로 편리하다.

> > 노드 8.5 버전이 나오기 전까지는 이 방식을 파일을 복사하곤 했다.
> > 지금은 새로운 파일 복사 방식을 사용한다.



`pipe`는 스트림 사이에 연결할 수 있다. 다음 코드는 파일을 읽은 후 gzip 방식으로 압축하는 코드이다.

```js
//gzip.js
const zlib = require('zlib');
const fs = require('fs');

const readStream = fs.createReadStream('./3ch/3.5/readme4.txt');
const zlibStream = zlib.createGzip();
const writeStream = fs.createWriteStream('./3ch/3.5/readme4.txt.gz');
readStream.pipe(zlibStream).pipe(writeStream);
```

![image-20191216170205561](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216170205561.png)

![image-20191216170249913](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191216170249913.png)

노드에서는 파일을 압축하는 `zlib`이라는 모듈도 제공한다.
`zlib`의 `createGzip()`이라는 메서드가 스트림을 지원하므로 `readStream`과 `writeStream` 중간에서 파이핑을 할 수 있다. 버퍼 데이터가 전달되다가 gzip 압축을 거친 후 파일로 쓰여진다.



---

###### 3.6.3 기타 fs 메서드

> > `fs`는 파일 시스템을 조작하는 다양한 메서드를 제공한다.

파일과 폴더를 생성하고 삭제할 수도 있다.

```js
//fsCreate.js
const fs = require('fs');

fs.access('./folder', fs.constants.F_OK | fs.constants.R_OK | fs.constants.W_OK, (err) => {
    if(err){
        if(err.code == 'ENOENT'){
            console.log('폴더 없음');
            fs.mkdir('./folder', (err) => {
                if(err){
                    throw err;
                }
                console.log('폴더 만들기 성공');
                fs.open('./folder/file.js', 'w', (err, fd) => {
                    if(err){
                        throw err;
                    }
                    console.log('빈 파일 만들기 성공', fd);
                    fs.rename('./folder/file.js', './folder/newfile.js', (err) => {
                        if(err){
                            throw err;
                        }
                        console.log('이름 바꾸기 성공');
                    });
                });
            });
        }
        else{
            throw err;
        }
    }
    else{
        console.log('이미 폴더 있음');
    }
});
```

![image-20191217102453833](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217102453833.png)

![image-20191217102531745](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217102531745.png)

- `fs.access(경로, 옵션, 콜백)` : 폴더나 파일에 접근할 수 있는지를 체크한다.
  두 번째 인자로 상수들을 넣는다. `F_OK`는 파일 존재 여부, `R_OK`는 읽기 권한 여부, `W_OK`는 쓰기 권한 여부를 체크한다. 
  파일/폴더나 권한이 없다면 에러가 발생한다. 이 때의 에러 코드는 `ENOENT`이다.
- `fs.mkdir(경로, 콜백)` : 폴더를 만드는 메서드. 이미 폴더가 있다면 에러가 발생하므로 `access()` 메서드를 호출해서 확인하는 것이 중요하다.
- `fs.open(경로, 옵션, 콜백)` : 파일의 아이디(fd 변수)를 가져오는 메서드이다.
  파일이 없다면 파일을 생성한 뒤 그 아이디를 가져온다. 가져온 아이디를 사용해 `fs.read()`나 `fs.write()`로 읽거나 쓸 수 있다.
  두 번째 인자로 어떤 동작을 할 것인지 설정할 수 있다. w(쓰기, 파일이 없다면 새로 만듬), r(읽기), a(기존 파일에 추가).
- `fs.rename(기존 경로, 새 경로, 콜백)` : 파일의 이름을 바꾸는 메서드. 파일의 기존 위치와 새 위치를 적어주면 된다.
  반드시 같은 폴더에 지정할 필요는 없으므로 잘라내기 같은 기능을 할 수도 있다.



```js
//fsDelete.js
const fs = require('fs');

fs.readdir('./folder', (err, dir) => {
    if(err){
        throw err;
    }
    console.log('폴더 내용 확인', dir);
    fs.unlink('./folder/newFile.js', (err) => {
        if(err){
            throw err;
        }
        console.log('파일 삭제 성공');
        fs.rmdir('./folder', (err) => {
            if(err){
                throw err;
            }
            console.log('폴더 삭제 성공');
        });
    });
});
```

![image-20191217104019353](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217104019353.png)

- `fs.readdir(경로, 콜백)` : 폴더 안의 내용물을 확인할 수 있다. 배열 안에 내부 파일과 폴더명이 나온다.
- `fs.unlink(경로, 콜백)` : 파일을 지울 수 있다. 파일이 없다면 에러가 발생하므로 먼저 파일이 있는지 꼭 확인해야한다.
- `fs.rmdir(경로, 콜백)` : 폴더를 지울 수 있다. 폴더 안에 파일이 있다면 에러가 발생하므로 먼저 내부 파일을 모두 지우고 호출해야 한다.



노드 8.5 부터, 파일 복사 방법도 새로 추가되었다. 
이제 `createReadStream`,`createWriteStream`을 `pipe`하지 않아도 된다.

```js
//copyFile.js
const fs = require('fs');

fs.copyFile('./3ch/3.5/readme4.txt', './3ch/3.5/writeme4.txt', (error) => {
    if(error){
        return console.error(error);
    }
    console.log('복사 완료');
});
```

![image-20191217105031132](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217105031132.png)

- `copyFile(파일, 경로, 콜백)` : 파일을 복사한다.
  첫 번째 인자는 복사할 파일, 두 번째는 복사될 경로, 세 번째는 복사 후 실행될 콜백 함수이다.



> > > **fs 프로미스**
> > >
> > > 노드 10 버전에 `fs`모듈을 프로미스 형식으로 사용하는 방법이 추가 되었다.
> > >
> > > const fsPromises = require('fs').promises;
> > > `fs` 모듈로부터 `promises` 객체를 불러와 사용한다.
> > >
> > > 하지만 아직 실험적인 기능.



---

#### 3.7 이벤트 이해하기

> `events` 모듈을 사용하여 직접 이벤트를 만들 수도 있다.

다양한 동작을 직접 구현할 수 있기 때문에 웹 서버를 구축할 때 많이 사용된다.

```js
//event.js
const EventEmitter = require('events');

const myEvent = new EventEmitter();
myEvent.addListener('event1', () => {
    console.log('이벤트 1');
});
myEvent.on('event2', () => {
    console.log('이벤트 2');
});
myEvent.on('event2', () => {
    console.log('이벤트 2 추가');
});

myEvent.emit('event1');
myEvent.emit('event2');

myEvent.once('event3', () => {
    console.log('이벤트 3');
});
myEvent.emit('event3');
myEvent.emit('event3');

myEvent.on('event4', () => {
    console.log('이벤트 4');
});
myEvent.removeAllListeners('event4');
myEvent.emit('event4');

const listener = () => {
    console.log('이벤트 5');
};
myEvent.on('event5', listener);
myEvent.removeListener('event5', listener);
myEvent.emit('event5');

console.log(myEvent.listenerCount('event2'));
```

![image-20191217112513085](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217112513085.png)

- `.on(이벤트명, 콜백)` : 이벤트 이름과 이벤트 발생 시의 콜백을 연결해준다. 
  이렇게 연결하는 동작을 이벤트 리스닝이라고 부른다. 이벤트 하나에 이벤트 여러 개를 달아줄 수도 있다.
- `.addListener(이벤트명, 콜백)` : `.on`과 기능이 같다.
- `.emit(이벤트명)` : 이벤트를 호출하는 메서드. 이벤트 이름을 넣어주면 미리 등록해뒀던 이벤트 콜백이 실행된다.
- `.once(이벤트명, 콜백)` : 한 번만 실행되는 이벤트.
- `.removeAllListeners(이벤트명)` : 이벤트에 연결된 모든 이벤트 리스너를 제거한다.
- `.removeListener(이벤트명, 리스너)` : 이벤트에 연결된 리스너를 하나씩 제거한다.
- `.off(이벤트명, 콜백)` : `.removeListener`와 같은 기능. (노드 10 버전에 추가된 메서드)
- `listenerCount(이벤트명)` : 현재 리스너가 몇 개 연결되어 있는지 확인한다.



---

#### 3.8 예외 처리하기

> 노드에서는 예외처리가 정말 중요하다.

보통 처리하지 못한 에러를 예외라고 한다. 예외들은 노드 프로세스를 멈추게 한다.
노드는 싱글 스레드이기에 에러로 전체 서버가 멈춘다.

`try` `catch` 문으로 에러가 발생할 것 같은 부분을 감싸자.

```js
//error1.js
setInterval(() => {
    console.log('시작');
    try{
        throw new Error('서버를 고장내주마!');
    }
    catch(err){
        console.error(err);
    }
}, 1000);
```

![image-20191217132220934](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217132220934.png)



노드 자체에서 잡아주는 에러도 있다.

```js
//error2.js
const fs = require('fs');

setInterval(() => {
    fs.unlink('./abcdef.js', (err) => {
        if(err){
            console.error(err);
        }
    })
}, 1000);
```

![image-20191217132509312](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217132509312.png)

에러가 발생하지만 다행히 노드 내장 모듈의 에러는 실행 중인 프로세스를 멈추지 않는다.
에러 로그를 기록해두고 나중에 원인을 찾아 수정하면 된다.

`throw`를 하면 노드 프로세스가 멈춰버린다. 따라서 `throw`를 하는 경우에는 반드시 `try` `catch` 문으로 `throw`한 에러를 잡아줘야 한다.

정말 예측 불가능한 에러를 처리하는 방법은 다음과 같다.

```js
//error3.js
process.on('uncaughtException', (err) => {
    console.error('예기치 못한 에러', err);
});

setInterval(() => {
    throw new Error('서버를 고장내주마!');
}, 1000);

setTimeout(() => {
    console.log('실행');
}, 2000);
```

![image-20191217133220872](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217133220872.png)

처리하지 못한 에러가 발생했을 때 이벤트 리스너가 실행되고 프로세스가 유지된다.

`process` 객체에 `uncaughtException` 이벤트 리스너를 달아주었는데, 이 부분이 없다면 위의 예제에서는 `setTimeout`이 실행되지 않는다. `setTimeout`이 작동되기 전에 `setInterval`에서 에러가 발생하고 프로세스가 멈추기 때문이다.

> > 어떻게 보면 `uncaughtException` 이벤트 리스너로 모든 에러를 처리할 수 있을 것 같아 보이지만...
> > 노드 공식 문서에서는 `uncaughtException` 이벤트를 최후의 수단으로 사용하라고 한다.
> > 노드는 `uncaughtException` 이벤트 발생 후 다음 동작이 제대로 동작하는지를 보증하지 않는다.
> > 즉, 복구 작업 코드를 넣어두었더라도 그것이 동작하는지 확실할 수 없다.
> >
> > 따라서 단순히 에러 내용을 기록하는 정도로 사용하고 `process.exit()`로 프로세스를 종료하는 것이 좋다.

---

#### 3.9 함께 보면 좋은 자료

- **노드 공식 문서** : https://nodejs.org/dist/latest-v10.x/docs/api/
- **uncaughtException** : https://nodejs.org/dist/latest-v10.x/docs/api/process.html#process_event_uncaughtexception
- **fs 프로미스** : https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_promises_api