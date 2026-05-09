# OpenCode_Prj

* YOUTUBE :
    * https://youtu.be/cdJJ578gP6k

* Links
    * https://github.com/code-yeongyu/oh-my-openagent
    * https://github.com/code-yeongyu/oh-my-codex

## 1. Install

   * https://opencode.ai/

```
npm i -g opencode-ai
```

```
(base) C:\Users\Administrator>npm i -g opencode-ai

added 3 packages in 5s
npm notice
npm notice New minor version of npm available! 11.6.2 -> 11.14.1
npm notice Changelog: https://github.com/npm/cli/releases/tag/v11.14.1
npm notice To update run: npm install -g npm@11.14.1
npm notice
```

## 2. Opencode 실행

```
opencode
```

<img src="000.png">

## 3. Mode : Tab 버튼 : Plan <-> Build

<img src="001.png"><br>
<img src="002.png">

## 4. 다른모델 선택
   * "/"
   * /connect 에서 모델을 선택하면 됨.

<details>
<summary>접기/펼치기</summary>
<img src="003.png"><br>
<img src="004.png"><br>
<img src="005.png"><br>
<img src="006.png"><br>
<img src="007.png"><br>
<img src="008.png"><br>
<img src="009.png"><br>
<img src="010.png"><br>
<img src="011.png"><br>
</details>

* Popular (인기 항목)
   * OpenCode Zen (Recommended)
   * OpenCode Go
   * OpenAI (ChatGPT Plus/Pro or API key)
   * GitHub Copilot
   * Anthropic (API key)
   * Google
* Providers (제공자 목록)
   * 302.AI
   * Alibaba / Alibaba (China) / Alibaba Coding Plan (China)
   * Scaleway
   * NanoGPT
   * Abacus
   * Perplexity / Perplexity Agent
   * SiliconFlow / SiliconFlow (China)
   * submodel
   * MiniMax (minimaxi.com) / MiniMax Coding Plan (minimax.io / minimaxi.com)
   * DeepSeek
   * Llama
   * OpenRouter
   * Fireworks AI
   * Kimi For Coding
   * Moark
   * IO.NET
   * Jiekou.AI
   * Bailing
   * iFlow
   * v0
   * Hugging Face
   * ZenMux
   * Upstage
   * NovitaAI
   * Xiaomi Token Plan (China / Europe / Singapore)
   * Weights & Biases
   * Chutes
   * DInference
   * Vivgrid
   * Deep Infra
   * Qiniu
   * Kilo Gateway
   * SAP AI Core
   * Morph
   * Cloudflare AI Gateway / Cloudflare Workers AI
   * Mixlayer
   * Z.AI / Z.AI Coding Plan
   * StepFun
   * Nebius Token Factory
   * Poe
   * Helicone
   * Ollama Cloud
   * Amazon Bedrock
   * The Grid AI
   * Baseten
   * FrogBot
   * Zhipu AI / Zhipu AI Coding Plan
   * Venice AI
   * AIHubMix
   * Cerebras
   * LMStudio
   * LucidQuery AI
   * Moonshot AI / Moonshot AI (China)
   * Azure Cognitive Services / Azure
   * abliteration.ai
   * Wafer
   * Cohere
   * CloudFerro Sherlock
   * KUAE Cloud Coding Plan
   * xAI
   * Meganova
   * Vertex (Anthropic) / Vertex
   * evroc
   * Synthetic
   * Nvidia
   * Inference
   * Inception
   * Kiro
   * Requesty
   * DigitalOcean
   * Vultr
   * Mistral
   * OVHcloud AI Endpoints
   * Friendli
   * Cortecs
   * Vercel AI Gateway
   * LLM Gateway
   * Groq
   * FastRouter
   * STACKIT
   * Tencent TokenHub / Tencent Coding Plan (China)
   * Privatemode AI
   * D.Run (China)
   * Berget.AI
   * GitHub Models
   * Neuralwatt
   * Together AI
   * QiHang
   * HPC-AI
   * GitLab Duo
   * Clarifai
   * Regolo AI
   * Nova
   * Other Custom provider


## OpenCode AI Model Providers Overview (2026 Edition)

이 문서는 OpenCode 플랫폼에서 연결 가능한 주요 모델 제공자들의 기술적 특징, 비용 구조 및 오케스트레이션 적합성을 정리한 가이드입니다.

### 🚀 모델 제공자 비교 테이블

| 제공자 (Provider) | 유료/무료 여부 | 성능 (SWE-bench) | 사용자 규모 | Orchestration/Harness | 주요 파라미터 및 특징 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **OpenCode Zen** | Pay-as-you-go | **최상** (80.6%+) | 수백만 명 (SOTA 타겟) | **매우 유리** (전용 API) | 프리미엄 모델 전문, $20 충전 방식 |
| **OpenCode Go** | $10/월 구독 | 상 (70~80%급) | 중 (개인 개발자 위주) | **유리** (Fallback 지원) | 달러 기반 크레딧($60/월), MiniMax/Qwen 포함 |
| **OpenAI (Codex)** | $20~$200/월 | **최상** (80.0%+) | 전 세계 1위 | **최상** (Agent 전용) | GPT-5.4 기반, 5단계 추론 수준 설정 가능 |
| **Anthropic** | $20/월 (Pro) | **최상** (80.8%+) | 최상위권 | **강력** (Claude Code) | 1M Context Window, Opus 4.6 주력 |
| **DeepSeek (V4)** | 무료/저렴한 API | 상 (80.0% claimed) | 급성장 중 | 유리 (가성비 오케스트레이션) | $2-5/1M 토큰 (가장 경제적) |
| **GitHub Models** | 무료(제한적)/유료 | 중상 (다양한 모델) | 수천만 명 | 유리 (GitHub Ecosystem) | BYOK(Bring Your Own Key) 방식 지원 |
| **MiniMax** | $10/월 (Go 포함) | 상 (80.2%) | 중 (아시아권 강세) | 보통 (Routine tasks) | SWE-Bench 고득점, 루틴 코딩 최적화 |
| **Kimi (Moonshot)** | 유료/무료 | 상 (K2.6 기반) | 중 | **우수** (Agentic 성능) | 다단계 자율 추론(8시간 연속 작동 가능) |
| **Google (Gemini)** | 무료/유료($19+) | 중상 (70% 추정) | 최상위권 | 유리 (GCP 통합) | Gemini 3.1 Pro, 대규모 컨텍스트 처리 강점 |
| **Ollama (Local)** | **무료** (Self-host) | 하~상 (HW 사양 의존) | 오픈소스 커뮤니티 | **강력** (Privacy/Local) | 개인 정보 보호 최우선, 로컬 GPU 자원 사용 |

### 🛠 주요 선정 지표 가이드

1. **Performance (SWE-bench Verified):** 실제 소프트웨어 엔지니어링 문제를 해결하는 능력을 나타내는 지표입니다. 80% 이상은 업계 최상위(SOTA) 수준으로 평가됩니다.
2. **Orchestration/Harness:** 여러 모델을 연동하거나(Chain), 에이전트가 자율적으로 도구를 사용하는 환경에서의 안정성 및 API 지원 여부입니다.
3. **사용자 규모:** 커뮤니티 지원 및 검증된 케이스의 양을 의미하며, "최상"일수록 문제 해결이 용이합니다.
4. **특이사항:**
   * **OpenCode Zen**은 종량제(Pay-as-you-go)로 가장 강력한 모델을 필요할 때만 호출하기 좋습니다.
   * **OpenCode Go**는 월 $10로 MiniMax M2.7 등 우수한 오픈 소스 계열 모델을 무제한에 가깝게 사용할 수 있는 가성비 플랜입니다.

---







