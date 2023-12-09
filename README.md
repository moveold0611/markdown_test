markdown_test
=============

markdown_test_sub_title
------------------------   

# 헤더
> 블럭1
>  > 블럭2
>  >  > 블럭3

***   

# 목록
1. 1번 목록
2. 2번 목록
3. 3번 목록

***    

1. 1번 목록
   1. 1-1번 소목록
   2. 1-2번 소목록
2. 2번 목록
   1. 2-1번 소목록
   2. 2-2번 소목록
   3. 2-3번 소목록
3. 3번 목록

***    

### **여닫이 목록**
  <details>
  <summary>여닫이 목록</summary>
  <div markdown="1">
  내용
  </div>
  </details>
  
<br/>


***   

# 기호 목록
* 별1
  * 별2
    * 별3   

+ 더하기1
  + 더하기2
    + 더하기3   

- 빼기1
  - 빼기2
    - 빼기3   

***   

# 링크

Link: [Google][googlelink]

[googlelink]: https://google.com "Go google"

***   

# 강조

*강조*
_강조2_
**굵음**
__굵음2__
~~취소선~~

***   

# 이미지

![Alt text](./img/test_img.gif "Optional title")

***   

# 코드   


```java
public class TestMarkdownClass {
  public static void main(String[] args) {
    int a = 0;
    system.out.println("자바 문법 테스트");
  }
}
```

```javascript
function tester() {
    const testMarkdown = () => {
        console.log("JS 문법 테스트");
    }
}
```
