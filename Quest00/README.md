# Quest 00. 형상관리 시스템

## Introduction

- git은 2021년 현재 개발 생태계에서 가장 각광받고 있는 버전 관리 시스템입니다. 이번 퀘스트를 통해 git의 기초적인 사용법을 알아볼 예정입니다.

## Topics

- git
  - `git clone`, `git add`, `git commit`, `git push`, `git pull`, `git branch`, `git stash` 명령
  - `.git` 폴더
- GitHub

## Resources

- [Resources to learn Git](https://try.github.io)
- [Learn Git Branching](https://learngitbranching.js.org/?locale=ko)
- [Inside Git: .Git directory](https://githowto.com/git_internals_git_directory)

## Checklist

- 형상관리 시스템은 왜 나오게 되었을까요?

  1. 코드추적의 필요성: 이전 버전 변경사항을 남겨서 코드가 잘못되는 등 상황 발생시, 롤백하여 작업을 진행할 필요가 있었다.
  2. 효율적인 협업의 필요성: 여러 명이서 협업할 때, 각자의 변경사항을 하나의 파일로 안정적으로, 쉽게 병합할 수 있다.

- 형상관리 시스템의 발전과정을 간단하게 설명해보세요.

  1. CVS(Concurrent Versions System)

  - 특징: 중앙집중식 버전 관리 시스템 / 파일단위 델타 저장 방식(파일별로 버전 정보 관리)
    > 델타 저장 방식: 파일의 변경된 부분만을 저장한다. 특정 버전을 구성할 때 이전 버전의 델타를 적용한다.
    - 내부 저장소 예시:
      - 파일 A:
        - 버전 1: "Hello"
        - 버전 2: 델타("Hello" -> "Hello, CVS")
      - 파일 B:
        - 버전 1: "World"
      - 파일 C:
        - 버전 1: "New File"
  - 장점: 기존 RCS에서 여러 사용자가 동시 작업을 가능하게 만들었다.
  - 한계점
    1. 원자적 커밋 미지원: 여러 파일 Commit 시, 한 파일씩 커밋되어서 중간에 오류 발생 시 일부 파일만 커밋되는 현상발생.
       > 원자적: 나눌 수 없는, 중간 단계가 없는 수행 혹은 미수행 이분법적인 방식. 원자적 커밋 방식이면 아예 커밋이 되지않거나, 전체가 커밋되거나 둘 중하나이다.
       > <br>또한 커밋 과정 중에는 변경사항이 적용되지 않는다.
    2. 디렉토리 버전관리 미지원: 파일 구조, 이름 변경 등에 대한 추적이 되지 않음.
    3. 바이너리 파일관리 비효율: 텍스트파일일 때는 델타 저장방식으로 효율적 관리가 가능하지만 바이너리 파일관리의 경우,<br>
       각 버전별로 변경사항만 관리하는 것이 아니라, 전체 파일들을 저장하는 방식으로 저장공간 비효율 발생.

  2. SVN(Subversion)

  - 특징: 중앙집중식 버전 관리 시스템 / 프로젝트단위 델타 저장 방식(프로젝트 단위 버전 정보 관리)
    - 내부 저장소 예시:
      - 메타데이터:
        - 리비전 1: 파일 A와 파일 B가 추가됨
        - 리비전 2: 파일 A가 수정됨, 파일 C가 추가됨
      - 델타:
        - 파일 A: 리비전 1 ("Hello") -> 리비전 2 ("I'm korean")
        - 파일 B: 리비전 1 ("World") (변경 없음)
        - 파일 C: 리비전 2 ("New File")
  - 장점: CVS에서 디렉토리 버전관리 지원, 원자적 커밋 지원, 바이너리 파일 관리 효율적 개선
  - 한계점
    1. 중앙집중식 시스템의 한계: 중앙 서버에 문제가 생기면 버전 관리 기능 자체를 쓸 수가 없다.
    2. 높은 네트워크 트래픽: 모든 연산 (커밋, 업데이트 등)은 중앙 서버와 통신하므로 네트워크 지연이 발생 가능성.
    3. 여러 리비전에 대한 파일들을 가져와야한다면, git에 비해 중복되는 파일들 때문에 용량이 커진다.

- git은 어떤 형상관리 시스템이고 어떤 특징을 가지고 있을까요?
  - git의 특징
    1. 분산버전관리시스템 => 히스토리가 중앙 서버가 아닌 로컬에 저장된다. 즉, 버전관리가 로컬에서도 가능하다.
    2. 스냅샷 저장 방식(프로젝트 단위로): 변경된 파일에 대해 변경 사항만 저장하는 것이 아니라, 아예 새로운 스냅샷을 저장. 변경되지 않은 파일은 이전 버전 참조.
    - 스냅샷 저장방식의 특징( vs 델타 저장방식)
      1. 데이터 무결성
      - 데이터 손상이 발생했을 때 전파되지 않는다는 점.
      - 에러 추적이 쉽다는 점.
      2. 빠른 접근 속도: 변경 사항들을 추적, 연산하지 않아도 되기 때문에 조회가 더 빠르다.
- 분산형 형상관리란? (DVCS: Distributed Version Control Systems / Git, Mercurial, Bazaar)

  - 장점:
    1. 성능: 로컬에서 연산이 이뤄지기 때문에 빠르다. 브랜치 병합, 생성 작업 또한 그렇다.
    2. 중앙서버의존성: 네트워크 문제 발생 시에도 로컬에서 버전관리 기능 가능하며, 해결됐을 때 서버에 push, pull 하면된다.
  - 단점:
    1. 전체 히스토리를 가져오기 때문에 첫 클론이 느리다.
    2. 개발자들의 저장공간이 더 필요하다.
    3. 모든 개발자들이 전체 히스토리가 존재하는 프로젝트 파일을 갖기 때문에 관리가 더 복잡하다.

- git은 어떻게 개발되게 되었을까요? git이 분산형 시스템을 채택한 이유는 무엇일까요?
  - git의 개발과정
    - 리누스 토르발스가 개발, 소스코드를 효율적으로 관리하면서 빠른 성능을 유지할 수 있는 시스템의 필요성.
  - 분산형 시스템을 채택한이유
    1. 중앙서버에 의존하지 않고, 오프라인에서도 버전관리를 하며 개발을 진행할 수 있다 => 스냅샷기법으로 프로젝트가 커지더라도 높은 성능을 유지
    2. 데이터 무결성을 유지하는데 어려움을 해결했다. => 모든 커밋, 브랜치 등 Object마다 해쉬ID를 부여한 체크섬 사용.
    
- git과 GitHub은 어떻게 다를까요?
  -git: 로컬에서 버전관리를 하기 위한 툴
  -github: git으로 관리되는 프로젝트를 원격에서 협업 관리하기위한 저장소.

- git의 clone/add/commit/push/pull/branch/stash 명령은 무엇이며 어떨 때 이용하나요? 그리고 어떻게 사용하나요?

- git의 Object, Commit, Head, Branch, Tag는 어떤 개념일까요? git 시스템은 프로젝트의 히스토리를 어떻게 저장할까요?
  -Object: git에서 데이터를 저장하는 기본단위로 Object를 사용한다. 4가지 타입으로 나뉨.
    1. Blob: 실제 파일 데이터
    2. Tree: 디렉터리구조 정보
    3. Commit: 프로젝트의 특정 상태
    4. Tag: Commit Obj에 label을 붙인 것
  -Commit: 프로젝트의 기록된 특정 시점을 의미. 커밋내역을 통해 코드 추적이 가능하다.
  -Head: 현재 브랜치를 가리키는 참조, 따로 분리하지 않으면 HEAD는 커밋 마다 해당 브랜치의 최신 커밋으로 업데이트됨.
  -Branch: 특정 커밋에서 분리되어 나온 독립된 작업공간. 작업을 병렬로 수행하게 하는 핵심적 역할.
  -Tag: 특정 커밋에 tag를 붙여서 해당 시점을 더 쉽게 찾을 수 있게 한다. 주로 버전 릴리즈에서 사용된다.
  
- 리모트 git 저장소에 원하지 않는 파일이 올라갔을 때 이를 되돌리려면 어떻게 해야 할까요?
  1. git revert: 커밋 이력을 변경하지 않고, 새로운 커밋을 생성해서 해결하기 때문에 안전하다.
  2. git reset 돌아갈포인트 => git push --force: reset을 하면 로컬에서 이전 커밋내역이 지워지고, 수정 후 다시 푸시할 수 있다. <br>
  단, 리모트에는 reset이 적용되지 않아서 로컬과 히스토리 불일치로 push를 할 수 없으므로, 강제 push를 해야한다. => 해당 작업 사이에 동료가 push한 이력이 덮어쓰기 되므로, 협업 시 위험.
## Quest

- GitHub에 가입한 뒤, [이 커리큘럼의 GitHub 저장소](https://github.com/KnowRe-Dev/WebDevCurriculum)의 우상단의 Fork 버튼을 눌러 자신의 저장소에 복사해 둡니다.
- Windows의 경우 같이 설치된 git shell을, MacOSX의 경우 터미널을 실행시켜 커맨드라인에 들어간 뒤, 명령어를 이용하여 복사한 저장소를 clone합니다.
  - 앞으로의 git 작업은 되도록 커맨드라인을 통해 하는 것을 권장합니다.
- 이 문서가 있는 폴더 바로 밑에 있는 sandbox 폴더에 파일을 추가한 후 커밋해 보기도 하고, 파일을 삭제해 보기도 하고, 수정해 보기도 하면서 각각의 단계에서 커밋했을 때 어떤 것들이 저장되는지를 확인합니다.
- `clone`/`add`/`commit`/`push`/`pull`/`branch`/`stash` 명령을 충분히 익혔다고 생각되면, 자신의 저장소에 이력을 push합니다.

## Advanced

- Mercurial은 어떤 형상관리 시스템일까요? 어떤 장점이 있을까요?
- 실리콘밸리의 테크 대기업들은 어떤 형상관리 시스템을 쓰고 있을까요?
