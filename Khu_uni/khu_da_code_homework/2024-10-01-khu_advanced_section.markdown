---
layout: post
title: 	"5th & 6th chapter of Data analyst"
date: 	2024-09-15 00:50:17 +0900
categories: KhuDa
---

테크닉 41 데이터를 읽어 들이고 이용데이터를 수정하자.

read the csv files
```python
import pandas as pd
customer = pd.read_csv('customer_join.csv')
uselog_months = pd.read_csv('use_log_months.csv')
```

```python
# 제시한 변수
# customer
# uselog_months
year_months = list(uselog_months["연월"].unique()) # 1행

uselog = pd.DataFrame()
for i in range(1, len(year_months)):
    tmp = uselog_months.loc[uselog_months["연월"]==year_months[i]] # uselog_months
    #print(tmp["연월"]) 각 같은 연월이 나온다.
    #print(tmp) /연월 customer_id  count
    
    #print(tmp["count"])
    tmp.rename(columns={"count":"count_0"}, inplace =True)
    #print(tmp["count"])
    # col의 이름만 바꾼 것이다.
    
    tmp_before = uselog_months.loc[uselog_months["연월"]==year_months[i-1]]
    # 이전 월을 구하기 위해서 201805 > 201804
    #print(tmp_before["연월"])

    #print(tmp_before)
    del tmp_before["연월"]
    #print(tmp_before) /customer_id  count
    
    tmp_before.rename(columns={"count":"count_1"},inplace =True)
    tmp = pd.merge(tmp, tmp_before, on="customer_id", how="left")
    #print(tmp) /연월 customer_id  count_0  count_1
    
    uselog= pd.concat([uselog, tmp], ignore_index =True)
uselog.head()
```
![alt text](image.png)

- 1행 : 연월 칼럼을 리스트화
- for 문 : 참고 테크닉 36

테크닉 42 탈퇴 전월의 탈퇴 고객 데이터를 작성하자

왜 end_date 칼럼의 탈퇴 월이 아닌 탈퇴 전월의 데이터를 작성할까?

우린 탈퇴를 예측하는 것이다.
예를 들어, 9월에 탈퇴를 한다면 8월에는 탈퇴를 신청했을 것이므로 9월로는 탈퇴고객을 미연에 방지할 수 없다.
그래서 1개월 전인 7월을 이용하여 8월에 탈퇴 신청을 할 확률을 예측해야 한다.
```python
from dateutil.relativedelta import relativedelta
exit_customer = customer.loc[customer["is_deleted"]==1]
exit_customer["exit_date"] = None
exit_customer["end_date"] = pd.to_datetime(exit_customer["end_date"])
for i in range(len(exit_customer)):
    exit_customer["exit_date"].iloc[i] = exit_customer["end_date"].iloc[i] - relativedelta(months=1)
exit_customer["연월"] = pd.to_datetime(exit_customer["exit_date"]).dt.strftime("%Y%m")
uselog["연월"]=uselog["연월"].astype(str)
exit_uselog = pd.merge(uselog,exit_customer, on=["customer_id", "연월"], how="left")
print(len(uselog))
exit_uselog.head()
```
![alt text](image-1.png)

```python
#print(exit_uselog["customer_id"][19]) #??  이렇게 쓴다.
exit_uselog = exit_uselog.dropna(subset = ["name"])
print(len(exit_uselog))
print(len(exit_uselog["customer_id"].unique()))
exit_uselog.head()
```
![alt text](image-2.png)

결측치가 많기에 name column을 기준으로 결측치가 있는 값은 제거한다.

43 지속회원의 데이터를 작성하자

```python
conti_customer = customer.loc[customer["is_deleted"]==0]
#print(conti_customer["is_deleted"])
conti_uselog = pd.merge(uselog,conti_customer, on = ["customer_id"],how="left")
#print(conti_uselog)
#print(len(conti_uselog))
conti_uselog = conti_uselog.dropna(subset=["name"])
#print(len(conti_uselog))

#33851
#27422
```

데이터를 섞고 중복을 제거하기
```python
conti_uselog= conti_uselog.sample(frac=1).reset_index(drop=True)
#중복 제거하기
conti_uselog = conti_uselog.drop_duplicates(subset = "customer_id")

print(len(cont_uselog))
conti_uselog.head()
```
sample(frac=1) : 하나의 특성을 가져와 


지속, 탈퇴 회원 데이터를 세로로 결합해보자.
```python
predict_data = pd.concat([conti_uselog, exit_uselog, ignore_index=True])
print(len(predict_data))
predict_data.head()
```

테크닉 44 예측한 달의 재적 기간을 작성하자
```python

```
```python

```
```python

```