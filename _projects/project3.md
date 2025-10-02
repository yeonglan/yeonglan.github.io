---
title: "청년층 미충족 의료율 영향 요인 분석"
order: 3
thumbnail: assets/img/thumbs/shap_summary.png
tags: [Health, Policy, ML]
links:
  repo: https://github.com/YOUR/repo1
  report: /assets/docs/umnd-report.pdf
---

## 주요 성과
- 2025 강서구 데이터분석 아이디어 공모전 참가
- 정책 타깃 및 아이디어 제안


## 개요
데이터 설계, 모델링, 시각화 전반에 걸친 파이프라인을 만들고 데이터분석을 수행하였습니다.

1. 서울시 자치구별 시민 건강행태를 K-Means 알고리즘으로 분류
2. RFRegressor와 SHAP 활용하여 서울시 청년층 미충족의료율 영향 요인 파악
* 미충족의료율: (footer)


## 상세 내용
공모전에 참가하기 위해 주제를 고민하던 중, K-Means 알고리즘으로 서울시 시민 건강행태를 군집화했습니다.

그 결과, 강서구를 비롯한 클러스터 0에서 다른 클러스터들과 구분되는 독특한 현상을 관찰할 수 있었습니다.

1. 높은 의료비를 지출함에도 불구하고, 연간 미충족의료율은 높음
2. 실제 건강 지표는 양호하나 주관적으로 느끼는 건강 수준은 낮음
3. 우울감 경험률, 스트레스 인지율 등 부정적인 감정 상태 지표가 높음
4. 인구 구조적으로는 1인가구와 청년 가구 비율이 높음

추가적인 자료 조사 결과, 이 현상은 ~ 등의 연구 결과에 어느 정도 부합했습니다.
하지만 기존 연구가 대체로 노년층을 조사하였기에, 저는 청년층을 타겟으로 하는 추가 분석을 계획했습니다.

이를 위해 분석 목적에 맞는 데이터를 새로 수집하였습니다.
다만, 공공데이터를 구하는 데 한계가 있어 레코드 개수보다 변수의 개수가 더 많아지는 일이 있었습니다.
따라서 핵심 변수만을 추려 보다 체계적인 분석을 수행하고자 했습니다.

ElasticNet으로 선별한 변수 목록.

이후 랜덤포레스트 회귀분석과 SHAP을 기반으로 청년층의 미충족 의료율에 영향을 미치는 요인 상위 5개를 도출했습니다.


## 느낀 점
데이터가 문제를 말해준다. 해당 프로젝트를 수행하며 가장 먼저 느낀 점입니다.
만일 시민의 건강행태에 있어 운동에 관련된 데이터만 사용했더라면 미충족 의료율과 의료비 등 보건행동에 따른 자치구별 차별점을 인식하지 못했을 것입니다.
찾고자 하는 문제와 관련된 데이터를 다양하게 수집한 덕분에 좀더 포괄적인 관점에서 문제를 진단할 수 있었습니다.
또한 강서구 등 특정 자치구에 집중된 독특한 현상을 확인하고 영향 요인을 분석하였는데, 분석 결과가 선행연구를 뒷받침하는 점이 신기했습니다.
저의 분석에서 도출한 고무적인 포인트는 바로 간호사의 수였습니다. Choi, Kim, Cho & Jeong(2025) 문헌에서 노동력 부족을 언급했지만, 이는 수도권과 비수도권의 격차를 비교한 것이지 수도권 내를 연구한 것은 아니었습니다. 하지만 분석 결과, 수도권 내에서도 간호 인력 부족이 의료 공백으로 이어질 수 있다는 시사점이 드러났습니다.
비록 데이터 부족으로 인해 완전히 신뢰하기는 어려운 결과지만...~




## 참고문헌
Changes in Nursing Grades and Nurse Staffing Levels following the 2024 Revision of Nursing Management Fee Standards: A Focus on Tertiary Hospitals and Medical Institutions in Seoul
Hyeyoung Choi, Kiyoung Kim, Su-Jin Cho, Suyong Jeong
Health Insurance Review & Assessment Service Research.2025; 5(1): 58.  


## 기술스택
Python(pandas, scikit-learn, SHAP), QGIS


## 링크
- 코드: [GitHub]({{ page.links.repo }})
- 보고서: [PDF]({{ page.links.report }})
