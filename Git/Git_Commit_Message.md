# Git Commit Message

오롯이 혼자 작업하는 경우에는 크게 영향을 받지 않겠지만, 다른 이들과의 협업 시에는 커뮤니케이션이 매우 중요합니다.

변경된 코드를 다 보지 않아도, Commit Log만 보고도 어느정도의 내용을 알 수 있다면 어떨까요?

협업과 리뷰, 유지보수성이 올라갈 것입니다.

- 제목과 본문을 한 줄 띄워 분리하기
- 제목은 영문 기준 50자 이내로
- 제목의 첫글자는 대문자로
- 단, 제목의 마지막에 온점(.)은 금지
- 제목은 **명령문**으로 (본문은 평서문)
- 본문은 영문 기준 72자마다 줄 바꾸기
- 본문은 **무엇을, 왜**에 맞춰 작성하기

하단의 내용은 [bitcoin에 commit된 log](https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6) 입니다.

```markdown
// 제목은 대문자로 시작하며 영문 기준 50자 이내의 명령문으로 한다. 또한 온점은 금지
Simplify serialize.h's exception handling
// 제목과 본문을 한 줄 띄워 분리하기

// 본문은 영문 기준 72자마다 줄 바꾸기
// 본문은 무엇을, 왜에 맞춰 작성하기
Remove the 'state' and 'exceptmask' from serialize.h's stream implementations,
as well as related methods.

As exceptmask always included 'failbit', and setstate was always called with
bits = failbit, all it did was immediately raise an exception. Get rid of
those variables, and replace the setstate with direct exception throwing
(which also removes some dead code).

As a result, good() is never reached after a failure (there are only 2
calls, one of which is in tests), and can just be replaced by !eof().

fail(), clear(n) and exceptions() are just never called. Delete them.

```

## Reference

- https://chris.beams.io/posts/git-commit/
- https://meetup.toast.com/posts/106

