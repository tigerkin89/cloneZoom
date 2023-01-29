# Noom

Zoom Clene using NodeJS, WebRTC and Websockets.

# 01 클론 줌 시작하기
## 서버 준비하기
### package.json
- Node.js를 기반으로 한  프로젝트 정보와 의존성을 기록하고 관리.
- npm(node pakcage manager)이 제공하는 npm init 명령어를 이용하면 package.jon이 자동 생성
- npm init -y 기본 템플릿 생성 과정을 좀 더 빠르게 처리

### nodemon 설치

- 소스 코드의 변화를 감지하여 자동으로 서버 프로그램을 재시작해주는 도구
- npm i nodemon -D ## nodemon 설치
  -- npm의 -D 옵션은 개발 및 테스트를 위해 설치
  -- nodemon 설치후에 package.json 파일을 확인해 보자

### babel 설치

- 자바스크립트 컴파일러
- ES6로 작성된 코드를 ES5로 변환하는데 사용
- ES6(ES2105) 이상의 최신 자바스크립트 문법으로 작성된 코드가 노드JS(NodeJS)에서 실행이 안 되는 경우가 종종있습니다. 이럴 경우 어쩔 수 없이 예전 자바스크립트 문법으로 코드를 재작성하기도 하는데요. 이번 포스팅에서는 자바스크립트 Transpiler인 Babel을 이용하여 이 문제를 해결해보겠습니다
- Babel은 JavaScript Transpiler 중에서 가장 널리 사용
- https://velog.io/@jiheon/Node.js-Babel
- npm i @babel/core @babel/cli @babel/node @babel/preset-env -D

### 서버 파일 및 설정 파일 생성하기

- ./src/server.js
- ./nodemon.json , ./babel.config.json

### 서버 프로그램 실행

- npm run dev
- server.js 파일을 변경하면 nodemon이 감지하여 재시작하는 것을 볼 수 있다.

### 웹 프레임워크 express 설치

- Node.js 환경에서 API서버를 개발할 때 사용할 수 있는 웹 프레임워크
- npm i express

### ./src/server.js 수정

- express 환경에 맞게 수정

## 프로트엔즈 준비 작업
### ./src/public/js 폴더 생성

- app.js  현재는 빈파일 만들기


### pug (뷰엔진) 설치

- 다양한 뷰엔진중 pug 설치
- npm i pug

### ./src/views 폴더 생성하고 뷰 만들기

- home.pug 간단하게 만들기

### server.js에 뷰엔진 설정하기

### MVP.css 적용하기


# ch02. 웹소켓을 이용한 실시간 기능 구현하기

## 1. 웹소켓
http는 connectless, 웹소켓은 connect oriented

## 2. 웹소켓 설치하고 서버 만들기

### ws 패키지 설치

```
npm i ws
```

### 웹소켓 프로토콜 추가
express 서버에 ws 패키지 기능 이용하기 

./src/server.js 수정

    //02-2 Express 서버에 websocket 기능 추가 하기
    //app.listen(3000,handleListen);

    const server = http.createServer(app);
    //HTTP 서버위에 웹소켓 서버를 추가하기
    const wss = new WebSocket.Server({server});
    server.listen(3000,handleListen);

## 3. 웹소켓 이벤트

| 이벤트명 | 설명 |
|---|---|
|close|서버가 닫혔을 때 발생|
|connection|서버와 사용자간의 연결 될때 |
|error|연결되어 있는 서버에서 오류가 발생 될때 |
|headers|서버의 응답 헤더가 소켓에 기록되기 전에 발생|
|listening| 연결되어 있는 서버가 바인딩되었을때 발생|


서버는 wss.on()메소드를 이용하여 이벤트를 등록한다.

    ./src/server.js
  
    function handleConnection(socket){
      console.log(socket)
    }

    wss.on("connection",handleConnection)

클라이언트는 websocket 연결을 한다.
1. websocket 프로토콜은 ws 또는 wss을 지원한다.
2. 클라이언트의 주소창에 입력한 것을 JavaScript 변수 ${window.location.host}를 이용한다. 
3. `` 사용하여야 한다. ""사용하면 에러 발생
<pre>
// const socket = new WebSocket("ws://localhost:3000")
// 에러 const socket = new WebSocket("ws://${window.location.host}");
const socket = new WebSocket(`ws://${window.location.host}`);
</pre>

## 4. 메세지 주고 받기

### 서버 이벤트 핸들러를 직권적으로 수정

변경전
<pre>
function handleConnection(socket){
    console.log(socket)
  }
//02-3 웹소켓 이벤트 핸들링 
wss.on("connection",handleConnection)
</pre>

변경후
<pre>
wss.on("connection",(socket) =>{
    console.log(socket);
})
</pre>

### 서버가 클라이언트에게 메세지 보내기
<pre>
./src/server.js
wss.on("connection",(socket) =>{
    // console.log(socket);
    console.log("Connected to Brower");
    socket.send("Hello!!");
})
</pre>

### 클라이언트 웹소켓 이벤트 추가
https://developer.mozilla.org/ko/docs/Web/API/WebSocket
https://ko.javascript.info/websocket

| 이벤트명 | 설명 |
|---|---|
|open|커넥션이 제대로 만들어졌을 때 발생함|
|message|데이터를 수신하였을 때 발생함 |
|error|연결되어 있는 서버에서 오류가 발생 될때 |
|close|커넥션이 종료되었을 때 발생함|

<pre>
./src/public/js/app.js
 socket.addEventListener("open",()=>{
    console.log("Connected to Server");
 })

 socket.addEventListener("message",(message)=> {
    console.log("Just got this",message,"from the Server");
 })
 <!-- 출력 결과
 Just got this MessageEvent {isTrusted: true, data: 'Hello!!', origin: 'ws://localhost:3000', lastEventId: '', source: null, …} from the Server -->

 socket.addEventListener("close",()=>{
    console.log("Disconnected from Server");
 })
 </pre>

 JS의 message 구조체는 많은 정보를 갖고 있다. 서버가 전달한 데이타는 message.data에 저장이 되어 있다.
 <pre>
 message 구조체
{isTrusted: true, data: 'Hello!!', origin: 'ws://localhost:3000', lastEventId: '', source: null, …} 
</pre>

# ch3. 실시간 채팅 완성하기
## 03-1 채팅 기능 준비하기
브라우저에서 입력 필드의 목록을 만들고, 메세지를 주고받은 결과를 바로 표시

### 화면에 텍스트 입력 준비 웹 요소 추가하기
<pre>
./src/views/home.png
  ul 
  form
    input(type="text",placeholder="write a msg",required)
    button Send 
</pre>

### 클라이언트 form 이벤트 등록하기

<pre>
// ch 3-1 form 이벤트 등록하기
function handleSummit(event) {
    event.preventDefault();
    const input = messageForm.querySelector("input");
    socket.send(input.value);
    input.value = "";
}

messageForm.addEventListener("submit",handleSummit);
</pre>

### 서버는 클라이언트 메세지를 돌려주기
<pre>
wss.on("connection",(socket) =>{
    // console.log(socket);
    console.log("Connected to Brower");
    socket.on("close",() => {
        console.log("Disconnect from Broser1")
    });
    socket.on("message", (message) => {
        //03-1 클라이언트 메세지를 돌려준다
        console.log( "recv : " +`${message}`)
        socket.send(`${message}`);

    });
    // socket.send("Hello!!");

</pre>

## 03-2 사용자간 채팅하기

여러사람/클라이언트가 동시에 접속한 경우 처리 필요. 브라우저 2개 탭에서 동시에 접속한 경우 각 탭마다 서버는 다른 메세지를 보내기

서버는 client 접속 정보를 기록하고, client로부터 메세지를 수신하면 접속된 client에게 메세지를 전달한다.
<pre>
./src/server.js
wss.on("connection",(socket) =>{
    // console.log(socket);
    //03-2 클라이언트의 접속 정보를 기록ㅎㄴㄷ.
    sockets.push(socket);
    console.log("Connected to Brower");
    socket.on("close",() => {
        console.log("Disconnect from Broser1")
    });
    socket.on("message", (message) => {
        //03-1 클라이언트 메세지를 돌려준다
        console.log( "recv : " +`${message}`)
        // socket.send(`${message}`);
        //03-2 모든 클라이언트에게 메세지를 돌려준다
        sockets.forEach(aSocket => {
            aSocket.send(`${message}`);
        });

    });
    // socket.send("Hello!!");
})
</pre>

클라이언트는 서버로부터 수신된 메세지를 화면에 출력한다.
<pre>
./src/public/js/app.js
socket.addEventListener("message",(message)=> {

     console.log("Recv : " +message.data)
    // 03-2 서버로부터 수신된 메세지를 브라우저에게 출력하기
    const li = document.createElement("li");
    li.innerText = message.data;
    messageList.append(li)

 })
 </pre>

 ## 03-3 닉네임추가하기
 서버에 클라이언트가 여러명 접속할때 클라이언트를 구분하기 위해 클라이언트에 닉네임 부여

html에는 nickname의 form 추가
 <pre>
  ./src/views/home.pug 

            //- h2 Welcome to Noom
            // 03-3 닉네임 폼 추가하기
            // 03-3 form을 구분하기 위해 id 지정
            form#nick
                input(type="text",placeholder="choose a nicname",required)
                button Save  
            ul 
            form#message
                input(type="text",placeholder="write a msg",required)
                button Send 
 </pre>

 JS에는 nickname의 event handler 추가
 <pre>
 ./src/public/js/app.js
 function handleNickSubmit(event) {
    event.preventDefault();
    const input = nickForm.querySelector("input");
    socket.send(input.value);
    input.value="";
}
nickForm.addEventListener("submit",handleNickSubmit);
 </pre>

 ### JS에서 닉네임, 메시지를 JSON 문자열로 전달하기

 JS에서 JSON을 문자열로 변환은 JSON.stringfy이용한다.

 JS에서 닉네임, 메시지를 서버로 보낼때 JSON문자열로 변환해서 보낸다.
 <pre>
 ./src/public/js/app.js
 // ch 3-3 JSON을 문자열로 변환
function makeMessage(type,payload) {
    const msg = {type,payload};
    return JSON.stringify(msg)
}

// ch 3-1 form 이벤트 등록하기
function handleSummit(event) {
    event.preventDefault();
    const input = messageForm.querySelector("input");

    // ch 3-3 JSON을 문자열로 변환
    // socket.send(input.value);
    socket.send(makeMessage("new_message",input.value));
    input.value = "";
}
</pre> 

## 03-4 서버에서 닉네임 처리 하기

### 문자열을 JSON으로 변환하기 (JSON.parse).

<pre>
socket.on("message", (message) => {
  // 03-4 msg.type별 행동하기, message를 수신하면 전체 전달하고,
  // nick정보를 저장ㅎㄴ다.
  const msg = JSON.parse(message);
  console.log("RECV", msg.type,msg.payload);
  switch (msg.type) {
    case "new_message":
      sockets.forEach ( aSocket => {
      //03-4 익명자 고려
      // aSocket.send(`${msg.payload}`);
        aSocket.send(`${socket.nickname}: ${msg.payload}` );
      });
      break;
    case "nickname":
      socket["nickname"] = msg.payload;
      break;
  }
        

});
</pre>

