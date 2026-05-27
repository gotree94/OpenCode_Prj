# OpenCode Ubuntu 설치 가이드

## 사전 요구사항

- 최신 터미널 에뮬레이터 (권장: WezTerm, Alacritty, Kitty)
- 사용할 LLM 제공자의 API 키 (Claude, OpenAI, Gemini 등)

---

## 1. 설치 스크립트 (가장 간편)

```bash
curl -fsSL https://opencode.ai/install | bash
```

```bash
# 방법 1: 현재 셸에 .bashrc 재적용
source ~/.bashrc

# 방법 2: 터미널 완전히 껐다가 새로 열기
```

그 다음에:

```bash
# 버전 확인
opencode --version

# 프로젝트 디렉토리로 이동 후 실행
cd ~/Desktop/프로젝트폴더
opencode
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

## 2. npm (Node.js 필요)

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

## 3. Homebrew

```bash
brew install anomalyco/tap/opencode
```

> `brew install opencode`도 가능하지만, 공식 formula는 업데이트 주기가 느리니 **tap 버전**을 권장합니다.

---

## 4. 데스크탑 앱 (Beta)

GitHub [Releases 페이지](https://github.com/anomalyco/opencode/releases)에서 `.deb` 다운로드:

```bash
sudo dpkg -i opencode-desktop-linux-x64.deb
```

AppImage 버전도 사용 가능합니다.

---

## 5. 수동 설치 (바이너리 직접 다운로드)

### x86_64
```bash
wget https://github.com/anomalyco/opencode/releases/latest/download/opencode-linux-amd64
chmod +x opencode-linux-amd64
sudo mv opencode-linux-amd64 /usr/local/bin/opencode
```

### ARM64
```bash
wget https://github.com/anomalyco/opencode/releases/latest/download/opencode-linux-arm64
chmod +x opencode-linux-arm64
sudo mv opencode-linux-arm64 /usr/local/bin/opencode
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
