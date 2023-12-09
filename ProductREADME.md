상품 페이지 코드 리뷰
==================

***    

상품 목록 페이지
---------------
  <details>
  <summary>객체</summary>
  <div markdown="1">
## Dto, Entity

  </div>
  </details>
  
<br/>




  <details>
  <summary>Front-End</summary>
  <div markdown="1">
## Front-End 코드

  </div>
  </details>
  
<br/>



  <details>
  <summary>Back-End</summary>
  <div markdown="1">
## Back-End 코드

**Controller**
```java
@GetMapping("api/products/minmax")
public ResponseEntity<?> searchProductsWithMinPriceAndMaxPrice(SearchMasterProductReqDto searchMasterProductReqDto) {
    return ResponseEntity.ok().body(productService.searchProductsWithMinPriceAndMaxPrice(searchMasterProductReqDto));
}

@GetMapping("api/products/count")
public ResponseEntity<?> getCountOfSearchedProducts(SearchMasterProductReqDto searchMasterProductReqDto) {
    return ResponseEntity.ok().body(productService.getCountOfSearchedProducts(searchMasterProductReqDto));
}
```


  </div>
  </details>
  
<br/>





***    


구매 확인 페이지
---------------
