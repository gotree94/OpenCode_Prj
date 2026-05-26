# OpenCode macOS 설치 가이드

## 사전 요구사항

- 최신 터미널 에뮬레이터 (권장: WezTerm, Alacritty, Ghostty, Kitty)
- 사용할 LLM 제공자의 API 키 (Claude, OpenAI, Gemini 등)

---

## 1. Homebrew (권장)

### 항상 최신 버전 유지 (tap)
```bash
brew install anomalyco/tap/opencode
```

### 공식 formula (업데이트 주기 느림)
```bash
brew install opencode
```

> 가급적 **tap 버전**을 사용하세요.

---

## 2. 설치 스크립트

```bash
curl -fsSL https://opencode.ai/install | bash
```

시스템을 자동으로 감지하여 최신 버전을 설치합니다.

> 설치 경로 우선순위:
> 1. `$OPENCODE_INSTALL_DIR`
> 2. `$XDG_BIN_DIR` (예: `$HOME/.local/bin`)
> 3. `$HOME/bin`
> 4. `$HOME/.opencode/bin` (기본)

커스텀 경로 지정:
```bash
OPENCODE_INSTALL_DIR=/usr/local/bin curl -fsSL https://opencode.ai/install | bash
```

---

## 3. npm (Node.js 필요)

```bash
npm install -g opencode-ai
```

다른 패키지 매니저:
```bash
# bun
bun install -g opencode-ai

# pnpm
pnpm install -g opencode-ai

# yarn
yarn global add opencode-ai
```

---

## 4. 데스크탑 앱 (Beta)

### Homebrew Cask
```bash
brew install --cask opencode-desktop
```

### 수동 다운로드
[opencode.ai/download](https://opencode.ai/download) 또는 GitHub [Releases 페이지](https://github.com/anomalyco/opencode/releases)에서 `.dmg` 다운로드

| 플랫폼 | 파일 |
|---|---|
| Apple Silicon | `opencode-desktop-darwin-arm64.dmg` |
| Intel | `opencode-desktop-darwin-x64.dmg` |

---

## 5. 수동 설치 (바이너리 직접 다운로드)

### Apple Silicon (M1/M2/M3/M4)
```bash
wget https://github.com/anomalyco/opencode/releases/latest/download/opencode-darwin-arm64
chmod +x opencode-darwin-arm64
sudo mv opencode-darwin-arm64 /usr/local/bin/opencode
```

### Intel
```bash
wget https://github.com/anomalyco/opencode/releases/latest/download/opencode-darwin-amd64
chmod +x opencode-darwin-amd64
sudo mv opencode-darwin-amd64 /usr/local/bin/opencode
```

---

## 6. Nix

```bash
# 안정 버전
nix run nixpkgs#opencode

# 최신 개발 버전
nix run github:anomalyco/opencode
```

---

## 7. mise

```bash
mise use -g opencode
```

---

## 설치 확인

```bash
opencode --version
```

정상적으로 설치되면 버전 정보가 출력됩니다.
