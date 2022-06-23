---
layout: post
title:  "Redirection permission 문제해결 방법"
subtitle: "Linux Redirection"
categories: envOps
tags: blog github pages jekyll
comments: true
---

## Redirection Error

file1와 file2 에 대한 권한이 모두 없는 상태에서 redirection 명령을 사용할 때 발생하는 에러이다.
```bash
sudo sed -n '10,20p' file1 >> file2
-bash: file2: Permission denied
```

sudo 명령어를 사용하면 file1 내용을 읽는 부분은 문제없이 실행되지만, ``file2에 대한 쓰기 부분``은
권한 문제에 의해 실행이 되지 않는다.

## Solution

> https://stackoverflow.com/questions/82256/how-do-i-use-sudo-to-redirect-output-to-a-location-i-dont-have-permission-to-wr

부등호(>)를 사용하는 대신 `tee` 명령어를 사용하는 방법을 제시한다.  

```
sudo sed -n '10,20p' file1 | sudo tee -a file2 > /dev/null
```

화면에 내용이 출력되는 것 `/dev/null` 으로 redirect 하는 부분도 추가되어 있다.

사용 설명서는 다음과 같다

```
$ man tee

NAME
  tee - read from standard input and write to standard output and files
SYNOPSIS
  tee [OPTION]... [FILE]...

DESCRIPTION
  Copy standard input to each FILE, and also to standard output.
  -a, --append
        append to the given FILEs, do not overwrite
```
