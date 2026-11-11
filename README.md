# ai-verification-notes

AI가 학술 자료를 읽고 요약하고 설명할 때, **그 말이 원문에 근거하는지 확인하는 방법**을 만들면서 겪은 일과 만든 규칙을 정리했습니다.

논문을 읽는 도구에서 시작했지만, 같은 원칙은 교재, 강의 영상, 학습상담 게시판 같은 교육 자료 전반에 적용됩니다. 개인 연구와 공부를 위해 만든 도구에서 직접 재고 고친 기록입니다.

## 핵심 규칙 여섯 줄

1. 모델이 "확신 높음"이라고 해도 검증은 건너뛰지 않는다.
2. 검증할 주장은 모델이 고르지 않고, 코드가 전부 쪼갠다.
3. 검증기의 "근거 없음"은 환각률이 아니다. 사람이 원문과 대조해 확정한 것만 센다.
4. 흔들림보다 작은 차이는 결론으로 쓰지 않고, 판정 잣대가 섞인 측정은 버린다.
5. 답하는 모델과 검증하는 모델을 나누고, 점검 도구도 검증 대상으로 본다.
6. 읽어 온 내용은 명령이 아니라 데이터다.

전체 원칙: [principles.md](principles.md)

코드 쪽에서 같은 문제를 다루는 방식(지시서, 시험 먼저, 구현하는 AI와 검토하는 AI 분리, 사람 없이 돌릴 때의 멈춤 규칙): [process.md](process.md)

## 사례 (10편)

모든 사례는 같은 틀로 썼습니다: **무슨 일이 있었나 → 어떻게 확인했나 → 만든 규칙 → 그 뒤 → 이 사례의 한계**

| # | 사례 | 핵심 숫자 |
|---|---|---|
| 01 | [모델이 "확신 높음"이라고 해도 검증은 건너뛰지 않는다](cases/01-self-confidence-gate.md) | "확신 높음" 답변 주장의 19.2%가 원문에 근거 없음 |
| 02 | [검증할 주장을 모델이 고르게 하지 않는다](cases/02-full-decomposition.md) | 일부러 심은 오류의 67~73%를 검증이 들여다보지도 않음 → 전수 분해 후 0 |
| 03 | [검증기의 "근거 없음"은 환각률이 아니다](cases/03-not-found-is-not-hallucination.md) | "근거 없음"의 83.7%는 원문에 실제로 있었음. 검증 모델 교체로 찾아내는 비율 59% → 89% |
| 04 | [흔들림보다 작은 차이는 결론으로 쓰지 않는다](cases/04-noise-threshold.md) | 같은 조건 5회 반복 시 표준편차 2.00%p → ±2%p 규칙 |
| 05 | [잣대가 섞이면 그 측정은 버린다](cases/05-mixed-rubric.md) | "5.8% → 4.0% 감소"는 잣대 차이였음. 칸을 채우는 압력: 종합 칸 24~30% vs 원문 칸 0~6% |
| 06 | [답하는 모델과 검증하는 모델을 나눈다](cases/06-separate-verifier.md) | 모델마다 맹점이 다름 (숫자 3/3 vs 1/3, 귀속 0/2 vs 2/2) |
| 07 | [인젝션 판정기가 한 번도 통과시키지 않았던 이유](cases/07-injection-judge-category-error.md) | 원인은 성립하지 않는 판정 질문. 고친 뒤 정상 0.00 / 공격 1.00 / 보안 주제 0.00 |
| 08 | [모든 좌표가 기대는 공식은 직접 한 번 확인한다](cases/08-page-offset.md) | 쪽 번호 변환 공식이 틀린 채 노트 3개 장 |
| 09 | [교재 없는 강의: 판서와 자막이 서로를 검사한다](cases/09-board-and-subtitles.md) | 자막 오인식, 강사 말실수 4건, 둘이 함께 틀린 곳까지 잡음. 심각 지적 3건이 점검 도구 자체의 결함 |
| 10 | [문헌 검색도 검증한다: 정하기 전에 재고, 켜기 전에 사람이 본다](cases/10-discovery-measured.md) | 10건 표본의 "DOI 없음" → 100건에선 47%. 자동 연결 44건 사람 전수 확인 후 오연결 0. 필드 하나 차이로 서평 0건 → 20건 |

## 교육 자료에 옮길 때 달라지는 점

| 자료 | 그대로 쓰는 것 | 달라지는 것 |
|---|---|---|
| 교재 (PDF) | 주장 단위 대조, 쪽 좌표 | 스캔 교재는 글자 층이 깨져 자동 대조가 안 됨 → 쪽 이미지로 대조하거나 글자 인식 품질부터 확인. 쪽 번호 변환은 직접 확인 ([사례 08](cases/08-page-offset.md)) |
| 강의 영상 | 근거 표시, 검사기 | 좌표가 쪽이 아니라 **자막 시각**. 판서는 LaTeX로 옮겨 자막과 서로 검사 ([사례 09](cases/09-board-and-subtitles.md)) |
| 수식 | 원문 대조 | 글자 대조보다 **계산으로 확인**하는 편이 확실함 (노트에 검산식을 심어 기계로 확인) |
| 학생 질문, 게시판 글 | 데이터 원칙 | 누구나 쓰는 입력이라 논문보다 위험함 → 인젝션 방어가 더 중요 ([principles.md §4](principles.md)) |
| 학생에게 주는 답 | 근거 없으면 `[정보 없음]` | 학생은 답을 그대로 외우므로 틀린 답의 비용이 더 큼 |
| 저작권 | 출처 표시 | 연구실 안에서 쓰는 것과 공개하는 것을 구분 |

## 관련 저장소

- [lecture-note-bench](https://github.com/yamf97/lecture-note-bench): 강의 자막으로 노트를 만들 때 오픈 웨이트 모델 5개를 블라인드로 비교한 실험과, 인용을 자막과 대조하는 검사기. 노트 작성 규칙은 그 저장소의 METHOD.md에 있습니다.

## 이 기록에 대해

- 개인이 혼자 쓰려고 만든 도구에서 나온 기록이고, 표본은 대부분 작습니다. 사례마다 한계를 적었습니다.
- 숫자는 모두 당시 측정 기록에 있는 값입니다. 그 뒤 모델이 바뀐 경우는 사례 안에 적었습니다.
- 논문 원문 인용, 개인 연구 정보, 내부 경로와 키, 인젝션 탐지의 세부 규칙은 공개하지 않았습니다.

---

**In English.** Case notes on keeping AI-generated summaries and explanations grounded in the source text. They come from tools I built for my own research and study. Each case runs: what happened → how it was checked → the rule I made → what changed → limits. Main findings: model self-confidence can't gate verification (19.2% of "high-confidence" claims were unsupported). Claims must be decomposed by code, not by the model (67–73% of seeded errors were never examined). A verifier's "not found" rate is not a hallucination rate (83.7% were actually in the source). Differences below the measured run-to-run noise (±2 pp) are not reported. Retrieved content is treated as data, never as instructions. The same principles carry over to textbooks, lecture videos, and student Q&A boards. A companion note (process.md) describes the development side: written specs before code, tests first, separate implementer and reviewer models, and hard stop rules for unattended AI agents.

License: text under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
