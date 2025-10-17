---
title: "[음성분석] 너의 소울을 들려줘: 랩 시뮬레이터"
order: 4
thumbnail: assets/img/thumbs/rap_simulator.png
tags: [ML, DL, Voice, Education, Transformer]
links:
  repo: https://github.com/YOUR/repo1
  report: /assets/docs/Rap_Simulator.pdf
---

# 랩 가수 지망생을 위한 음성 분석 및 유사 아티스트 추천 시스템
> 분석목적: 음성 데이터의 특징을 학습하여 랩 가수와 지망생 데이터 간 거리 기반 유사도 도출<br>
> 진행기간: 25.07.21 ~ 25.08.14 (4주)<br>
> 참여인원: 6인<br>
> 기술스택: Python(pandas, scikit-learn, pytorch, matplotlib)<br>
> 프로젝트 기여도: 80% (데이터 전처리, 모델링, 시각화)


## 주요 성과
- Siamese 구조를 통해 딥러닝 모델 구현
  * 계층별 KFold 결과: **평균 검증 정확도 0.83, 평균 AUC 0.90**
- 설명 가능한 추천 시스템
  * 멜스펙트로그램을 활용한 톤, 템포, 리듬, 에너지 등 아티스트 특징 시각화
  * L2 거리 및 다차원 축소(t-SNE) 통한 거리 벡터 시각화


### 담당 과업

<table>
  <tr>
    <th style="width: 5%; text-align:center;">구분</th>
    <th style="width: 95%;">내용</th>
  </tr>
  <tr>
    <td style="text-align:center;">전처리</td>
    <td>리샘플링(음질 정규화), 무음 제거, 길이 정규화, 유의미한 구간 추출,<br>데이터 증강 시도 (Pitch shift, Time stretch, GaussianNoise)</td>
  </tr>
  <tr>
    <td style="text-align:center;">모델링</td>
    <td>1️⃣ 사전 훈련된 트랜스포머 <b>HuBERT</b> 모델 임베딩<br>2️⃣ <b>Siamese Network</b> 기반 아티스트 유사도 계산 모델 구현</td>
  </tr>
  <tr>
    <td style="text-align:center;">시각화</td>
    <td><b>t-SNE</b>, <b>MFCC</b> 기반 샘플 분포 및 스펙트럼 분석</td>
  </tr>
</table>



---

## 상세 내용

이 프로젝트는 '가수 지망생들이 독학 위주 훈련에서 벗어나 데이터 기반 학습을 할 수 있다면 어떨까?' 라는 질문에서 시작되었습니다. 특히 '랩'이라는 장르는 멜로디에서 벗어나 아티스트의 목소리, 톤, 리듬, 발성, 플로우 등 **다양한 음성적 특징을 활용**할 수 있다는 장점이 있어 분석 대상으로 선정했습니다.

### 분석 데이터
- 훈련 데이터: 랩 가수 3인의 음원
- 테스트 데이터: 일반인의 랩 커버 음원

### 데이터 전처리
* Jay-Z, Eminem, Snoop Dogg 등 주요 래퍼 음성 클립 수집 후 MR 제거
  * 수동으로 수집한 음원에서 음원 길이, MR 제거 상태 등이 불균등했습니다. 이를 해결하기 위해 **리샘플링, 무음 제거, 30초 절삭** 전처리를 추가로 수행하여 실제 유의미한 음성 정보를 가진 정규화된 데이터를 얻을 수 있었습니다.
* Librosa를 이용해 비트 단위 구간 정규화 및 특징 벡터화(MFCC)
* 녹음 품질·환경 편차 완화를 위해 데이터 증강을 적용하였으나, 오히려 모델 정확도가 하락해 증강 없이 진행

![Artists](https://github.com/yeonglan/yeonglan.github.io/blob/main/assets/img/mel-spectrogram.png?raw=true)
*▲ 래퍼별 멜 스펙트로그램*

### 모델
HuBERT 모델을 임베딩해 음성 특성을 추출한 뒤, 선행 연구를 참고해 **샴 네트워크 + Contrastive Loss** 모델을 구축했습니다.
* positive와 negative로 쌍간 비교를 하여, 분류보다 유사성 판정에 중점을 두었습니다.
   * 동일 래퍼(positive): 임베딩 거리 축소
   * 상이 래퍼(negative): 임베딩 거리 확장
   * 하이퍼파라미터 튜닝: 임베딩된 샘플 간 거리(margin)를 0.1로 설정했을 때 최상의 성능
   * 지망생 음성 데이터 입력 → 임베딩 추출 → 코사인 거리 계산 → Top-k 래퍼 추천
<br>그후 softmax 및 cosine 거리를 계산하여 새로 입력된 데이터가 학습된 아티스트와 얼마나 유사한지 알아보고자 했습니다.

![tSNE_Embedding](https://github.com/yeonglan/yeonglan.github.io/blob/main/assets/img/t-SNE_projected_embeddings.png?raw=true)
*▲ 래퍼별 임베딩 군집 시각화 (t-SNE)*

### 분석 결과
샴 네트워크는 평균 검증 정확도 0.83, 평균 AUC 0.90라는 높은 정확도를 보였습니다.<br>

**Cross-Validation Summary**
| Mean AUC           |   0.895 ± 0.030   |
| Mean Accuracy      |   0.833 ± 0.027   |

![Model Results](https://github.com/yeonglan/yeonglan.github.io/blob/main/assets/img/thresholds.png?raw=true)
*▲ Stratified KFold 결과 예시*

하지만 산출된 아티스트별 유사도가 비슷하다는 문제점이 있었습니다.
이를 해결하기 위해 **AudioClassification** 모델을 도입함으로써 각 랩가수의 고유한 음성 임베딩 공간을 구축하였고, 보다 명확한 아티스트 추천 알고리즘이 완성되었습니다.

### 


### 모델 성능 요약

**평가 기준**
> 정성적 기준: **모델이 ‘누구의 목소리인가’보다 ‘누구와 닮았는가’를 이해하는가?**
> 정량적 기준: 실루엣 계수, 테스트 정확도, 유사도 기반 검색 정확도

---

| 지표                           | 설명                     | 수치        | 해석                                         |
| ---------------------------- | ---------------------- | --------- | ------------------------------------------ |
| **Silhouette (Cosine)**      | 임베딩 공간 내 래퍼 간 분리도      | **0.586** | 각 래퍼의 스타일이 군집 단위로 뚜렷하게 구분됨                 |
| **softmax 기반 Test Accuracy** | Softmax 분류 헤드 기준 테스트 정확도 | **0.778** | 클래스를 직접 분류하는 방식으로 평가했을 때, 전체 정확도 77.8% 수준 — 모델이 전반적으로 래퍼별 특징을 잘 구분함
| **cosine 기반 Test Accuracy** | Cosine 거리 기반 유사도 판별 정확도 | **0.880** | 임베딩 간 거리로 판단했을 때, 동일 래퍼를 88% 정확도로 구분 — **거리 학습 구조(Siamese)**가 softmax 분류보다 더 정교한 구분력을 보임
| **Mean Precision@5 (Train)** | 학습 데이터에서 유사도 기반 검색 정확도 | **0.815** | Top-5 내 81.5% 확률로 동일 래퍼가 포함됨 — 임베딩 구조가 안정적 |
| **Mean Precision@5 (Test)**  | 테스트 데이터에서 추천 정확도       | **0.467** | 새로운 음성에서는 다소 낮은 정확도 — 현실 환경 적응 여지 있음       |

---

```
Silhouette (0.586)     ●●●●○  
Precision@5 (Train)    ●●●●●  
Precision@5 (Test)     ●●●○○  
```

> **학습 데이터에서는 완성도 높고,
> 실제 테스트 환경에서는 절반 정도의 안정성을 확보한 상태.**
>
> 이는 테스트 데이터와 학습 데이터가 '일반인'과 '아티스트'로 서로 다르다는 점에 비추어볼 때, 타당한 결과라고 판단했습니다. 모델이 타당한 견고함을 갖추면서도 새로운 데이터에 대해서 유연하게 판단할 수 있기 때문입니다.

* **Silhouette 0.586** → 래퍼별 음성 스타일이 뚜렷이 분리되어,
  모델이 “누가 누구와 닮았는가”를 구별할 수 있음.<br>
  특히 분류 정확도를 보여주는 Softmax Accuracy보다 거리 기반 Cosine Accuracy가 더욱 높은 성능으로 나타나,
  분류 성능을 기반으로 유사 아티스트를 구별하는 능력을 입증.
* **Train Precision@5 0.815** → 임베딩 유사도 기반 검색이 매우 정확.
  내부 일관성이 높고, 학습된 스타일 구조가 안정적으로 형성됨.
* **Test Precision@5 0.467** → 테스트에서는 유사도 검색 정확도가 약 46.7%.
  일반적으로 이는 일반화 한계를 의미하지만,
  해당 프로젝트에서는 지망생에 대하여 일정 수준의 유효한 추천이 가능함을 보여줌.

따라서 거리 기반 표현 학습을 성공적으로 수행해 냈습니다.

![silhouette](https://github.com/yeonglan/yeonglan.github.io/blob/main/assets/img/silhouette_by_labels.png?raw=true)
*▲ 래퍼별 실루엣 계수*


   * 유사도 매트릭스 기반 Heatmap으로 스타일 관계를 한눈에 표현.

---

### 느낀 점

이 프로젝트를 통해 단순한 분류 정확도보다 **‘해석 가능한 감각적 이해’**가 얼마나 중요한지를 깨달았습니다.
샴 네트워크는 분류 정확도는 높았지만, 서비스 구현 측면에서 아티스트 간 차별성은 뚜렷하게 보여주지 못했습니다.
이에 HuBERT 임베딩 기반 AudioClassification 모델을 빌드하여 아티스트 유사성이 더욱 잘 보이도록 했습니다.

프로젝트 시간 제한과 인력 부족으로 음성 데이터 수집에는 세 명의 랩 가수만을 선정해 음원을 추출했습니다.
이들은 흑인과 백인 남성 랩 가수로, 차후에는 아시안 및 여성의 음성 데이터를 포함해 분석하면 모델 개선 여지가 있을 거라 생각합니다.

---

## 기술스택
Python(pandas, scikit-learn, pytorch)


## 링크
- 코드: [GitHub]({{ page.links.repo }})
- 보고서: [PDF]({{ page.links.report }})
