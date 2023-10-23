# WAU-Drop-Analysis

## 요약
### ① 기본정보
* 개인프로젝트 (기여도 100%)
* 데이터 출처 : [Mode Analytics Dataset](https://mode.com/)

### ② 프로젝트 진행 배경
* [그로스해킹(저자 : 양승화)](https://product.kyobobook.co.kr/detail/S000001766457)를 읽고 AB Test를 포함한 **그로스해킹**에 대해 처음으로 배우게 되었습니다.
* 지식 습득에 그치지 않고 이를 실제 데이터 분석에 응용하여 그로스해킹을 내재화하고자 합니다.

### ③ 결과 및 직무에 적용할 점
* WAU 감소 원인을 밝히고자 AB Test를 수행했으나, 통제변수 관리 실패 등으로 신뢰도가 낮습니다.
  *  **신뢰도 높은 AB Test를 수행하기 위해 통제변수가 그룹 간에 동일한지 반드시 확인하겠습니다.**
  *  **예상 시나리오를 수립하기 전 충분한 EDA를 통해 정보를 수집하도록 하겠습니다.**
 
### ④ 주요 액션
<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/4f7ab891-8912-4fc2-91ab-10cb67a8a8c1'></p>

---
## 상세
### ① 문제인식

* Yammer는 기업용 협업도구 및 소셜 네트워크 서비스(SNS)임. 전세계 47개국에서 사용하고 있으며 2012년 Microsoft로 인수되어 현재는 [Viva Engage](https://www.microsoft.com/ko-kr/microsoft-viva/engage)라는 이름으로 운영되고 있음
* 유저 인게이지먼트의 흐름을 시각화한 결과, **2014년 8월 이후 WAU가 감소했음을 확인함**

<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/d090b796-6149-4b52-8746-a691cab6da25' width = 80%></p>

---
* **EDA**를 수행한 결과, 다음과 같은 사실을 확인함 [[SQL·Python 코드 링크]](https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/blob/main/SQL_Python%20Code.md#%EB%AC%B8%EC%A0%9C-%EC%9D%B8%EC%8B%9D)
  * 북반구 사용자가 남반구에 비해 10배 가량 많음
  * 5~7월 평균과 8월을 비교했을 때, 북반구 국가의 70%에서 이벤트 발생 횟수가 감소함
  * 반면 남반구 국가의 67%에서 이벤트 발생 횟수가 증가함
<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/2097f43c-5fb2-4caa-90b7-a8e4f6e00379'></p>

---
* EDA 결과를 통해 아래와 같은 **예상 시나리오**를 수립할 수 있었음
<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/faf0aa5e-a508-4606-be21-c2ae8d6c2e2e'></p>

* 아래 가정 하에 **북반구/남반구의 8월 업무 참여도에 대한 가설 검정을 실시하여, 휴가가 유저 인게이지먼트에 미치는 영향을 확인**하고자 함

$${\color{gray}Yammer\\,서비스의\\,특성\\,상,\\,WAU는\\,사용자의\\,업무\\,참여도에\\,비례한다.}$$

$${\color{gray}2014년\\,8월\\,북반구\\,/\\,남반구\\,사용자\\,그룹의\\,유일한\\,차이점은\\,휴가\\,시즌\\,여부이다.}$$
