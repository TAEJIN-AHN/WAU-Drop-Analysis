#  문제 인식

## Yammer 북반구/남반구 사용자 수 비율 비교

```postgresql
-- 사용 환경 특성상 VIEW를 생성할 수 있는 권한이 없어 대신 CTE를 사용했습니다.
-- GROUP BY - 각 유저별 사용 국가정보 추출 (최빈값 기준)
WITH user_location(user_id, location) AS (
  SELECT
    user_id,
    MODE() within group (
      ORDER BY
        location
    ) AS location
  FROM
    tutorial.yammer_events
  GROUP BY
    user_id
),

-- CASE문 - 각 유저별 북반구/남반구 국가정보 추출
user_hemisphere(user_id, hemisphere) AS (
  SELECT
    user_id,
    CASE
      WHEN latitude > 0 THEN 'northern'
      ELSE 'southern'
    END AS hemisphere
  FROM
    user_location
    INNER JOIN nuno3332.area_info USING(location)
)

-- GROUP BY - 북반구/남반구 그룹의 유저 수 집계
SELECT
  hemisphere,
  count(user_id) AS number_of_users
FROM
  user_hemisphere
GROUP BY
  hemisphere
```

```python
# 필요한 라이브러리 불러오기
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib as mat
import seaborn as sns

# Matplotlib/Seaborn 한글 및 마이너스 기호 사용 설정
plt.rc('font', family='Malgun Gothic')
mat.rcParams['axes.unicode_minus'] = False

# 데이터 읽기
data = pd.read_csv('./hemisphere_group.csv')

# northern/southern을 북반구/남반구로 변경
data.replace({'hemisphere' : {'northern' : '북반구', 'southern' : '남반구'}}, inplace = True)

# 컬럼명을 영어에서 한글로 변경
data.rename({'hemisphere' : '북반구/남반구', 'number_of_users' : '사용자 수'}, axis = 1, inplace = True)

# Pie Plot 생성
explode = [0.1, 0.1]
colors = ['red', 'blue']
wedgeprops = {'width': 0.7, 'edgecolor': 'w', 'linewidth': 2}
_, _, autotexts = plt.pie(data['사용자 수'], 
                          labels = data['북반구/남반구'], 
                          autopct = '%.1f%%', 
                          explode = explode, 
                          shadow = True,
                          colors = colors,
                          wedgeprops = wedgeprops)

# Pie plot 설명 추가
plt.title('Yammer 북반구/남반구 사용자 수 비율 비교', weight = 'bold')
plt.annotate('(8,925명)', (-0.87, 0.03), color = 'white', weight = 'bold')
plt.annotate('(835명)', (0.7, -0.33), color = 'white', weight = 'bold')
for ins in autotexts:
    ins.set_color('white')
    ins.set_weight('bold')

# Pie plot 최종 출력
plt.show()
```

## 국가별 평균 이벤트 수 대비 8월 이벤트 수 변화율

```postgresql
-- INNER JOIN - 직접 모은 수도별 위도 정보와 이벤트 발생내역 결합
WITH plus_areainfo(
  user_id,
  occurred_at,
  event_name,
  location,
  latitude
) AS (
  SELECT
    user_id,
    occurred_at,
    event_name,
    location,
    latitude
  FROM
    tutorial.yammer_events
    INNER JOIN nuno3332.area_info USING (location)
),

-- GROUP BY -국가별/월별 이벤트 발생 횟수를 집계 (Long-Form)
event_agg_locmonth_long(location, latitude, MONTH, number_of_events) AS (
  SELECT
    location,
    latitude,
    date_part('month', occurred_at :: timestamptz) AS MONTH,
    count(*) AS number_of_events
  FROM
    plus_areainfo
  GROUP BY
    location,
    latitude,
    date_part('month', occurred_at :: timestamptz)
),

-- CASE문 - 국가별/월별 이벤트 발생 횟수를 집계 (Wide-Form)
event_agg_locmonth_wide(location, latitude, may, june, july, august) AS (
  SELECT
    location,
    latitude,
    sum(
      CASE
        WHEN MONTH = 5 THEN number_of_events
        ELSE 0
      END
    ) AS may,
    sum(
      CASE
        WHEN MONTH = 6 THEN number_of_events
        ELSE 0
      END
    ) AS june,
    sum(
      CASE
        WHEN MONTH = 7 THEN number_of_events
        ELSE 0
      END
    ) AS july,
    sum(
      CASE
        WHEN MONTH = 8 THEN number_of_events
        ELSE 0
      END
    ) AS august
  FROM
    event_agg_locmonth_long
  GROUP BY
    location,
    latitude
),

-- 국가별 5월 - 7월 이벤트 발생 횟수를 평균함
avg_may_to_july(location, latitude, avg_may_to_july, august) AS (
  SELECT
    location,
    latitude,
    (may + june + july) / 3 AS avg_may_to_july,
    august
  FROM
    event_agg_locmonth_wide
)

-- 5-7월 이벤트 발생 횟수와 8월 이벤트 발생 횟수를 비교하여 국가별 변화율 계산
SELECT
  location,
  latitude,
  (august - avg_may_to_july) / avg_may_to_july * 100 AS change_rate
FROM
  avg_may_to_july
```

```python
# 데이터 읽기
data = pd.read_csv('./location_engagement_change_rate.csv')

# 범례가 북반구 - 남반구 순서로 나오도록 정렬
data.sort_values(by = 'hemisphere', ascending = False, inplace = True)

# Scatter Plot 생성
sns.scatterplot(data = data, x = 'change_rate', y = 'latitude', hue = 'hemisphere', palette = ['red', 'blue'])

# Plot 설명정보 추가
plt.xlabel('이벤트 수 변화율 (5월-7월 평균 → 8월)')
plt.ylabel('국가별 위도 (수도 기준)')
plt.title('국가별 평균 이벤트 수 대비 8월 이벤트 수 변화율')
plt.legend(bbox_to_anchor = (1, 1), title = '북반구/남반구')

# 북반구/남반구, 이벤트 증가/감소를 구분하기 위한 보조선 추가
ax = plt.subplot()
ax.axvline(0, c = 'b', ls = '--')
ax.axhline(0, c = 'b', ls = '--')

# Scatter Plot 최종 출력
plt.show()
```

# 데이터 분석

## 그룹별 이메일 퍼널 차트

```postgresql
-- GROUP BY - 각 유저별 사용 국가정보 추출 (최빈값 기준)
WITH user_location (user_id, location) AS (
  SELECT
    user_id,
    MODE() within group (
      ORDER BY
        location
    ) AS location
  FROM
    tutorial.yammer_events
  GROUP BY
    user_id
),

-- CASE문 - 각 유저별 북반구/남반구 국가정보 추출
user_hemisphere(user_id, hemisphere) AS (
  SELECT
    user_id,
    CASE
      WHEN latitude > 0 THEN 'northern'
      ELSE 'southern'
    END AS hemisphere
  FROM
    user_location
    INNER JOIN nuno3332.area_info USING(location)
),

-- DATE_PART 함수 사용 - 8월 유저별 이메일 퍼널 단계 진입여부 확인
august_email_action (user_id, ACTION) AS (
  SELECT
    DISTINCT user_id,
    ACTION
  FROM
    tutorial.yammer_emails
  WHERE
    date_part('month', occurred_at :: timestamptz) = 8
),

-- CASE문, GROUP BY - 8월 유저별 이메일 퍼널 단계 진입여부 Wide Form으로 전환
email_action_agg (user_id, receive_email, open_email, click_link) AS (
  SELECT
    user_id,
    sum(
      CASE
        WHEN (ACTION = 'sent_weekly_digest')
        OR (ACTION = 'sent_reengagement_email') THEN 1
        ELSE 0
      END
    ) AS receive_email,
    sum(
      CASE
        WHEN ACTION = 'email_open' THEN 1
        ELSE 0
      END
    ) AS open_email,
    sum(
      CASE
        WHEN ACTION = 'email_clickthrough' THEN 1
        ELSE 0
      END
    ) AS click_link
  FROM
    august_email_action
  GROUP BY
    user_id
)

-- GROUP BY 사용 - 북반구/남반구 그룹별 이메일 퍼널 진입 유저 집게
SELECT
  hemisphere,
  sum(receive_email) AS receive_email,
  sum(open_email) AS open_email,
  sum(click_link) AS click_link
FROM
  user_hemisphere
  INNER JOIN email_action_agg USING (user_id)
GROUP BY
  hemisphere
```

## 정규분포 그래프

```python
# 필요한 라이브러리 불러오기
from scipy.stats import norm
import numpy as np

# 그래프 사이즈 조정
plt.figure(figsize = (15, 5))
plt.rc('xtick', labelsize = 15)
plt.rc('ytick', labelsize = 15)

# 정규분포 그리기
x = np.linspace(0.4, 0.65, 500)
plt.plot(x, norm.pdf(x, loc = 0.4884, scale = 0.008626), color = 'red')
plt.plot(x, norm.pdf(x, loc = 0.5190, scale = 0.028107), color = 'blue')
plt.fill_between(x, norm.pdf(x, loc = 0.5190, scale = 0.028107), color = 'yellow', where = (x > 0.502), interpolate = True)

# 보조정보 추가하기
plt.legend(bbox_to_anchor = (1, 1), labels = ['북반구 그룹', '남반구 그룹'])
plt.title('북반구/남반구 전환율 예상 분포', fontdict = {'fontsize' : 20})
plt.ylim([0, 50])
plt.annotate('95%', (0.504, 30), rotation = 90, fontsize = 15)
ax = plt.subplot()
ax.axvline(0.4884, c = 'red', ls = '--')
ax.axvline(0.5190, c = 'blue', ls = '--')
ax.axvline(0.502, c = 'gray', ls = '--')

# 정규분포 그래프 최종 출력
plt.show()
```