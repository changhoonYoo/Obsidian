### - 자주 사용하는 명령어

Git은 버전 관리 시스템으로, 프로젝트를 효과적으로 관리할 수 있도록 도와주는 도구입니다. 아래는 Git에서 자주 사용되는 명령어 몇 가지입니다:

1. **저장소 생성 및 설정:**
    
    - `git init`: 현재 디렉토리를 Git 저장소로 초기화합니다.
    - `git clone <repository_url>`: 원격 저장소를 복제합니다.
2. **변경사항 확인:**
    
    - `git status`: 작업 디렉토리의 상태를 확인합니다.
    - `git diff`: 변경된 내용을 확인합니다.
3. **변경사항 추가 및 커밋:**
    
    - `git add <file>`: 파일을 스테이징 영역에 추가합니다.
    - `git add .` 또는 `git add --all`: 모든 변경 사항을 스테이징 영역에 추가합니다.
    - `git commit -m "커밋 메시지"`: 스테이징 영역에 추가된 변경 사항을 커밋합니다.
4. **브랜치 관리:**
    
    - `git branch`: 브랜치 목록을 보여줍니다.
    - `git checkout <branch_name>`: 특정 브랜치로 이동합니다.
    - `git checkout -b <new_branch>`: 새로운 브랜치를 만들고 해당 브랜치로 이동합니다.
    - `git merge <branch_name>`: 다른 브랜치를 현재 브랜치에 병합합니다.
5. **원격 저장소 관리:**
    
    - `git remote -v`: 현재 프로젝트에 연결된 원격 저장소 목록을 확인합니다.
    - `git pull origin <branch>`: 원격 저장소에서 변경 사항을 가져옵니다.
    - `git push origin <branch>`: 현재 브랜치의 변경 사항을 원격 저장소에 업로드합니다.
6. **로그 및 이력 확인:**
    
    - `git log`: 커밋 이력을 확인합니다.
    - `git log --oneline`: 각 커밋을 한 줄로 표시합니다.
7. **기타:**
    
    - `gitignore`: Git이 무시해야 할 파일 및 디렉토리를 지정하는 파일입니다.

### - 프로젝트 깃허브에 올리기

프로젝트를 GitHub 레포지토리에 올리려면 다음 단계를 따르세요:

1. **로컬 프로젝트 초기화:** 프로젝트 폴더로 이동하여 Git을 초기화합니다. 명령 프롬프트나 터미널에서 다음 명령을 실행하세요:
    
    `git init`
    
2. **GitHub 레포지토리 생성:** GitHub 웹사이트에서 "New repository"를 선택하여 새 레포지토리를 만듭니다. README 파일을 생성할 것인지, .gitignore 파일을 추가할 것인지 등을 선택할 수 있습니다.
    
3. **로컬 프로젝트에 원격 레포지토리 추가:** 원격 레포지토리의 주소를 복사하고, 로컬 프로젝트 폴더에서 다음 명령을 실행하여 원격 레포지토리를 추가합니다:
    
    `git remote add origin <레포지토리 주소>`
    
    여기서 `<레포지토리 주소>`는 GitHub에서 만든 레포지토리 주소입니다.
    
4. **로컬 변경사항 커밋:** 변경사항을 커밋하려면 다음 명령을 사용하세요:
    
    `git add . git commit -m "커밋 메시지"`
    
5. **원격 레포지토리에 푸시:** 변경사항을 원격 레포지토리로 푸시하려면 다음 명령을 사용하세요:
    
    `git push -u origin main`
    
    위 명령에서 `main`은 기본 브랜치 이름일 수 있습니다. 프로젝트에 따라 `master`나 다른 브랜치일 수도 있습니다.
    

이제 GitHub 웹사이트에서 해당 레포지토리에 접속하면 코드와 커밋 내역이 업로드된 것을 확인할 수 있습니다.
