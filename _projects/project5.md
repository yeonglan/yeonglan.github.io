---
title: "관악구 상담버스 최적 입지 분석"
date: 2025-10-22
layout: project
permalink: /projects/consulting-bus/
cover: /assets/img/consulting-bus/cover.png
---

## 관악구 상담버스 최적 입지 분석
**Folium 기반 인터랙티브 지도**  
세 가지 가중치 시나리오(거리중시 / 인구중시 / 인프라공백중시)에 따라  
상담버스 설치 우선순위를 시각적으로 비교할 수 있습니다.

<div class="tab-wrap">
  <input type="radio" id="tab1" name="tab" checked>
  <label for="tab1">거리중시</label>
  <input type="radio" id="tab2" name="tab">
  <label for="tab2">인구중시</label>
  <input type="radio" id="tab3" name="tab">
  <label for="tab3">인프라공백중시</label>

  <div class="tab-content" id="content1">
    <iframe src="{{ '/assets/maps/consulting_bus_map_distance.html' | relative_url }}" loading="lazy"></iframe>
  </div>
  <div class="tab-content" id="content2">
    <iframe src="{{ '/assets/maps/consulting_bus_map_population.html' | relative_url }}" loading="lazy"></iframe>
  </div>
  <div class="tab-content" id="content3">
    <iframe src="{{ '/assets/maps/consulting_bus_map_infra.html' | relative_url }}" loading="lazy"></iframe>
  </div>
</div>

---

### 📍 해석 요약

| 시나리오 | 설명 | 주요 변수에 부여한 가중치(예시) |
|-----------|------|--------------------------------|
| 거리중시 | 의료 접근성 중심 | 보건소거리 0.35 / 의료진수_1인당 0.20 / 기타 0.45 |
| 인구중시 | 인구 규모 및 활동량 중심 | 유동인구 0.30 / 총인구수 0.25 / 상권밀도 0.15 / 기타 0.30 |
| 인프라공백중시 | 인프라 부족 지역 탐색 | 의료진수_1인당 0.25 / 상담센터수 0.25 / 노인_상권거리 0.15 / 기타 0.35 |

---

### 🧠 분석 프로세스
- K-means(3군집)으로 지역 특성 분류  
- MCDM(가중합 점수)로 시나리오별 우선순위 계산  
- Cluster 2는 **황색 점**, Cluster 0/1은 파랑·초록으로 표현  
- HTML 지도는 Folium으로 생성 → `_assets/maps/`에 저장

---

### 📎 파일 링크
- [거리중시 지도 바로 보기](../assets/maps/consulting_bus_map_distance.html)
- [인구중시 지도 바로 보기](../assets/maps/consulting_bus_map_population.html)
- [인프라공백중시 지도 바로 보기](../assets/maps/consulting_bus_map_infra.html)
