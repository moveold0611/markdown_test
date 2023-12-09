관리자 상품 관리 페이지
=======================

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

