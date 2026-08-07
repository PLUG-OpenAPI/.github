# NH투자증권 · NHPLUG Open API

[![PyPI](https://img.shields.io/pypi/v/nhplug?color=0073b7&label=pip%20install%20nhplug)](https://pypi.org/project/nhplug/)
[![License](https://img.shields.io/badge/license-MIT-green)](https://github.com/PLUG-OpenAPI/nhplug-sdk/blob/main/LICENSE)
[![Docs](https://img.shields.io/badge/docs-llms.txt-informational)](https://www.nhplug.com/llms.txt)

**NH투자증권 REST Open API(NHPLUG)의 공식 개발자 지원 계정입니다.**
국내·해외 주식, 파생, 채권, 금현물의 시세·계좌·주문 API를 코드와 AI 도구로 쉽게 쓰도록 라이브러리·샘플·명세를 제공합니다.

🔗 포털 — 나무 [www.nhplug.com](https://www.nhplug.com) · N2 [www.n2plug.com](https://www.n2plug.com) &nbsp;·&nbsp; ✉️ apisupport@nhsec.com

---

## 🔑 시작 전 준비

**1. 앱키·앱시크릿 발급** — 포털에서 신청하세요. 모든 API 호출에 필요합니다.

&nbsp;&nbsp;&nbsp;&nbsp;나무 → **[www.nhplug.com/intro](https://www.nhplug.com/intro)** &nbsp;·&nbsp; N2 → **[www.n2plug.com/intro](https://www.n2plug.com/intro)**

**2. 실행 환경** — 아래 중 쓰실 것만 준비하시면 됩니다.

| 쓸 것 | 필요한 것 |
|---|---|
| Python SDK | **Python 3.11 이상** (`python --version`) |
| MCP (대화형) | **Node.js 18 이상** (`node -v`) + Claude Desktop |

---

## 어떻게 쓰시겠어요?

| 하고 싶은 일 | 방법 | 시작 |
|---|---|---|
| **대화로** 시세·잔고 조회 (코딩 불필요) | **MCP** — [nhplug-mcp](https://github.com/PLUG-OpenAPI/nhplug-mcp) | Claude 설정에 `npx` 한 줄 |
| **내 프로그램에 넣기** (자동매매·퀀트) | **PyPI** — [nhplug](https://pypi.org/project/nhplug/) | `pip install nhplug` |
| **예제 보며 배우기** | **GitHub** — [nhplug-sdk](https://github.com/PLUG-OpenAPI/nhplug-sdk) | `git clone` 후 `snippets/` |

> 세 가지는 경쟁이 아니라 **단계**입니다. 대화로 확인해 보고 → 예제로 익히고 → 내 프로그램에 넣는 흐름이 자연스럽습니다.

> ⚠️ **기본 접속 환경은 운영(`api`)이며 주문이 실제로 체결됩니다.**
> 개발·검증은 **모의투자(`moapi.nhplug.com:8443`)** 로 전환해서 진행하세요.

---

## 빠른 시작

### ① 대화로 (MCP)

Claude Desktop 설정에 붙여넣고 **완전히 종료 후 재실행**하세요.

```json
{ "mcpServers": { "nhplug": {
  "command": "npx", "args": ["-y", "github:PLUG-OpenAPI/nhplug-mcp"],
  "env": { "NHPLUG_APP_KEY": "발급받은_APP_KEY", "NHPLUG_APP_SECRET": "발급받은_APP_SECRET" }
} } }
```

Claude에게 **"삼성전자 현재가 알려줘"** 라고 하면 시세가 나옵니다.

### ② 코드로 (Python)

```bash
pip install nhplug
```

```python
from nhplug import call
from nhplug.instruments import load_master

call("/krstock/quote/v1/currentPrice", {"iem_cd": "005930", "market_cd": "KRX"})
load_master("m_new_stock")     # 전 종목 마스터 (자동 다운로드·캐시)
```

인증·토큰 캐시(24시간)·에러 판정이 모두 포함되어 있어 직접 짜실 필요가 없습니다.

### ③ 예제 보며 (clone)

```bash
git clone https://github.com/PLUG-OpenAPI/nhplug-sdk
cd nhplug-sdk && cp .env.example .env    # .env 에 앱키/시크릿 입력
python snippets/krstock/current_price/chk_current_price.py
```

```
✅ 삼성전자 212,500원
```
이렇게 나오면 준비 완료입니다.

---

## AI·에이전트로 개발한다면

1. **명세 정본** — [llms.txt](https://www.nhplug.com/llms.txt) (N2: [n2plug.com/llms.txt](https://www.n2plug.com/llms.txt))
   전체 문맥이 한 번에 필요하면 [llms-full.txt](https://www.nhplug.com/llms-full.txt) (약 160KB)
2. **규칙 파일** — [templates/](https://github.com/PLUG-OpenAPI/nhplug-sdk/tree/main/templates) 를 프로젝트에 넣으세요
   Antigravity·Codex → `AGENTS.md` · Claude Code → `CLAUDE.md` · **Cursor → `.cursor/rules/nhplug.mdc`**
3. **IDE 가이드** — [Antigravity·Cursor 로 바이브코딩하기](https://github.com/PLUG-OpenAPI/nhplug-sdk/blob/main/guides/antigravity.md)
4. ⚠️ **호출 식별자 주의** — Python SDK 는 **URI 경로**(`/krstock/quote/v1/currentPrice`), MCP 는 **operationId**(`krstockQuoteCurrentPrice`). **섞어 쓰면 동작하지 않습니다.**

---

## 종목마스터 (Instruments)

전 종목 코드·종목명·업종·지수편입 등 **정적 종목정보는 REST API 가 아니라 마스터 파일(.mst)** 로 제공합니다. **28종**(국내·해외주식, 국내선물옵션, 해외파생, 장내채권).

- **구조체 정의** — `https://www.nhplug.com/instruments/<파일명>.h` (N2: `www.n2plug.com`)
  마스터와 **1:1 대응** · 오프셋·길이·코드값 · 파이썬 파서 코드 내장 · **인증 불필요**
  
---

## 저장소

| 저장소 | 내용 |
|---|---|
| [nhplug-sdk](https://github.com/PLUG-OpenAPI/nhplug-sdk) | 파이썬 라이브러리(PyPI `nhplug`) · 샘플 · 종목마스터 파서 · AI 규칙 |
| [nhplug-mcp](https://github.com/PLUG-OpenAPI/nhplug-mcp) | Claude Desktop 연결용 로컬 MCP 서버 (TypeScript) |

> 대화형 AI 는 안전정책상 **실제 주문을 대신 체결하지 않습니다.** MCP 는 조회·분석·주문 준비까지 담당하고, **실제 매수/매도는 코드(SDK)** 로 실행하는 구성을 권장합니다.

---

## 브랜드 · 공식 확인

**나무(Namuh)=`nhplug.com` / N2=`n2plug.com`.** API·저장소·패키지는 **공용**이며 접속 도메인만 다릅니다. 본인 브랜드의 도메인과 키를 사용하세요.

> N2 고객은 `.env` 의 세 줄(`NHPLUG_BASE_URL`·`NHPLUG_AUTH_URL`·`NHPLUG_INSTRUMENTS_BASE`)을 **모두** n2plug 로 바꾸세요.

**공식 여부 확인**: 이 계정과 저장소는 포털에서 상호 링크됩니다. 앱키/앱시크릿은 계좌 접근 권한이므로 **반드시 포털에서 발급한 자격증명만** 사용하세요.

---

<sub>⚠️ 자동매매·주문 기능은 실제 손실이 발생할 수 있습니다. 반드시 모의투자(moapi) 환경에서 검증 후 사용하시고, 실거래 적용은 이용자 책임입니다. · MIT License · © NH Investment & Securities Co., Ltd.</sub>
