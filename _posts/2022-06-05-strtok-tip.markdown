---
layout: post
title:  "strtok 팁"
subtitle: "Linux Redirection"
categories: envOps
tags: blog github pages jekyll
comments: true
---


strtok는 빈칸이 중복되어도 상관 없다 고려하지 않는다

```c++
int main() {
    char str[] = "MY   NAME IS   TOM";
    char *temp = strtok(str," ");
    char *buf;

    while (temp != NULL) {
        printf("str: %s\n", str);
        printf("temp: %s\n",temp);
        temp = strtok(NULL, " ");
    }

    return 0;
}

/*
[result]
str: MY
temp: MY
str: MY
temp: NAME
str: MY
temp: IS
str: MY
temp: TOM
*/
```
