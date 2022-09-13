# 문제 상황

- PR을 올렸는데 두 개의 commit이 합쳐져 있었음
- 두 커밋을 분리해서 따로 PR을 올려야 함

# 해결 방법

- 해결 방법으로는 cherry-pick, rebase가 있었고, rebase 선택
- 로컬 브랜치에서 commit1, commit2 별로 추가적인 브랜치 생성
- `git rebase -i HEAD~3` 명령어로 vi 실행
- 삭제할 커밋을 pick에서 drop으로 변경 후 반영
- conflict 수정 후 git push origin [commit1]
- 해당 작업을 commit2 브랜치에서 동일하게 수행

# Breadcrumbs 🍞

- git rebase는 주로 merge에 사용되지만 커밋 내역을 수정/삭제할 때에도 사용 가능
- rebase를 취소하고 싶을 땐, `git rebase --abort` 단, push 전에만
- vi에서 edit을 위해서는 i 입력하고 수정, 끝나면 esc, 나가려면 :wq
- cherry-pick으로 다른 브랜치에서 원하는 커밋만 가져올수도 있는데 같은 내용을 가진 commit이 여러개 생기므로 협업 시 cherry picking의 로깅이 힘듦
- rebase는 현재 브랜치에서만 가능하므로 다른 브랜치에서 commit을 가져오려면 해당 브랜치를 merge 해야하는 번거로움
- 그럼에도 안정성을 위해 rebase 권장

# 더 공부하기

- rebase vs merge 차이점

# 참고자료

[https://gre-eny.tistory.com/301](https://gre-eny.tistory.com/301)  
[https://velog.io/@whoyoung90/TIL-51-git-rebase](https://velog.io/@whoyoung90/TIL-51-git-rebase%EB%A5%BC-%EC%9E%98%EB%AA%BB%ED%95%9C-%EA%B2%BD%EC%9A%B0-%EB%90%98%EB%8F%8C%EC%95%84%EA%B0%80%EA%B8%B0)  
[https://cau-dosc.github.io/how-to-write-commit-messages-using-vi.html](https://cau-dosc.github.io/how-to-write-commit-messages-using-vi.html)  
[https://cselabnotes.com/kr/2021/03/31/56/](https://cselabnotes.com/kr/2021/03/31/56/)
