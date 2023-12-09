관리자 상품 관리 페이지
=======================

***

관리자 상품 추가 페이지
---------------------------------

  <details>
  <summary>객체</summary>
  <div markdown="1">
   
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
```
   <br>
   
  </div>
  </details>
<br/>

  <details>
  <summary>Front-End</summary>
  <div markdown="1">
   
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
   
  </div>
  </details>
<br/>


  <details>
  <summary>Back-End</summary>
  <div markdown="1">

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

   <br>

  </div>
  </details>
<br/>

***

관리자 상품 관리 목록 페이지
----------------------------

  <details>
  <summary>객체</summary>
  <div markdown="1">
   
**Dto**
```java

```
   <br>
   
  </div>
  </details>
<br/>



  <details>
  <summary>Front-End</summary>
  <div markdown="1">
   
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

   
  </div>
  </details>
<br/>


  <details>
  <summary>Back-End</summary>
  <div markdown="1">

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
```
   <br>

**Mybatis Query**
```java
```
   <br>

  </div>
  </details>
<br/>

***


관리자 상품 수정 페이지
-----------------------

  <details>
  <summary>객체</summary>
  <div markdown="1">
   
**Dto**
```java
```
   <br>
   
  </div>
  </details>
<br/>



  <details>
  <summary>Front-End</summary>
  <div markdown="1">
   
**요청 코드**
```javascript
```

<br>

**화면 출력 코드**
```javascript
```

<br>
   
  </div>
  </details>
<br/>


  <details>
  <summary>Back-End</summary>
  <div markdown="1">

**Controller**
```java
```
   <br>

**Service**
```java
```
   <br>

**Repository**
```java
```
   <br>

**Mybatis Query**
```java
```
   <br>

  </div>
  </details>
<br/>


***


관리자 상품 상세 정보 조회 페이지
---------------------------------

  <details>
  <summary>객체</summary>
  <div markdown="1">
   
**Dto**
```java
```
   <br>
   
  </div>
  </details>
<br/>



  <details>
  <summary>Front-End</summary>
  <div markdown="1">
   
**요청 코드**
```javascript
```

<br>

**화면 출력 코드**
```javascript
```

<br>
   
  </div>
  </details>
<br/>


  <details>
  <summary>Back-End</summary>
  <div markdown="1">

**Controller**
```java
```
   <br>

**Service**
```java
```
   <br>

**Repository**
```java
```
   <br>

**Mybatis Query**
```java
```
   <br>

  </div>
  </details>
<br/>

