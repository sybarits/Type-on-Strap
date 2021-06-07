---
layout: post
title: CSR
tags: [csr, ssl, tls, certificate, key]
---

  CSR(Certificate Signing Request)에 대해서 알아본다.  
  

### 인증서(Certificate)란
public key는 모두에게 공개하는 키다.public key를 사용하는 입장에서 그조에서 보낸 키가 정말 맞는지 알 수 없기 대문에 이에 대한 확인을 제 3자가 대신 해준다. 이 제 3자가 인증기관(CA)라고 한다. 인증기관에서는 소유자 이름, 이메일, 인증서 용도, 유효기간, 위치 등을 포함, public key 와 public key의 해시값으로 인증서를 생성한다. 다시 말하면 개인키 소유자의 공개키와 인증기관의 개인키로 전자서명한 데이터를 인증서라 한다. 모든 인증서는 발급 기관이 있다. 최상위 인증기관은 Root CA라 부른다. 최상위 인증기관의 인증서는 자신의 개인키로 스스로 인증서에 서명하여 인증서를 만든다. 이를 Selt Signed Certificate라고 부른다.

### Private Key, Public Key
private key 를 이용해 암호화한 내용은 public key로 복호화 가능하고, 반대도 가능하다. key 하나는 개인이 비공개로 보유(private key)하고, 다른 key는 모두에게 공개(public key)한다. private key를 이용해 암호화하여 보낸다면, public key를 가진 모두가 그내용을 해독하여 볼 수 있다. private key를 이용한 암호화 메시지는 특정 개인(private key를 소유한)이 보냈다는 증명이다.

### CSR
CSR(Certificate Signing Request)는 인증서 서명 요청으로 인증서 발급을 위해 필요한 정보를 담고 있는 인증서 신청 형식 파일 또는 데이터이다. 포함하는 내용으로 개인키 생성 단계에서 만들어지는 키쌍 중 공개키가 포함되며, 인증서가 적용되는 도메인에 대한 정보가 들어있다. 이 데이터를 인증 기관으로 보내 자신의 공개키가 맞음을 확인한 인증서를 발급 받는다. 인증서를 구한 통신 대상은 인증서의 공개키를 이용해 인증서의 주인(인증서에 포함된 공개키의 쌍인 개인키를 가지고 있는 대상)과 암호화 통신을 할 수 있다. CSR 생성과 전달이 어렵고 조금씩 방법이 다르기 때문에 인증서 발급 대행 기관에서 개인키까지 생성해서 보내주기도 한다.

### OCSP
OCSP(Online Certificate Status Protocol)은 온라인 인증서 상태 프로토콜로 인증서 유효성 확인을 제공하는 프로토콜이다. 인증서가 폐기된 것인지 정상인지 빠르게 확인 가능하다. CRL(Certificate Revocation List)도 동일한 기능을 하지만, 이보다 개선된 표준이다.

### PEM
PEM(Privacy Enhanced Mail)은 디지털 인증서 및 키에 대한 매우 일반적인 컨테이너 형식이다. x509 인증서의 PEM 파일은 Base64로 인코딩된 인증서이다. 시작과 끝은 표시하는 머리글과 바닥글을 포함하는 텍스트 파일이다. 예시는 아래와 같다.

```
-----BEGIN CERTIFICATE-----
MIIGbzCCBFegAwIBAgIICZftEJ0fB/wwDQYJKoZIhvcNAQELBQAwfDELMAkGA1UE
BhMCVVMxDjAMBgNVBAgMBVRleGFzMRAwDgYDVQQHDAdIb3VzdG9uMRgwFgYDVQQK
DA9TU0wgQ29ycG9yYXRpb24xMTAvBgNVBAMMKFNTTC5jb20gUm9vdCBDZXJ0aWZp
...
Nztr2Isaaz4LpMEo4mGCiGxec5mKr1w8AE9n6D91CvxR5/zL1VU1JCVC7sAtkdki
vnN1/6jEKFJvlUr5/FX04JXeomIjXTI8ciruZ6HIkbtJup1n9Zxvmr9JQcFTsP2c
bRbjaT7JD6MBidAWRCJWClR/5etTZwWwWrRCrzvIHC7WO6rCzwu69a+l7ofCKlWs
y702dmPTKEdEfwhgLx0LxJr/Aw==
-----END CERTIFICATE-----
```

  
출처: https://www.ssl.com/ko/%EC%9E%90%EC%A3%BC-%EB%AC%BB%EB%8A%94-%EC%A7%88%EB%AC%B8/x-509-%EC%9D%B8%EC%A6%9D%EC%84%9C-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9E%85%EB%8B%88%EA%B9%8C/
  https://sarc.io/index.php/miscellaneous/213-ssl-tls
  https://www.lesstif.com/system-admin/openssl-root-ca-ssl-6979614.html  
  
