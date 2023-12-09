상품 페이지 코드 리뷰
==================

***    

상품 목록 페이지
---------------

  <details>
  <summary>여닫이 기능</summary>
  <div markdown="1">
    ```java
    public class ProductController {
      @GetMapping("api/products/sizes")
      public ResponseEntity<?> searchProductsWithAllSizes(SearchMasterProductReqDto searchMasterProductReqDto) {
          return ResponseEntity.ok().body(productService.searchProductsWithAllSizes(searchMasterProductReqDto));
      } 
    }
    ```
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
