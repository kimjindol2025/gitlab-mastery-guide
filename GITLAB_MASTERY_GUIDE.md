# 🚀 GitLab 완전 마스터 가이드
## 초보 → 전문가 (8주 커리큘럼)

---

## 📋 목차
1. [Level 1: 초보 (1-2주)](#level-1-초보)
2. [Level 2: 중급 (3-4주)](#level-2-중급)
3. [Level 3: 고급 (5-6주)](#level-3-고급)
4. [Level 4: 전문가 (7-8주)](#level-4-전문가)

---

# Level 1: 초보 (1-2주)

## 🎯 목표
기본 Git 개념과 GitLab 웹 인터페이스 이해

### 1-1. Git 기초
```bash
# 저장소 복제
git clone http://192.168.45.253:15000/kim/freelang-main.git
cd freelang-main

# 파일 수정
echo "hello" > test.txt

# 스테이징 (준비)
git add test.txt

# 커밋 (기록)
git commit -m "Add test file"

# 푸쉬 (업로드)
git push origin main
```

**개념**:
- `clone`: 저장소 다운로드
- `add`: 변경사항 준비
- `commit`: 변경사항 기록 (메시지 필수)
- `push`: 서버에 업로드

### 1-2. GitLab 웹 인터페이스
```
http://192.168.45.253:15000/kim/freelang-main
├─ Code: 파일 브라우징
├─ Commits: 커밋 히스토리
├─ Issues: 작업 관리
├─ Merge Requests: 코드 리뷰
└─ Settings: 저장소 설정
```

**실습**:
1. 웹에서 Code 탭 → 파일 확인
2. Commits 탭 → 커밋 히스토리 보기
3. 로컬에서 git push → 웹에서 즉시 반영 확인

### 1-3. 커밋 메시지 작성법
```bash
# 좋은 예
git commit -m "feat: 새 기능 추가"
git commit -m "fix: 버그 수정"
git commit -m "docs: 문서 업데이트"
git commit -m "refactor: 코드 정리"

# 나쁜 예 (피하기)
git commit -m "수정"
git commit -m "asdf"
git commit -m "일"
```

**형식**:
```
<타입>: <제목>

<본문 (선택)>
```

**타입**:
- `feat`: 새 기능
- `fix`: 버그 수정
- `docs`: 문서
- `style`: 코드 스타일
- `refactor`: 리팩토링
- `perf`: 성능 개선
- `test`: 테스트

### 1-4. 상태 확인
```bash
# 현재 상태 확인
git status

# 변경사항 보기
git diff

# 로그 보기
git log --oneline
```

---

# Level 2: 중급 (3-4주)

## 🎯 목표
팀 협업, 브랜치 관리, 코드 리뷰

### 2-1. 브랜치 (Branch)
```bash
# 브랜치 목록
git branch

# 새 브랜치 생성
git branch feature/user-auth

# 브랜치 전환
git checkout feature/user-auth
# 또는 (최신)
git switch feature/user-auth

# 브랜치 생성 + 전환
git checkout -b feature/user-auth

# 브랜치 삭제
git branch -d feature/user-auth
```

**브랜치 전략**:
```
main (프로덕션)
  ↑
develop (개발 버전)
  ↑
feature/* (기능 개발)
  ↑
bugfix/* (버그 수정)
```

### 2-2. Merge Request (MR) = 코드 리뷰
```bash
# 1. feature 브랜치에서 개발
git checkout -b feature/new-api

# 2. 코드 수정 및 커밋
echo "new api" > api.js
git add api.js
git commit -m "feat: 새 API 추가"

# 3. Push
git push origin feature/new-api

# 4. GitLab 웹에서:
#   - New Merge Request 클릭
#   - From: feature/new-api
#   - To: main
#   - Create
```

**MR 검토 프로세스**:
```
기능 개발
  ↓
MR 생성 (Pull Request)
  ↓
팀원 코드 리뷰
  ↓
의견 반영 & 수정
  ↓
승인 (Approve)
  ↓
Merge (병합)
```

### 2-3. 충돌 해결 (Conflict)
```bash
# 상황: 같은 파일을 동시에 수정

# 충돌 표시 (<<<< ==== >>>>)
git status

# 파일 열어서 수정 (<<<< 와 >>>> 부분 정리)

# 해결 후
git add 파일명
git commit -m "fix: conflict resolved"
git push origin 브랜치명
```

### 2-4. 협업 워크플로우
```bash
# 팀원 1: 기능 개발
git checkout -b feature/auth
# ... 코드 작성 ...
git push origin feature/auth
# → MR 생성 → 리뷰 → Merge

# 팀원 2: 최신 코드 받기
git pull origin main
```

---

# Level 3: 고급 (5-6주)

## 🎯 목표
CI/CD 자동화, 배포 파이프라인

### 3-1. GitLab CI/CD 파이프라인
```yaml
# .gitlab-ci.yml 파일 생성

stages:
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "18"

test:
  stage: test
  image: node:18
  script:
    - npm install
    - npm test
  only:
    - merge_requests
    - main

build:
  stage: build
  image: node:18
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
  only:
    - main

deploy_staging:
  stage: deploy
  image: alpine
  script:
    - echo "배포 중..."
    - npm start
  environment:
    name: staging
  only:
    - develop

deploy_production:
  stage: deploy
  image: alpine
  script:
    - echo "프로덕션 배포!"
    - npm start
  environment:
    name: production
  only:
    - main
  when: manual
```

**작동 원리**:
```
git push
  ↓
.gitlab-ci.yml 실행
  ↓
Stage 1: test (테스트)
  ├─ npm install
  └─ npm test
  ↓
Stage 2: build (빌드)
  ├─ npm run build
  └─ dist/ 저장
  ↓
Stage 3: deploy (배포)
  ├─ staging (자동)
  └─ production (수동 승인)
```

### 3-2. 파이프라인 상태 확인
```
GitLab 웹:
CI/CD → Pipelines
├─ 실행 중 (🟡)
├─ 성공 (🟢)
├─ 실패 (🔴)
└─ 취소 (⚫)

각 stage 클릭 → 로그 확인
```

### 3-3. 배포 환경 관리
```yaml
# Staging (개발 환경)
deploy_staging:
  environment:
    name: staging
    url: http://staging.example.com

# Production (실제 운영)
deploy_production:
  environment:
    name: production
    url: http://example.com
  when: manual  # 수동 승인 필요
```

### 3-4. 성공/실패 처리
```yaml
test:
  script:
    - npm test
  on_failure:
    # 실패 시 알림
    - echo "테스트 실패!"
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure

# allow_failure: true → 실패해도 계속 진행
```

---

# Level 4: 전문가 (7-8주)

## 🎯 목표
고급 배포, 모니터링, 보안, 성능 최적화

### 4-1. 고급 배포 전략
```yaml
# 롤링 배포 (무중단 배포)
deploy:
  script:
    - docker build -t app:v1 .
    - docker push registry.example.com/app:v1
    - kubectl set image deployment/app app=app:v1
  # 천천히 Pod 교체

# Canary 배포 (일부만 먼저 배포)
canary:
  script:
    - kubectl set image deployment/app-canary app=app:v2
  environment:
    name: canary
```

### 4-2. 모니터링 & 로깅
```yaml
monitor:
  script:
    - curl http://192.168.45.253:15000/health
    - if [ $? -ne 0 ]; then exit 1; fi
  only:
    - main
  when: on_success
```

### 4-3. 보안: 시크릿 관리
```yaml
# GitLab Settings → CI/CD → Variables

# 사용법
deploy:
  script:
    - docker login -u $DOCKER_USER -p $DOCKER_PASSWORD
    - export DATABASE_URL=$DB_URL
    - npm start
```

**설정 방법**:
```
GitLab 웹:
1. Settings → CI/CD
2. Variables → Add Variable
3. Key: DOCKER_PASSWORD
4. Value: ****
5. Protected / Masked 체크
```

### 4-4. 성능 최적화
```yaml
# 캐싱으로 빌드 속도 개선
test:
  cache:
    paths:
      - node_modules/
  script:
    - npm install
    - npm test

# 병렬 실행
test_unit:
  parallel: 5
  script:
    - npm run test:unit -- --shard=true

# Artifact 크기 제한
build:
  artifacts:
    paths:
      - dist/
    expire_in: 1 week  # 1주 후 자동 삭제
```

### 4-5. 고급 브랜칭 전략
```
GitFlow 모델:
┌─ main (프로덕션 v1.0, v1.1, ...)
│
├─ release/v1.1 (배포 준비)
│
├─ develop (다음 버전 개발)
│
├─ feature/auth (기능 개발)
├─ feature/api
├─ feature/ui
│
└─ bugfix/critical (버그 수정)
```

### 4-6. 자동 태그 및 릴리스
```bash
# 릴리스 버전 태그
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0

# GitLab에서 자동으로 Release Note 생성
```

### 4-7. Webhook 연동
```bash
# GitLab Settings → Webhooks

URL: http://your-api.com/webhook
Events:
  ☑️ Push events
  ☑️ Merge requests
  ☑️ Issues
  ☑️ Deployments
```

---

# 📊 진도 체크리스트

## Level 1: 초보 (1-2주)
- [ ] Git 기초 (clone, add, commit, push)
- [ ] GitLab 웹 인터페이스 이해
- [ ] 커밋 메시지 작성
- [ ] Status 확인

## Level 2: 중급 (3-4주)
- [ ] 브랜치 생성/전환
- [ ] Merge Request 생성
- [ ] 코드 리뷰 실습
- [ ] 충돌 해결

## Level 3: 고급 (5-6주)
- [ ] .gitlab-ci.yml 작성
- [ ] Pipeline 실행 확인
- [ ] Staging 배포
- [ ] Production 배포

## Level 4: 전문가 (7-8주)
- [ ] 롤링 배포 구현
- [ ] 모니터링 설정
- [ ] 시크릿 관리
- [ ] 성능 최적화

---

# 🔗 추가 자료

## 공식 문서
- https://docs.gitlab.com/
- https://docs.gitlab.com/ee/ci/

## 유용한 명령어
```bash
# 전체 히스토리 시각화
git log --graph --oneline --all

# 특정 파일 히스토리
git log -p filename

# Blame (누가 수정했나)
git blame filename

# Reset (되돌리기)
git reset --soft HEAD~1  # 마지막 커밋 취소 (파일은 유지)
git reset --hard HEAD~1  # 마지막 커밋 완전 취소
```

## 실무 팁
1. **커밋 자주 하기** - 1주일에 1번보다 하루에 여러 번
2. **메시지 자세히** - 무엇을 했는지뿐 아니라 왜 했는지
3. **MR 작게** - 큰 기능은 여러 MR로 나누기
4. **테스트 먼저** - Push 전에 항상 로컬 테스트
5. **문서 함께** - 코드와 함께 README 업데이트

---

## 완성! 🎓
이 가이드를 따르면 8주 후에는 GitLab 전문가!

마지막 Q&A: 뭐 궁금한 게 있으면 언제든 물어봐.
