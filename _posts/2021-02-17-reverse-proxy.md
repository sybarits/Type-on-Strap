---
layout: post
title: Reverse Proxy
tags: [proxy, server, reverse]
---

## Proxy
프록시 서버는 중계 서버이다. 보안, 트래픽 분산 등을 할 수 있도록 도와주는 서버다. 프록시 서버는 요청 내용에 대한 응답을 캐시하여 동일한 요청의 경우 캐시에서 바로 응답을 보내 줄 수 있다.

## Forward Proxy
흔히 말하는 프록시란 포워드 프록시(forward proxy)를 의미한다. 웹브라우저나 토렌트를 사용할 때 프록시 설정을 하면 그 프록시를 경유하여 데이터를 주고 받는다. www.naver.com에 요청을 보낼 경우 직접 요청을 보내지 않고 설정된 프록시를 거쳐 요청을 보낸다. 요청을 보내는 입장에서 요청을 보내는 목적지는 그대로지만 프록시 설정을 통해 데이터가 오가도록 한다. 서버는 요청자가 누구인지 알 수 없다. 서버는 프록시 서버로 부터 요청을 받았기 때문이다.


## Reverse Proxy
리버스 프록시는 요청을 받은 프록시 서버가 다른 서버로 부터 응답을 가져와 전달하도록 한다. 요청을 보내는 쪽에서 요청의 목적지는 리버스 프록시 설정이 이루어진 서버이다. 리버스 프록시 설정이 된 서버는 내부 설정 정보를 이용해 실제 운영 중인 서버로 요청을 보낸다. 실제 운영 중인 서버의 응답은 요청자에게 직접 전달 되지 않고, 리버스 프록시 서버로 전달된다. 리버스 프록시 서버는 요청에 대한 응답으로 자신이 운영 서버에서 받은 응답을 보낸다. 요청을 보내는 쪽은 실제 운영 서버를 알지 못한다.

출처: http://blog.naver.com/PostView.nhn?blogId=alice_k106&logNo=221190043948&redirect=Dlog&widgetTypeCall=true&directAccess=false    
https://www.lesstif.com/system-admin/forward-proxy-reverse-proxy-21430345.html  
https://brainbackdoor.tistory.com/113  
https://firework-ham.tistory.com/23  
