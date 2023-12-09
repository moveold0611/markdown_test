상품 페이지 코드 리뷰
==================
***    
상품 목록 페이지
---------------
  <details>
  <summary>객체</summary>
  <div markdown="1">
    
## Dto

### 검색 요청 RequestDto
```java
public class SearchMasterProductReqDto {
    private String petTypeName;
    private String productCategoryName;
    private String searchOption;
    private String searchValue;
    private String sortOption;
    private int pageIndex;
}
```
- 검색 조건에 해당하는 상품 정보를 받아오기 위한 요청 Dto

<br>

### 상품 데이터 ResponseDto
```java
public class GetAllProductsRespDto {
    private int productMstId;
    private String productName;
    private String petTypeId;
    private String productCategoryId;
    private String productThumbnailUrl;
    private String minPrice;
    private String maxPrice;
    private int tempStock;
}
```
- front-end에서 사용하기 위한 상품 목록 데이터 Dto<br>기본적인 정보와 최소가격, 최대가격, 모든 재고의 총 합을 포함한 정보를 담고있다.

<br>

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
- 검색 옵션을 변경하는 부분은 각 기능별로 onChange를 사용하여 searchData를 변경한다.
- back-end에서 응답받은 데이터를 map을 사용하여 화면에 출력한다.

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

<br>

**Repository**
```java
public List<GetAllProductsVo> searchProductsWithMinPriceAndMaxPrice(SearchMasterProductVo searchMasterProductVo);
public Integer selectCountOfSearchedProducts(SearchMasterProductVo searchMasterProductVo);
```

<br>

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

- 상품의 기본 정보에 추가로 최소가격과 최대가격, 그리고 모든 재고의 총합을 응답 객체로 반환한다.<br>모든 재고의 총합이 0이하일 경우 front-end에서 품절로 출력하기 위해 사용한다.

- 검색 조건에 일치하는 상품의 갯수도 pagenation기능을 위해 응답한다.

<br>
  </div>
  </details>
  
<br/>

***    

구매 확인 페이지
---------------

  <details>
  <summary>객체</summary>
  <div markdown="1">
    
## Dto

## ResponseDto
```java
public class GetProductRespDto {
    private String productName;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;
    private int petTypeId;
    private String petTypeName;
    private int productCategoryId;
    private String productCategoryName;
    private String createDate;

    private List<ProductDtl> productDtlList;
}
```
- 상품에 대한 이름, 설명, 사진 등은 상위 객체에 담겨있고, 각 사이즈별 가격과 재고는 productDtlList에 담아서 응답

<br>
  </div>
  </details>
  
<br/>
  <details>
  <summary>Front-End</summary>
  <div markdown="1">

## Front-End 코드

### 요청 전송
```javascript
const { productId } = useParams();

const getProduct = useQuery(["getProduct"], async () => {
    try {
        const response = getProductMstApi(productId);
        return await response
        
    } catch(error) {
        console.log(error)
    }
}, {
    refetchOnWindowFocus: false,
    onSuccess: response => {
        setProduct(response.data)
    }
})

const getReviewByProduct = useQuery(["getReviewByProduct"], async () => {
    try {
        const option = {
            headers: {
                Authorization: localStorage.getItem("accessToken")
            }
        }
        const response = await getReviewByProductApi(productId, option);
        return response;
    } catch (error) {
        console.log(error)
    }
}, {
    refetchOnWindowFocus: false,
    onSuccess: response => {
        setProductReview(response?.data)
    }
})
```
- 상품 목록 페이지에서 상품을 클릭 시, params로 상품Id를 받아서 해당 상품에 상세정보(모든 사이즈의 가격과 재고를 포함)와 리뷰를 조회하도록 요청 전송

<br>

### 화면 출력
```javascript
return (
    <RootContainer>
        <div>
          <div css={S.SLayout}>
              <div css={S.STopContainer} >
                  <div>
                      <img css={S.SThumbnailImg} src={product.productThumbnailUrl} />
                  </div>
                  <div css={S.SOrderInfoBox}>
                      <h2>{product.productName}</h2>
                      <p dangerouslySetInnerHTML={{__html: product.productDetailText}}></p>
                      <div css={S.SSelectBox}>
                          <Select css={S.SSelect} onChange={selectOnChange} options={product.productDtlList?.map(pdt => {
                              return {
                                  value: pdt.productDtlId,
                                  label: `${pdt.size.sizeName === "no" ? product.productName : pdt.size.sizeName}${pdt.tempStock > 0 ? "(수량: " + pdt.tempStock + ")" : "(품절)"}`};
                          })
                          }/>
                      </div>
                      <ul css={S.SOrderListBox}>
                          {selectedProducts.map((selectedProduct, index) => 
                              <li key={index}>
                                  <div css={S.SListBox}>
                                      <h5>
                                          {product.productName}<br/>
                                          -{selectedProduct.sizeName}
                                      </h5>
                                      <input type="number" defaultValue={1} min={1} max={99} onChange={(e) => countOnChange(e.target, index)}/>
                                      <p>{selectedProduct.price.toLocaleString("ko-KR")}원</p>
                                      <button onClick={() => handleDeleteProductOnClick(index)}>X</button>
                                  </div>
                              </li>
                          )}
                      </ul>
                      <div css={S.SPriceInfo}>
                          <p>Total</p>
                          <h3>{selectedProducts.reduce((total, selectedProduct) => {
                              return total += selectedProduct.price}, 0).toLocaleString("ko-KR")}원</h3>
                      </div>
                      <div css={S.SButtonBox}>
                          <button onClick={buyNowOnClick}>BUY NOW</button>
                          <button onClick={handleAddToCartOnClick}>ADD TO CART</button>
                      </div>
                  </div>
              </div>
              <div css={S.SDetailContainer}>
                  <img css={S.SDDetailImg} src={product.productDetailUrl} alt="" />
              </div>
          </div>
          <div css={S.SReviewContainer}>
              <div css={S.SH1}>고객님들의 소중한 구매후기 ⭐</div>
              <ul>
                  {productReview.map(rev => {
                      return <li>
                          <div css={S.SReviewList}>
                              <div css={S.SreviewHeader}>
                                  <img src={rev.profileUrl} alt=""/>
                                  <div css={S.SNickname}>{rev.nickname}</div>
                                  <div css={S.SProductSizeBox}>
                                      <div>{product.productName} / </div>
                                      <div> size {rev.sizeName}</div>
                                  </div>
                                  <div css={S.SReivewDate}>{rev.reviewDate}</div>
                              </div>
                              <div css={S.SReviewContentBox}>
                                  {!!rev.reviewImgUrl ? <div><img src={rev.reviewImgUrl} alt="" css={S.SReviewImg}/></div> : <div></div>}
                                  <div css={S.SReviewContent}>{rev.reviewContent}</div>
                              </div>
                          </div>
                      </li>
                  })}
              </ul>
          </div>
        </div>
    </RootContainer>
);
```
- 응답받은 객체의 사이즈별 가격과 재고를 확인하여 품절 혹은 가격을 출력하도록 조건 설정
- 결제 절차를 진행하거나 장바구니에 담기 전 선택한 상품의 갯수를 추가하거나 여러 사이즈의 상품을 담을 수 있도록 코드가 작성되었다.

<br>

### 예외 상황 처리
```javascript
const buyNowOnClick = () => {
    if(!principal.data) {
        alert("로그인 후 사용해주세요.")
        navigate("/auth/signin")
    } else {
        if(selectedProducts.length === 0) {
            alert("상품을 선택해주세요.")
        } else {
            localStorage.setItem("orderData", JSON.stringify(selectedProducts));
            localStorage.setItem("isCart", false);
            navigate("/order")
        }
    }
}

const selectOnChange = (option) => {
    const productDtl = product.productDtlList.filter(pdt => pdt.productDtlId === option.value)[0];

    if(productDtl.tempStock <= 0) {
        alert("해당 상품은 품절입니다.");
        return;
    }

    if(selectedProducts.filter(selectedProduct => selectedProduct.productDtlId === productDtl.productDtlId).length > 0){
        alert("해당 상품은 이미 선택된 상품 입니다.");
        return;
    }

    const newSelectedProduct = {
        productDtlId: productDtl.productDtlId,
        sizeName: productDtl.size.sizeName,
        price: productDtl.price,
        count: 1,
        productDtl: {
            price: productDtl.price,
            size: {
                sizeId: productDtl.size.sizeId,
                sizeName: productDtl.size.sizeName
            },
            productMst: {
                productName: product.productName,
                productThumbnailUrl: product.productThumbnailUrl
            }
        },
    }
    setSelectedProducts([...selectedProducts, newSelectedProduct]);
}

const countOnChange = (target, index) => {
    const pdt = product.productDtlList.filter(pdt => pdt.productDtlId === selectedProducts[index].productDtlId)[0]; 
    const updateSelectedPorudcts = [...selectedProducts];
    updateSelectedPorudcts[index].count = parseInt(target.value);
    updateSelectedPorudcts[index].price = pdt.price * parseInt(target.value);

    if(updateSelectedPorudcts[index].count > pdt.tempStock) {
        alert("현재 재고 보다 많이 선택되었습니다.")
        target.value = pdt.tempStock;
        return
    }

    setSelectedProducts([...updateSelectedPorudcts]);
}
```
- 정상적인 동작이 아닐 경우의 처리<br>ex) 비로그인 구매, 상품 미선택 구매, 품절 상품 구매, 재고보다 많은 수량 구매

<br>

  </div>
  </details>
  
<br/>
  <details>
  <summary>Back-End</summary>
  <div markdown="1">
    
## Back-End 코드
**Controller**
```java
@GetMapping("/api/product/master/{productMstId}")
public ResponseEntity<?> getProductByProductMstId(@PathVariable int productMstId) {
    return ResponseEntity.ok().body(productService.getProductByProductMstId(productMstId));
}
```

<br>

**Service**
```java
public GetProductRespDto getProductByProductMstId(int productMstId) {
    try {
        return productMapper.selectProductByProductMstId(productMstId).toProductRespDto();
    }catch (Exception e) {
        throw new ProductException(errorMapper.errorMapper
                ("상품 오류", "상품 정보 조회 중 오류가 발생하였습니다."));
    }
}
```

<br>

**Repository**
```java
public ProductMst selectProductByProductMstId(int productMstId);
```

<br>

**Mybatis Query**
```java
    <select id="selectProductByProductMstId" resultMap="productMstMap">
        select
            pdt.product_dtl_id,
            pmt.product_mst_id,
            pmt.product_name,
            pdt.price,
            ptt.pet_type_id,
            ptt.pet_type_name,
            pct.product_category_id,
            pct.product_category_name,
            st.size_id,
            st.size_name,
            pmt.product_detail_text,
            pmt.product_thumbnail_url,
            pmt.product_detail_url,
            psv.actual_stock,
            psv.temp_stock,
            pmt.create_date
        from
            product_mst_tb pmt
            left outer join  product_dtl_tb pdt on(pdt.product_mst_id = pmt.product_mst_id)
            left outer join product_category_tb pct on(pct.product_category_id = pmt.product_category_id)
            left outer join pet_type_tb ptt on(ptt.pet_type_id = pmt.pet_type_id)
            left outer join size_tb st on(st.size_id = pdt.size_id)
            left outer join product_stock_view psv on(psv.product_dtl_id = pdt.product_dtl_id)
        where
            pmt.product_mst_id = #{productMstId}
    </select>
```

<br>

  </div>
  </details>
  
<br/>
