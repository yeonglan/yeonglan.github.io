---
title: "Toeic Speaking 학습을 위한 LLM 챗봇 서비스 구축"
order: 1
thumbnail: assets/img/thumbs/shap_summary.png
tags: [Education, LLM, Customer-Service, Prompt-Engineering]
links:
  repo: https://github.com/yeonglan/sPicking
  report: /assets/docs/umnd-report.pdf
---

## 주요 성과
- 자체 개발한 챗봇 서비스화
링크.


## 개요
토익 스피킹 독학자를 위한 파트별 맞춤형 챗봇 서비스 'sPicking'을 개발했습니다.
sPicking은 영어로 말하다는 뜻을 지닌 단어 **speak**와 원하는 것을 고른다는 의미인 **pick**을 재치있게 합친 이름입니다.

- OpenAI 기반 챗봇 서비스 구축
- LangChain 활용: LLM API 교체 시 신속한 패치 가능하도록 LangChain으로 파이프라인 구성


## 상세 내용
"문제 유형에 맞춘 챗봇 튜터가 있다면 학습이 훨씬 수월하지 않을까?"

토익 스피킹을 공부하면서 이러한 생각이 들어 서비스 개발에 착수했습니다.
초기에는 챗GPT의 LLM인 OpenAI 모델을 주축으로 삼았지만, 사용자 편의성을 고려해 다양한 API를 포용하는 LangChain을 채택했습니다.


이미지
이미지
이미지


streamlit 패키지를 활용하여 위와 같이 서비스 UI/UX 화면을 디자인했습니다.
토익 스피킹 시험은 총 6개의 파트로 구성되어 있습니다. 사용자가 원하는 파트만을 집중 학습할 수 있도록 탭을 만들었습니다.
각 탭에는 다르게 프롬프트 엔지니어링된 챗봇 서비스가 대기하고 있습니다.




## 느낀 점



## 기술스택
Python, LangChain, Streamlit


## 링크
- 코드: [GitHub]({{ page.links.repo }})
- 보고서: [PDF]({{ page.links.report }})
