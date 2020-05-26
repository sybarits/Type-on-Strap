---
layout: post
title: response type
tags: [response, type, blob, arraybuffer, ajax]
---


## AJAX
AJAX란 비동기 자바스크립트와 XML (Asynchronous JavaScript And XML) 이다. 서버와 통신하기 위해 XMLHttpRequest 객체를 사용한다. JSON, XML, HTML 그리고 일반 텍스트 형식 등을 포함한 다양한 포맷을 주고 받을 수 있다. AJAX의 강력한 특징은 페이지 전체를 리프레쉬 하지 않고서도 수행 되는 "비동기성"이다. 이러한 비동기성을 통해 사용자의 Event가 있으면 전체 페이지가 아닌 일부분만 업데이트 할 수 있다.


## XMLHttpRequest
XHR 객체는 서버와 상호작용하기 위해 사용된다. 전체 페이지의 새로고침 없이도 URL로부터 데이터(모든 종류의)를 받아올 수 있다. 아래는 예제 코드이다.

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);

// Hack to pass bytes through unprocessed.
xhr.overrideMimeType('text/plain; charset=x-user-defined');

xhr.onreadystatechange = function(e) {
  if (this.readyState == 4 && this.status == 200) {
    var binStr = this.responseText;
    for (var i = 0, len = binStr.length; i < len; ++i) {
      var c = binStr.charCodeAt(i);
      //String.fromCharCode(c & 0xff);
      var byte = c & 0xff;  // byte at offset i
    }
  }
};

xhr.send();
```

### responseType
XMLHttpRequest는 어떠한 형식의 데이터도 전송이 가능하다고 하였다. 위의 예제를 통해 데이터를 받아 확인 할 수 있지만, 처리에 난감한 부분이 있다. 대신 XHR.responseType 지정을 통해 파일을 받고, mimeType을 설정해주는 방법을 사용할 수 있다. responseType은  "text", "arraybuffer", "blob", or "document" 가 가능하고, 아무 설정을 해주지 않으면 "text"로 응답을 받는다.

```javascript
var xhr = new XMLHttpRequest();
xhr.open('GET', '/path/to/image.png', true);
xhr.responseType = 'blob';

xhr.onload = function(e) {
  if (this.status == 200) {
    // Note: .response instead of .responseText
    var blob = new Blob([this.response], {type: 'image/png'});
    ...
  }
};

xhr.send();
```

### blob & arraybuffer
responseType으로 file을 받을 때 blob과 arraybuffer 를 설정해 받을 수 있다. 이 둘은 file data를 받는 것은 동일하지만, arraybuffer는 javascript에서 조작하기 쉬운 array형태로 data를 받을수 있다는 차이점이 있다.  

## axios responseType blob
브라우저에서 XMLHttpRequest 객체를 이용해 통신 하는 모든 방법들은 위 설정을 활용하여 데이터를 주고 받을 수 있다. axios도 마찬가지다.
```javascript
downFileGet(){
    axios({
        method: 'GET',
        url: '/GETDownload',                 
        responseType: 'blob'
    })    
    .then(response =>{        
        const url = window.URL.createObjectURL(new Blob([response.data], { type: response.headers['content-type'] }));
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'test.xlsx');
        document.body.appendChild(link);
        link.click();
    })                                
}

downFilePost(){
  axios({
      method: 'POST',
      url: '/POSTdownload',
      responseType: 'blob',
      headers: {
          "Content-Type": "application/json"
      },   
      data: {
          imgName: star       
      }
  })
  .then(response =>{
      const url = window.URL.createObjectURL(new Blob([response.data], { type: response.headers['content-type'] }));
      const link = document.createElement('a');
      link.href = url;
      link.setAttribute('download', 'star.jpg');
      document.body.appendChild(link);
      link.click();
  })
}


///// node server code //////

const express = require("express");
const fs = require("fs");
const path = require("path");
const mime = require("mime");
const router = express.Router();

router.use(express.json());

router.post('/', function (req, res) {
    let file = "./" + req.body.filePath;
    try {
      if (fs.existsSync(file)) {
        let fileName = req.body.fileName ? req.body.fileName : path.basename(file);
        let mimetype = mime.getType(fileName);
        res.setHeader("Content-Disposition", "attachment; filename=" + fileName);
        res.setHeader("Content-Type",mimetype);
        let fileStream = fs.createReadStream(file).on('error',(err)=>{console.log("CREATEWRITESTREAM ERROR !!",err)});
        fileStream.pipe(res);
      } else {
        res.send("There is no such file.");
        return;
      }
    } catch (e) {
      console.log(e);
      res.send("There is an Error downloading your file.");
      return;
    }
});

module.exports = router;

```


출처: https://www.html5rocks.com/en/tutorials/file/xhr2/  
https://developer.mozilla.org/ko/docs/Web/API/XMLHttpRequest  
https://developer.mozilla.org/ko/docs/Web/Guide/AJAX/Getting_Started  
https://stackoverflow.com/questions/11821096/what-is-the-difference-between-an-arraybuffer-and-a-blob/39951543  
https://soonh.tistory.com/38  

