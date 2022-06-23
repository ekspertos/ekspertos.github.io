---
layout: post
title:  "vi 편집기 권한문제 해결방법"
subtitle: "vi permission error"
categories: envOps
tags: blog github pages jekyll
comments: true
---

## Solution

파일 권한이 없는 상태에서 이미 수정된 내용을 저장하는 방법이다

```
:w !sudo tee % > /dev/null
```

이 후 변경된 파일을 Load를 할 것인지 물어보는데 이때 ``L 키`` 를 눌러주면 된다.
