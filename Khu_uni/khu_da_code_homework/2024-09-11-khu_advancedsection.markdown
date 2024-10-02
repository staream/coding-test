---
layout: post
title: 	"1st & 2nd chapter of data analysis technic"
date: 	2024-09-11 00:50:17 +0900
categories: KhuDa
---
1~57page

# 01장 웹에서 주문 수를 분석하는 테크닉 10
비즈니스 현장에서의 데이터 분석은 화려하지 않고, 현장이라서 해야 하는 사소한 업무가 많다.
<br>
이런 업무의 축적이 기업이나 나라와 같은 큰 조직에서는 **현황 분석**, **미래 예측**으로 귀결된다.
<br>
이번 장에서는 상품 주문 수의 추세를 분석함으로써 판매량 개선의 방향을 찾는 것이 목적이다.
쇼핑몰 사이트의 데이터는 웹에서 얻을 수 있는 비교적 '깨끗한' 데이터.
<br>
분석에 사용하는 데이터는 매출 추세만이 아니다.

1) 언제
2) 누가

위처럼 상세한 데이터를 통해 더 치밀한 분석이 가능해진다. 

이런 분석이 힘든 이유

- 대부분의 데이터는 한 곳에서 관리되고 있지 않는 것이 일반적이다.

sol
- 부서 간의 데이터를 **연결**하는 작업이 필요해짐.
### 중요한 요소 데이터의 연결
데이터 분석가의 역량
- 어떤 데이터를 어떻게 연결해서 활용할 것인가가 데이터 분석가의 역량을 보여주는 부분이다.

아래의 예시를 통해 어떻게 해결하는지 알아보자.

customer needs
- plz, analyze dataesets of our shopping site.

전제조건
- 품목은 컴퓨터, 가격대별로 5개의 상품이 존재한다.
- 데이터는 4종류의 6개 데이터로 존재한다.

| No.| 사용데이터 | 입력된 시기 |
|---|---|---|
| 1 | 고객 정보 | 회원 등록 시|
| 2. | 상품 데이터 | 상품명, 가격|
|3-1,2 | 구매내역 데이터 | 언제, 어느 고객이 얼마나 샀는가?|
| 4-1,2 | 구매내역 상세 데이터 | 구체적으로 어떤 상품을 몇 개 샀는지에 대해 나와있다. |
여기서 n- 1,2는 *database*의 데이터는 시스템에서 가져올 때 월별이나 1,000건 단위로 분할하는 경우가 많다. 

## 테크닉 1 데이터를 읽어보자.
csv 형식의 파일을 읽는 방법은
```python
import pandas as pd
customer_master = pd.read_csv('')
customer_master.head()
# 이 과정을 6번 진행한다.
```

head() 메소드를 통해 처음 5행을 표시한 것이다.
<br>
이와 같은 작업을 하여 얻을 수 있는 이점

1. 어떤 데이터가 존재하는지
2. 각 데이터 간에 어떤 관계가 있는지

이렇게 모은 데이터를 우리는 무엇을 할 수 있을까?
1. 분석의 목적과 상관없이 데이터 전체를 파악하는 것이 중요하다.
    1) 상세한 정보가 있는 데이터를 가공하는 것이 중요하다.
    - 방법으로 세로로 결합(유니언), 가로로 결합(조인)
## 테크닉 2 데이터를 결합(유니언)해보자
1) customer_master
    - customuer_id, customer_name
    - regi._date, email
    - gen., age
    - birth, pref
2) item_master
    - item_id, name, price
3) transaction_
    - tran_id, price, date, cus_id    
4) trans_detail
    - detail_id, trans_id, item_id, quantity

```python
# pd.read_csv로 각각 transaction_1,2를 읽어준다.
transaction = pd.concat([transaction_1, transaction_2], ignore_index=True)
transaction.head()
```

pd.concat 메소드
- 기능 : 행 방향으로 늘리는(세로로 결합) 것이므로 데이터의 개수에 변화가 있다.

이를 확인하기 위해 *len함수*를 사용하면 된다.

## 테크닉 3 매출 데이터끼리 결합(조인)해 보자.
### 생각할 점 
1) 조인할 때에 기준이 되는 데이터를 정확하게 결정하기
2) 어떤 칼럼을 키로 조인할지 생각하기

이에 대한 결정짓는 데이터 셋은 현재 존재하는 가장 설명이 많은 transaction_detail을 기준으로 결정한다.

### 과정
1) 부족한 데이터 칼럼이 무엇인가?
2) 공통되는 데이터 칼럼이 무엇인가?

부족한 데이터 칼럼은 아예 존재하지 않는지를 알아보는 것으로 생각하면 될 것 같다.

공통되는 데이터 칼럼은 현재로썬 내가 만들 수 있는 데이터 칼럼은 제외한다.

```python
# payment_date, customer_id를 추가한다.
join_data = pd.merge(transaction_detail, transaction[["transaction_id","payment_date","customer_id", on="transaction_id", how="left"]])
join_data.head()
```
### 사용함수
- pd.merge
- 내용 : 
    1) transaction_detail을 기준으로, 
    2) 조인키(on)는 결합할 transaction 데이터의 필요한 칼럼 transaction_id, 
    3) 조인 종류는 left join으로 지정했다.
    - merge는 기준 데이터셋과 뽑을 데이터 셋을 가지고와서 거기에서의 특정 칼럼을 가져와서 데이터셋에 더한다. 이때 조인키(JOIN KEY)는 두개의 데이터셋에 있는 것으로 데이터 객체를 맞추는 역할을 한다.
- len
- 세로로 더하지 않았다면 출력값은 변화가 없다.

## 테크닉 4 마스터데이터를 결합(조인)해 보자
상기 내용과 동일

## 테크닉 5 필요한 데이터 칼럼을 만들자
이때 사용하는 것은 indexing이다.

indexing을 사용하는 곳
1) 값을 계산
    - 같은 타입의 경우는 새로운 col을 만들어서 값을 넣을 수 있다.
2) 한번에 표현하는 방법
    - 이때는 복수의 col을 표현하는 경우는 list 안의 list의 형태로 데이터 셋을 만든다.
```python
# 1) 
join_data["price"]=join_data["quantity"]* join_data["item_price"]
# 2)
join_data[["quantity","item_price","price"]].head()
```
위의 과정으로 새롭게 추가한 데이터가 만들어진다.

이때 데이터 가공은 한번 잘못되면 집계 실수로 인해 수치상 에러가 생긴다. 이는 치명적이다.

유의점
1) 데이터 결합할때의 신중히 개수 확인
2) 검산이 가능한 데이터 칼럼을 찾기

## 테크닉 6 데이터를 검산하자
검산하라는 기존의 데이터와 옮기는 도중에 **불일치**라는 오류가 없는지 알아보라는 내용이다.
```python
print(join_data["price"].sum())
```

## 테크닉 7 각종 통계량을 파악하자
파악해야 할 숫자
1) 결손치
2) 전체를 파악하는 숫자감?

결손치는 제거하거나 보간하는 방법을 이용
- 절대로 그냥 두지 말기

전체 숫자감에 따라 현재의 수치를 해석하는 내용이 달라지기에 중요하다.
```python
# 1)
join_data.isnull().sum()
# 2)
join_data.describe()
```
1)은 결손치의 개수를 출력한다. isnull()을 이용하면 결손치가 True/False로 반환하기에 

sum을 이용하여 True의 개수를 합한다.

2)은 전체적인 느낌을 파악하기 위한 각종 통계량을 출력한다.

각각 count, mean, std, min, 25%, 75%, 50%,max를 간단히 출력할 수 있다.

25%나 75%는 헤당 위치에 있는 object의 attribute를 가져온다

### 추가적인 Tip
숫자뿐만 아니라 데이터의 기간을 알아보는 것도 권장한다.
```python
print(join_data["payment_date"].min())
print(join_data["payment_date"].max())
```

## 테크닉 8 월별로 데이터를 집계
위에선 전체적인 분위기를 파악했다.

우선 시계열 상황을 살펴보자.

시계열을 사용하는 이유 
1) 전체 데이터를 한 번에 분석하면 시계열 변화를 잘못 파악하는 경우가 있다.
    - sol. 데이터 볌위를 좁혀서 분석하는 것도 하나의 방법이다.
2) 전체적으로 매출이 늘어나고 있는지 줄어들고 있는지를 파악하는 것이 분석의 첫걸음이다.

방법
1. 우선 데이터형을 확인
```python
join_data.dtypes
```
이 메소드는 각 칼럼마다 데이터형을 확인할 수 있다.

이때 date가 object형이라면
1) 그대로 문자열로 다룬다.
2) **datetime**형으로 변환하여 연월 칼럼을 작성한다.
```python
join_data["payment_date"]= pd.to_datetime(join_data["payment_date"])
join_data["payment_month"]= join_data["payment_date"].dt.strftime("%Y%m")
join_data[["payment_date","payment_month"]].head()
```
#### 사용함수
pd.to_datetime( 해당 칼럼 )
- datetime으로 변환한다.
해당칼럼.dt.strftime("%Y%m")
- 연월 단위로 작성한다.

### 할 수 있는 다른 기능
sum함수와 더불어 월에 따른 데이터를 가져온다.
```python
join_data.groupby("payment_month").sum()["price"]
```
#### 사용함수
groupby("").sum()[""]: 같은 값으로 group화 하여 그 것의 object중에서 특정 col의 값들을 더한다. 

## 테크닉 9 월별, 상품별로 데이터 집계
```python
join_data.groupby(["payment_month","item_name"]).sum()["price","quantity"]
```
월에 따른 상품명에 대한 가격과 수량을 반환한다.
### 방법
groupby에서 다수의 col을 넣으려면 list를 이용해야 한다.
### 단점 
다수의 attribute를 넣어서 출력하면, 결과를 **직관적으로 보기** 어렵다.

그래서 다른 방법인 pivot_table을 사용하자.

### pivot_table
```python
pd.pivot_table(join_data, index='item_name', columns='payment_month', values=['price','quantity'], aggfunc='sum')
```
#### 사용함수
pd.pivot_table : 
- 행 : index
- 열 : columns
- values : 집계하고 싶은 칼럼(price, quantity)
- aggfunc : 집계방법(sum) 지정

## 테크닉 10 추이 가시화
시계열 데이터를 그래프화를 할 때는 
- index(행, 가로의 이름)에는 **시간의 흐름**을 
- columns(열, 세로의 이름)에는 **상품을 이름**을 
```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.plot(list(graph_data.index),graph_data["상품명"],label= "상품명")



plt.legend()
```
### 사용함수
plt.plot : 
- 1st는 가로축을 어떻게 할 것인가?
- 2nd는 그래프에 들어갈 value는 무엇인가?
- 3rd는 label로 선의 이름이다.

# 2장 대리점 데이터를 가공하는 테크닉 10
1장과의 차이점 
- 쇼핑몰 : 시스템에 의해 관리
- 대리점 : 인력이 들어감
    - 입력 실수, 데이터 누락 등 '오류'가 많이 나온다.
    - 이는 **잘못된 결과**, **데이터 가공 처리**가 안 되는 문제가 발생
    <br/>
    - 문법의 차이는 오작동의 원리가 된다.
    - 다른 부서에서 수집된 데이터도 **통일**의 어려움이 있다. 
### 전제조건
1) A~Z 26개의 상품
2) 매출 이력, 고객정보 데이터는 인력으로 저장
3) 집계 기간의 단가 변동은 없음.
4) 매출 이력은 시스템에서 CSV로 출력
5) 고객 정보는 EXCEL 이용

## 테크닉 11 데이터 읽기

```python
import pandas as pd
# csv
uri_data = pd.read_csv(".csv")
uri_data.head()
# excel
kok_data = pd.read_excel(".xlsx")
kok_data.head()
```
#### 데이터 정합성에 문제가 있다.
입력 오류나 표기 방법의 차이가 부정합을 일으킬 때 사용하는 용어이다.
<br/>
이름을 A를 a라고 적거나, NaN인 경우
<br/>
이름 : 띄어쓰기의 유무나 위치
<br/>
날짜 : /,-,년월일,YY/MM/DD or DD/MM/YY인 경우

이를 해결하기 위해 데이터를 파악하여 오류를 찾아보자.
## 테크닉 12 데이터의 오류를 살펴보기
- 이름 명명의 오류 : 대소문자, 공백
- 값 : 결측치

# 테크닉 13 오류 상태로 집계


```python
#
uri_data["purchase_date"]= pd.to_datetime(uri_data["purchase_date"])
#
uri_data["purchase_month"]= uri_data["purchase_date"].dt.strftime("%Y%m")
#
res =uri_data.pivot_table(index="purchase_month", columns="item_name", aggfunc="size", fill_value=0)
res
```

- 1~2행은 날짜를 연월 형태로 변환한다.
- 3행에서 세로축에 구입 연월, 가로축에 상품 건수(aggfunc="size")로 집계
- 4행은 집계 결과를 표시

7rows x 99 col

이 결과는 가로축(col)을 '상품'으로 집계했는데, 데이터에 오류가 있기에 원래 26개에서 99개의 상품으로 늘어났다.

# 테크닉 14 상품명 오류를 수정하자.

## 반환 코드
```python
print(len(pd.unique(uri_data["item_name"])))
```
### 사용함수
pd.unique(~~)
- ~~ 내의 요소들에서 중복을 제외하여, 단일의 요소들만을 모인 list를 반환한다.

str.upper()

str.replace(" ","")
## 오류 수정 코드
```python
#
uri_data["item_name"]= uri_data["item_name"].str.upper()
#
uri_data["item_name"]= uri_data["item_name"].str.replace(" ","")
#
uri_data.sort_values(by=["item_name",ascending=True])
```
1행에서 상품명에 있는 소문자를 str.upper()울 통해 대문자로 변환한다.

2행은 str.replace(" ","")을 통해 공백을 없앤다.

3행은 item_name 순으로 정렬해 화면에 표시한다.

결론
<br/>
pd.unique, str.upper(), str.replace()로 object type의 데이터를 변환했다.

# 테크닉 15 금액의 결측치를 수정하자
## 결측치 찾기
```python
uri_data.isnull().any(axis=0)
```
isnull()
- 이에 대한 결과로 각 column의 데이터에 결측치가 있는지 판단하고, 결측치가 있다면 해당 column은 True를 반환한다.

## 결측치 수정하기
```python
flg_is_null = uri_data["item_price"].isnull()
for trg in list(uri_data.loc[flg_is_null,"item_name"].unique()):
    price = uri_data.loc[(~fig_is_null)& (uri_data["item_name"]==trg), "item_price"].max()
    uri_data["item_price"].loc[(fig_is_null)&(uri_data["item_name"]==trg)]=price
uri_data.head()
```
1행 : item_price 중에 결측치가 있는지를 조사한다.

2행 : 반복문 처리는 list(uri_data.loc[flg_is_null, "item_name"].unique())를 이용한다.
<br/>
현재 flg_is_null을 이용해 결측치가 있는 상품명 리스트를 작성한다.

list()
- list는 변수의 값을 list형으로 반환한다.

loc()
- uri_data.loc[flg_is_null,"item_name"]에서 loc함수는 조건에 일치하는 **데이터를 추출**합니다.
- 여기서의 조건은 flg_is_null이 True인 경우이다.
- 2번째 item_name은 조건과 일치하는 데이터 중에서 어떤 칼럼을 가져올지를 지정한다.
    - 결론 : uri_data["item_name"]에서 flg_is_null이 True인 데이터들을 추출하는 것이다.

unique()
- 중복을 제거한다.

price = uri_data.loc[(~fig_is_null)& (uri_data["item_name"]==trg), "item_price"].max()
- price에 uri_data에서 결측치가 아니지만, 상품명이 같은 것의 가격을 가져와서 price에 대입하는 것이다.
## 수정된 값 불러오기
```python
for trg in list(uri_data["item_name"].sort_values().unique()):
    print(trg+ "의 최고가" + str(uri_data.loc[uri_data["item_name"]==trg]["item_price"].max())+"의 최저가"+ str(uri_data.loc[uri_data["item_name"]==trg]["item_price"].min(skipna=False)))
```

max와 min을 가져온 이유는 각각 이상한 값이 있는지를 판단하기 위해 가져온 것이다.

min(skipna=False)
- 이 메소드에서 skipna는 NaN의 무시 여부를 설정합니다. 이번에는 False로 지정했기 때문에 NaN이 존재할 경우 최솟값이 NaN으로 표시된다.
<br/>
그래서

- NaN이 표시되지 않았기에 잘 된 것을 볼 수 있다. 

# 테크닉 16 고객 이름의 오류를 수정
이름에 공백의 유무
```python
kok_data["고객이름"]=kok_data["고객이름"].str.replace("  ","")
```
str.replace(pre,aft)

# 테크닉 17 날짜 오류를 수정하자

## 주의할 점 
- 서식이 다른 데이터가 섞여 있을 수 있다.

## 해결법
날짜를 동일한 포맷으로 통일하기.

### 숫자로 표기된 날짜 찾기
```python
flg_is_serial = kok_data["등록일"].astype("str").str.isdigit()
flg_is_serial.sum()
```
- str.isdigit()
    <br/>
    고객 정보의 등록일이 숫자인지 아닌지를 
    <br/>
    이 메소드에서 판정한다.
    <br/>
    판정의 결과를 flg_is_serial에 저장합니다.
- .sum()
    <br/>
    위의 flg_is_serial의 True인 값들을
    <br/>
    모두 더하는 과정을 가진다.
    <br/>
    이는 숫자로 이루어진 타입의 개수를 알려준다.

### 숫자로 표기된 부분을 수정
```python
fromSerial= pd.to_timedelta(kok_data.loc[flg_is_serial, "등록일"].astype("float"), unit="D")+pd.to_datetime("1900/01/01")
```
#### 함수 소개
1. pd.to_timedelta 
    - 숫자를 날짜로 변환한다.
2. loc
    - flg_is_serial의 판정 기준으로 데이터를 추출한다.
### 서식이 다른 날짜 데이터 수정
```python
fromString = pd.to_datetime(kok_data.loc[~fig_is_serial, "등록일"])
fromString
```
### 변환한 데이터의 혼합
```python
kok_data["등록일"]=pd.concat([fromSerial, fromString])
kok_data
```
#### 사용함수
pd.concat

### 등록일로부터 등록월을 추출하여 집계
```python
kok_data["등록연월"]=kok_data["등록일"].dt.strftime("%Y%m")
rslt = kok_data.groupby("등록연월").count()["고객이름"]
print(rslt)
print(len(kok_data))
```
#### 사용함수
1. groupby()
    - 등록연월에 해당하는 고객들의 수를 계산
2. len(kok_data)
    - 고객 대장의 총데이터 수를 표시했다.

# 테크닉 18 고객 이름을 키로 두 개의 데이터를 결합하자
```python
join_data= pd.merge(uri_data, kok_data, left_on="customer_name",right_on="고객이름", how="left")
join_data = join_data.drop("customer_name",axis=1)
join_data
```
#### 사용함수
1. pd.merge
    - 서로 다른 열을 지정해서 결합한다.
    - 합칠 데이터 : uri_data, kok_data
    - left_on : uri의 customer_name col을 키로 지정
    - right_on : kok의 고객 이름 칼을 키로 지정
    - how : 결합방법, left로 지정
        - combine kok based on uri 


이 과정을 **데이터 정제**라고 한다.

# 테크닉 19 정제한 데이터를 덤프하자
덤프 : 출력하자는 말

이는 분석할 때 출력한 파일을 다시 읽어 들이면 데이터 정제를 할 필요없다.
```python
# new dataset
dump_data = join_data[["purchase_date","purchase_month","item_name","item_price","고객이름","지역","등록일"]] 
# save csv
dump_data.to_csv("dump_data.csv",index=False)
```
#### 사용함수
to_csv : 같은 폴더에 해당 이름의 파일을 출력한다.
<br/>
```python
kok_data["고객이름"]=kok_data["고객이름"].str.replace("  ","")
```