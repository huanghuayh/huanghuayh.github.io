---
layout: post
title: Find greatest common divider using Java
---

```java
private int gcd(int a,int b){
    return b==0? a: gcd(b,a%b);
}
```
