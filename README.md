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

## OpenWebUI

* Ctrl + 'p' : 지금은 안보이네?

---

## 예제1 : 간단한 투두리스트 웹앱을 만들어줘. 디자인은 모던하게.


```
# Wrote todo-app\index.html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Todo App</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<style>
*, *::before, *::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: 'Inter', -apple-system, sans-serif;
  background: #f1f5f9;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  padding: 60px 24px;
  color: #1e293b;
}
.container {
  width: 100%;
  max-width: 540px;
}
h1 {
  font-size: 28px;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 8px;
  letter-spacing: -0.5px;
}
.subtitle {
  color: #64748b;
  font-size: 14px;
  margin-bottom: 28px;
}
.input-row {
  display: flex;
  gap: 10px;
  margin-bottom: 32px;
}
.input-row input {
  flex: 1;
  padding: 14px 18px;
  border: 2px solid #e2e8f0;
  border-radius: 12px;
  font-size: 15px;
  font-family: inherit;
  background: #fff;
  transition: border-color 0.2s, box-shadow 0.2s;
  outline: none;
}
.input-row input:focus {
  border-color: #6366f1;
  box-shadow: 0 0 0 4px rgba(99,102,241,0.12);
}
.input-row input::placeholder {
  color: #94a3b8;
}
.btn {
  padding: 14px 24px;
  border: none;
  border-radius: 12px;
  font-size: 15px;
  font-weight: 600;
  font-family: inherit;
  cursor: pointer;
  transition: all 0.2s;
  white-space: nowrap;
}
.btn-primary {
  background: #6366f1;
  color: #fff;
}
.btn-primary:hover {
  background: #4f46e5;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(99,102,241,0.35);
}
.btn-primary:active {
  transform: translateY(0);
}
.stats {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
  font-size: 13px;
  color: #64748b;
}
.stats .count {
  font-weight: 600;
  color: #1e293b;
}
.filters {
  display: flex;
  gap: 6px;
}
.filter-btn {
  padding: 6px 14px;
  border-radius: 8px;
  border: none;
  background: transparent;
  font-size: 13px;
  font-weight: 500;
  font-family: inherit;
  color: #64748b;
  cursor: pointer;
  transition: all 0.2s;
}
.filter-btn:hover {
  background: #e2e8f0;
  color: #1e293b;
}
.filter-btn.active {
  background: #6366f1;
  color: #fff;
}
.todo-list {
  list-style: none;
}
.todo-item {
  display: flex;
  align-items: center;
  gap: 14px;
  background: #fff;
  padding: 16px 18px;
  border-radius: 12px;
  margin-bottom: 8px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.04);
  transition: all 0.2s;
  animation: slideIn 0.25s ease;
}
.todo-item:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
  transform: translateY(-1px);
}
@keyframes slideIn {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}
.todo-check {
  width: 22px;
  height: 22px;
  border-radius: 50%;
  border: 2px solid #cbd5e1;
  cursor: pointer;
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.25s;
  background: #fff;
}
.todo-check:hover {
  border-color: #6366f1;
}
.todo-check.checked {
  background: #6366f1;
  border-color: #6366f1;
}
.todo-check.checked::after {
  content: '';
  width: 8px;
  height: 4px;
  border-left: 2px solid #fff;
  border-bottom: 2px solid #fff;
  transform: rotate(-45deg) translateY(-1px);
  margin-top: -1px;
}
.todo-text {
  flex: 1;
  font-size: 15px;
  line-height: 1.4;
  word-break: break-word;
  cursor: pointer;
  padding: 2px 0;
}
.todo-text.done {
  text-decoration: line-through;
  color: #94a3b8;
}
.todo-delete {
  width: 32px;
  height: 32px;
  border: none;
  background: transparent;
  border-radius: 8px;
  cursor: pointer;
  font-size: 18px;
  color: #94a3b8;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  flex-shrink: 0;
}
.todo-delete:hover {
  background: #fef2f2;
  color: #ef4444;
}
.empty {
  text-align: center;
  padding: 48px 24px;
  color: #94a3b8;
  font-size: 14px;
  line-height: 1.8;
}
.empty-icon {
  font-size: 40px;
  margin-bottom: 12px;
  opacity: 0.5;
}
.clear-all {
  margin-top: 16px;
  text-align: center;
}
.btn-ghost {
  background: transparent;
  color: #94a3b8;
  font-size: 13px;
  padding: 8px 16px;
}
.btn-ghost:hover {
  color: #ef4444;
  background: #fef2f2;
}
</style>
</head>
<body>
<div class="container">
  <h1>할 일</h1>
  <p class="subtitle"><span id="dateDisplay"></span></p>
  <div class="input-row">
    <input type="text" id="todoInput" placeholder="새 할 일을 입력하세요..." autofocus>
    <button class="btn btn-primary" id="addBtn">추가</button>
  </div>
  <div class="stats">
    <span>총 <span class="count" id="totalCount">0</span>개</span>
    <div class="filters">
      <button class="filter-btn active" data-filter="all">전체</button>
      <button class="filter-btn" data-filter="active">진행중</button>
      <button class="filter-btn" data-filter="done">완료</button>
    </div>
  </div>
  <ul class="todo-list" id="todoList"></ul>
  <div class="clear-all">
    <button class="btn btn-ghost" id="clearDoneBtn">완료된 항목 삭제</button>
  </div>
</div>
<script>
const STORAGE_KEY = 'todos';
let todos = JSON.parse(localStorage.getItem(STORAGE_KEY)) || [];
let filter = 'all';
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const totalCount = document.getElementById('totalCount');
const dateDisplay = document.getElementById('dateDisplay');
function updateDate() {
  const now = new Date();
  const y = now.getFullYear();
  const m = String(now.getMonth() + 1).padStart(2, '0');
  const d = String(now.getDate()).padStart(2, '0');
  const days = ['일요일', '월요일', '화요일', '수요일', '목요일', '금요일', '토요일'];
  const day = days[now.getDay()];
  dateDisplay.textContent = `${y}. ${m}. ${d}. ${day}`;
}
updateDate();
function save() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
}
function render() {
  const filtered = todos.filter(t => {
    if (filter === 'active') return !t.done;
    if (filter === 'done') return t.done;
    return true;
  });
  totalCount.textContent = todos.length;
  if (filtered.length === 0) {
    const msg = filter === 'all' ? '할 일을 추가해보세요!' : filter === 'active' ? '모든 할 일을 완료했습니다!' : '완료된 항목이 없습니다.';
    todoList.innerHTML = `<li class="empty"><div class="empty-icon">📋</div>${msg}</li>`;
    return;
  }
  todoList.innerHTML = filtered.map(t => `
    <li class="todo-item" data-id="${t.id}">
      <div class="todo-check ${t.done ? 'checked' : ''}" onclick="toggle(${t.id})"></div>
      <span class="todo-text ${t.done ? 'done' : ''}" onclick="toggle(${t.id})">${escapeHtml(t.text)}</span>
      <button class="todo-delete" onclick="remove(${t.id})">✕</button>
    </li>
  `).join('');
}
function escapeHtml(text) {
  const div = document.createElement('div');
  div.textContent = text;
  return div.innerHTML;
}
function addTodo() {
  const text = todoInput.value.trim();
  if (!text) return;
  todos.push({ id: Date.now(), text, done: false });
  todoInput.value = '';
  save();
  render();
  todoInput.focus();
}
function toggle(id) {
  const todo = todos.find(t => t.id === id);
  if (todo) {
    todo.done = !todo.done;
    save();
    render();
  }
}
function remove(id) {
  todos = todos.filter(t => t.id !== id);
  save();
  render();
  todoInput.focus();
}
addBtn.addEventListener('click', addTodo);
todoInput.addEventListener('keydown', e => { if (e.key === 'Enter') addTodo(); });
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    filter = btn.dataset.filter;
    render();
  });
});
document.getElementById('clearDoneBtn').addEventListener('click', () => {
  todos = todos.filter(t => !t.done);
  save();
  render();
});
render();
</script>
</body>
</html>
```

## 예제2 : 에이전트 협업
   * oh-my-opencode










