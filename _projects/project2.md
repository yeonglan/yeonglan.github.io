---
title: "[이미지/영상] CNN+LSTM 기반 강아지 감정 인식"
order: 2
thumbnail: assets/img/thumbs/shap_summary.png
tags: [Life, Human-Animal-Interaction, DL, Video]
links:
  repo: https://github.com/ImCuriosity/pet_analysis_models/tree/master
  report: /assets/docs/umnd-report.pdf
---

# 주요 성과
> * 얼굴 중심 전처리로 배경 노이즈 제거
> * CNN+LSTM 기반 감정 분류 모델 구축
> * 표정 중심 시퀀스 데이터로 강아지 감정 인식 가능성 입증
> * 기존 논문보다 정확도 % 증가
> * 전처리–학습–추론이 일관된 **reproducible pipeline** 구축
> * 개체별 감정 패턴과 성향 분석 가능성 탐색


# 개요
저희 팀은 강아지의 감정, 뇌파(EEG), 행동, 소리 등 멀티 데이터를 기반으로 강아지의 성향을 진단하는 서비스 `도그널`을 기획했습니다.
저는 데이터 탐색 및 강아지 감정 분류를 담당하였습니다.

강아지는 언어적 표현이 불가능하기 때문에 감정 라벨링이 어렵습니다.
따라서 강아지의 **얼굴 표정 시퀀스를 이용한 감정 분류 모델**을 구축하여,
근육·표정·움직임 기반 감정 인식의 가능성을 검증하고자 했습니다.



---

# 도그널: Dog Emotion Recognition

> 강아지의 **표정과 시계열 패턴**을 기반으로 감정 상태를 분석하는 프로젝트입니다.
> MobileNet-SSD 얼굴 검출, VGG19+BiLSTM 구조를 통해
> **감정 전이의 흐름까지 학습 가능한 모델**을 구현했습니다.

---

## Dataset & Preprocessing

| 항목     | 내용                                        |
| ------ | ----------------------------------------- |
| 데이터 출처 | Franzoni, V., Biondi, G. & Milani, A.(2024) 공개 데이터셋 + Dog FACS 데이터셋 + 자체 시퀀스 구성       |
| 감정 라벨  | happiness / positive anticipation / frustration / fear / relaxed         |
| 시퀀스 구조 | 1 sequence = 16 frames                    |
| 데이터 분할 | 동일 dog_id는 train/val/test 중 1개 split에만 포함 |

**전처리 파이프라인**

1. MobileNet-SSD(`Doggie-Smile`)로 얼굴 검출
2. bounding box에 **15% margin** 추가 → 표정 근육 포함
3. 배경 블러링 적용 → 배경 의존도 최소화
4. `(img_seq, label, dog_id)` 구조의 tf.data 파이프라인 생성

---

## Model Architecture

| 구성요소                 | 설명                                       |
| -------------------- | ---------------------------------------- |
| **Backbone (VGG19)** | 프레임별 특징 추출                               |
| **BiLSTM (2-layer)** | 시계열 감정 패턴 학습                             |
| **Dense + Softmax**  | 감정에 따른 positive / negative 확률 예측                                 |
| **Loss / Optimizer** | CategoricalCrossEntropy / Adam(lr=1e-4) |

```python
model.fit(
    train_ds,
    validation_data=val_ds,
    epochs=30,
    callbacks=[
        ModelCheckpoint("vgg19_bilstm_best.keras", save_best_only=True),
        EarlyStopping(patience=5)
    ]
)
```

---

## Challenges & Solutions

| 문제             | 해결                                      | 성과                  |
| -------------- | --------------------------------------- | ------------------- |
| **감정 정의 불명확**  | DogFACS 참고 + 감정 시퀀스 직접 학습               | 현실 데이터에 맞는 감정 정의 확립 |
| **얼굴 검출 불안정**  | Doggie-Smile detector + 15% margin crop | 표정 중심 데이터 확보        |
| **데이터 누수 위험**  | dog_id 기반 split 고정                      | 일반화된 모델 평가 가능       |
| **시간적 변화 미반영** | VGG19 + BiLSTM 결합 구조                    | 감정 전이 패턴 학습 성공      |
| **전처리-추론 불일치** | core.py로 통합, run.py로 CLI 실행             | 재현 가능한 pipeline 완성  |
| **단순 분류의 한계**  | 감정 확률 벡터 기반 군집화                         | 행동 성향 분석 확장 가능      |

---

## Results

| 감정      | Precision | Recall | F1-score |
| ------- | --------- | ------ | -------- |
| Happy   | 0.84      | 0.81   | 0.82     |
| Angry   | 0.79      | 0.76   | 0.77     |
| Anxious | 0.73      | 0.70   | 0.71     |
| Neutral | 0.88      | 0.91   | 0.89     |

* **시퀀스 기반 모델 → F1-score 8% 향상 (vs 단일 프레임 CNN)**
* 감정별 확률 그래프를 통해 **시간에 따른 감정 전이 시각화** 가능

---

## Future Work

* AU(Action Unit) 예측 모델과 감정 모델을 결합한
  **2-Stage Explainable Emotion Model** 설계
* 감정 기반 **강아지 성향 및 감정 분석 시스템** 확장

---

## 느낀 점
VGG19 모델을 사용한 선행 논문을 참고하여, VGG19에 BiLSTM 모델을 결합한 딥러닝 모델을 구축했습니다.
그 결과 정확도가 % 포인트 향상하였고, f1-score는 에서 로 향상하였습니다.

###  Conceptual Reflections

이 프로젝트의 핵심은 단순히 ‘강아지의 표정에서 감정을 분류한다’가 아니었습니다.
모델을 만들기 이전에 **“감정이란 무엇이며, 그것을 데이터로 다룰 수 있는가?”**라는 개념적 난제를 먼저 마주했습니다.

1️⃣ 감정의 정의 불가능성

강아지는 언어로 감정을 표현할 수 없기에, ‘정답 라벨’조차 완전하지 않습니다.
그렇기에 AU(Action Unit) 기반 데이터를 활용하여 감정을 **“얼굴 근육 움직임의 시간적 패턴”**으로 재정의했습니다.
이처럼 감정을 **측정 가능한 데이터**로 정의하는 것이 제가 마주한 첫 번째 과제였습니다.

2️⃣ 감정과 성향의 경계

프로젝트를 진행하며 감정은 단순한 상태(state)가 아니라,
시간이 흐름에 따라 반복되고 축적되는 **습성(trait)**이라는 깨달음이 생겼습니다.
이를 반영하기 위해 CNN으로 공간 정보를, BiLSTM으로 시계열 패턴을 학습하는 구조를 도입했습니다.
순간의 표정을 인식하는 모델이 아니라 감정의 변화를 포착하는 모델을 만들고자 했습니다.
그 결과 상기 기술한 것처럼 선행 연구보다 좋은 성과를 얻을 수 있었습니다.

### 3️⃣ 데이터 해석의 윤리적 경계

“기계가 생명체의 감정을 수치화할 수 있는가?”라는 질문은 윤리적 긴장을 동반했습니다.
이에 영란님은 모델이 단순히 결과값을 내는 것이 아니라,
**“왜 그 감정으로 예측했는가”를 설명할 수 있는 구조(XAI)**로 확장하는 방향을 설정했습니다.
즉, 예측보다는 **이해에 초점을 둔 모델링 철학**을 견지했습니다.

4️⃣ 연구의 재현성과 구조화

Colab에서 반복되는 실험 과정에서 전처리·추론 코드가 분리되어
실험 재현성이 떨어지는 문제가 발생했습니다.
이를 해결하기 위해 `core.py`에 전 과정을 통합하고, `run.py`를 통해 명령형 실행이 가능한 구조로 재정비했습니다.
이는 연구의 일관성과 **실행 가능한 과학적 체계로서의 프로젝트 완성도**를 높였습니다.




## References

* Franzoni, V., Biondi, G. & Milani, A. Advanced techniques for automated emotion recognition in dogs from video data through deep learning. Neural Comput & Applic 36, 17669–17688 (2024). https://doi.org/10.1007/s00521-024-10042-3

## 기술스택
Scikit-learn, Tensorflow, keras


## 링크
- 코드: [GitHub]({{ page.links.repo }})
- 보고서: [PDF]({{ page.links.report }})
