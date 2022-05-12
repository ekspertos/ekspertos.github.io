---
layout: post
title:  "마크다운 사용해보기"
subtitle: "마크다운(Markdown)"
categories: unclassified
tags: blog github pages jekyll markdown  
comments: true
---

## 개요
> Github 첫 글을 장식해봅시다
> [TheoryDB님](https://ekspertos.github.io/envops/2019/05/22/envops-blog-how-to-use-md/#markdown%EC%9D%98-%EB%B0%98%EB%93%9C%EC%8B%9C-%EC%95%8C%EC%95%84%EC%95%BC-%ED%95%98%EB%8A%94-%EB%AC%B8%EB%B2%95)의 글을 참고하여 작성했습니다

- 목차
  - [Markdown이란?](#markdown이란)
  - [마크다운 문법1](#마크다운-문법1)
  - [마크다운 문법2](#마크다운-문법2)




  ---
## Markdown이란?

[Markdown](http://kirkstrobeck.github.io/whatismarkdown.com/)은 문서작성을 지원하는 태그 형식의 문법입니다.
> __마크다운(출처- wiki백과)__
> 일반 텍스트로 서식이 있는 문서를 작성하는 데 사용되며, 일반 마크업 언어에 비해 문법이 쉽고 간단한 것이 특징이다. HTML과 리치 텍스트(RTF) 등 서식 문서로 쉽게 변환되기 때문에 응용 소프트웨어와 함께 배포되는 README 파일이나 온라인 게시물 등에 많이 사용된다.

* __마크다운 왜 사용하나요?__
이전에 사용하던 티스토리의 경우, 표와 수식을 사용할 때 많은 불편함을 느꼈습니다. 이 불편함을 해소하고자 찾아보다 마크다운을 이용하게 됬습니다.

* __마크다운의 좋은점이 뭔가요?__
  - 워드와 같은 편집기가 없은 웹 상에서 글을 작성하기 편리합니다.
  - 배우기가 정말 `쉽고 직관적`입니다.
  - 용량이 적어 `보관에 용이`편리합니다.
  - 작성 후 `HTML로 변환이 가능`합니다
  - ``링크를 작성하기 편리``합니다


---

## 마크다운 문법1
이 글이 작성되기 위해 사용된 문법들을 설명합니다.

* __강조(Emphasis)__
_문장에서 강조하고 싶은 단어를 눈에 띄게 합니다._
```
`텍스트 블록`
__볼드(진하게)__
_이탤릭체(기울기)_
~~취소선~~
<u>밑줄</u>
<span style="color:red">강조</span>
```
`텍스트 블록`
__볼드(진하게)__
_이탤릭체(기울기)_
~~취소선~~
<u>밑줄</u>
<span style="color:red">강조</span>

---

* __헤더(Header)__
_글의 구조 및 큰 틀을 잡을 떄 사용합니다._
```
# 글1
## 글2
### 글3
#### 글4
##### 글5
###### 글 6
```
# 글1
## 글2
### 글3
#### 글4
##### 글5
###### 글 6

---

* __목록(List)__
_목차, 요소 나열 시 사용됩니다._

```
1. A
2. B
3. C
+ 1
  - 2
  * 3
  + 4
```

1. A
2. B
3. C
+ 1
  - 2
  * 3
    + 4
---

* __텍스트박스(TextBox)__
_인용문 혹은 강조하고 싶은 문장이 있을 때 사용합니다._

```
> 아이즈원 조유리
>> 좋아
>>> 조유리 화이팅!
```
> 아이즈원 조유리
>> 좋아
>>> 조유리 화이팅!

---

* __링크(Link)__
_클릭하면 다른 페이지로 이동하거나 같은 페이지의 다른 문단으로 이동합니다._
```
1. [클릭하면 URL로 이동](https://ekspertos.github.io)
2. <https://ekspertos.github.io>
3. [문단이동](#개요)
```
1. [클릭하면 URL로 이동](https://ekspertos.github.io)
2. <https://ekspertos.github.io>
3. [문단이동](#개요)

---

## 마크다운 문법2
이 글에서는 사용되지 않은 유용한 문법들을 설명합니다.

* __이미지(Image)__
_글에 이미지를 삽입합니다._
```
1. (``이미지`` 삽입) :
![이미지](https://ekspertos.github.io/assets/img/me2.jpg "인공지능")
2. (`사이즈를 조절`하고 싶은 경우 HTML 태그로 처리) :   
<img src="https://ekspertos.github.io/assets/img/me2.jpg" width="300" height="200">

3. (이미지 삽입 후, `링크 걸기`)
[![이미지](https://ekspertos.github.io/assets/img/me2.jpg)](https://ekspertos.github.io/assets/img/me2.jpg)  
```
1. (``이미지`` 삽입) :
![이미지](https://ekspertos.github.io/assets/img/me2.jpg "인공지능")
2. (`사이즈를 조절`하고 싶은 경우 HTML 태그로 처리) :   
<img src="https://ekspertos.github.io/assets/img/me2.jpg" width="300" height="200">

3. (이미지 삽입 후, `링크 걸기`)
[![이미지](https://ekspertos.github.io/assets/img/me2.jpg)](https://ekspertos.github.io/think/2019/06/25/think-future-ai/)  

---
* __표 작성__
```
|     | 표 | 작성 |  
| :---| ---: | :---: |  
| A | 100 | 호 |  
| B | 200 | 우 |
```

|     | 표 | 작성 |  
| :---| ---: | :---: |  
| A | 100 | 호 |  
| B | 200 | 우 |

---
