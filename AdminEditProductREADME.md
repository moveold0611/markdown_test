관리자 상품 관리 페이지
=======================

***


  <details>
  <summary>관리자 상품 추가 페이지</summary>
  <div markdown="1">

## 객체
   
**RequestDto**
```java
@Data
public class AddProductReqDto {
    private String productName;
    private int price;
    private int petTypeId;
    private int productCategoryId;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;
}
```
   <br>


 ## Front-End
   
**요청 코드**
```javascript
const petTypes = [
    { value: 1, label: "강아지" },
    { value: 2, label: "고양이" }
]

const productDogCategoeies = [
    { value: 1, label: "홈·리빙" },
    { value: 2, label: "산책" },
    { value: 3, label: "이동" },
    { value: 4, label: "패션" },
    { value: 5, label: "장난감" },
]

const productCatCategoeies = [
    { value: 1, label: "홈·리빙" },
    { value: 2, label: "산책" },
    { value: 3, label: "이동" },
    { value: 5, label: "장난감" },
]

const [ product, setProduct ] = useState({
    productName: "",
    price: 0,
    productDetailText: "",
    productThumbnailUrl: "",
    productDetailUrl: "",
    // default값을 각 옵션들의 defalut값의 value로 맞춰놈
    petTypeId: 1,
    productCategoryId: 1
})

const handleProductSubmitClick = async () => {
    try {
        const option = {
            headers: {
                Authorization: !!localStorage.getItem("accessToken")
                ? localStorage.getItem("accessToken") : ""
            }
        }
        await addProductApi(product, option);
        alert("상품 등록 완료")
        window.location.replace("/admin/product/edit")
    } catch (error) {
        console.error(error);
    }    
}   
```
- 카테고리, 설명, 상품명, 이미지Url 등 필요한 정보를 Body에 담아서 전송한다.
- 사이즈가 여러개 존재하는 카테고리 등록 시, 자동으로 모든 사이즈에 같은 가격이 입력된다. 이 가격은 상품 수정 페이지에서 수정 가능하다.

<br>

```javascript
const handleProductThumnailImgChange = (e) => {
    const file = e.target.files[0];
    setProductThumbnailUrlSrc(file);

    if(file === "") {
        setUploadFiles([]);
        setProductThumbnailUrlSrc("");
        return;
    } 

    setUploadFiles([file]);

    reader.onload = (e) => {
        const productThumnailImageUrl = e.target.result;
        setProductThumbnailUrlSrc(productThumnailImageUrl);

        if(!!uploadFiles) {
            const storageRef = ref(storage, `files/product/${file.name}`);

            uploadBytesResumable(storageRef, file)
            .then((uploadTaskSnapshot) => {
                getDownloadURL(storageRef)
                    .then((downloadUrl) => {
                        setProduct({
                            ...product,
                            productThumbnailUrl: downloadUrl,
                        });
                    });
            });
        }
    }
    reader.readAsDataURL(file);
}

const handleProductDetailImgChange = (e) => {
    const file = e.target.files[0];
    setUploadDetailImgFile(file);

    if(file === "") {
        setUploadFiles([]);
        setProductDetailUrlSrc("");
        return;
    } 

    reader.onload = (e) => {
        const productDetaimgUrl = e.target.result;
        setProductDetailUrlSrc(productDetaimgUrl);

        if(!!uploadFiles) {
            const storageRef = ref(storage, `files/product/${file.name}`);

            uploadBytesResumable(storageRef, file)
            .then((uploadTaskSnapshot) => {
                getDownloadURL(storageRef)
                    .then((downloadUrl) => {
                        setProduct({
                            ...product,
                            productDetailUrl: downloadUrl,
                        });
                    });
            });
        }
    };
    reader.readAsDataURL(file);
}
```
- 이미지 Url은 변경 시 firebase에 업로드 된 후 그 주소를 받아와 Body객체에 저장한다.


<br>

**화면 출력 코드**
```javascript
return (
    <Mypage>
        <div css={S.SContainer}>
            <div css={S.STopTitle}>
                <h2>상품 등록</h2>
            </div>
            <div css={S.SubContainer}>
                <div>
                    <h1 css={S.SH1}>상품 메인 이미지 등록</h1>
                </div>
                <div css={S.SImgBox} >
                    <img src={productThumbnailUrlSrc} alt='상품 메인 이미지'/>
                </div>
                <div css={S.SButtonBox}>
                    <input css={S.Sfile} type="file" onChange={handleProductThumnailImgChange} ref={productThumnailImgRef}/>
                    <button onClick={handleProductThumnailImgUploadClick}>메인 이미지 수정 파일 업로드</button>
                </div>
                <div>
                    <h1 css={S.SH1}>상품 상세 이미지 등록</h1>
                </div>
                <div css={S.SImgBox}>
                    <img src={productDetailUrlSrc} alt='상품 상세 이미지'/>
                </div>
                <div css={S.SButtonBox}>
                    <input css={S.Sfile} type="file" onChange={handleProductDetailImgChange} ref={productDetailImgRef}/>
                    <button onClick={handleProductDetailImgUploadClick}>상품 상세 이미지 파일 업로드</button>
                </div>
                <div>
                    <h1 css={S.SH1}>상품 정보 등록</h1>
                </div>
                <div css={S.SInputBox}>
                    <div css={S.SInfoInput}>
                        <h2>상품명</h2> 
                        <input type="text" 
                            name='productName'
                            placeholder='상품명'
                            onChange={handleInputChange}/>
                    </div>
                    <div css={S.SInfoInput}>
                        <h2>상품 설명</h2>
                        <textarea type="text" 
                            name='productDetailText'
                            placeholder='상품 설명'
                            onChange={handleInputChange}/>
                    </div>
                    <div css={S.SInfoInput}>
                        <h2>동물타입</h2> 
                        <select 
                            options={petTypes}
                            onChange={handlePetTypeOptionChange}
                            css={S.SSelect}>
                            {petTypes.map(type => {
                                return <option key={type.value} value={type.value} label={type.label}>{type.label}</option>
                            })}
                        </select>
                    </div>
                    {product.petTypeId === 1 ? 
                        <div css={S.SInfoInput}>
                            <h2>카테고리</h2>
                            <select
                                options={productDogCategoeies}
                                onChange={handleCategoryTypeOptionChange}
                                css={S.SSelect}>
                                {productDogCategoeies.map(category => {
                                    return <option key={category.value} value={category.value} label={category.label}>{category.label}</option>
                                })}
                            </select> 
                        </div>
                        :
                        <div css={S.SInfoInput}>
                            <h2>카테고리</h2>
                            <select
                                options={productCatCategoeies}
                                onChange={handleCategoryTypeOptionChange}
                                css={S.SSelect}>
                                {productCatCategoeies.map(category => {
                                    return <option key={category.value} value={category.value} label={category.label}>{category.label}</option>
                                })}
                            </select> 
                        </div>
                    }
                    <div css={S.SInfoInput}>
                        <h2>가격</h2>
                        <input type="text" name='price' placeholder='가격' onChange={handleInputChange} />
                        <h4>*사이즈별 가격 설정은 등록 후 수정 페이지에서 수정*</h4>
                    </div>
                </div>
                <div>
                    <button onClick={handleProductSubmitClick} css={S.SButton}>등록하기</button>
                </div>
            </div>
        </div>
    </Mypage>
);
```
- 이미지는 확인을 위해 useState상태를 이용하고 있고, 업로드 데이터가 변경 시 onChange를 이용하여 요청보낼 Body 객체 정보가 갱신된다.

<br>
   


## Back-End
    
**Controller**
```java
@PostMapping("/api/admin/product")
public ResponseEntity<?> addProduct(@RequestBody AddProductReqDto addProductReqDto) {
    return ResponseEntity.ok().body(productService.addProduct(addProductReqDto));
}
```
   <br>

**Service**
```java
@Transactional(rollbackFor = Exception.class)
public boolean addProduct(AddProductReqDto addProductReqDto) {
    try {
        Map<String, Object> map = new HashMap<>();
        ProductMst productMst = addProductReqDto.toEntity();
        productMapper.addProductMaster(productMst);

        map.put("productMstId", productMst.getProductMstId());
        map.put("price", addProductReqDto.getPrice());

        if(addProductReqDto.getProductCategoryId() == 4 && addProductReqDto.getPetTypeId() == 1) {
            map.put("sizeId", 2);
            for(int i = 0; i < 6; i++) {
                productMapper.addProductDetail(map);
                map.replace("sizeId", i + 3);
            }
        }else {
            map.put("sizeId", 1);
            productMapper.addProductDetail(map);
        }
        return true;
    }catch (Exception e) {
        throw new ProductException
                (errorMapper.errorMapper("상품 오류", "상품 추가 중 오류가 발생하였습니다."));
    }
}
```
- 상품의 카테고리 조건에 맞춰 자동으로 사이즈를 정해서 업로드하는 조건문이 들어있다.<br>사이즈 종류가 변경될 일이 없다고 판단되어 이와같이 코드를 작성하였다.
- 변경이 필요하다면 front-end에서 List로 사이즈와 가격정보를 받아오는 방식으로 수정이 가능하다.

<br>

**Repository**
```java
@Options(useGeneratedKeys = true, keyProperty = "productMstId")
public Integer addProductMaster(ProductMst productMst);
public Integer addProductDetail(Map<String, Object> map);
```
- 정규화된 테이블에 모두 정보가 들어가야하기 때문에 상품명, 설명, 이미지 Url과 같은 통합 정보를 Master 테이블에 insert 후<br>useGeneratedKeys를 사용하여 받아온 key를 사용하여 사이즈와 가격 정보를 Detail 테이블에 insert한다.

   <br>

**Mybatis Query**
```java
<insert id="addProductMaster" parameterType="com.woofnmeow.wnm_project_back.entity.ProductMst" useGeneratedKeys="true" keyProperty="productMstId">
    insert into
        product_mst_tb
    values(0, #{productName}, #{petTypeId}, #{productCategoryId}, #{productDetailText}, #{productThumbnailUrl}, #{productDetailUrl}, now())
</insert>



<insert id="addProductDetail" parameterType="hashmap">
    insert into
        product_dtl_tb
    values(0, #{productMstId}, #{price}, #{sizeId})
</insert>
```
- useGeneratedKeys를 사용하여 insert에 사용된 entity객체에 productMstId값을 저장하고 addProductDetail에 사용한다.



  </div>
  </details>







  <details>
  <summary>관리자 상품 관리 목록 페이지</summary>
  <div markdown="1">

## 객체
   
**RequestDto**
```java
@AllArgsConstructor
@NoArgsConstructor
@Data
public class SearchMasterProductReqDto {
    private String petTypeName;
    private String productCategoryName;
    private String searchOption;
    private String searchValue;
    private String sortOption;
    private int pageIndex;
}
```
   <br>


**Vo**
```java
@Data
@Builder
public class SearchMasterProductVo {
    private String petTypeName;
    private String productCategoryName;
    private String searchOption;
    private String searchValue;
    private String sortOption;
    private int pageIndex;
    private int limit;
}
```
- limit에 동적값을 할당하기 위한 vo

<br>

```java
@Data
public class GetProductVo {
    private int productMstId;
    private String productName;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;
    private int petTypeId;
    private String petTypeName;
    private int productCategoryId;
    private String productCategoryName;
    private LocalDateTime createDate;
    private String sizeAndPrice;

    public SearchMasterProductRespDto toRespDto(Map<String, Object> map) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String text = "";
        if(productDetailText != null) {
            text = productDetailText.replaceAll("\n", "<br>");
        }

        String[] sizes = {"no", "XS", "S", "M", "L", "XL", "XXL"};

        for (int i = 0; i < sizes.length; i++) {
            if(map.get(sizes[i]) == null) {
                map.put(sizes[i], "");
            }
        }


        SearchMasterProductRespDto body = SearchMasterProductRespDto.builder()
                .productMstId(productMstId)
                .productName(productName)
                .productDetailText(text)
                .productThumbnailUrl(productThumbnailUrl)
                .productDetailUrl(productDetailUrl)
                .petTypeId(petTypeId)
                .petTypeName(petTypeName)
                .productCategoryId(productCategoryId)
                .productCategoryName(productCategoryName)
                .createDate(createDate.format(formatter))
                .no((String) map.get("no"))
                .XS((String) map.get("XS"))
                .S((String) map.get("S"))
                .M((String) map.get("M"))
                .L((String) map.get("L"))
                .XL((String) map.get("XL"))
                .XXL((String) map.get("XXL"))
                .build();
        return body;
    }
```
- Service에서 가공한 사이즈별 가격을 __문자열__ 형태로 응답한다.
- 문자열이 비어있을 시 해당 사이즈는 상품에 없는 사이즈다.<br> 이것으로 front-end에서 사이즈 유무를 판별한다.

<br>

**ResponseDto**
```java
@Builder
@Data
public class SearchMasterProductRespDto {
    private int productMstId;
    private String productName;
    private int petTypeId;
    private String petTypeName;
    private int productCategoryId;
    private String productCategoryName;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;
    private String createDate;
    private String no;
    @JsonProperty("XS")
    private String XS;
    @JsonProperty("S")
    private String S;
    @JsonProperty("M")
    private String M;
    @JsonProperty("L")
    private String L;
    @JsonProperty("XL")
    private String XL;
    @JsonProperty("XXL")
    private String XXL;
}
```
   <br>

   
## Front-End
   
**요청 코드**
```javascript
const [ searchData, setSearchData ] = useState({
    petTypeName: "all",
    productCategoryName: "all",
    searchOption: 'name',
    searchValue: '',
    sortOption: 'number',
    pageIndex: 1});


const getProducts = useQuery(["getProducts"], async () => {
    const response = await getProductsApi(searchData);
    return response;
},
{
    refetchOnWindowFocus: false,
    retry: 0,
    onSuccess: response => {
        setOldSearchData(searchData)
        setProductList(response?.data)
    }
});


const getProductCount = useQuery(["getProductCount"], async () => {
    try {
        const response = getProductsCountApi(searchData);
        return response;
    } catch (error) {
        alert(error.message)
    }
},{
    refetchOnWindowFocus: false,
    retry: 0,
    onSuccess: response => {
        const respLastPage = getLastPage(response?.data, 10);
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
- 검색 조건에 일치하는 상품 정보와 상품의 갯수를 가져오는 요청<br>해당 요청으로 응답받은 객체에는 모든 사이즈에 대한 가격 정보가 들어있다.

<br>

**화면 출력 코드**
```javascript
return (
    <Mypage>
        <div css={S.SContainer}>
            <div css={S.STopTitle}>
                <h2>상품 관리</h2>
            </div>
            <div css={S.SSelectBox}>
                <button onClick={() => { navigate("/admin/incoming")}}>입고 관리</button>
                <button onClick={() => { navigate("/admin/outgoing")}}>출고 관리</button>
                <select option={petType} onChange={handleSearchSelectChange} name='petTypeName'>
                    {petType?.map(pt => {
                        return <option key={pt.value} label={pt.label} value={pt.value} />
                    })}
                </select>
                <select option={category} onChange={handleSearchSelectChange} name='productCategoryName'>
                    {category?.map(ct => {
                        return <option key={ct.value} label={ct.label} value={ct.value}/>
                    })}
                </select>
                <select option={sortOption} onChange={handleSearchSelectChange} name='sortOption'>
                    {sortOption?.map(so => {
                        return <option key={so.value}  label={so.label} value={so.value}/>
                    })}
                </select>
                <select option={searchOption} onChange={handleSearchSelectChange} name='searchOption'>
                    {searchOption?.map(op => {
                        return <option key={op.value} label={op.label} value={op.value}/>
                    })}
                </select>

                <input type='text' value={searchInput} onKeyDown={handleOnKeyPress} onChange={handleSearchInputChange}/>
                <button onClick={handleSearchClick}>검색</button>
            </div>
            <div css={S.STableBox}>
                <table css={S.STable}>
                    <thead>
                        <tr css={S.SThBox}>
                            <th>상품 번호</th>
                            <th>상품 이미지</th>
                            <th>상품 명</th>
                            <th>동물 종류</th>
                            <th>카테고리</th>
                            <th>상품 조회</th>
                            <th>수정</th>
                            <th>삭제</th>
                        </tr>
                    </thead>
                    <tbody>
                        {productList?.map(product => {
                            return <tr key={product.productMstId} css={S.STdBox}>
                                <td>{product.productMstId}</td>
                                <td>
                                    <img css={S.SImg} src={product.productThumbnailUrl} alt="" />
                                </td>
                                <td>{product.productName}</td>
                                <td>{product.petTypeName}</td>
                                <td>{product.productCategoryName}</td>
                                <td>
                                    <button css={S.SSelectButton} onClick={()=>handleNavigateJoinProductDetailPageClick(product.productMstId)}>상세 조회</button>
                                </td>
                                <td>
                                    <button css={S.SEditButton} onClick={()=>handleEditProductClick(product.productMstId)}>수정</button>
                                </td>
                                <td >
                                    <button css={S.SDeleteButton} onClick={()=>handleRemoveProductClick(product.productMstId)}>삭제</button>  
                                </td>
                            </tr>
                        })}
                    </tbody>
                </table>
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
    </Mypage>
);
```
- 목록에서 확인 해야하는 기본적인 정보들과 입/출고, 상세정보 확인 페이지, 수정 페이지로 이동하는 버튼과 삭제 요청을 보내는 버튼이 구현되어있다.

<br>

**기타**
```javascript
const petType = [
    { value: "all", label: "전부"},
    { value: "dog", label: "강아지"},
    { value: "cat", label: "고양이"}
]
const category = [
    { value: "all", label: "전부" },
    { value: "home-living", label: "홈·리빙" },
    { value: "walk", label: "산책" },
    { value: "movement", label: "이동" },
    { value: "fashion", label: "패션" },
    { value: "toy", label: "장난감" }
]

const searchOption = [
    { value: "name", label: "제목"},
    { value: "number", label: "마스터아이디"}
]

const sortOption = [
    { value: "name", label: "상품명"},
    { value: "number", label: "상품번호"},
]

useEffect(() => {
    if(principal?.data?.data.roleName !== "ROLE_ADMIN" || !principal?.data) {
        alert("정상적인 접근이 아닙니다.")
        navigate("/")
    }
}, [])
```
- 검색 옵션들과 Admin이 아닐 시 접근을 차단하는 코드

<br>

```javascript
const handleRemoveProductClick = async (productMstId) => {
    try {
        const option = {
            headers: {
                Authorization: localStorage.getItem("accessToken")
            }
        }
        if(window.confirm("정말로 해당 상품을 삭제 하시겠습니까?")) {
            await removeProductApi(productMstId, option)
            alert("상품이 삭제되었습니다.")
            getProducts.refetch();
        } else {
            alert("취소 되었습니다.")
        }
    } catch (error) {
        console.log(error)
    }
}
```
- 상품 삭제를 클릭 시 해당 상품의 Id로 삭제 요청 전송 후 useQuery를 갱신시키는 코드

  <br>

   
## Back-End

**Controller**
```java
@GetMapping("api/products/sizes")
public ResponseEntity<?> searchProductsWithAllSizes(SearchMasterProductReqDto searchMasterProductReqDto) {
    return ResponseEntity.ok().body(productService.searchProductsWithAllSizes(searchMasterProductReqDto));
}

@GetMapping("api/products/count")
public ResponseEntity<?> getCountOfSearchedProducts(SearchMasterProductReqDto searchMasterProductReqDto) {
    System.out.println(searchMasterProductReqDto);
    return ResponseEntity.ok().body(productService.getCountOfSearchedProducts(searchMasterProductReqDto));
}

@DeleteMapping("/api/admin/product/{productMstId}")
public ResponseEntity<?> removeProduct(@PathVariable int productMstId) {
    return ResponseEntity.ok().body(productService.removeProduct(productMstId));
}
```

   <br>

**Service**
```java
public List<SearchMasterProductRespDto> searchProductsWithAllSizes(SearchMasterProductReqDto searchMasterProductReqDto) {
    try {
        List<GetProductVo> getProductVo = productMapper.searchProductsWithAllSizes(searchMasterProductReqDto.toVo());
        Map<String, Object> map = new HashMap<>();
        List<SearchMasterProductRespDto> reqList= new ArrayList<>();
        getProductVo.forEach(vo -> {
            String allSizeAndPrice = vo.getSizeAndPrice();
            String[] sizeAndPrice = allSizeAndPrice.split(", ");
            for (int i = 0; i < sizeAndPrice.length; i++) {
                String[] sizes = sizeAndPrice[i].split("/ ", 0);
                String[] prices = sizeAndPrice[i].split("/ ", 1);
                String size = sizes[0].substring(1);
                String price = prices[0].substring(prices[0].indexOf("/ ") + 2).replace(")", "");
                map.put(size, price.toString());
            }
            reqList.add(vo.toRespDto(map));
        });
        return reqList;
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


@Transactional(rollbackFor = Exception.class)
public boolean removeProduct(int productMstId) {
    try {
        return productMapper.deleteProduct(productMstId) > 0;
    }catch (Exception e) {
        throw new ProductException
                (errorMapper.errorMapper("상품 오류", "상품 삭제 중 오류가 발생하였습니다."));
    }
}
```
- 위에서부터 상품 정보를 가져오는 기능, 상품의 갯수를 가져오는 기능, 상품을 삭제하는 기능이다.
- 상품 정보를 가져오면서 문자열로 나눠진 데이터 형태를 substring을 활용하여 key, value 형태로 분류하여 응답한다.

   <br>

**Repository**
```java
public List<GetProductVo> searchProductsWithAllSizes(SearchMasterProductVo searchMasterProductVo);
public Integer selectCountOfSearchedProducts(SearchMasterProductVo searchMasterProductVo);
public Integer deleteProduct(int productMstId);
```
   <br>

**Mybatis Query**
```java
<select id="searchProductsWithAllSizes" resultMap="productsWithAllSizesMap">
    select
        pmt.product_mst_id,
        pmt.product_name,
        ptt.pet_type_id,
        ptt.pet_type_name,
        pct.product_category_id,
        pct.product_category_name,
        pmt.product_thumbnail_url,
        pmt.product_detail_text,
        pmt.product_detail_url,
        pmt.create_date,
        GROUP_CONCAT(CONCAT('(', st.size_name, '/ ', pdt.price, ')') SEPARATOR ', ') AS sizes_and_prices
    from
        product_mst_tb pmt
        left outer join product_dtl_tb pdt on(pdt.product_mst_id = pmt.product_mst_id)
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


<delete id="deleteProduct">
    delete
    from
    product_mst_tb
    where
    product_mst_id = #{productMstId}
</delete>
```
- 다수의 사이즈를 하나의 객체에 담아오기 위해 GROUP_CONCAT을 사용하여 "(key/ value), (key/ value)"형태의 문자열로 select 하였다. <br>해당 문자열은 Service에서 가공하여 일반적인 Body형태로 변환하여 응답한다.


  </div>
  </details>






  <details>
  <summary>관리자 상품 수정 페이지</summary>
  <div markdown="1">

  ## 객체

   ### 정보를 받아오는 객체는 관리자 상품 관리 목록 페이지와 동일하게 사용한다.

<br>
   
**RequsetDto**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class EditProductReqDto {
    private String productName;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;

    // 사이즈별 가격
    @JsonProperty("no")
    private String no;
    @JsonProperty("XS")
    private String XS;
    @JsonProperty("S")
    private String S;
    @JsonProperty("M")
    private String M;
    @JsonProperty("L")
    private String L;
    @JsonProperty("XL")
    private String XL;
    @JsonProperty("XXL")
    private String XXL;
}
```


   <br>

## Front-End
   
**요청 코드**
```javascript
const productMstId = params.productMstId

const searchData = {
    petTypeName: "all",
    productCategoryName: "all",
    searchOption: 'number',
    searchValue: parseInt(productMstId),
    sortOption: 'number',
    pageIndex: 1}

const getProduct = useQuery(["getProduct"], async () => {
    const response = await getProductsApi(searchData);
    return response;
},
{
    refetchOnWindowFocus: false,
    retry: 0,
    onSuccess: response => {
        setProductThumbnailSrc(response?.data[0]?.productThumbnailUrl)
        setProductDetailImgSrc(response?.data[0]?.productDetailUrl);
        setProductData({
            productName: response?.data[0].productName,
            productDetailText: response?.data[0].productDetailText,
            productThumbnailUrl: response?.data[0].productThumbnailUrl,
            productDetailUrl: response?.data[0].productDetailUrl,
            no: response?.data[0].no,
            XS: response?.data[0].XS,
            S: response?.data[0].S,
            M: response?.data[0].M,
            L: response?.data[0].L,
            XL: response?.data[0].XL,
            XXL: response?.data[0].XXL,
        })

        for(let j = 0; j < response?.data.length; j++) {
            const numbers = [parseInt(response?.data[j].no), parseInt(response?.data[j].XS), parseInt(response?.data[j].S), parseInt(response?.data[j].M), parseInt(response?.data[j].L), parseInt(response?.data[j].XL), parseInt(response?.data[j].XXL)]
            const nums = [];
    
            for(let i = 0; i < numbers.length; i++) {
                if(isNaN(numbers[i])) {
                    numbers[i] = 0;
                }
                if(numbers[i] !== 0) {
                    nums.push(parseInt(numbers[i]))
                }
            }
            let lastNum = nums[0]
            for(let i = 0; i < nums.length; i++) {
                if(lastNum > nums[i]) {
                    lastNum = nums[i]
                }
            }
            productMinimumData.push({
                productName: response?.data[j].productName,
                productDetailText: response?.data[j].productDetailText,
                productThumbnailUrl: response?.data[j].productThumbnailUrl,
                productDetailUrl: response?.data[j].productDetailUrl,
                no: response?.data[j].no,
                XS: response?.data[j].XS,
                S: response?.data[j].S,
                M: response?.data[j].M,
                L: response?.data[j].L,
                XL: response?.data[j].XL,
                XXL: response?.data[j].XXL,
            })

        }
}
})
```
- params에서 key를 받아 상품 목록 정보를 받아오는 검색 객채를 재활용하여 요청을 보낸다.<br> 검색 객체를 보내지만 해당 페이지에 검색 기능은 없으므로 페이지 최초 로딩 시 한번만 요청이 일어난다.

<br>

**화면 출력 코드**
```javascript
return (
    <Mypage>
        <div css={S.SContainer}>
            <div css={S.STopTitle}>
                <h2>상품 정보 수정</h2>
            </div>
                <div css={S.SubContainer}>
                    <div>
                        <h1 css={S.SH1}>상품 메인 이미지 수정</h1>
                    </div>
                    <div css={S.SImgBox}> 
                        <img src={productThumbnailSrc} alt='상품 메인 이미지' onChange={handleProductDataOnChange}/>
                    </div>
                    <div css={S.SButtonBox}>

                        <input type="file" css={S.SFile} ref={productThumnailImgRef} onChange={handleThumbnailChange}/>
                        <button onClick={handleProductThumnailImgUploadClick}>메인 이미지 수정 파일 업로드</button>
                    </div>
                    <div>
                        <h1 css={S.SH1}>상품 상세 이미지 수정</h1>
                    </div>
                    <div css={S.SImgBox}>
                        <img src={productDetailImgSrc} alt='상품 상세 이미지' width={'700px'} onChange={handleProductDataOnChange}/>
                    </div>
                    <div css={S.SButtonBox}>

                        <input type="file" css={S.SFile} onChange={handleDetailImgChange} ref={productDetailImgRef}/>
                        <button onClick={handleProductDetailImgUploadClick}>상세 이미지 수정 파일 업로드</button>
                    </div>
                    <div>
                        <h1 css={S.SH1}>상품 정보 수정</h1>
                    </div>

                    <div css={S.SInformation}>
                        <div css={S.SInfoNameInput}>
                            <h2>상품명</h2>
                            <input type="text" 
                                name='productName' 
                                value={productData.productName} 
                                onChange={handleProductDataOnChange}/>
                        </div>
                        <div css={S.SInfoTextInput}>
                            <h2>상품 설명</h2>
                            <textarea type="text" 
                                name='productDetailText' 
                                value={productData.productDetailText} 
                                onChange={handleProductDataOnChange}/>
                        </div>
                        <div css={S.SInfoSizeInput}>
                            <h2>사이즈별 가격</h2> 
                            <ul>
                            {productData.no === "" ?
                                <>
                                    <li>
                                        <label>XS</label>
                                        <input value={productData.XS} 
                                                type='text' 
                                                name='XS' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                    <li>
                                        <label>S</label>
                                        <input value={productData.S} 
                                                type='text' 
                                                name='S' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                    <li>
                                        <label>M</label>
                                        <input value={productData.M} 
                                                type='text' 
                                                name='M' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                    <li>
                                        <label>L</label>
                                        <input value={productData.L} 
                                                type='text' 
                                                name='L' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                    <li>
                                        <label>XL</label>
                                        <input value={productData.XL} 
                                                type='text' 
                                                name='XL' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                    <li>
                                        <label>XXL</label>
                                        <input value={productData.XXL} 
                                                type='text' 
                                                name='XXL' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                </> :
                                <>
                                    <li>
                                        <label>NO SIZE</label>
                                        <input value={productData.no} 
                                                type='text' 
                                                name='no' 
                                                onChange={handleProductDataOnChange}/>
                                    </li>
                                </>}
                            </ul>
                        </div>
                    </div>
                <div >
                    <button css={S.SSubmit}onClick={handleUpdateSubmit} >등록하기</button>
                </div>
            </div>
        </div>
    </Mypage>
);
```
- 사이즈가 없는 제품과 사이즈별로 가격이 나뉘는 제품을 구분하여 가격 정보를 수정할 수 있는 Input창을 다르게 표시해주도록 코드를 작성하였다.
- 각 정보를 수정 시 onChange를 통해 요청데이터의 최신화가 이뤄지도록 코드를 작성하였다.

<br>

**기타**
```javascript
useEffect(() => {
    if(principal?.data?.data.roleName !== "ROLE_ADMIN" || !principal?.data) {
        alert("정상적인 접근이 아닙니다.")
        navigate("/")
    }
}, [])

const handleUpdateSubmit = async () => {
    try {
        if(!!productThumbnailFile) {
            const thumbnailStorageRef = ref(storage, `files/product/${productThumbnailFile?.name}`);
            await uploadBytesResumable(thumbnailStorageRef, productThumbnailFile);
            const downLoadURL = await getDownloadURL(thumbnailStorageRef);
            productData.productThumbnailUrl = downLoadURL;
        }

        if(!!productDetailImgFile) {
            const detailImgStorageRef = ref(storage, `files/product/${productDetailImgFile?.name}`);
            await uploadBytesResumable(detailImgStorageRef, productDetailImgFile);
            const downLoadURL = await getDownloadURL(detailImgStorageRef);
            productData.productDetailUrl = downLoadURL
        }

        await updateProductApi(productMstId, productData, option);
        alert("수정이 완료되었습니다.")
        window.location.replace("/admin/product/edit")
    }catch(error) {
        console.log(error.response.data)
    }
}
```
- 관리자가 아니라면 접근을 차단하는 코드
- 수정 버튼을 클릭 시, firebase에 업로드 후 해당 Url을 받아서 요청 객체에 담는다. 그 후 Back-end로 요청을 전송한다.

<br>
   
## Back-End

**Controller**
```java
@PutMapping("/api/admin/product/{productMstId}")
public ResponseEntity<?> editProduct(@PathVariable int productMstId, @RequestBody EditProductReqDto editProductReqDto) {
    return ResponseEntity.ok().body(productService.editProduct(productMstId, editProductReqDto));
}
```

   <br>

**Service**
```java
@Transactional(rollbackFor = Exception.class)
public boolean editProduct(int productMstId, EditProductReqDto editProductReqDto) {
    try {
        Map<String, Object> mstReqMap = new HashMap<>();
        mstReqMap.put("productMstId", productMstId);
        mstReqMap.put("productName", editProductReqDto.getProductName());
        mstReqMap.put("productDetailText", editProductReqDto.getProductDetailText());
        mstReqMap.put("productThumbnailUrl", editProductReqDto.getProductThumbnailUrl());
        mstReqMap.put("productDetailUrl", editProductReqDto.getProductDetailUrl());
        productMapper.updateProductMst(mstReqMap);


        if(editProductReqDto.getNo().equals("")) {
            Map<String, Integer> dtlReqMap = new HashMap<>();
            dtlReqMap.put("2", Integer.parseInt(editProductReqDto.getXS()));
            dtlReqMap.put("3", Integer.parseInt(editProductReqDto.getS()));
            dtlReqMap.put("4", Integer.parseInt(editProductReqDto.getM()));
            dtlReqMap.put("5", Integer.parseInt(editProductReqDto.getL()));
            dtlReqMap.put("6", Integer.parseInt(editProductReqDto.getXL()));
            dtlReqMap.put("7", Integer.parseInt(editProductReqDto.getXXL()));

            for (Map.Entry<String, Integer> entry : dtlReqMap.entrySet()) {
                int sizeId = Integer.parseInt((String) entry.getKey());
                Integer price = entry.getValue();
                productMapper.updateProductDtl(productMstId, sizeId, price);
            }
        }else {
            productMapper.updateProductDtl(productMstId, 1, Integer.parseInt(editProductReqDto.getNo()));
        }
        return true;
    }catch (Exception e) {
        throw new ProductException
                (errorMapper.errorMapper("상품 오류", "상품 정보 수정 중 오류가 발생하였습니다."));
    }
}
```
- 요청 Body에 비어있는 객체를 확인하여 사이즈가 있는 상품인지 없는 상품인지 구분하여 데이터를 업데이트한다.
- 사이즈 Id를 직접 key에 입력해줘아햐는 한계가 있어 수정이 필요해보인다.

   <br>

**Repository**
```java
public Integer updateProductMst(Map<String, Object> map);
public Integer updateProductDtl(int productMstId, int sizeId, int price);
```

   <br>

**Mybatis Query**
```java
<update id="updateProductMst" parameterType="hashmap">
    update
        product_mst_tb
    set
        product_name = #{productName},
        product_detail_text = #{productDetailText},
        product_thumbnail_url = #{productThumbnailUrl},
        product_detail_url = #{productDetailUrl}
    where
        product_mst_id = #{productMstId}
</update>



<update id="updateProductDtl" parameterType="hashmap">
    update
        product_dtl_tb
    set
        price = #{price}
    where
        product_mst_id = #{productMstId}
        and size_id = #{sizeId}
</update>
```

  </div>
  </details>







  <details>
  <summary>관리자 상품 상세 정보 조회 페이지</summary>
  <div markdown="1">

## 객체
   
**ResponseDto**
```java
@Data
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class ProductMst {
    private int productMstId;
    private String productName;
    private int petTypeId;
    private int productCategoryId;
    private String productDetailText;
    private String productThumbnailUrl;
    private String productDetailUrl;
    private LocalDateTime createDate;

    private PetType petType;
    private Category category;
    private List<ProductDtl> productDtlList;
}

@Data
public class PetType {
    private int petTypeId;
    private String petTypeName;
}

@Data
public class Category {
    private int productCategoryId;
    private String productCategoryName;
}


@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ProductDtl {
    private int productDtlId;
    private int productMstId;
    private int price;
    private int sizeId;
    private int actualStock;
    private int tempStock;

    private Size size;
    private ProductMst productMst; // 빈 데이터로 전송된다.
}

@NoArgsConstructor
@AllArgsConstructor
@Builder
@Data
public class Size {
    private int sizeId;
    private String sizeName;
}
```

   <br>
   
## Front-End
   
**요청 코드**
```javascript
const param = useParams()
const productMstId = parseInt(param.productMstId)

const getProduct = useQuery(["getProduct"], () => {
    return getProductMstApi(productMstId);
},{
    refetchOnWindowFocus: false,
    retry: 0
})
```
- params에 있는 Id를 사용하여 요청을 전송한다.

<br>

**화면 출력 코드**
```javascript
return (
    <Mypage>
        <div css={S.SContainer}>
            <div css={S.STopTitle}>
                <h2>상품 상세 정보</h2>
            </div>
            <div css={S.SSubTitleBox}>
                <h3>상품 정보</h3>
                <button onClick={handleReturnToEditPageClick}>전체 상품 리스트</button>
            </div>
            <div css={S.STableBox}>
                <table css={S.SToptable}>
                    <tbody>
                    <tr css={S.SThtdBox}>
                        <th>상품 추가 일자</th>
                        <td>{getProduct?.data?.data.createDate}</td>
                    </tr>
                    <tr css={S.SThtdBox}>
                        <th>상품 이름</th>
                        <td>{getProduct?.data?.data.productName}</td>
                    </tr>
                    <tr css={S.SThtdBox}>
                        <th>상품 설명</th>
                        <td>{getProduct?.data?.data.productDetailText}</td>
                    </tr>
                    <tr css={S.SThtdBox}>
                        <th>동물 종류 이름[ID]</th>
                        <td>{getProduct?.data?.data.petTypeName}[{getProduct?.data?.data.petTypeId}]</td>
                    </tr>
                    <tr css={S.SThtdBox}>
                        <th>카테고리 이름[ID]</th>
                        <td>{getProduct?.data?.data.productCategoryName}[{getProduct?.data?.data.productCategoryId}]</td>
                    </tr>
                    <tr css={S.SThtdBox}>
                        <th>상품 마스터 ID</th>
                        <td>{productMstId}</td>
                    </tr>
                    </tbody>
                </table>
            </div>

            <div css={S.SSubTitleBox}>
                <h3>상품 사이즈별 정보</h3>
            </div>
            <div css={S.STableBox}>
                {getProduct?.data?.data.productDtlList?.map(dtl => {
                return <table key={dtl.productDtlId} css={S.SBottomTable}>
                    <tbody>
                        <tr css={S.SThtdBox}>
                            <th>상품 상세(사이즈별) ID</th>
                            <td>{dtl.productDtlId}</td>
                        </tr>
                        <tr css={S.SThtdBox}>
                            <th>가격</th>
                            <td>{dtl.price}</td>
                        </tr>
                        <tr css={S.SThtdBox}>
                            <th>사이즈[사이즈ID]</th>
                            <td>{dtl.size.sizeName}[{dtl.sizeId}]</td>
                        </tr>
                        <tr css={S.SThtdBox}>
                            <th>실제 재고</th>
                            <td>{dtl.actualStock}</td>
                        </tr>
                        <tr css={S.SThtdBox}>
                            <th>임시 재고(실제 재고 - 주문재고)</th>
                            <td>{dtl.tempStock}</td>
                        </tr>
                    </tbody>
                </table>
                })}
            </div>
            <div css={S.SImgContainer}>
                <div>
                    <h2 css={S.SH2}>상품 메인 이미지</h2>
                </div>
                <div>
                    <img src={getProduct?.data?.data.productThumbnailUrl} alt='' width={'700px'}/>
                </div>
                <div css={S.SMidBox}>
                    <h3>상품 메인 이미지 URL</h3>
                    <div>{getProduct?.data?.data.productThumbnailUrl}</div>
                </div>
                <div>
                    <h2 css={S.SH2}>상품 상세 정보 이미지</h2>
                </div>
                <div>
                    <img src={getProduct?.data?.data.productDetailUrl} alt='' width={'700px'}/>
                </div>
                <div css={S.SMidBox}>
                    <h3>상품 상세 정보 이미지 URL</h3>
                    <div>{getProduct?.data?.data.productDetailUrl}</div>
                </div>
                <button css={S.SButton} onClick={handleReturnToEditPageClick}>전체 상품 리스트</button>
            </div>

        </div>
    </Mypage>
);
```
- 관리자가 상품에 대한 모든 정보를 확인하는 페이지이므로 카테고리 ID와 같은 상세한 정보까지 모두 화면에 출력한다.

<br>
   
## Back-End

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
<resultMap id="productMstMap" type="com.woofnmeow.wnm_project_back.entity.ProductMst">
    <id property="productMstId" column="product_mst_id" />
    <result property="productName" column="product_name" />
    <result property="petTypeId" column="pet_type_id" />
    <result property="productCategoryId" column="product_category_id" />
    <result property="productDetailText" column="product_detail_text" />
    <result property="productThumbnailUrl" column="product_thumbnail_url" />
    <result property="productDetailUrl" column="product_detail_url" />
    <result property="createDate" column="create_date" />
    <association property="petType" resultMap="petTypeMap"/>
    <association property="category" resultMap="categoryMap"/>
    <collection property="productDtlList" javaType="list" resultMap="productDtlMap"/>
</resultMap>

<resultMap id="productDtlMap" type="com.woofnmeow.wnm_project_back.entity.ProductDtl">
    <id property="productDtlId" column="product_dtl_id" />
    <result property="productMstId" column="product_mst_id" />
    <result property="price" column="price" />
    <result property="sizeId" column="size_id" />
    <result property="actualStock" column="actual_stock" />
    <result property="tempStock" column="temp_stock" />
    <association property="size" resultMap="sizeMap" />
</resultMap>

<resultMap id="sizeMap" type="com.woofnmeow.wnm_project_back.entity.Size">
    <id property="sizeId" column="size_id"/>
    <result property="sizeName" column="size_name"/>
</resultMap>

<resultMap id="petTypeMap" type="com.woofnmeow.wnm_project_back.entity.PetType">
    <id property="petTypeId" column="pet_type_id"/>
    <result property="petTypeName" column="pet_type_name"/>
</resultMap>

<resultMap id="categoryMap" type="com.woofnmeow.wnm_project_back.entity.Category">
    <id property="productCategoryId" column="product_category_id"/>
    <result property="productCategoryName" column="product_category_name"/>
</resultMap>
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

