# git diff

- 파일의 변경 내용을 알 수 있는 명령어
- Working directory, Staging area 간 비교
- Commit, branch 간 비교 가능

## More Commands

- `git diff`

  commit된 파일상태와 현재 수정중인 상태 비교

- `git diff --staged`

  commit된 파일상태와 add된 파일 상태 비교

- `git diff [비교할 commit hash1] [비교할 commit hash2]`

  commit간의 상태 비교하기 - commit hash 이용

- `git diff [비교할 commit HEAD1] [비교할 commit HEAD2]`

  commit간의 상태 비교하기 - HEAD 이용

  e.g. git diff HEAD HEAD^ <br/>
  가장 최근의 커밋과 그 전의 커밋을 비교한다

- `git diff [비교할 branch1] [비교할 branch2]`

  branch간의 상태 비교하기 - HEAD 이용

# git remote update

- 원격 저장소의 브랜치 최신 상태로 업데이트 명령어

# git branch

브랜치 조회 명령어

- `git brach`
  로컬 브랜치 조회

- `git brach -r`
  원격 브랜치 조회

- `git brach -a`
  모든 브랜치 조회

# git checkout -t [원격 저장소 브랜치 이름]

원격 저장소의 브랜치를 로컬 저장소로 가져온다

- `git checkout -b [생성할 로컬 저장소 브랜치 이름] [가져올 원격 저장소 브랜치 이름]`

-b 태그를 사용하면 원격 저장소 브랜치의 이름을 원하는 브랜치 이름으로 바꿔서 가져올 수 있음
