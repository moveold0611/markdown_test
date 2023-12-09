상품 페이지 코드 리뷰
==================

***    

상품 목록 페이지
---------------

  <details>
  <summary>여닫이 기능</summary>
  <div markdown="1">

## 객체
**Dto**

    
## Back-End 코드

**Controller**
```java
public ResponseEntity<?> searchProductsWithMinPriceAndMaxPrice(SearchMasterProductReqDto searchMasterProductReqDto) {
    return ResponseEntity.ok().body(productService.searchProductsWithMinPriceAndMaxPrice(searchMasterProductReqDto));
}
```
* 해당 컨트롤러로 상품 목록을 List로 받아온다.

  </div>
  </details>
  
<br/>


  <details>
  <summary>여닫이 기능</summary>
  <div markdown="2">
  

  </div>
  </details>
  
<br/>


***    


구매 확인 페이지
---------------
