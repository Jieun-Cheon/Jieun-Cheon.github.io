---
title: "[통계] 빈도주의자 통계"
excerpt: "의사결정나무에서 사용되는 가장 기본적인 이론"

categories:
  - Statistics
tags:
  - [statistics]

permalink: /categories/statistics/

toc: true
toc_sticky: true

use_math: true
 
date: 2022-06-24
last_modified_at: 2022-06-24
---

# 빈도주의(frequentist) 통계

Estimand, estimator, estimate, 신뢰구간, bootstrap, 표본분포, 표본오차

<br>
<br>

# 빈도주의 확률모형
![frequentist-1](/assets/images/posts_img/frequentist/frequentist-1.png)

불확실성을 계량적으로 표현하는 전략 → 확률모형

불확실성이 생기는 원인은 절대적 진리인 데이터가 없어서 생김(전수조사 하는 시간, 비용, qhrwkqeh 등등..)

따라서, 변하지 않는 절대적 진리를 추정량/추정치를 통하여 구하는 것

- 빈도주의자의 **확률**: 여러번 **반복**했을 때, **발생하는 빈도의 비중**
- 절대적 진리: 불확실성이 없음! 왜냐면 전체 조사를 통해서 확인하기 때문에
                   → 전체 표본을 조사하여 얻게되는 것
- 추정량/추정치(estimator, $ \hat{p} $): 관찰된 정보를 이용하여 관심변수를 추정하는 전략/함수

<br>
<br>


# 표본분포

$ p $**(관측 불가능한 미지의 숫자)**, $ \hat{p} $(관측 가능한 분포의 표본)

추정량의 분포: 여러번 반복해서 측정함.
                       멀티버스 안의 추정치가 다양하게 많음
                       이 값을 모은 것이 **표본분포(sampling distribution)** 

```python
# q=관심변수(확률), n=한 세계 내의 관찰 횟수, b=멀티버스 개수
def plot_histogram(q, n, b):
  sns.histplot(np.random.binomial(1, q, size=(b,n)).mean(axis=1))
```

```python
plot_histogram(0.2, 10, 100)
```

```python
plot_histogram(0.2, 10, 10000)
```

![frequentist-2](/assets/images/posts_img/frequentist/frequentist-2.png)

![frequentist-3](/assets/images/posts_img/frequentist/frequentist-3.png)

멀티버스 개수가 늘면 확률값은 넓게 퍼지고, 확률값에 수렴 → 원래 성질에 수렴하는 것, 더 나아지는거 x

```python
plot_histogram(0.2, 100, 100)
```

```python
plot_histogram(0.2, 100, 10000)
```

![frequentist-4](/assets/images/posts_img/frequentist/frequentist-4.png)
![frequentist-5](/assets/images/posts_img/frequentist/frequentist-5.png)

한 번에 관찰하는 횟수가 많아질수록 추정치 근처로 촘촘하게 모임 → 한번에 관찰하는 횟수(표본의 크기, n) 영향 

- 표본분포의 불확실성(분산)은 표본의 크기(n)에 영향을 받음
- 표준오차(standard error) = 표본분포의 표준편차

<br>
<br>

# 신뢰구간

$ a \leq \hat{p}-p\leq b $

(95%) 신뢰구간의 뜻: (95%)의 확신, 실제값이 이론적으로 표본분포의 95% 확률 범위에 있음

100% 신뢰구간: 0~1 사이 😬

- 표본분포가 정규분포를 따른다 → a=-1.95s, b=+1.95s
- 표본분포에 대한 특별한 가정이 없이, 표본분포 자체를 추정할 수 있다면?

     - 모든 ‘평균’값은 관측 수가 증가할 수록 정규분포로 수렴, 그러나 데이터 가정, 추정량이 복잡해지면 어려움

     - **컴퓨터 시뮬레이션(bootstrap)**: 광범위한 적용, 전통통계에 비해 느림, 실제 관측 데이터에 영향 많이 받음 

<br>
<br>


# 부트스트랩(bootstrap)

- 거짓 표본분포 만들기

$ p -------------X -------------\hat{p} $

$ X $(데이터)가 대표성을 갖는다고 가정

데이터셋을 shuffle하여 resample 진행 → $\hat{p}(n)$, bootstrap sample을 만듦

```python
obs = np.array([1]*6 + [0]*4)

def bootstrap(obs, n_universes):
  return np.random.choice(obs, size=(n_universes, len(obs)), replace=True).mean(axis=1)
```

```python
#p_hats: 임의로 만든 표본분포 그룹
p_hats = bootstrap(obs, 1000)
sns.histplot(p_hats, bins = 10)
```

![frequentist-6](/assets/images/posts_img/frequentist/frequentist-6.png)

```python
p_hat = obs.mean() #p_hat = 0.6
s = p_hats.std() #s = 0.1563163139278815

#95% 신뢰구간 구하는 방법
a = (1 - 0.95) / 2
z = norm.ppf([a, 1-a]) #array([-1.95996398,  1.95996398])
p_hat + (z * s) #array([0.29362565, 0.90637435])

#정규분포가 아닌 경우, 백분위 계산 후, 표준화
percentile = np.percentile(p_hats, (2.5, 97.5)) - p_hat
p_hat - percentile #array([0.9, 0.3])
```

데이터의 제한성 

- 데이터의 완전성(유용한 정보의 부재): 다방면으로 고려가 필요함
- 확률모형의 현실성: 표본이 정말 “독립시행"인지, 빈도주의자의 “확률"이 적용되는 상황

<br>
<br>

# 좋은 의사결정

## 의사결정의 6요소

- 가용한 모든 선택지, 정보, 가치를 고려했는가
- 의사결정의 틀이 명확한가
- 의사결정자가 분명한가
- 의사결정의 논리가 바른가
