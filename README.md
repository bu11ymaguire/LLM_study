> ⚠️ **Notice / 알림** 
> 
> 이 레포지토리는 상하이 교통대학교(Shanghai Jiao Tong University)의 [Lordog/dive-into-llms](https://github.com/Lordog/dive-into-llms) 원본 실습 자료를 바탕으로 만들어졌습니다.
> 본 저장소는 개인적인 **학습을 목적으로 원본 및 강의 자료를 한국어로 번역 및 수정한 개인 포트폴리오/학습용 저장소**입니다. 
> 모든 원본 콘텐츠에 대한 권리는 원작자에게 있으며, 상용 목적 없이 공익 목적으로 수정한 버전임을 밝힙니다.

---

<p align="center">
<h1 align="center">《대규모 언어 모델 직접 배우기》 시리즈 프로그래밍 실습 튜토리얼</h1>
</p>

<p align="center">
  <img alt="version" src="https://img.shields.io/badge/version-v0.1.0-blue?color=FF8000?color=009922" />
  <img alt="Status-building" src="https://img.shields.io/badge/Status-building-blue" />
  <img alt="PRs-Welcome" src="https://img.shields.io/badge/PRs-Welcome-red" />
</p>

---

## 💡 업데이트

**2025/06/06** — 관심과 적극적인 피드백을 보내주신 모든 분들께 감사드립니다! 다음 두 가지 측면에서 튜토리얼을 업데이트하였습니다:

- [x] 국산화 《대규모 모델 개발 전 과정》 공익 튜토리얼 공개 (PPT, 실험 매뉴얼, 영상 포함) — 화웨이 Ascend 커뮤니티의 지원에 특별히 감사드립니다!
- [x] 기존 시리즈 프로그래밍 실습 튜토리얼의 내용을 업데이트하고, 새로운 주제(수학 추론, GUI 에이전트, 대규모 모델 정렬, 스테가노그래피 등)를 추가하였습니다!

---

## 🎯 프로젝트 동기

《대규모 언어 모델 직접 배우기》 시리즈 프로그래밍 실습 튜토리얼은 상하이 교통대학교의 《자연어처리 최신 기술》(NIS8021) 및 《인공지능 보안 기술》(NIS3353) 강의 자료를 바탕으로 확장된 것으로, 담당 교수는 [장줘성(张倬胜)](https://bcmi.sjtu.edu.cn/home/zhangzs/)입니다. 이 튜토리얼은 대규모 모델 관련 입문 프로그래밍 참고자료를 제공하는 것을 목표로 하며, 공익 목적으로 완전 무료로 제공됩니다. 간단한 실습을 통해 학생들이 대규모 모델을 빠르게 입문하고, 과제 설계나 학술 연구를 보다 효과적으로 수행할 수 있도록 돕습니다.

---

## 📚 튜토리얼 목차

| 튜토리얼 내용 | 소개 | 링크 |
|---|---|---|
| **파인튜닝 및 배포** | 사전학습 모델 파인튜닝 및 배포 가이드: 특정 작업에서 사전학습 모델의 성능을 향상시키고 싶나요? 적절한 사전학습 모델을 선택하고, 특정 작업에 파인튜닝한 후, 이를 편리하게 사용할 수 있는 Demo로 배포해 봅시다! | [[강의자료](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter1/dive-into-llm.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter1/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter1/dive-tuning.ipynb)] |
| **프롬프트 학습 및 사고 연쇄** | 대규모 모델 API 호출 및 추론 가이드: "AI가 온라인으로 격려를 구하고 있다? 대규모 모델이 일부 문제에 대해 황당한 답변을 내놓기도 하지만, 어쩌면 그냥 한마디 '격려'가 필요한 것일 수도 있습니다" | [[강의자료](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter2/dive-into-prompting.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter2/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter2/dive-prompting.ipynb)] |
| **지식 편집** | 언어 모델의 편집 방법 및 도구: 언어 모델이 특정 지식을 기억하는 방식을 조작하고 싶나요? 적절한 편집 방법을 선택하여 특정 지식을 편집하고, 편집된 모델을 검증해 봅시다! | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter3/dive_edit_0410.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter3/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter3/dive_edit.ipynb)] |
| **수학 추론** | 대규모 모델이 수학적 추론을 배우게 하는 방법은? 미니 R1을 빠르게 증류해 봅시다! | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter4/math.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter4/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter4/sft_math.ipynb)] |
| **모델 워터마킹** | 언어 모델의 텍스트 워터마킹: 언어 모델이 생성하는 콘텐츠에 사람이 인식할 수 없는 워터마크를 삽입합니다 | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter5/watermark.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter5/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter5/watermark.ipynb)] |
| **탈옥 공격** | 더 나은 보안을 원한다면, 먼저 공격하는 법을 배워야 합니다. 탈옥 공격이 어떻게 대규모 모델의 입을 여는지 알아봅시다! | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter6/dive-Jailbreak.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter6/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter6/dive-jailbreak.ipynb)] |
| **대규모 모델 스테가노그래피** | "보이지 않는 잉크"! 대규모 모델이 자연스럽게 답변하면서 동시에 '내부 사람'만 알아볼 수 있는 정보를 몰래 담을 수 있을까요? 대규모 모델 스테가노그래피가 알려드립니다! | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter7/stega.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter7/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter7/llm_stega.ipynb)] |
| **멀티모달 모델** | 실제 세계를 보다 충분히 시뮬레이션할 수 있는 멀티모달 대규모 언어 모델은 어떻게 더 강력한 멀티모달 이해 및 생성 능력을 구현할까요? 멀티모달 대규모 언어 모델이 AGI 실현에 도움이 될 수 있을까요? | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter8/mllms.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter8/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter8/mllms.ipynb)] |
| **GUI 에이전트** | AI가 배달 주문, 메시지 답변, 쇼핑 가격 비교를 대신 해줬으면 하시나요? AI 에이전트와 함께 손을 놓고 편안하게 지내봅시다! | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter9/GUIagent.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter9/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter9/GUIagent.ipynb)] |
| **에이전트 보안** | 대규모 모델 에이전트는 미래의 운영체제로 향하는 여정을 시작했습니다. 그러나 개방적인 에이전트 환경에서 대규모 모델은 위험과 위협을 인식할 수 있을까요? | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter10/dive-into-safety.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter10/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter10/agent.ipynb)] |
| **RLHF 안전 정렬** | PPO 기반 RLHF 실험 가이드: 이 튜토리얼은 "매우 위험"합니다. 읽은 후에는 여러분의 대규모 모델이 냉소적으로 웃고 있지는 않은지 확인해 보세요. | [[강의자료](https://github.com/Lordog/dive-into-llms/blob/main/documents/chapter11/RLHF.pdf)] [[튜토리얼](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter11/README.md)] [[스크립트](https://github.com/Lordog/dive-into-llms/tree/main/documents/chapter11/RLHF.ipynb)] |

---

## 🔥 신규 공개: 국산화 《대규모 모델 개발 전 과정》

- **✨ 화웨이 Ascend와 공동 출시한 《대규모 모델 개발 전 과정》 공익 튜토리얼 정식 공개! 최신 기술 + 코드 실습으로 AI 대규모 모델을 손쉽게 마스터하세요 ✨**

  《대규모 언어 모델 직접 배우기》 원본 시리즈 튜토리얼을 기반으로, 화웨이와 공동으로 《대규모 모델 개발 전 과정》 시리즈 과정을 개발하였습니다. 본 시리즈 튜토리얼은 Ascend 기반 소프트웨어·하드웨어로 개발되었으며, PPT, 실험 매뉴얼, 영상 등 다양한 형식의 튜토리얼을 포함합니다. 초급·중급·고급 시리즈로 구성되어 다양한 대규모 모델 실습 수요에 맞추어, 최신 기술을 코드 실습을 통해 관련 연구자 및 개발자에게 입문부터 심화까지 Ascend가 지원하는 모델 및 신규 모델 마이그레이션·파인튜닝의 전 과정 개발 가이드를 제공합니다.

- **🚀 Ascend 커뮤니티에서 《대규모 모델 개발 전 과정》 시리즈 과정 탐색하기**:

  👉 《[대규모 모델 개발 학습 전문 구역](https://www.hiascend.com/edu/growth/lm-development#classification-floor-1)》 @ Ascend 커뮤니티 👈

---

## 🙏 면책 조항

본 튜토리얼의 모든 내용은 기여자들의 개인적인 경험, 인터넷 데이터, 일상적인 연구 활동에서 축적된 자료에서만 비롯됩니다. 모든 기법은 참고용이며, 100% 정확성을 보장하지 않습니다. 문제가 있을 경우 Issue 또는 PR 제출을 환영합니다. 또한 본 프로젝트에서 사용된 배지는 인터넷에서 가져온 것으로, 이미지 저작권을 침해한 경우 삭제 요청을 위해 저희에게 연락해 주시기 바랍니다. 감사합니다.

---

## 🤝 기여 환영

본 튜토리얼은 현재 진행 중인 프로젝트로, 미흡한 부분이 있을 수 있습니다. 어떠한 PR 및 Issue 토론도 환영합니다.

---

## ❤️ 기여자 목록

본 프로젝트를 지지하고 기여해 주신 모든 선생님과 학생분들께 감사드립니다:

**《대규모 언어 모델 직접 배우기》 시리즈 튜토리얼 개발팀**:

- 상하이 교통대학교: 장줘성, 위안퉁신, 마신베이, 허즈웨이, 두웨이, 자오하오둥, 우쭝루, 우쩡, 동링중, 장위룽
- 싱가포르 국립대학교: 페이하오

**《대규모 모델 개발 전 과정》 시리즈 튜토리얼 개발팀**:

- 상하이 교통대학교: 장줘성, 류궁선, 천싱위, 청펑저우, 동링중, 허즈웨이, 쥐톈제, 마신베이, 우쩡, 우쭝루, 옌즈허, 야오야오, 위안퉁신, 자오하오둥
- 화웨이 Ascend 커뮤니티: ZOMI, 셰첸, 청리밍, 러우리화, 자오저위

---

이 리포지토리는 상하이 교통대학교에서 제작한 **LLM(대규모 언어 모델) 입문 실습 튜토리얼**로, 파인튜닝부터 탈옥 공격, RLHF, GUI 에이전트까지 총 **11개 챕터**로 구성되어 있습니다. 각 챕터마다 강의자료, 튜토리얼 문서, Jupyter 노트북 스크립트가 함께 제공됩니다.