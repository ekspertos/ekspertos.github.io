---
layout: post
title:  "20210604 Git version 3.20.0 오류"
subtitle: "Git 오류"
categories: envOps
tags: blog github pages jekyll Git Push OpenSSH
comments: true
---

## 개요
> [TheoryDB](#https://theorydb.github.io/envops/2019/05/03/envops-blog-github-pages-jekyll/)님의 글을 보며 Github 블로그를 구축하는 와중 직면한 발생한 Git에러와 해결 방법을 제시하고자 합니다.

## Git Error
 `git push`를 한 후 OpenSSH 인증과정으로 넘어가는 과정에서 ``서버가 응답을 하지 않는 에러``가 발생했습니다.
 ![이미지](https://ekspertos.github.io/assets/img/envops/2021-06-24-Git-Error-Resolution2.PNG "Git-Error")

```
 $ GIT_TRACE=1 GIT_TRANSFER_TRACE=1 GIT_CURL_VERBOSE=1 git push -u origin master
02:43:50.317253 exec-cmd.c:237          trace: resolved executable dir: C:/Program Files/Git/mingw64/bin
02:43:50.317253 git.c:455               trace: built-in: git push -u origin master
02:43:50.325254 run-command.c:667       trace: run_command: GIT_DIR=.git git remote-https origin https://github.com/ekspertos/ekspertos.github.io.git
02:43:50.349249 exec-cmd.c:237          trace: resolved executable dir: C:/Program Files/Git/mingw64/libexec/git-core
02:43:50.357248 git.c:744               trace: exec: git-remote-https origin https://github.com/ekspertos/ekspertos.github.io.git
02:43:50.357248 run-command.c:667       trace: run_command: git-remote-https origin https://github.com/ekspertos/ekspertos.github.io.git
02:43:50.405246 exec-cmd.c:237          trace: resolved executable dir: C:/Program Files/Git/mingw64/libexec/git-core
02:43:50.421242 http.c:774              == Info: Couldn't find host github.com in the (nil) file; using defaults
02:43:50.445240 http.c:774              == Info:   Trying 15.164.81.167:443...
02:43:50.469256 http.c:774              == Info: Connected to github.com (15.164.81.167) port 443 (#0)
02:43:50.469256 http.c:774              == Info: ALPN, offering h2
02:43:50.469256 http.c:774              == Info: ALPN, offering http/1.1
02:43:50.485238 http.c:774              == Info: successfully set certificate verify locations:
02:43:50.485238 http.c:774              == Info:  CAfile: C:/Program Files/Git/mingw64/ssl/certs/ca-bundle.crt
02:43:50.485238 http.c:774              == Info:  CApath: none
02:43:50.485238 http.c:774              == Info: TLSv1.3 (OUT), TLS handshake, Client hello (1):
02:43:50.493312 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Server hello (2):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Certificate (11):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, CERT verify (15):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Finished (20):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
02:43:50.501234 http.c:774              == Info: TLSv1.3 (OUT), TLS handshake, Finished (20):
02:43:50.501234 http.c:774              == Info: SSL connection using TLSv1.3 / TLS_AES_128_GCM_SHA256
02:43:50.501234 http.c:774              == Info: ALPN, server accepted to use h2
02:43:50.501234 http.c:774              == Info: Server certificate:
02:43:50.501234 http.c:774              == Info:  subject: C=US; ST=California; L=San Francisco; O=GitHub, Inc.; CN=github.com
02:43:50.501234 http.c:774              == Info:  start date: Mar 25 00:00:00 2021 GMT
02:43:50.501234 http.c:774              == Info:  expire date: Mar 30 23:59:59 2022 GMT
02:43:50.501234 http.c:774              == Info:  subjectAltName: host "github.com" matched cert's "github.com"
02:43:50.501234 http.c:774              == Info:  issuer: C=US; O=DigiCert, Inc.; CN=DigiCert High Assurance TLS Hybrid ECC SHA256 2020 CA1
02:43:50.501234 http.c:774              == Info:  SSL certificate verify ok.
02:43:50.501234 http.c:774              == Info: Using HTTP2, server supports multi-use
02:43:50.501234 http.c:774              == Info: Connection state changed (HTTP/2 confirmed)
02:43:50.501234 http.c:774              == Info: Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
02:43:50.501234 http.c:774              == Info: Using Stream ID: 1 (easy handle 0x2bb94867b88)
02:43:50.501234 http.c:721              => Send header, 0000000209 bytes (0x000000d1)
02:43:50.501234 http.c:733              => Send header: GET /ekspertos/ekspertos.github.io.git/info/refs?service=git-receive-pack HTTP/2
02:43:50.501234 http.c:733              => Send header: Host: github.com
02:43:50.501234 http.c:733              => Send header: user-agent: git/2.32.0.windows.1
02:43:50.501234 http.c:733              => Send header: accept: */*
02:43:50.501234 http.c:733              => Send header: accept-encoding: deflate, gzip, br, zstd
02:43:50.501234 http.c:733              => Send header: pragma: no-cache
02:43:50.501234 http.c:733              => Send header:
02:43:50.517251 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
02:43:50.517251 http.c:774              == Info: TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
02:43:50.517251 http.c:774              == Info: old SSL session ID is stale, removing
02:43:50.709536 http.c:721              <= Recv header, 0000000013 bytes (0x0000000d)
02:43:50.709536 http.c:733              <= Recv header: HTTP/2 401
02:43:50.709536 http.c:721              <= Recv header, 0000000026 bytes (0x0000001a)
02:43:50.709536 http.c:733              <= Recv header: server: GitHub Babel 2.0
02:43:50.709536 http.c:721              <= Recv header, 0000000026 bytes (0x0000001a)
02:43:50.709536 http.c:733              <= Recv header: content-type: text/plain
02:43:50.709536 http.c:721              <= Recv header, 0000000054 bytes (0x00000036)
02:43:50.709536 http.c:733              <= Recv header: content-security-policy: default-src 'none'; sandbox
02:43:50.709536 http.c:721              <= Recv header, 0000000020 bytes (0x00000014)
02:43:50.709536 http.c:733              <= Recv header: content-length: 26
02:43:50.709536 http.c:721              <= Recv header, 0000000040 bytes (0x00000028)
02:43:50.709536 http.c:733              <= Recv header: www-authenticate: Basic realm="GitHub"
02:43:50.709536 http.c:721              <= Recv header, 0000000023 bytes (0x00000017)
02:43:50.709536 http.c:733              <= Recv header: x-frame-options: DENY
02:43:50.709536 http.c:721              <= Recv header, 0000000055 bytes (0x00000037)
02:43:50.709536 http.c:733              <= Recv header: x-github-request-id: E5DE:5CC8:546996:5C847B:60D372D5
02:43:50.709536 http.c:721              <= Recv header, 0000000002 bytes (0x00000002)
02:43:50.709536 http.c:733              <= Recv header:
02:43:50.709536 http.c:774              == Info: Connection #0 to host github.com left intact
02:43:50.709536 run-command.c:667       trace: run_command: 'git credential-manager-core get'
02:43:50.837386 exec-cmd.c:237          trace: resolved executable dir: C:/Program Files/Git/mingw64/libexec/git-core
02:43:50.837386 git.c:744               trace: exec: git-credential-manager-core get
02:43:50.837386 run-command.c:667       trace: run_command: git-credential-manager-core get
```
이후로 진행되지 않습니다.

`ctrl+c` 를 누르고 기다려 보니 openSSH가 뜨기는 하지만 더 이상 진행이 되지 않습니다.
이게 SSH문제인지 알고 방화벽을 수정하고 난리를 쳐봤지만 헛고생이였습니다.
![이미지](https://ekspertos.github.io/assets/img/envops/2021-06-24-Git-Error-Resolution.PNG "Git-Error2")

---

## 오류해결
구글링을 오래동안, 열심히 한 결과 저와 똑같은 문제를 직면한 사람을 찾았습니다.
<https://github.com/microsoft/Git-Credential-Manager-Core/issues/364>
그리고 찾고 찾던 해결방법 또한 올려져 있었지요.
```
git config --global credential.provider generic
```
다음 문장을 치고 git push를 하니 마법과 같이 팝업창이 뜨면서 git 서버와 연결할 수 있었지요.

---

여러분들은 저와 같이 5시간동안 시간낭비하지 않길 바라며 이 글을 올립니다!
