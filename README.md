# quote-light

공유 가능한 가벼운 견적서 엑셀 파일을 자동 생성하는 **Claude 스킬**입니다.
외부 고객·파트너에게 보낼 수 있는 일반 견적서 양식으로, 채팅으로 알려준
고객 정보와 제품명/수량/단가를 받아 `.xlsx` 파일을 만들어 줍니다.

> ℹ️ **Claude 스킬이란?** Anthropic의 Claude AI가 특정 작업을 일관되게 잘
> 수행하도록 만든 "지침 + 스크립트" 묶음입니다. `.skill` 파일 하나를 Claude에
> 업로드하면 해당 기능이 활성화됩니다.

---

## 미리보기

| 빈 템플릿 | 데이터 입력 후 |
|----------|--------------|
| 회사명만 (주)샘플로 채워진 깔끔한 양식 | 견적번호·고객사·제품 자동 채움 + 합계 자동 계산 |

`examples/` 폴더에 샘플 파일이 있습니다.

- `empty_template.xlsx` — 빈 견적서 템플릿
- `sample_minimal.xlsx` — 최소 정보(고객사명+제품 1개)만 입력한 견적서
- `sample_full.xlsx` — 고객사·공급자·제품 다 채운 견적서

---

## 설치 방법

### 1. `.skill` 파일 다운로드

[`dist/quote-light.skill`](dist/quote-light.skill) 파일을 다운로드합니다.

또는 명령줄에서:
```bash
curl -L -o quote-light.skill \
  https://github.com/jay-willog/quotesample/raw/main/dist/quote-light.skill
```

### 2. Claude에 설치

**Claude.ai (웹)**
1. Claude.ai 접속 → 우측 상단 프로필 → **Settings** → **Capabilities**
2. **Skills** 섹션에서 **Upload skill** 버튼 클릭
3. 다운받은 `quote-light.skill` 파일 업로드
4. 활성화 (toggle ON)

> ℹ️ Skills 기능은 일부 플랜에서만 제공될 수 있습니다. 현재 본인 플랜의 정확한
> 지원 범위는 Claude 설정 화면에서 확인하세요.

**Claude Code (터미널)**
Claude Code의 스킬 디렉토리에 압축을 풀어 넣습니다. 위치는 OS별로 다를 수 있으니
[Claude Code 공식 문서](https://docs.claude.com)를 참고하세요.

```bash
# .skill 파일은 사실 zip 파일입니다
mkdir -p ~/.claude/skills/quote-light
unzip quote-light.skill -d ~/.claude/skills/
```

---

## 사용 방법

설치 후 Claude에게 다음과 같이 말하면 자동으로 견적서를 만들어 줍니다.

### 빈손 시작 (필드 양식 안내 받기)
```
자동 견적 생성!
```
→ Claude가 필요한 전체 필드 양식을 보여줍니다. 알고 있는 항목만 채워서
한 번에 답하면 견적서가 생성됩니다.

### 정보 한 줄로 던지기
```
(주)예시고객에 노트북 받침대 50개, 단가 35,000원으로 견적서 만들어줘
```
→ Claude가 자연어를 파싱해서 바로 견적서를 만듭니다. 최소 정보만 있으면
나머지는 빈칸으로 둡니다.

### 풍부한 정보 한 번에
```
자동 견적 생성!

고객사:
- 법인명: (주)예시고객
- 담당자: 김철수
- 연락처: 010-1234-5678
- 견적번호: Q-2605-001

공급자:
- 법인명: (주)우리회사
- 등록번호: 123-45-67890
- 대표자: 홍길동

제품:
1. 노트북 받침대 | 50개 | 35,000원 | 검정색
2. 무선 마우스 | 100개 | 18,000원
3. USB-C 허브 | 20개 | 45,000원
```

---

## 입력 필드 전체 목록

### 고객사 정보 (공급받는자)
| 필드 | 필수도 |
|------|-------|
| 법인명 | **필수** |
| 담당자 / 연락처 / 이메일 / 주소 | 권장 |
| 견적번호 / 발행일 / 유효기간 / 담당부서 | 선택 |

### 공급자 정보 (자사)
| 필드 | 기본값 |
|------|-------|
| 법인명 | (주)샘플 |
| 등록번호 / 대표자 / 주소 / 담당부서 / 연락처 | 빈칸 |

> 모든 공급자 필드는 옵션입니다. 안 채우면 엑셀에서 직접 입력하면 됩니다.

### 제품 항목 (1~5개)
| 필드 | 필수도 |
|------|-------|
| 제품명 / 수량 / 단가 | **필수** |
| 비고 | 선택 |

### 자동 계산
- 공급가액 = 수량 × 단가
- 합계 = 모든 공급가액 합 (VAT 별도)

---

## 파일 구조

```
quotesample/
├── README.md                    ← 이 문서
├── LICENSE                      ← MIT 라이선스
├── dist/
│   └── quote-light.skill        ← 빌드된 스킬 파일 (이거 다운받으면 됨)
├── examples/
│   ├── empty_template.xlsx      ← 빈 견적서 템플릿
│   ├── sample_minimal.xlsx      ← 최소 입력 샘플
│   └── sample_full.xlsx         ← 전체 입력 샘플
└── quote-light/                 ← 스킬 소스 코드
    ├── SKILL.md                 ← Claude가 읽는 스킬 정의
    ├── assets/
    │   └── quote_template.xlsx  ← 빈 견적서 템플릿 원본
    └── scripts/
        ├── create_template.py   ← 템플릿 재생성 스크립트
        └── fill_quote.py        ← 데이터 채우기 핵심 스크립트
```

---

## 직접 빌드하기 (개발자용)

스킬을 수정한 후 `.skill` 파일을 다시 빌드하려면 Anthropic의 `skill-creator`
도구가 필요합니다.

```bash
# 1. 템플릿 디자인 수정 후 재생성
cd quote-light
python3 scripts/create_template.py assets/quote_template.xlsx

# 2. .skill로 패키징 (skill-creator 설치된 환경에서)
python3 -m scripts.package_skill ./quote-light ./dist/
```

---

## 라이선스

MIT License — `LICENSE` 파일 참조.

자유롭게 수정·재배포 가능합니다. 출처 표기만 유지해주세요.

---

## 기여 및 이슈

버그·개선 아이디어는 [Issues](https://github.com/jay-willog/quotesample/issues)에
올려주세요.
