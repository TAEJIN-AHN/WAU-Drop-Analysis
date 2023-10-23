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

---

### ② 난관 및 해결책
* 본 프로젝트에 사용한 데이터셋에는 **유저의 근무상태를 알 수 있는 정보가 없음**.
* 아래의 배경에 따라, **이메일 열람-클릭 전환율을 업무 참여도를 나타내는 대체 지표로 제시**함
  *  Yammer에서는 조직의 주요 이슈를 요약한 뉴스레터를 정기적으로 사용자에게 발송한다.
  *  유저의 뉴스레터 수신 및 열람, Call to Action 링크 클릭 등 행동에 대한 데이터를 사용할 수 있다.
  *  업무 참여도가 높은 사람이라면 조직 이슈를 파악하기 위한 행동에 적극적일 것이다.

<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/ce22d183-bdb0-44da-af9a-354f999b5d6e' width = 50%></p>

---

### ③ 데이터 분석
* 관련 SQL 및 Python 코드는 [Github Link](https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/blob/main/SQL_Python%20Code.md#%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%84%EC%84%9D)를 참고하여 주시기 바랍니다.
* **그룹별 이메일 퍼널 차트**
  * **북반구의 열람 - 클릭 전환율이 남반구에 비해 3.1% 낮음**
<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/23cfadb5-a095-471d-b910-e1555d65864d'</p>

* **가설검정**
  * 북반구 그룹의 열람 - 클릭 전환율을 a, 남반구는 b라고 했을 때 **통계적 가설**은 다음과 같음

<p><img src = ''></p>
  * 검정 결과 (유의수준 95%), **p-value가 0.149로 귀무가설을 기각할 수 없었음**

<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/9588b938-3219-493e-9778-d07de693f28b' width = 70%></p>

---
### ④ 결과

<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/c20e961c-37e3-431a-a83b-4cfeb8a17229' width = 80%></p>

---
### ⑤ 반성
* **통제변수가 적절히 관리되었는가?**
  * 본 프로젝트를 본격적으로 시작하기 전 아래와 같이 가정한 바 있음
$${\color{gray}2014년\\,8월\\,북반구\\,/\\,남반구\\,사용자\\,그룹의\\,유일한\\,차이점은\\,휴가\\,시즌\\,여부이다.}$$
  * 그러나 이는 사실과 거리가 멀 가능성이 높으며, 다른 요인에서 차이가 날 수 있음
  * **즉, 통제 변수가 철저히 관리되었다고 판단하기는 어려움**
* **대체 지표 설정은 설득력이 있었는가?**
  * 업무 참여도를 바로 확인할 수 없어 이메일 퍼널 전환율을 대체 지표로 설정한 바 있음
  * 업무 참여도가 높은 사람은 조직 이슈를 파악하는 적극적일 것이라는 가정이 있었음
  * 휴가로 인해 잠시 참여도가 감소한 사람도 동일한 행동을 보일 수 있음 (업무 복귀를 위해)
  * **즉, 대체 지표의 설득력을 강화하기 위한 아이디어가 필요함**
* **충분한 EDA를 통해 예상 시나리오를 수립했는가?**
  * EDA를 통해 8월 시점 북반구/남반구의 이벤트 발생 증감이 서로 다르다는 것을 확인함
  * 이는 8월 휴가가 동월 WAU를 감소시켰다는 시나리오의 기초가 되었음
  * 그러나 북반구와 남반구를 단순 비교하기에는 남반구 국가의 표본 수*가 너무 적음

$${\color{gray}*\\,북반구\quad41개국\quad v.s\quad남반구\quad6개국}$$
  * **즉, WAU가 줄어든 시나리오를 수립하기 위해서는 EDA 과정이 더욱 충실했어야 함**

---
### ⑥ 직무에 적용할 점
* A/B 테스트 수행 시에는 통제변수가 그룹 간에 동일하게 유지되는지 점검하도록 하겠습니다.
* 분석에 필요한 데이터가 없어 다른 데이터로 대체할 때는 설득력을 보완할 수 있도록 하겠습니다.
* 본격적인 분석을 시작하기 전에 충분한 EDA를 수행하여 문제 및 상황 인식을 명확히 하겠습니다.
