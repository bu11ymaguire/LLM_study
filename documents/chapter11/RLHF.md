# RLHF (人間 피드백을 통한 강화학습) — 핵심 개념 정리

> **출처**: Chapter 11 실습 노트북 (`RLHF.ipynb`) 기반 학습 정리  
> **주제**: PPO 알고리즘을 활용한 RLHF 실습 — GPT-2로 긍정적 영화 리뷰 생성하기  
> **실험 환경**: NVIDIA A800-SXM4-80GB (단일 GPU), VRAM 10097MiB, 학습 시간 35분 19초

---

## 1. RLHF란?

**RLHF (Reinforcement Learning from Human Feedback)** 는 인간의 선호도(피드백)를 보상 신호로 삼아 언어 모델을 파인튜닝하는 기법이다.

### 핵심 아이디어

> 언어 모델이 생성한 텍스트를 인간(또는 인간 선호를 학습한 모델)이 평가하고,  
> 그 점수를 강화학습 보상(reward)으로 활용하여 모델을 개선한다.

### RLHF의 일반적인 파이프라인

```
1. 사전학습 LM (Pretrained LM)
       ↓
2. 지도 학습 파인튜닝 (SFT)
       ↓
3. 보상 모델 학습 (Reward Model Training) — 인간 선호 데이터로 학습
       ↓
4. PPO를 활용한 강화학습 최적화
```

---

## 2. PPO (Proximal Policy Optimization)

### 2.1 PPO란?

**PPO**는 정책 경사(Policy Gradient) 기반 강화학습 알고리즘으로, 업데이트 폭을 제한(proximal)하여 학습 안정성을 높인다. LLM 파인튜닝에 가장 널리 사용되는 RL 알고리즘이다.

### 2.2 LLM에서 PPO의 동작 방식 (3단계)

```
[1단계] Rollout (롤아웃)
  └─ 언어 모델(정책)이 query를 받아 response를 생성

[2단계] Evaluation (평가)
  └─ 보상 함수(또는 보상 모델)가 query+response 쌍을 평가
  └─ 각 쌍에 대해 스칼라 보상값(scalar reward) 1개 출력

[3단계] Optimization (최적화)
  └─ (query, response, reward) 세 쌍으로 PPO 업데이트
  └─ KL 발산 패널티를 추가 보상으로 활용
```

### 2.3 KL 발산 제약의 역할

| 항목 | 내용 |
|------|------|
| **목적** | 최적화된 모델이 원래 언어 모델(참조 모델)에서 너무 멀어지지 않도록 제한 |
| **계산** | 학습 모델의 로그 확률 분포 vs 참조 모델의 로그 확률 분포 간 KL 발산 |
| **적용** | KL 발산을 음수 보상으로 추가하여 PPO 학습에 반영 |
| **효과** | 모델 붕괴(reward hacking) 방지, 언어 자연스러움 유지 |

> **Reward Hacking**: 보상 점수만을 극대화하다가 실제 품질과 무관한 텍스트를 생성하는 현상  
> 예: `"good good good good good..."` 처럼 점수는 높지만 의미 없는 출력

---

## 3. 실험 구성: GPT-2 × BERT × IMDB

### 3.1 실험 목표

> IMDB 영화 리뷰의 **앞부분(query)**을 GPT-2에 입력하면,  
> BERT 감성 분류기의 긍정 점수를 보상으로 삼아  
> GPT-2가 **긍정적인 후속 내용(response)**을 생성하도록 학습

### 3.2 사용 모델

| 역할 | 모델 | 설명 |
|------|------|------|
| **정책 모델** (학습 대상) | `lvwerra/gpt2-imdb` | IMDB 데이터로 1 에폭 추가 파인튜닝된 GPT-2 |
| **참조 모델** (KL 기준) | `lvwerra/gpt2-imdb` | 정책 모델과 동일한 초기 체크포인트, 가중치 고정 |
| **보상 모델** | `lvwerra/distilbert-imdb` | IMDB로 파인튜닝된 DistilBERT 감성 분류기 |

### 3.3 데이터셋: IMDB

- **크기**: 50,000개 영화 리뷰 (긍정/부정 레이블)
- **전처리**: 200자 이상 리뷰만 필터링
- **토크나이징**: `LengthSampler`로 2~8 토큰 길이로 무작위 잘라냄 → **query로 사용**

---

## 4. 핵심 구현 요소

### 4.1 Value Head (값 헤드)

```python
model = AutoModelForCausalLMWithValueHead.from_pretrained(config.model_name)
```

- PPO는 상태(state)에서 예상 보상을 추정하는 **가치 함수(value function)**가 필요
- `AutoModelForCausalLMWithValueHead`는 기존 LLM에 **선형 레이어(value head)**를 추가하여 상태 가치 추정
- 학습 모델과 참조 모델을 **따로 로드**하는 이유: KL 발산 계산에 참조 분포가 필요하기 때문

### 4.2 PPOTrainer 설정

```python
config = PPOConfig(
    model_name="model/gpt2-imdb",
    learning_rate=1.41e-5,   # 원본 논문 "Fine-Tuning LMs from Human Preferences" 기준
    log_with="wandb",
)
```

| 파라미터 | 값 | 출처 |
|----------|-----|------|
| `learning_rate` | `1.41e-5` | 원본 RLHF 논문 기본값 |
| `log_with` | `"wandb"` | Weights & Biases로 학습 추적 |

### 4.3 보상 신호 추출 방식

```python
# BERT 분류기는 logit 값 출력 (소프트맥스 미적용)
sent_kwargs = {"top_k": None, "function_to_apply": "none", "batch_size": 16}

# 긍정(POSITIVE) 클래스의 logit을 보상으로 사용
positive_scores = [
    item["score"]
    for output in pipe_outputs
    for item in output
    if item["label"] == "POSITIVE"
]
```

- `function_to_apply: "none"` → 소프트맥스 미적용, **로직(logit) 그대로** 반환
- 긍정 logit이 클수록 높은 보상 → GPT-2가 긍정적 텍스트를 더 생성하도록 유도

**예시 출력:**

| 텍스트 | NEGATIVE logit | POSITIVE logit |
|--------|----------------|----------------|
| `"this movie was really bad!!"` | +2.34 | -2.73 |
| `"this movie was really good!!"` | -2.29 | +2.56 |

### 4.4 생성 설정 (순수 샘플링)

```python
gen_kwargs = {
    "min_length": -1,
    "top_k": 0.0,    # top-k 비활성화
    "top_p": 1.0,    # nucleus sampling 비활성화
    "do_sample": True,
}
```

- top-k와 nucleus sampling을 모두 비활성화 → **순수 확률 분포에서 샘플링**
- 이유: PPO 최적화에서 탐색(exploration)을 충분히 보장하기 위함

### 4.5 학습 루프 구조

```python
for epoch, batch in enumerate(tqdm(ppo_trainer.dataloader)):
    # 1. 정책 모델(GPT-2)로 응답 생성
    response_tensors = ppo_trainer.generate(query, **generation_kwargs)

    # 2. BERT로 감성 점수(보상) 계산
    rewards = [torch.tensor(score) for score in positive_scores]

    # 3. PPO 스텝: 정책 업데이트
    stats = ppo_trainer.step(query_tensors, response_tensors, rewards)
    ppo_trainer.log_stats(stats, batch, rewards)
```

---

## 5. 실험 결과 분석

### 5.1 정량적 결과

학습 전/후 보상(긍정 logit) 비교:

| 지표 | 학습 전 (before) | 학습 후 (after) | 변화 |
|------|-----------------|-----------------|------|
| **평균 (mean)** | 0.59 | 2.24 | **+1.65 ↑** |
| **중앙값 (median)** | 0.65 | 2.49 | **+1.84 ↑** |

### 5.2 정성적 예시

| query | response (before) | response (after) |
|-------|-------------------|------------------|
| `Well I guess I know` | `that Cantor may be an` | `..but I loved it` |
| `What a terrible` | `movie!` | `chopping sounded so good, I love it!` |
| `We tend` | `not to see Arthur` | `to like this very` |

> **핵심 관찰**: 학습 후 모델은 부정적인 문맥(`"What a terrible"`)을 받아도 긍정적인 방향으로 전환하여 완성합니다.

### 5.3 학습 수렴 양상

- 초반 몇 번의 최적화 스텝만으로도 평균 보상이 빠르게 상승
- 대부분의 샘플에서 보상이 2.0 이상으로 수렴 (최대 약 2.85)
- 일부 샘플(인덱스 10, 14)은 상대적으로 낮은 수준 → 특정 query에서의 한계

---

## 6. 핵심 개념 심화

### 6.1 참조 모델(Reference Model)이 필요한 이유

```
최적화 모델 로그 확률 - 참조 모델 로그 확률 = KL 발산 패널티
```

- 참조 모델 없이 보상만 극대화하면 **reward hacking** 발생
- KL 발산 패널티를 통해 "여전히 자연스러운 언어"를 유지하면서 보상을 높이도록 강제
- 두 모델의 구조는 완전히 동일하지만, **참조 모델의 가중치는 고정(frozen)**

### 6.2 `LengthSampler`의 역할

```python
input_size = LengthSampler(input_min_text_length=2, input_max_text_length=8)
# LLM 입력(query): 2~8 토큰

output_length_sampler = LengthSampler(output_min_length=4, output_max_length=16)
# LLM 출력(response): 4~16 토큰
```

- 매 샘플마다 **무작위 길이**로 자름 → 다양한 길이의 텍스트로 학습
- 특정 길이에 과적합되는 것을 방지

### 6.3 `collator` 함수의 역할

```python
def collator(data):
    return dict((key, [d[key] for d in data]) for key in data[0])
```

- PyTorch DataLoader의 기본 `collate_fn` 대신 사용
- 리스트 형태의 배치를 딕셔너리 형태로 변환
- PPOTrainer가 `query`, `input_ids` 등 각 필드에 쉽게 접근할 수 있도록 지원

### 6.4 Weights & Biases (wandb) 연동

```python
config = PPOConfig(..., log_with="wandb")
wandb.init()
ppo_trainer.log_stats(stats, batch, rewards)  # 매 스텝 자동 로깅
```

- 학습 중 **보상 평균, KL 발산, 정책 손실** 등을 실시간 시각화
- [예제 리포트](https://wandb.ai/huggingface/trl/runs/w9l3110g)에서 수렴 곡선 확인 가능

---

## 7. TRL(Transformer Reinforcement Learning) 라이브러리

### 7.1 TRL이란?

Hugging Face에서 개발한 RLHF 전용 라이브러리. `transformers`와 완벽히 통합되어 있어 LLM의 PPO 학습을 간단하게 구현할 수 있다.

### 7.2 주요 구성 요소

| 클래스 | 역할 |
|--------|------|
| `PPOConfig` | PPO 하이퍼파라미터 설정 (lr, batch_size, KL 계수 등) |
| `PPOTrainer` | 학습 루프 관리, 디바이스 할당, 그래디언트 업데이트 |
| `AutoModelForCausalLMWithValueHead` | LLM에 value head를 추가한 PPO 전용 모델 래퍼 |
| `LengthSampler` | 무작위 길이 샘플링 유틸리티 |

### 7.3 `ppo_trainer.step()`의 내부 동작

```
query_tensors + response_tensors + rewards
         ↓
1. 학습 모델과 참조 모델의 로그 확률 계산
2. KL 발산 계산 → 보상에서 차감
3. GAE (Generalized Advantage Estimation)로 어드밴티지 추정
4. PPO 클리핑된 목적 함수로 정책 업데이트
5. 가치 함수(value head) 업데이트
```

---

## 8. 전체 흐름 요약 다이어그램

```
IMDB 데이터셋
     │
     ▼ (LengthSampler로 잘라냄)
  Query (2~8 토큰)
     │
     ▼ [정책 모델: gpt2-imdb]
  Response (4~16 토큰, 순수 샘플링)
     │
     ▼ [보상 모델: distilbert-imdb]
  Reward (POSITIVE logit 스칼라값)
     │
     ▼ [PPOTrainer.step()]
  Policy Update ← KL 발산 패널티 (참조 모델 대비)
     │
     ▼ (반복)
  더 긍정적인 응답을 생성하는 GPT-2
```

---

## 9. 시사점 및 한계

### 9.1 이 실험에서 배울 수 있는 점

1. **보상 설계가 핵심**: BERT 감성 점수를 보상으로 쓰면 GPT-2가 정말로 긍정적 텍스트 생성을 학습함
2. **KL 제약의 중요성**: 보상만 극대화하면 텍스트 품질이 무너지므로 참조 모델과의 KL 발산 제약이 필수
3. **소규모로도 검증 가능**: 단일 GPU에서 35분 만에 의미 있는 결과를 재현 가능

### 9.2 실제 RLHF의 차이점

| 항목 | 이 실험 | 실제 RLHF (e.g., InstructGPT) |
|------|---------|-------------------------------|
| 보상 모델 | DistilBERT (자동) | 사람이 직접 응답 쌍을 비교·선호도 레이블링 |
| 태스크 | 감성 조종 | 명령 수행, 안전성, 유용성 등 복합 목표 |
| 모델 크기 | GPT-2 (124M) | GPT-3/4 수준 (수백B 파라미터) |
| 학습 단계 | PPO만 적용 | SFT → 보상 모델 학습 → PPO 3단계 |

---

## 참고 자료

| 자료 | 링크 |
|------|------|
| 원본 블로그 | https://newfacade.github.io/notes-on-reinforcement-learning/17-ppo-trl.html |
| TRL 예제 노트북 | https://github.com/huggingface/trl/blob/main/examples/notebooks/gpt2-sentiment.ipynb |
| 원본 RLHF 논문 | https://huggingface.co/papers/1909.08593 |
| wandb 학습 리포트 | https://wandb.ai/huggingface/trl/runs/w9l3110g |
| TRL 라이브러리 | https://github.com/huggingface/trl |
| gpt2-imdb 모델 | https://huggingface.co/lvwerra/gpt2-imdb |
| distilbert-imdb 보상 모델 | https://huggingface.co/lvwerra/distilbert-imdb |
