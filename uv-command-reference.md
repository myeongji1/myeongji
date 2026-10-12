# uv 커맨드 레퍼런스

> 기준 버전: **uv 0.13.0** (Windows / PowerShell)
> 전체 도움말: `uv help` · 하위 명령 도움말: `uv <명령> --help`

---

## 0. 설치 위치 & PATH

uv 실행 파일 위치: `C:\Users\wins\.local\bin\uv.exe`

`uv : 'uv' 용어가 ... 인식되지 않습니다` 오류가 나면 PATH에 등록되지 않은 것입니다.

```powershell
# 현재 터미널에서만 적용
$env:Path = "$env:USERPROFILE\.local\bin;$env:Path"

# 영구 적용 (적용 후 VS Code / 터미널 재시작)
[Environment]::SetEnvironmentVariable("Path", "$env:USERPROFILE\.local\bin;" + [Environment]::GetEnvironmentVariable("Path","User"), "User")
```

---

## 1. 자주 쓰는 명령 한눈에 보기

| 하고 싶은 일 | 명령 |
|---|---|
| 새 프로젝트 만들기 | `uv init` |
| 패키지 추가 | `uv add requests` |
| 개발용 패키지 추가 | `uv add --dev pytest` |
| 패키지 제거 | `uv remove requests` |
| 환경을 lock 파일에 맞추기 | `uv sync` |
| 스크립트 실행 | `uv run main.py` |
| 가상환경 만들기 | `uv venv` |
| Python 설치 | `uv python install 3.12` |
| 일회성 도구 실행 | `uvx ruff check` |
| 의존성 트리 보기 | `uv tree` |
| uv 업데이트 | `uv self update` |

---

## 2. 프로젝트 관리 (`pyproject.toml` + `uv.lock` 기반)

### `uv init` — 새 프로젝트 생성
```powershell
uv init                  # 현재 폴더를 프로젝트로
uv init myapp            # myapp 폴더 생성
uv init --lib mylib      # 라이브러리(패키지) 구조
uv init --app            # 애플리케이션 구조 (기본값)
uv init --python 3.12    # Python 버전 지정
```

### `uv add` / `uv remove` — 의존성 추가·제거
```powershell
uv add requests
uv add "fastapi>=0.110"           # 버전 조건
uv add pandas numpy               # 여러 개
uv add --dev pytest ruff          # 개발 의존성 (dev 그룹)
uv add --group docs mkdocs        # 특정 그룹
uv add --optional plot matplotlib # extra(선택 의존성)
uv add -r requirements.txt        # requirements.txt에서 일괄 추가
uv add git+https://github.com/user/repo   # Git 저장소
uv add --editable ./local-pkg     # 로컬 패키지(편집 모드)

uv remove requests
uv remove --dev pytest
```

### `uv sync` — 환경 동기화
`uv.lock` 기준으로 `.venv`를 맞춥니다. (없는 건 설치, 불필요한 건 제거)
```powershell
uv sync
uv sync --no-dev          # 개발 의존성 제외
uv sync --all-extras      # 모든 extra 포함
uv sync --extra plot      # 특정 extra 포함
uv sync --group docs      # 특정 그룹 포함
uv sync --locked          # lock 파일이 최신이 아니면 에러 (CI용)
uv sync --frozen          # lock 파일 갱신 없이 그대로 설치
uv sync --upgrade         # 전체 업그레이드 후 동기화
```

### `uv lock` — lock 파일 갱신
```powershell
uv lock
uv lock --upgrade                    # 모든 패키지 최신으로
uv lock --upgrade-package requests   # 특정 패키지만
uv lock --check                      # lock 파일 최신 여부만 확인
```

### `uv run` — 프로젝트 환경에서 실행
실행 전 자동으로 lock/sync 하므로 가상환경 활성화가 필요 없습니다.
```powershell
uv run main.py
uv run python -m mymodule
uv run pytest
uv run --with rich main.py      # 임시로 패키지 추가해서 실행
uv run --python 3.13 main.py    # 다른 Python 버전으로
uv run --no-sync main.py        # 동기화 생략
```

### `uv tree` — 의존성 트리
```powershell
uv tree
uv tree --depth 1
uv tree --outdated        # 업데이트 가능한 패키지 표시
uv tree --invert          # 역방향(누가 이걸 쓰는지)
```

### `uv export` — lock 파일을 다른 형식으로
```powershell
uv export --format requirements-txt -o requirements.txt
uv export --no-dev --no-hashes -o requirements.txt
```

### `uv version` — 프로젝트 버전 읽기/변경
```powershell
uv version                # 현재 버전 출력
uv version 1.2.0          # 버전 지정
uv version --bump minor   # major / minor / patch
```

---

## 3. 코드 품질 도구

### `uv format` — 코드 포맷팅 (Ruff 사용)
```powershell
uv format
uv format --check          # 변경 없이 검사만
uv format --diff           # 변경 사항 diff 출력
uv format -- --line-length 100   # Ruff에 추가 인자 전달
```

### `uv check` — 프로젝트 검사 (타입 체크 등)
```powershell
uv check
uv check --fix             # 안전한 수정 자동 적용
uv check --all-packages    # 워크스페이스 전체
```

### `uv audit` — 의존성 보안 취약점 검사
```powershell
uv audit
uv audit --no-dev          # 개발 의존성 제외
uv audit --locked
```

---

## 4. 스크립트 (단일 파일, PEP 723)

```powershell
uv init --script hello.py --python 3.12   # 스크립트 템플릿 생성
uv add --script hello.py requests         # 스크립트 상단에 의존성 기록
uv run hello.py                           # 격리된 환경에서 실행
uv lock --script hello.py                 # 스크립트용 lock 생성
```

---

## 5. Python 버전 관리 (`uv python`)

```powershell
uv python list               # 사용 가능한/설치된 버전 목록
uv python install 3.12       # 설치
uv python install 3.11 3.13  # 여러 버전 설치
uv python upgrade            # 설치된 버전들 최신 패치로
uv python find 3.12          # 해당 버전 경로 찾기
uv python pin 3.12           # .python-version 파일 생성 (프로젝트 고정)
uv python dir                # 설치 디렉터리 확인
uv python uninstall 3.11
uv python update-shell       # Python 실행 폴더를 PATH에 등록
```

---

## 6. 도구 실행·설치 (`uv tool` / `uvx`)

전역 CLI 도구(ruff, black, httpie 등)를 프로젝트와 분리해서 사용합니다.

```powershell
uvx ruff check .                 # 설치 없이 일회성 실행 (= uv tool run)
uvx --from httpie http GET example.com   # 패키지명과 명령명이 다를 때
uvx ruff@0.6.0 --version         # 특정 버전

uv tool install ruff             # 전역 설치
uv tool list                     # 설치된 도구 목록
uv tool upgrade ruff             # 업그레이드
uv tool upgrade --all
uv tool uninstall ruff
uv tool audit                    # 설치된 도구 보안 검사
uv tool update-shell             # 도구 폴더를 PATH에 등록
uv tool dir                      # 도구 설치 위치
```

---

## 7. pip 호환 인터페이스 (`uv pip`)

기존 pip 사용 방식 그대로 쓰고 싶을 때. (`pyproject.toml`/`uv.lock`에 기록되지 않음)

```powershell
uv pip install requests
uv pip install -r requirements.txt
uv pip install -e .                  # 편집 모드 설치
uv pip uninstall requests
uv pip list
uv pip freeze > requirements.txt
uv pip show requests
uv pip tree
uv pip check                         # 의존성 충돌 확인

uv pip compile requirements.in -o requirements.txt   # 버전 고정 파일 생성
uv pip sync requirements.txt                         # 정확히 그 목록으로 맞춤
```

> 💡 프로젝트 관리라면 `uv add` / `uv sync`를, 단순 설치라면 `uv pip install`을 쓰세요.

---

## 8. 가상환경 (`uv venv`)

```powershell
uv venv                     # .venv 생성
uv venv myenv               # 이름 지정
uv venv --python 3.12       # Python 버전 지정
uv venv --seed              # pip/setuptools 포함

# 활성화 (PowerShell)
.\.venv\Scripts\Activate.ps1
# 비활성화
deactivate
```

> ⚠️ 이미 `.venv`가 있는 곳에서 `uv venv`를 실행하면 기존 환경을 덮어씁니다.
> 활성화 스크립트 실행이 막히면: `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned`

---

## 9. 빌드 & 배포

```powershell
uv build                    # dist/ 에 sdist + wheel 생성
uv build --wheel            # wheel만
uv publish                  # PyPI 업로드
uv publish --token <TOKEN>
uv publish --index testpypi
```

---

## 10. 워크스페이스 (`uv workspace`)

여러 패키지를 하나의 저장소에서 관리할 때.

```powershell
uv workspace list           # 멤버 목록
uv workspace dir --package mypkg   # 특정 멤버 경로
uv workspace metadata       # 워크스페이스 메타데이터
uv sync --all-packages
uv run --package mypkg main.py
```

---

## 11. 인증 (`uv auth`)

사설 패키지 인덱스 등에 로그인할 때.

```powershell
uv auth login <서비스 URL>
uv auth logout <서비스 URL>
uv auth token <서비스 URL>
uv auth dir                 # 자격증명 저장 위치
```

---

## 12. 캐시 & uv 자체 관리

```powershell
uv cache dir                # 캐시 위치
uv cache size               # 캐시 크기
uv cache clean              # 전체 캐시 삭제
uv cache clean requests     # 특정 패키지 캐시만
uv cache prune              # 쓰지 않는 캐시 정리

uv self update              # uv 업데이트
uv self version             # uv 버전 (= uv --version)
```

---

## 13. 전역 옵션

| 옵션 | 설명 |
|---|---|
| `-q`, `--quiet` | 출력 줄이기 |
| `-v`, `--verbose` | 자세한 출력 |
| `-n`, `--no-cache` | 캐시 사용 안 함 |
| `--offline` | 네트워크 사용 안 함 |
| `--directory <DIR>` | 지정 폴더로 이동 후 실행 |
| `--project <DIR>` | 지정 폴더에서 프로젝트 탐색 |
| `--no-python-downloads` | Python 자동 다운로드 끄기 |
| `--managed-python` | uv가 관리하는 Python만 사용 |
| `--system-certs` | OS 인증서 저장소 사용 (사내망 SSL 문제 시) |
| `--no-progress` | 진행 표시 숨김 |

---

## 14. 주요 파일

| 파일 | 역할 |
|---|---|
| `pyproject.toml` | 프로젝트 메타데이터 + 의존성 선언 |
| `uv.lock` | 정확한 버전이 고정된 lock 파일 (Git에 커밋) |
| `.python-version` | 프로젝트 기본 Python 버전 (`uv python pin`) |
| `.venv/` | 프로젝트 가상환경 (Git에 커밋하지 않음) |
| `uv.toml` | uv 설정 파일 (선택) |

---

## 15. 일반적인 작업 흐름

```powershell
# 새 프로젝트 시작
uv init myapp
cd myapp
uv add requests
uv run main.py

# 기존 프로젝트 클론 후
uv sync
uv run main.py

# requirements.txt 프로젝트를 uv로 옮기기
uv init
uv add -r requirements.txt
```
