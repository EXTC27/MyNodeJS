# Node.js 혼자 깨부수기 4일차

## 4. http 모듈로 웹 서버 만들기

#### 4.1 요청과 응답 이해하기

> 서버는 클라이언트가 있기에 동작한다.

클라이언트에서 서버로 요청(request)을 보내고, 서버에서는 요청의 내용을 읽고 처리한 뒤 클라이언트에게 응답(response)을 보낸다.

따라서 서버에는 요청을 받는 부분과 응답을 보내는 부분이 있어야 한다.
요청과 응답은 이벤트 방식이라고 생각하면 된다.
클라이언트로부터 요청이 왔을 때 어떤 작업을 수행할지 이벤트 리스너를 미리 등록해두어야 한다.

```js
//이벤트 리스너를 가진 노드 서버를 만들어보자
//createServer.js
const http = require('http');

http.createServer((req, res) => {
    //여기에 어떻게 응답할지 적어준다.
});
```

http 서버가 있어야 웹 브라우저의 요청을 처리할 수 있으므로 `http` 모듈을 사용했다.

`http` 모듈에는 `createServer` 메서드가 있다. 인자로 요청에 대한 콜백 함수를 넣을 수 있다. 요청이 들어올 때마다 매번 콜백 함수가 실행된다. 따라서 이 콜백 함수에 응답을 적어주면 된다.

> > 보통 request를 줄여서 req, response를 줄여서 res 라고 적는다.(매개변수의 이름은 마음대로 바꿔도 된다.)

`req` 객체는 요청에 관한 정보들, `res` 객체는 응답에 관한 정보들을 담고 있다.



```js
//응답을 보내는 부분과 서버 연결 부분을 추가해보자
//server1.js
const http = require('http');

http.createServer((req, res) => {
    res.write('<h1>Hello Node!</h1>');
    res.end('<p>Hello Server!</p>');
}).listen(8080, () => {
    console.log('8080번 포트에서 서버 대기 중이다.');
});
```

`createServer` 메서드에 `listen` 메서드를 붙이고 클라이언트에게 공개할 포트 번호와 포트 연결 완료 후 실행될 콜백 함수를 넣어준다. 위의 파일을 실행하면 서버는 8080 포트에서 요청이 오기를 대기한다.



```js
//listen 메서드에 콜백 함수를 넣는 대신, 서버에 listening 이벤트 리스너를 붙여도 된다. 추가로 error 이벤트 리스너도 붙여보자.
//server1-0.js
const http = require('http');

const server = http.createServer((req, res) => {
    res.write('<h1>Hello Node!</h1>');
    res.end('<p>Hello Server!</p>');
});
server.listen(8080);
server.on('listening', () => {
    console.log('8080번 포트에서 서버 대기 중이다.');
});
server.on('error', (error) => {
    console.error(error);
});
```

- `res.write(데이터, ...)` : 클라이언트로 데이터를 보내는 메서드. 첫 번째 인자는 클라이언트로 보낼 데이터이다. 버퍼를 보낼 수도 있다. 여러 번 호출해서 데이터를 여러 개 보내도 된다.
- `res.end(데이터, ...)` : 응답를 종료하는 메서드.



![image-20191217191636186](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217191636186.png) ![image-20191217191647997](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217191647997.png)

![image-20191217192359564](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217192359564.png)

> > > **localhost와 포트란?**
> > >
> > > **localhost**는 <u>현재 컴퓨터의 내부 주소</u>를 가리킨다.
> > > 외부에서는 접근할 수 없고 자신의 컴퓨터에서만 접근할 수 있다.
> > > 따라서 서버 개발 시 <u>테스트용</u>으로 많이 쓰인다. 
> > > **127.0.0.1**을 사용해도 된다. 이러한 숫자 주소를 IP라고 부른다.
> > >
> > > **포트**는 서버 내에서 <u>프로세스를 구분하는 번호</u>이다.
> > > 서버는 HTTP 요청을 대기하는 것 외에도 다양한 작업을 한다.
> > > DB와 통신을 하거나, FTP 요청을 처리하기도 한다.
> > > 따라서 서버는 프로세스에 포트를 다르게 할당하여 들어오는 요청을 구분한다.
> > > 유명한 포트 번호로는 21(FTP), 80(HTTP), 443(HTTPS), 3306(MySQL)이 있다.
> > > 포트 번호는 IP 주소 뒤에 (:)과 함께 붙여 사용한다.
> > > 80번 포트를 사용하면 주소에서 포트를 생략할 수 있다. http://naver.com = http://naver.com:80
> > >
> > > ---
> > >
> > > **포트 충돌**
> > >
> > > 보통 포트 하나에 서비스를 하나만 사용할 수 있다.
> > > 이미 사용 중인 포트를 사용하려고 하면 에러난다.
> > > `Error: listen EADDRINUSE :::포트번호`



```js
//res.write 메서드로 HTML코드를 적는 것은 비효율적이다. HTML 파일을 읽어서 전송하는 방법이 있다.
//server2.js
const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
    fs.readFile('./4ch/4.1/server2.html', (err, data) => {
        if(err){
            throw err;
        }
        res.end(data);
    });
}).listen(8081, () => {
    console.log('8081번 포트에서 서버 대기 중');
});
```

```html
<!-- server2.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">  
    <title>Node.js 웹 서버</title>
</head>
<body>
    <h1>Node.js 웹 서버</h1>
    <p>만들 준비됨?</p>
</body>
</htm>
```

먼저 `fs` 모듈로 HTML 파일을 읽는다. `data` 변수에 저장된 버퍼를 그래로 클라이언트에 보내주면 된다.



![image-20191217194313288](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217194313288.png)![image-20191217194232770](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191217194232770.png)



---

#### 4.2 쿠키와 세션 이해하기

>로그인

클라이언트에서 보내는 요청에는 한 가지 큰 단점이 있다. 누가 요청을 보내는지 모른다는 것이다. 
여러 컴퓨터가 공통으로 IP 주소를 가지거나, 한 컴퓨터를 여러 사람이 사용할 수도 있어 요청을 보내는 IP 주소나 브라우저 정보로는 분간하기 어렵다.

웹 사이트에서 로그인을 할 때 내부적으로는 쿠키와 세션을 사용하고 있다. 
클라이언트가 서버에게 누구인지 지속적으로 알려주고 있기 때문이다.

서버는 요청에 대한 응답을 할 때 **쿠키**를 같이 보내 준다.
**쿠키**는 단순한 '키-값'의 쌍이다.
서버로부터 쿠키가 오면 웹 브라우저는 쿠키를 저장해두었다가 요청할 때마다 쿠키를 동봉해서 보낸다. 서버는 동봉된 쿠키를 읽어서 사용자가 누구인지 파악한다.

브라우저는 쿠키가 있다면 자동으로 동봉해서 보내주기 때문에 따로 처리안해도 된다.
<u>서버에서 브라우저로 쿠키를 보낼 때만 코드를 작성하여 처리하면 된다.</u>
즉, 서버는 미리 클라이언트에 요청자를 추정할 만한 정보를 쿠키로 만들어 보내고, 그 다음 부터는 클라이언트로부터 쿠키를 받아 요청자를 파악한다.
<u>쿠키가 누구인지 추적하고 있는 것이다.</u>

쿠키는 요청과 응답의 헤더에 저장된다. 

```js
//서버에서 직접 쿠키를 만들어 요청자의 브라우저에 넣어보자.
//server3.js
const http = require('http');

const parseCookies = (cookie = '') => 
    cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ... vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

http.createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
    console.log(req.url, cookies);
    res.writeHead(200, {'Set-Cookie' : 'mycookie=test'});
    res.end('Hello Cookie');
}).listen(8082, () => {
    console.log('8082 포트에서 서버 대기 중');
});
```

`parseCookies` 함수를 직접 만든 이유는...
쿠키는 name=zerocho;year=1994 처럼 문자열 형식으로 오므로
이를 {name:'zerocho', year:'1994'} 같이 객체로 바꾸기 위함이다.

`createServer` 메서드의 콜백에서는 제일 먼저 `req` 객체에 담겨 있는 쿠키를 분석한다. 쿠키는 `req.headers.cookie`에 들어있다. `req.headers`는 요청의 헤더를 의미한다.

응답의 헤더에 쿠키를 기록해야 하므로 `res.writeHead` 메서드를 사용했다.
첫 번째 인자는 <u>상태 코드</u>인데, `200`은 성공을 의미한다.
두 번째 인자에는 헤더의 내용을 입력한다.
`Set-Cookie`는 브라우저한테 다음과 같은 값의 쿠키를 저장하라는 의미이다. 실제로 응답을 받은 브라우저는 `mycookie=test`라는 쿠키를 저장한다.

![image-20191218101712739](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218101712739.png)![image-20191218101744908](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218101744908.png)![image-20191218101809446](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218101809446.png)

첫 번째 요청이 비어있지 않다. 다른 사이트나 프로그램이 미리 쿠키를 넣어두었을 수도 있기 때문이다. 쿠키를 삭제하고 다시 해보면 첫 요청은 `/ {}` 이 나올 것이다.

근데 우리는 요청을 분명 한 번만 했는데 요청이 두개나 있다.
브라우저는 파비콘을 HTML에서 유추할 수 없으면 서버에 파비콘 정보에 대한 요청을 보낸다. 따라서 예제에서 HTML에 파비콘 정보를 안넣었기 때문에 브라우저가 추가로 `/favicon.ico`을 요청한 것이다.

여튼 이케이케 서버가 제대로 쿠키를 심어 주었음을 확인할 수 있다.
첫 번째 요청을 보내기 전에는 브라우저가 어떠한 쿠키 정보도 가지고 있지 않는다.
서버는 응답의 헤더에 `mycookie=test`라는 쿠키를 심으라고 브라우저에게 명령했다.
따라서 브라우저는 쿠키를 심었고, 두 번째 요청(`/favicon.ico`)의 헤더에 쿠키가 들어 있음을 확인할 수 있다.



> > > **HTTP 상태 코드**
> > >
> > > - 2XX : 성공을 알리는 상태 코드. 
> > >   - 200(성공), 201(작성됨)
> > > - 3XX : redirection(다른 페이지로 이동)을 알리는 상태 코드. 어떤 주소를 입력했는데 다른 주소의 페이지로 넘어갈 때 이 코드가 사용된다. 
> > >   - 301(영구 이동), 302(임시 이동)
> > > - 4XX : <u>요청 오류</u>를 나타냄. 요청 자체에 오류가 있을 때 표시된다.
> > >   - 401(권한 없음), 403(금지됨), 404(찾을 수 없음)
> > > - 5XX : <u>서버 오류</u>를 나타냄. 요청은 제대로 왔지만 서버에 오류가 생겼을 때 발생한다. 이 오류를 클라이언트로 `res.writeHead`로 직접 보내는 경우는 없고, 예기치 못한 에러 발생 시 서버가 알아서 5XX대 코드를 보낸다. 
> > >   - 500(내부 서버 오류), 502(불량 게이트웨이), 503(서비스를 사용할 수 없음)



> > > **헤더와 본문**
> > >
> > > 요청과 응답은 모두 헤더와 본문을 가지고 있다.
> > > 헤더는 요청 또는 응답에 대한 정보를 가지고 있고,
> > > 본문은 서버와 클라이언트 간에 주고받을 실제 데이터를 담아두는 공간이다.
> > > 쿠키는 부가적인 정보이므로 헤더에 저장한다.
> > >
> > > ![image-20191218110736287](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218110736287.png)
> > >
> > > General은 공통된 헤더, Request Headers는 요청의 헤더, Response Headers는 응답의 헤더이다.
> > >
> > > Response Headers의 Set-Cookie와 Request Headers의 Cookie만 보면 된다.
> > > ![image-20191218110934727](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218110934727.png)![image-20191218111004781](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218111004781.png)
> > >
> > > Set-Cookie는 브라우저에게 해당 쿠키를 심으라는 명령을 내리는 것.
> > > 브라우저는 쿠키를 심은 후, 다음부터 요청을 보낼 때 Requset Headers의 Cookie로 쿠키를 보낸다.
> > >
> > > 응답의 본문도 볼 수 있다. `res.end`로 보냈던 문자열이 보인다.
> > > ![image-20191218111233171](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218111233171.png)
> > >
> > > Cookie 탭에서 쿠키만 따로 확인할 수 있다.
> > > ![image-20191218111406240](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218111406240.png)



```js
//아직까지는 단순히 쿠키만 심었다. 그 쿠키가 나인지 식별해주는 방법에 대해 알아보자.
//server4.js
const http = require('http');
const fs = require('fs');
const url = require('url');
const qs = require('querystring');

const parseCookies = (cookie = '') => 
    cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ... vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

http.createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
    if(req.url.startsWith('/login')){
        const {query} = url.parse(req.url);
        const {name} = qs.parse(query);
        const expires = new Date();
        expires.setMinutes(expires.getMinutes() + 5);
        res.writeHead(302, {
            Location: '/',
            'Set-Cookie' : `name=${encodeURIComponent(name)}; Expires=${expires.toGMTString()}; HttpOnly; Path=/`,
        });
        res.end();
    }
    else if(cookies.name){
        res.writeHead(200, {'Content-Type' : 'text/html; charset=utf8'});
        res.end(`안녕? ${cookies.name}`);
    }
    else{
        fs.readFile('./4ch/4.2/server4.html', (err, data) => {
            if(err){
                throw err;
            }
            res.end(data);
        });
    }
}).listen(8083, () => {
    console.log('8083 포트에서 서버 대기 중');
})

```

```html
<!-- server4.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>쿠키&세션</title>
</head>
<body>
    <form action="/login">
        <input id="name" name="name" placeholder="이름 입력" />
        <button id='login'>로그인</button>
    </form>
</body>
</html>
```

![image-20191218122554635](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218122554635.png)![image-20191218124043142](C:\Users\SinJ\AppData\Roaming\Typora\typora-user-images\image-20191218124043142.png)

> >- 주소가 `/login`으로 시작할 경우
> >
> >  1. url과 querystring 모듈로 각각 주소와 주소에 딸려오는 query를 분석한다. 
> >  2. 쿠키의 만료 시간도 지금으로부터 5분 뒤로 설정했다.
> >  3. 302 응답코드, redirect 주소와 함께 쿠키를 헤더에 넣는다. 브라우저는 이 응답 코드를 보고 페이지를 해당 주소로 redirect한다.
> >  4. 헤더에는 한글을 설정할 수 없으므로 name 변수를 encodeURIComponenti 메서드로 인코딩한다.
> >
> >  
> >
> >- 그 외의 경우(/로 접속했을 때 등..)
> >
> >  1. 먼저 쿠키가 있는지 없는지 확인.
> >  2. 쿠키가 없다면 로그인 페이지를 보낸다. 쿠키가 있다면 로그인 상태로 간주하여 `.end()`가 응답을 한다. `.end` 메서드에 한글이 들어가면 인코딩 문제가 발생하므로 `res.writeHead`에 인코딩을 명시해준다.

쿠키를 설정할 때 만료 시간(Expires)과 HttpOnly, Path 같은 옵션을 부여했다. 쿠키는 설정할 때 각종 옵션을 부여할 수 있고 각각의 옵션들은 (;)으로 구분한다.

- **쿠키명=쿠키값** : 기본적인 쿠키의 값. `mycookie=test` 또는 `name=zerocho` 같이 설정한다.
- **Expires=날짜** : 만료 기한. 이 기한이 지나면 쿠키가 제거된다. 기본값은 클라이트가 종료될 때까지이다.
- **Max-age=초** : Expires와 비슷하지만 날짜 대신 초를 입력할 수 있다. 해당 초가 지나면 쿠키가 제거된다. Expires보다 우선이다.
- **Domain=도메인명** : 쿠키가 전송될 도메인을 특정할 수 있다. 기본값은 현재 도메인이다.
- **Path=URL** : 쿠키가 전송될 URL을 특정할 수 있다. 기본값은 (/)이고 이 경우 모든 URL에서 쿠키를 전송할 수 있다.
- **Secure** : HTTPS일 경우에만 쿠키가 전송된다.
- **HttpOnly** : 설정 시 JS에서 쿠키에 접근할 수 없다. 쿠키 조작을 방지하기 위해 설정하는 것이 좋다.



원하는 대로 동작하기는 하지만 이 방식은 상당히 위험하다. 쿠키가 노출되어 있어 조작될 위험이 있다. 따라서 이름 같은 민감한 개인정보를 쿠키에 넣어두는 것은 적절하지 못하다.

```js
//서버가 사용자 정보를 관리하도록 해보자
//server5.js
const http = require('http');
const fs = require('fs');
const url = require('url');
const qs = require('querystring');

const parseCookies = (cookie = '') => 
    cookie
    .split(';')
    .map(v => v.split('='))
    .map(([k, ... vs]) => [k, vs.join('=')])
    .reduce((acc, [k, v]) => {
        acc[k.trim()] = decodeURIComponent(v);
        return acc;
    }, {});

const session = {};

http.createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
    if(req.url.startsWith('/login')){
        const {query} = url.parse(req.url);
        const {name} = qs.parse(query);
        const expires = new Date();
        expires.setMinutes(expires.getMinutes() + 5);
        const randomInt = Date.now();
        session[randomInt] = {
            name,
            expires,
        };
        res.writeHead(302, {
            Location: '/',
            'Set-Cookie' : `name=${encodeURIComponent(name)}; Expires=${expires.toGMTString()}; HttpOnly; Path=/`,
        });
        res.end();
    }
    else if(cookies.session && session[cookies.session].expires > new DataCue()){
        res.writeHead(200, {'Content-Type' : 'text/html; charset=utf8'});
        res.end(`안녕? ${cookies.name}`);
    }
    else{
        fs.readFile('./4ch/4.2/server4.html', (err, data) => {
            if(err){
                throw err;
            }
            res.end(data);
        });
    }
}).listen(8083, () => {
    console.log('8083 포트에서 서버 대기 중');
})
```

`server4.js` 와 달리 쿠키에 이름을 담아서 보내는 대신, `randomInt` 라는 임의의 숫자를 보냈다. 사용자의 이름과 만료 시간은 `session` 이라는 객체에 대신 저장했다.

`cookie.session`이 있고 만료 기한을 넘기지 않았다면 `session` 변수에서 사용자 정보를 가져와서 사용한다.