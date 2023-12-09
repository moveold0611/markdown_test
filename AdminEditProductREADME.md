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

