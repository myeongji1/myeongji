# Git 커맨드 레퍼런스

> Windows / PowerShell 기준
> 전체 도움말: `git help` · 명령 도움말: `git <명령> --help`

---

## 1. 자주 쓰는 명령 한눈에 보기

| 하고 싶은 일 | 명령 |
|---|---|
| 현재 상태 보기 | `git status` |
| 변경 파일 전부 스테이징 | `git add .` |
| 커밋 | `git commit -m "메시지"` |
| GitHub에 올리기 | `git push` |
| GitHub에서 받아오기 | `git pull` |
| 커밋 기록 보기 | `git log --oneline` |
| 변경 내용 보기 | `git diff` |
| 브랜치 목록 | `git branch` |
| 브랜치 이동 | `git switch 브랜치명` |
| 저장소 복제 | `git clone <주소>` |

---

## 2. 처음 한 번만 하는 설정

```powershell
git config --global user.name "myeongji1"
git config --global user.email "govlaudwl@naver.com"
git config --global init.defaultBranch main   # 새 저장소 기본 브랜치를 main으로

git config --global --list                     # 설정 확인
```

---

## 3. 저장소 시작하기

```powershell
git init                          # 현재 폴더를 Git 저장소로
git clone https://github.com/<아이디>/<저장소>.git   # 원격 저장소 복제
git clone <주소> 폴더명             # 폴더 이름 지정
```

---

## 4. 기본 작업 흐름 (수정 → 저장 → 올리기)

```powershell
git status                        # 무엇이 바뀌었는지 확인
git add 파일명                     # 특정 파일 스테이징
git add .                         # 전체 스테이징
git commit -m "작업 내용"          # 커밋
git push                          # 원격에 올리기
```

### 상태 기호 (`git status --short`)
| 기호 | 의미 |
|---|---|
| `??` | 추적 안 되는 새 파일 |
| `A` | 새로 스테이징된 파일 |
| `M` | 수정된 파일 |
| `D` | 삭제된 파일 |
| `R` | 이름 바뀐 파일 |

---

## 5. 원격 저장소 (remote)

```powershell
git remote -v                                       # 등록된 원격 확인
git remote add origin https://github.com/<아이디>/<저장소>.git
git remote set-url origin <새 주소>                  # 주소 변경
git remote remove origin                            # 삭제

git push -u origin main         # 처음 push (upstream 연결)
git push                        # 이후엔 이것만
git pull                        # 원격 변경 가져와서 병합
git fetch                       # 가져오기만 (병합 X)
```

---

## 6. 브랜치

```powershell
git branch                      # 로컬 브랜치 목록
git branch -a                   # 원격 포함 전체
git branch 새브랜치              # 생성
git switch 새브랜치              # 이동
git switch -c 새브랜치           # 생성 + 이동
git branch -M main              # 현재 브랜치 이름을 main으로 변경
git branch -d 브랜치명           # 삭제 (병합된 것만)
git branch -D 브랜치명           # 강제 삭제

git merge 브랜치명               # 현재 브랜치에 병합
git push origin --delete 브랜치명   # 원격 브랜치 삭제
```

---

## 7. 기록 & 비교

```powershell
git log                         # 전체 기록
git log --oneline               # 한 줄씩
git log --oneline --graph --all # 그래프로
git show <커밋ID>                # 특정 커밋 내용

git diff                        # 아직 add 안 한 변경
git diff --staged               # add 한 변경
git diff main..브랜치명           # 브랜치 간 비교
```

---

## 8. 되돌리기

```powershell
git restore 파일명               # 파일 수정 취소 (add 전)
git restore --staged 파일명      # add 취소 (수정 내용은 유지)
git commit --amend -m "새 메시지" # 마지막 커밋 메시지 수정 (push 전에만!)

git reset --soft HEAD~1         # 마지막 커밋 취소, 변경은 스테이징 상태로 유지
git reset HEAD~1                # 마지막 커밋 취소, 변경은 작업 폴더에 유지
git revert <커밋ID>              # 해당 커밋을 되돌리는 새 커밋 생성 (push 후엔 이걸로)
```

> ⚠️ `git reset --hard`, `git push --force`는 작업 내용이 영구 삭제될 수 있으니 주의.

---

## 9. 임시 저장 (stash)

```powershell
git stash                       # 현재 변경 임시 보관
git stash list                  # 목록
git stash pop                   # 꺼내서 적용 (목록에서 제거)
git stash apply                 # 적용만 (목록 유지)
git stash drop                  # 삭제
```

---

## 10. 자주 만나는 에러

| 에러 메시지 | 원인 | 해결 |
|---|---|---|
| `src refspec main does not match any` | 커밋이 없거나 `main` 브랜치가 없음 | `git commit` 후 `git branch -M main` |
| `does not have any commits yet` | 아직 커밋 0개 | `git add .` → `git commit -m "..."` |
| `'origin' does not appear to be a git repository` | remote 미등록 | `git remote add origin <주소>` |
| `could not read Username` / 인증 실패 | GitHub 로그인 필요 | 터미널에서 `git push` 후 브라우저 로그인 |
| `Repository not found` | 저장소 없음 또는 주소 오타 | GitHub에서 저장소 생성 / `git remote set-url` |
| `rejected ... (fetch first)` | 원격에 내가 없는 커밋이 있음 | `git pull --rebase` 후 `git push` |
| `refusing to merge unrelated histories` | GitHub에서 README 등 넣고 저장소 생성함 | `git pull origin main --allow-unrelated-histories` |
| `CONFLICT (content)` | 병합 충돌 | 파일에서 `<<<<<<<` 부분 정리 → `git add` → `git commit` |

---

## 11. 새 프로젝트를 GitHub에 올리는 전체 순서

```powershell
# 1. GitHub에서 빈 저장소 생성 (https://github.com/new, README 체크 X)

# 2. 로컬에서
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/myeongji1/Myeongji.git
git push -u origin main
```

---

## 12. .gitignore

Git에 올리지 않을 파일 목록. 프로젝트 루트의 `.gitignore`에 작성.

```gitignore
.venv
__pycache__/
*.py[oc]
.env
dist/
build/
```

```powershell
git rm --cached 파일명           # 이미 올라간 파일을 추적 중지 (파일은 남김)
git rm -r --cached 폴더명
```
