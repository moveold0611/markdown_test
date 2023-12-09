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

```javascript
const [ oldSearchData, setOldSearchData ] = useState({
    petTypeName: type,
    productCategoryName: !!category ? category : 'all',
    searchOption: "all",
    searchValue: "",
    sortOption: "name",
    pageIndex: 1
});

const [ searchData, setSearchData ] = useState({
    petTypeName: type,
    productCategoryName: !!category ? category : 'all',
    searchOption: "all",
    searchValue: "",
    sortOption: "name",
    pageIndex: 1
});

const getProducts = useQuery(["getProducts"], async () => {
    try {
        const response = getAllProductsApi(searchData);
        return await response
    } catch(error) {
        console.log(error)
    }
}, {
    retry: 0,
    refetchOnWindowFocus: false,
    onSuccess: response => {
        setOldSearchData(searchData)
        setProducts(response?.data)
    }
})

const getProductsPagenation = useQuery(["getProductsPageNation"], async () => {
    try {
        const response = getProductsCountApi(searchData);
        return response;
    } catch (error) {
        console.error("Error in getProductsCountApi:", error);
        throw error;
    }
},
{
    retry: 0,
    refetchOnWindowFocus: false,
    onSuccess: response => {
        const respLastPage = getLastPage(response?.data, 12);
        setLastPage(respLastPage)
        const respStartIndex = getStartIndex(currentPage)
        setStartIndex(respStartIndex)
        const respEndIndex = getEndIndex(respStartIndex, respLastPage);
        setEndIndex(respEndIndex)
        const respTotalPageIndex = getTotalPageIndex(respStartIndex, respEndIndex)
        setTotalPageIndex(respTotalPageIndex)
    }
})
```
- 랜더링 시 요청에 검색 객체를 담아서 조건에 일치하는 상품 정보와 pagenation에 필요한 데이터를 받아온다.<br>그리고 페이지 변경 시 다른 검색 조건들과 충돌을 방지하기 위해 기존 검색 조건을 oldSearchData에 저장해 둔다

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
