# WAU-Drop-Analysis

## ① 문제인식

* Yammer는 기업용 협업도구 및 소셜 네트워크 서비스(SNS)임. 전세계 47개국에서 사용하고 있으며 2012년 Microsoft로 인수되어 현재는 [Viva Engage](https://www.microsoft.com/ko-kr/microsoft-viva/engage)라는 이름으로 운영되고 있음
* 유저 인게이지먼트의 흐름을 시각화한 결과, 2014년 8월 이후 WAU가 감소했음을 확인함

<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/d090b796-6149-4b52-8746-a691cab6da25' width = 80%></p>

---
* EDA를 수행한 결과, 다음과 같은 사실을 확인함 [[SQL·Python 코드 링크]](https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/blob/main/SQL_Python%20Code.md#%EB%AC%B8%EC%A0%9C-%EC%9D%B8%EC%8B%9D)
  * 북반구 사용자가 남반구에 비해 10배 가량 많음
  * 5~7월 평균과 8월을 비교했을 때, 북반구 국가의 70%에서 이벤트 발생 횟수가 감소함
  * 반면 남반구 국가의 67%에서 이벤트 발생 횟수가 증가함
<p align = 'center'><img src = 'https://github.com/TAEJIN-AHN/WAU-Drop-Analysis/assets/125945387/2097f43c-5fb2-4caa-90b7-a8e4f6e00379'></p>
---

* EDA 결과를 통해 아래와 같은 예상 시나리오를 수립할 수 있었음
