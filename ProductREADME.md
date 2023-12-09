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
- 랜더링 시 요청에 검색 객체를 담아서 조건에 일치하는 상품 정보와 pagenation에 필요한 데이터를 받아온다.<br>그리고 페이지 변경 시 다른 검색 조건들과 충돌을 방지하기 위해 기존 검색 조건을 oldSearchData에 저장해 둔다.
- 
<br>

### 화면 출력 코드

```javascript
return (
  <RootContainer>
      <div css={S.SLayout}>
          <div css={S.SSubContainer}>
              <div css={S.SSelectBox}>
                  <select 
                      options={sortOptions}
                      onChange={handleSortOptionChange}>
                          {sortOptions.map(option => {
                              return <option name='sortOption' key={option.label} value={option.value}>{option.label}</option>
                          })}
                  </select> 
                  <input type="text" name='value' onKeyDown={handleOnKeyPress} onChange={handleSearchInputChange} value={searchInput}/>
                  <button onClick={handleSearchButtonClick}>검색</button>
              </div>
          </div>
          <div css={S.SProductContainer}>
              {!getProducts.isLoading && getProducts?.data?.data.map((product) => {
                  return  <div css={S.SProductBox} key={product.productMstId} >
                              <ul>
                                  <li onClick={handleProductOnclick}>
                                      <img id={product.productMstId} src={product.productThumbnailUrl} alt="" />
                                      <h3>{product.productName}</h3>
                                      <p>
                                          가격 : {product.tempStock <= 0 ? "품절" : product.minPrice?.slice(4, product.minPrice.lastIndexOf())}
                                      </p>
                                  </li>
                              </ul>
                          </div>
              })}
          </div>
          <div css={S.SPageButtonBox}>
              <button
                  onClick={() => handlePageClick(searchData.pageIndex - 1)}
                  disabled={currentPage === 1}
                  >
                      {"<"}
              </button>
              {totalPageIndex.map((page, index) => (
                  <button 
                      css={currentPage === page ? S.selectedPageButton : S.PageButton}
                      name="totalPageIndex"
                      onClick={() => handlePageClick(page)}
                      key={index}>
                      {page}
                  </button>
              ))}
              <button
                  onClick={() => handlePageClick(searchData.pageIndex + 1)}
                  disabled={currentPage === lastPage}
                  >
                      {">"}
              </button>
          </div>
      </div>
  </RootContainer>
);
```
- 
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

<br>

**Service**
```java
public List<GetAllProductsRespDto> searchProductsWithMinPriceAndMaxPrice(SearchMasterProductReqDto searchMasterProductReqDto) {
    try {
        List<GetAllProductsVo> getAllProductsVo = productMapper.searchProductsWithMinPriceAndMaxPrice(searchMasterProductReqDto.toSearchProduct());
        List<GetAllProductsRespDto> respDto = new ArrayList<>();
        getAllProductsVo.forEach(vo -> {
                    String allSizeAndPrice = vo.getSizeAndPrice();
                    String[] sizeAndPrice = allSizeAndPrice.split(", ");
                    List<String> priceList = new ArrayList<>();
                    for(String price: sizeAndPrice) {
                        priceList.add(price);
                    }
                    String minPrice = priceList.get(0).replace("/ ", ": ");
                    String maxPrice = "";
                    if (priceList.size() == 2) {
                        maxPrice = priceList.get(1).replace("/ ", ": ");
                    }
                    respDto.add(vo.toRespDto(minPrice, maxPrice));
                }
        );
        return respDto;
    }catch (Exception e) {
        throw new ProductException
                (errorMapper.errorMapper("상품 오류", "상품 검색 중 오류가 발생하였습니다."));
    }
}

public int getCountOfSearchedProducts(SearchMasterProductReqDto searchMasterProductReqDto) {
    try {
        return productMapper.selectCountOfSearchedProducts(searchMasterProductReqDto.toSearchProduct());
    }catch (Exception e) {
        throw new ProductException
                (errorMapper.errorMapper("상품 오류", "상품 리스트를 불러오는 중 오류가 발생하였습니다."));
    }
}
```

**Repository**
```java
public List<GetAllProductsVo> searchProductsWithMinPriceAndMaxPrice(SearchMasterProductVo searchMasterProductVo);
public Integer selectCountOfSearchedProducts(SearchMasterProductVo searchMasterProductVo);
```

**Mybatis Query**
```
<select id="searchProductsWithMinPriceAndMaxPrice" resultMap="productsWithMinPriceAndMaxPriceMap" >
    select
        pmt.product_mst_id,
        pmt.product_name,
        ptt.pet_type_id,
        pct.product_category_id,
        pmt.product_thumbnail_url,
        GROUP_CONCAT(CONCAT('(', st.size_name, '/ ', pdt.price, ')') SEPARATOR ', ') AS sizes_and_prices,
        (select
            sum(temp_stock)
        from
            product_stock_view psv
            left outer join product_dtl_tb pdt on(pdt.product_dtl_id = psv.product_dtl_id)
            left outer join product_mst_tb subpmt on(subpmt.product_mst_id = pdt.product_mst_id)
        where
            subpmt.product_mst_id = pmt.product_mst_id
        group by
            pmt.product_mst_id) as temp_stock
    from
        product_mst_tb pmt
        left outer join (select
                            product_dtl_id,
                            product_mst_id,
                            row_number() over(partition by product_mst_id order by price) as num,
                            row_number() over(partition by product_mst_id order by price desc) as num2,
                            size_id,
                            price
                        from
                            product_dtl_tb)
        pdt on(pdt.product_mst_id = pmt.product_mst_id and (pdt.num = 1 or pdt.num2 = 1))
        left outer join size_tb st on(st.size_id = pdt.size_id)
        left outer join pet_type_tb ptt on(ptt.pet_type_id = pmt.pet_type_id)
        left outer join product_category_tb pct on(pct.product_category_id = pmt.product_category_id)
    where
    1 = 1
    <choose>
        <when test="searchOption.equals('number')">
            <if test="!searchValue.equals('')">
                and pmt.product_mst_id = #{searchValue}
            </if>
        </when>
        <when test="searchOption.equals('name')">
            and pmt.product_name like concat("%", #{searchValue}, "%")
        </when>
        <otherwise>
            and (pmt.product_name like concat("%", #{searchValue}, "%")
            or pct.product_category_name like concat("%", #{searchValue}, "%")
            or ptt.pet_type_name like concat("%", #{searchValue}, "%"))
        </otherwise>
    </choose>
    <if test="!petTypeName.equals('all')">
        and ptt.pet_type_name_eng = #{petTypeName}
    </if>
    <if test="!productCategoryName.equals('all')">
        and pct.product_category_name_eng = #{productCategoryName}
    </if>
    group by
        pmt.product_mst_id
    order by
    <choose>
        <when test="sortOption.equals('name')">
            pmt.product_name COLLATE utf8mb4_unicode_ci
        </when>
        <when test="sortOption.equals('number')">
            pmt.product_mst_id desc
        </when>
        <when test="sortOption.equals('lowprice')">
            pdt.price asc
        </when>
        <when test="sortOption.equals('highprice')">
            pdt.price desc
        </when>
        <otherwise>
            pmt.product_mst_id desc
        </otherwise>
    </choose>
    limit
    #{pageIndex}, #{limit}
</select>


<select id="selectCountOfSearchedProducts" parameterType="com.woofnmeow.wnm_project_back.vo.SearchMasterProductVo" resultType="java.lang.Integer">
    select
        count(*)
    from
        product_mst_tb pmt
        left outer join pet_type_tb ptt on(ptt.pet_type_id = pmt.pet_type_id)
        left outer join product_category_tb pct on(pct.product_category_id = pmt.product_category_id)
    where
        1 = 1
    <choose>
        <when test="searchOption.equals('number')">
            <if test="!searchValue.equals('')">
                and pmt.product_mst_id = #{searchValue}
            </if>
        </when>
        <when test="searchOption.equals('name')">
            and pmt.product_name like concat("%", #{searchValue}, "%")
        </when>
        <otherwise>
            and (pmt.product_name like concat("%", #{searchValue}, "%")
            or pct.product_category_name like concat("%", #{searchValue}, "%")
            or ptt.pet_type_name like concat("%", #{searchValue}, "%"))
        </otherwise>
    </choose>
    <if test="!petTypeName.equals('all')">
        and ptt.pet_type_name_eng = #{petTypeName}
    </if>
    <if test="!productCategoryName.equals('all')">
        and pct.product_category_name_eng = #{productCategoryName}
    </if>
</select>
```

  </div>
  </details>
  
<br/>

***    

구매 확인 페이지
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
