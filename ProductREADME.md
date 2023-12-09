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

### 요청 코드
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
보 페이지
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

  </div>
  </details>
  
<br/>
