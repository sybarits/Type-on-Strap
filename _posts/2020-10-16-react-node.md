---
layout: post
title: React, Node.js 연동하기
tags: [React, node, nodejs, proxy]
---

react와 node.js를 이용해 간단한 웹 어플리케이션을 만들어보자. 이 글에서는 기본적인 어플리케이션 세팅을 다룬다.

## Node.js 설치 확인
터미널에 아래와 같이 명령어를 입력하여 Node.js가 설치되어있는지 확인한다.
```
node -v
npm -v
```
이 글은 node 10.21.0, npm 5.8.0 기준으로 작성하였다.

## React 사용 준비
npm 버전이 5.2 이상일 경우 아래 명령어를 통해 react를 사용할 수 있도록 준비한다.

```
npx install create-react-app
create-react-app {project name}
```
이하 작업은 프로젝트 폴더 아래에서 진행한다. 프론트엔드에서 백엔드로 요청을 보낼때 사용하기 위해 axios를 설치한다.
```
npm install axios --save
```

## 백엔드 준비
백엔드에서 사용할 미들웨어를 설치한다. 여기에서는 express를 사용한다. 몽고DB를 사용할 경우 mongodb도 함께 설치한다.

```
npm install express --save
npm install mongodb --save
```

프로젝트 폴더 아래에 server 폴더를 생성하고 그 아래에 server.js 파일을 만들어 백엔드 서버의 엔트리 포인트를 작성한다. mongodb 연결과 해제에 대한 내용과 서버 시작 로직을 ECMA5로 작성하였다.

```javascript
//server.js
const express = require('express');
const app = express();
const cors = require('cors');

app.use(cors());

const api = require('./routes/index');
app.use('/api',api);

const resultService = require('./routes/resultService');
app.use('/result',resultService);

var Client = require('mongodb').MongoClient;
const url = 'mogodb url';
Client.connect(url, function(error,db) {
    if (error) {
        console.log(':::: connect error ::::');
        console.log(error);
    } else {
        console.log(':::: connect success ::::');
        global.db = db;
    }
})

process.on('SIGINT', () => {
	  process.exit();
});

process.on('exit', () => {
    let db = global.db;
    db.close((err) => {
        if (err) {
            console.log("server :: DB close ERROR ",err);
        } else {
            console.log("server :: DB connection closed ");
        }
    });
});

const port = 30002;
app.listen(port, ()=>console.log(`Listening on port ${port}`));
```
server 폴더 아래 routes 폴더를 생성하고 실제 백엔드 로직 실행을 위한 파일들을 작성한다. 
```javascript
// routes/...
var express = require('express');
var router = express.Router();

router.use(express.json());

router.post('/findAll', function(req,res) {
    // console.log(':::: findAll called ::::');
    var cursor = global.db.db('DB name').collection('Collection Name').find({});
    cursor.toArray(function(err, docs) {
        console.log(docs);
        res.json(docs);
    })
});

module.exports = router;
```

## 프록시 설정
npm 명령어를 실행해 프록시 패키지를 설치한다.
```
npm install http-proxy-middleware --save
```
http-proxy-middleware 1.0.6 버전기준으로 src 폴더 아래에 setupProxy.js 파일을 만들고 아래와 같이 작성한다. /v1 경로로 요청하면 30002 포트로 요청이 간다. 예를 들어 axios를 이용해 프론트엔드에서 '/v1/api/apple'로 요청을 보내면 'http://localhost:30002/api/apple' 로 요청이 간다.(pathRewrite 설정 때문) 프록시 설정에 대한 자세한 내용은 https://www.npmjs.com/package/http-proxy-middleware 사이트를 참고한다.

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

module.exports = function (app) {
    app.use(
        '/v1',
        createProxyMiddleware( {
            target: 'http://localhost:30002/',
            changeOrigin: true,
            ws: true,
            pathRewrite: {
                '^/v1':''
            }
        })
    );
};
```

## 프론트엔드와 백엔드 동시에 실행하기
npm-run-all 패키지를 설치하고 package.json 파일에서 script 항목을 수정한다.
```
npm install npm-run-all --save

"scripts": {
    "start": "npm-run-all --parallel start:**",
    "start:client": "react-scripts start",
    "start:server": "node ./server/server.js",
    이하 생략
}
```
npm start 명령어를 실행하면 start:client에 적혀있는 react 실행 명령어가 실행되고 start:server에 적혀있는 서버 실행 명령어가 실행된다. 개발할때는 따로 실행하는 것이 편할수도 있다.


출처: https://singa-korean.tistory.com/46  
https://ssungkang.tistory.com/entry/React-React-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-create-react-app  
https://www.npmjs.com/package/http-proxy-middleware  
https://eunvanz.github.io/react/2018/06/05/React-create-react-app%EC%9C%BC%EB%A1%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0/  
