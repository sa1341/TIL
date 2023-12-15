# Git - 실전 가이드

`VCS(Version Control System)`는 버전관리 시스템으로 파일의 상태를 커밋 단위로 구분하여 버전을 관리해주는 시스템입니다. 백업용뿐만 아니라 협업용으로도 넘사벽으로 많은 개발자분들이 사용하는 협업 관리 툴입니다. 간단하게 Git은 거대한 오픈소스 프로젝트답게 정말 파워풀한 기능들을 제공하지만 Git을 활용하기 위해서는 그만큼 높은 런닝커브를 요구합니다. 간단하게 개인용으로 프로젝트를 만들면서 기존의 소스를 건드리지 않고 내 마음대로 테스트하고 소스가 잘못되는 경우에도 이전의 커밋 상태를 되돌릴 때 Git처럼 유용한 도구는 없을 것입니다. 하지만 너무나 많은 기능이 있기 때문에 자주 사용하는 기능을 위주로 정리해보았습니다.


# 세가지 상태
Git은 기본적으로 파일을 Commited, Modified, Staged 이렇게 세가지 상태로 관리합니다.

- commited란 데이터가 로컬 데이터베이스에 안전하게 저장됐다는 것을 의미합니다.
- Modified는 수정한 파일을 아직 로컬 데이터베이스에 커밋하지 않는 상태입니다.
- Staged란 현재 수정한 파일을 곧 커밋할 것이라고 표시한 상태를 의미합니다.


### 워킹 트리, Stagin Area(index), Git 디렉토리
![스크린샷 2019-11-29 오후 6 20 40](https://user-images.githubusercontent.com/22395934/69858260-f8842d00-12d4-11ea-9f43-edd9d97121e5.png)


Git을 설치하고 프로젝트를 추적 및 관리할 때 가장 먼저 사용해야 하는 명령어는 `git init`입니다. 그러면 .git 디렉토리가 생성되는 것을 확인 할 수가 있는데 이 Git 디렉토리는 Git이 프로젝트의 메타데이터와 객체 데이터베이스를 저장하는 곳을 말합니다. 이 Git 디렉토리가 Git의 핵심입니다.
다른 컴퓨터에 있는 저장소를 `clone` 할 때 Git 디렉토리가 만들어집니다.

워킹 트리는 프로젝트의 특정 버전을 Checkout 한 것입니다. Git 디렉토리는 지금 작업하는 디스크에 있고 그 디렉토리 안에 `압축된 데이터베이스`에서 파일을 가져와서 `워킹 트리`를 만듭니다.

Staging Area는 index라고 불리며 Git 디렉토리에 있습니다. 단순한 파일이고 곧 커밋할 파일에 대한 정보를 지정합니다. Staging Area는 Working Directoy에 있는 파일들 중 수정한 파일이 존재하면 개발자가 커밋에 포함시킬 파일을 선별해서 Staging Area에 올립니다. 그리고 커밋을 수행하면 새로운 커밋 개체가 생성되어 개발자가 올린 파일들에 대해서만 이전 커밋정보를 관리합니다. 따라서 언제든지 원복을 할 수 있는 장점이 있습니다.

### Git으로 하는 일은 기본적으로 아래와 같습니다.

1. 워킹 트리에서 파일을 수정합니다.
2. Staging Area에 파일을 Stage 해서 커밋할 스냅샷을 만듭니다. 모든 파일을 추가할 수도 있고 선택하여 추가할 수도 있습니다.
3. Staging Area에 있는 파일들을 커밋해서 Git 디렉토리에 영구적인 스냅샷으로 저장합니다.

Git 디렉토리에 있는 파일들은 Committed 상태입니다. Checkout 하고 나서 수정했지만, 아직 Staging Area에 추가하지 않았으면 Modified 입니다. 

참고로 Git은 데이터를 저장하기 전에 항상 체크섬을 구하고, 그 체크섬으로 데이터를 관리합니다.  그래서 체크섬을 이해하는 깃 없이는 어떠한 파일이나 디렉토리도 변경 할 수 없습니다. 기본적으로 Git은 SHA-1 해시를 사용하여 체크섬을 만듭니다. 체크섬은 40자 길이의 16진수 문자열로 구성되어 있고, 파일의 내용이나 디렉토리의 구조를 이용하여 체크섬을 구합니다.

> 24b9da6552252987aa493b52f8696cd6d3b00373

이제 위에 Git을 이해하기 위한 최소한의 개념과 용어들을 알아봤으니 바로 실제 많이 사용하는 Git 명령어들을 살펴보겠습니다.

# Git 실전 명령 가이드

먼저 IDE(인텔리제이, 이클립스 등)로 프로젝트를 만들고 나서 그 프로젝트를 추적하고 관리하기 위해 사용하는 명령어를 소개합니다.


1.현재 프로젝트 경로에 들어가서 git을 사용할 수 있도록 초기화 해주는 명령어입니다.

```java
git init 
```

2.프로젝트 디렉토리내의 모든 파일(.은 모든파일을 의미)을 Git의 Staging Area에 추가하는 명령어 입니다. 이 말은 커밋할 파일의 목록들을 의미하기도 합니다.

```java
git add .
```

> 참고로 .을 아큐먼트로 주게 되면 모든 파일을 Staging Area에 추가하기 때문에 만약 커밋에 포함시키고 싶지 않는 파일이 존재하게 된다면 `.gitignore` 파일을 만들고 그 안에 무시할 파일 패턴을 적으면 됩니다.


## ex) .gitignore 작성 예시
```java
# 확장자가 .a인 파일 무시
*.a

# 윗 라인에서 확장자가 .a인 파일은 무시하게 했지만 lib.a는 무시하지 않음
!lib.a

# 현재 디렉토리에 있는 TODO 파일은 무시하고 subdir/TODO 처럼 하위 디렉토리에 있는 파일은 무시하지 않음
/TODO

# build/ 디렉토리에 있는 모든 파일은 무시
build/

# doc/notes.txt 파일은 무시하고 doc/server/arch.txt 파일은 무시하지 않음
doc/*.txt

# doc 디렉토리 아래의 모든 .pdf 파일을 무시

doc/**/*.pdf
```

3.커밋과 동시에 `-m` 옵션으로 커밋 메시지를 작성할 수 있습니다.

```java
git commit -m "initial commit"
```

> 참고로 git commit -a -m "initial commit"에서 -a 옵션을 추가하게 된다면 Staging Area에 추가없이 Git이 관리하는 모든 파일을 자동으로 커밋상태로 만듭니다.

4.원격 저장소, 즉 github repository 주소를 origin이라는 이름으로 등록합니다.

```java
git remote add origin repository주소
```

5.origin이라는 원격 저장소에 master 브랜치에 푸시합니다. 이때  `-u`옵션을 쓴다면 다음번 부터 git push만 입력해도 origin의 master 브랜치로 푸시가 됩니다.

```java
git push -u origin master
```


# 새로운 변경 사항이 있을 때 

로컬에서 파일을 수정하고 원격 저장소에 변경 이력을 반영하고 싶을 때 명령어 순서입니다.

```java
git add
git commit -m "commit message"
git push
```

> 과정은 위에 내용이랑 비슷하지만 init과 원격 저장소 등록을 할 필요는 없습니다.


# branch, merge 사용법
브랜치 생성은 Git에서 제공해주는 최고의 기능이라고 생각합니다. 이것 때문에 오랫동안 VCS로 Git이 개발자들 사이에 각광받는 이유가 아닐까 싶습니다.

브랜치란 독립적으로 어떤 작업을 진행하기 위한 개념입니다. 필요에 의해 만들어지는 각각의 븐래치는 다른 브랜치의 영향을 받지 않기 때문에 여러 작업을 동시에 진행할 수 있습니다.


#### Branch
![스크린샷 2019-11-29 오후 7 16 45](https://user-images.githubusercontent.com/22395934/69861975-ce366d80-12dc-11ea-88ff-8f9af8e0e67a.png)

또한 이렇게 만들어진 브랜치는 다른 브랜치와 `병합(Merge)`함으로써, 작업한 내용을 새로운 하나의 브랜치로 모을 수 있습니다.
master 브랜치는 저장소를 처음 만들면, Git은 바로 `master`라는 이름의 브랜치를 만들어 둡니다. 이 새로운 저장소에 새로운 파일을 추가 한다거나 추가한 파일의 내용을 변경하여 그 내용을 저장(commit)하는 것은 모두 `master`라는 이름의 브랜치를 통해 처리할 수 있는 일이 됩니다.
`master`가 아닌 또 다른 새로운 브랜치를 만들어서 이제부터 이 브랜치를 사용할거야! 라고 선언하지 않는 이상 모든 작업은 `master` 브랜치에서 이루어 집니다.

이제 브랜치에 대한 개념을 살펴보았으니 위에 명령어와 마찬가지로 브랜치 생성 및 병합에 대한 명령어 순서를 살펴보겠습니다.

1.새로운 브랜치를 생성(기존 브랜치는 `master`)

```java
git branch 브랜치 이름설정
```
> 만약 브랜치 생성 후 Checkout까지 바로 하고 싶으면 아래 처럼 옵션을 주어서 사용할 수 있습니다.

```java
git checkout -b feature-01
```


2.1번에서 생성한 브랜치로 변경

```java
git checkout 브랜치이름
```

3.Staging Area에 모든 파일 추가

```java
git add .
```

4.커밋개체 생성 및 메시지 작성

```java
git commit -m "commit message"
```

5.origin 저장소의 새로운 브랜치에 푸시

```java
git push origin 브랜치이름
```

### Merge 수행

```java
git checkout master <- master 브랜치로 변경

git merge 브랜치 이름 
```
위의 과정으로 master 브랜치에 새로운 브랜치를 merge 할수 있습니다.


이제 Merge를 수행했기 때문에 새로운 브랜치가 필요없다면 삭제를 해줘야 합니다.
예를 들어서 feature-01 브랜치를 생성하고 master 브랜치와 Merge 후에 삭제한다고 가정한다면 아래와 같습니다.

```java
git checkout master <- master 브랜치로 이동해서 feature-01 브랜치 삭제

git branch -d feature-01
```
그러나 작업된 사항이나 commit한 이력이 남아 있는 경우, 해당 command로 branch가 삭제 되지 않는 경우가 있습니다.
이러한 경우에는 강제로 삭제할 수 있습니다.

```java
git branch -D  feature-01 <- -D옵션을 사용함
```

이 경우, local의 branch는 삭제 되었으나, remote branch는 삭제가 되지 않았습니다. remote branch를 삭제하기 위해서는, 다음과 같은 command를 수행합니다.

```java
git push origin :feature-01
```
해당 command를 통해서 원격 remote branch를 삭제할 수 있습니다.



`Github(https://github.com/)` 페이지에서 로그인 후에 새로운 repository 생성 후 로컬에서 작업한 내용을 올리기 위한 명령어 순서입니다.








# 충돌 시 해결 방법
pull이나 push를 했을 때 원격저장소의 내용과 로컬폴더내의 내용중 같은 라인에 다른 내용이 있다면 이 경우에는 충돌이 발생합니다.
터미널 로그에 충돌이 난 파일이 표시되니 직접 수정 후 다시 pull이나 push를 수행해야 합니다.

가장 좋은 방법은 협업자들끼리 역할을 완전히 분담해 애초에 같은 코드를 건드리지 않는 것이지만, 어쩔 수 없이 그렇게 된다면 항상 작업 전에 pull을 습관화하면 충돌을 최소한으로 줄일 수 있습니다.


# 풀 리퀘스트 보내기

오픈소스를 만들 때 가장 많이 쓰는 방법입니다.

내가 push한 내용을 repository의 마스터 권한을 가진 사람에게 pull 해달라고 요청합니다. 우선 참여하고 싶은 repository에 들어가 오른쪽 상단의 fork로 나의 repository에 복사합니다.

1.원본 repo의 코드들을 나의 로컬환경에 clone 합니다.

```java
git clone 원본 repositoty 주소
```

2.포크해온 나의 repo주소를 새로운 이름으로 추가해줍니다.

```java
git remote add 나의 repo 이름 나의 repo 주소
```

3.현재 등록되어 있는 원격 저장소가 어떤게 있나 확인합니다. 아마 origin이라는 이름의 원본 repo 주소와 새로 설정한 이름의 나의 repo의 주소가 존재할 것입니다.

```java
git remote -v <- 현재 원격 프로젝트 저장소 및 url을 출력해줍니다.

git branch -r <- 원격 저장소 branch 리스트 확인

git branch -a <- 로컬, 원격 저장소의 branch 리스트 확인
```

4,혹시 모를 코드 변화가 있을 수 있으니 한번 pull해서 확인해줍니다.

```java
git pull origin
```

5. 원본 repo에 이슈명이나 키워드로 브랜치를 생성하고 이동합니다.

```java
git checkout -b 브랜치이름(이슈)설정 origin/master
```

6.모든 작업 후에 6번으로 나의 repo에 푸시합니다.

```java
git push 나의 repo 이름 브랜치 이름
```

위의 과정 이후에 github에서 fork한 나의 repo에서 방금 푸시한 브랜치명을 선택하면 옆에 pull request 버튼이 생기고, 절차에 따라 누르면 풀리퀘스트가 진행됩니다.

여기서도 충돌이 발생할 수 있으니, 코드를 보며 적절히 수정하면 됩니다.


아래 워너비 스페셜님의 글을 대부분 참조하였습니다.
##### 참조:https://takeuu.tistory.com/103


# 원격 저장소의 branch 가져오기

현재 원격 저장소에 여러 개의 branch가 있다고 가정합니다. 하지만 원격 저장소의 모든 내용을 pull이나 clone을 받은 후에 `git branch`로 확인해 보면 원격 저장소의 branch는 받아지지 않고 기존에 있던 master 브랜치 하나만 존재합니다.


먼저 원격 브랜치에 접근하기 위해 git remote를 갱신해줄 필요가 있습니다.

```java
git remote update
```

위의 상황에 만약 원격 저장소의 feature/test01 branch를 가져오고 싶다면, 아래 명령어를 사용하면 됩니다.

```java
git checkout -t origin/feature/test01
```
-t옵션과 원격 저장소의 branch 이름을 입력하면 로컬의 동일한 이름의 branch를 생성하면서 해당 branch로 Checkout을 합니다.

만약 branch를 변경하여 가져오고 싶다면 위에서 언급한것 처럼 아래 명령어를 입력하면 됩니다.
```java
git checkout -b 생성할 branch 이름 원격 저장소의 branch 이름
```

> 참조 로컬 브랜치로 생성없이 원격 저장소의 브랜치로 바로 Checkout한다면 원격 저장소의 브랜치로 워킹트리로 변경이 됩니다. 하지만 메시지에 detached HEAD 상태이고 소스를 보고 변경도 해볼 수 있지만 이곳에서 변경한 것은 잠시 확인해 보는 용도로 사용될뿐 저장되지 않습니다. 다른 브랜치로 checkout을 하면 사라집니다.

그렇기 때문에 브랜치를 추적하고 싶다면 위의 언급한 명령어인 `git checkout -b 생성할브랜치명 원격브랜치명`처럼 해줘어야 합니다.
`이렇게 하는 이유는 Subversion과는 다르게 git같은 경우는 여러개의 원격저장소를 연결할 수 있고 그중에는 브랜치명이 겹칠 수도 있기 때문으로 보입니다.` 

# Git stash 명령어 사용하기

stash 명령어를 배우기 전에 왜 사용해야 되는지 상황을 설명하겠습니다.
자신이 어떤 작업을 하던 중에 다른 요청이 들어와 하던 작업을 멈추고 잠시 브랜치를 변경해야 할 일이 있다고 합시다. 아직 완료하지 않는 일을 commit하는것은 껄그럽습니다. 이때 stash 기능을 사용하면 됩니다.

![스크린샷 2019-11-29 오후 9 11 10](https://user-images.githubusercontent.com/22395934/69868343-cf6f9680-12ec-11ea-9924-e0a310be3ea7.png)

# Git stash란?

아직 마무리하지 않는 작업을 스택에 잠시 저장할 수 있도록 하는 명령어 입니다. 이를 통해 아직 완료하지 않는 일을 commit하지 않고 나중에 다시 꺼내와 마무리할 수 있습니다.

- git stash 명령을 사용하면 워킹 디렉토리에서 수정한 파일들만 저장합니다.

- stash란 아래에 해당하는 파일들을 보관해두는 장소입니다.

  \* Modified이면서 Tracked 상태인 파일
      
      - Tracked 상태인 파일을 수정한 경우
      - Tracked: 과거에 이미 commit하여 스냅샷에 넣어진 관리 대상 상태의 파일


  \*Staging Area에 있는 파일(Staged 상태의 파일)
      
      - git add 명령을 실행한 경우
      - Staged 상태로 만들려면 git add 명령을 실행해야 한다.
      - git add는 파일을 새로 추적할 때도 사용하고 수정한 파일을 Staged 상태로 만들 때도 사용한다.


## 하던 작업 임시로 저장하기

git stash 명령어를 통해 새로운 stash를 스택에 만들어 하던 작업을 임시로 저장합니다.

- 예를 들어, 파일 1개를 수정한다면 아직 commit할게 아니기 때문에 stash에 넣습니다.

![스크린샷 2019-11-29 오후 9 18 42](https://user-images.githubusercontent.com/22395934/69868700-d8149c80-12ed-11ea-814c-f4e79f48ea2c.png)

위의 명령어 git stash를 실행하면 스택에 새로운 stash가 만들어집니다. 이 과정을 통해서 Working directory는 깨끗해집니다.

## stash 목록 확인하기

```java
git stash list 

stash@{0}: WIP on master: 049d078 added the index file
```

## stash 적용하기(했던 작업을 다시 가져오기)

```java
// 가장 최근의 stash를 가져와 적용합니다.
git stash apply 
// stash 이름
git stash apply [stash 이름]
```


![스크린샷 2019-11-29 오후 9 22 11](https://user-images.githubusercontent.com/22395934/69868848-5cffb600-12ee-11ea-977a-b3ce797ea453.png)


- 위의 명령어로는 Staged 상태였던 파일을 자동으로 다시 Staged 상태로 만들어 주지 않는다. –index 옵션을 주어야 Staged 상태까지 복원한다. 이를 통해 원래 작업하던 파일의 상태로 돌아올 수 있다.

```java
git stash apply --index
```

- index 옵션 유무의 차이

#### git stash apply
![스크린샷 2019-11-29 오후 9 29 02](https://user-images.githubusercontent.com/22395934/69869119-49088400-12ef-11ea-8a1f-55d34047dc85.png)

#### git stash apply --index
![스크린샷 2019-11-29 오후 9 29 30](https://user-images.githubusercontent.com/22395934/69869131-57ef3680-12ef-11ea-91f6-de94ddb6a1d2.png)


수정했던 파일들을 복원할 때 반드시 stash했을 때와 같은 브랜치일 필요는 없습니다. 만약 다른 작업 중이던 브랜치에 이전의 작업들을 추가했을 때 충돌이 있으면 알려줍니다.

# stash 제거하기

```java
// 가장 최근의 stash를 제거합니다.
git stash drop

// stash 이름(ex. stash@{2})에 해당하는 stash를 제거한다.
git stash drop [stash 이름]
```

만약 적용과 동시에 스택에 해당 stash를 제거하고 싶으면 아래와 같은 명령어를 사용합니다.

```java
git stash pop
```

# stash 되돌리기

실수로 잘못 stash 적용한 것을 되돌리고 싶으면 위의 명령어를 이용합니다.

```java
// 가장 최근의 stash를 사용하여 패치를 만들고 그것을 거꾸로 적용한다.
git stash show -p | git apply -R
// stash 이름(ex. stash@{2})에 해당하는 stash를 이용하여 거꾸로 적용한다.
git stash show -p [stash 이름] | git apply -R
```

> TIP alias로 편리하게 사용이 가능합니다.

```java
git config --global alias.stash-unapply '!git stash show -p | git apply -R'
git stash apply
#... work work work
// alias로 등록한 stash 되돌리기 명령어
git stash-unapply
```


#### 참조:https://gmlwjd9405.github.io/2018/05/18/git-stash.html