
```sh
keys *2 <- 현재 저장되어 있는 key 중에 2로 끝나는 key만 검색 

rename 1113 1116 <- key:1113을 key:1116으로 변경

randomkey <- 현재 key 중에 랜덤으로 key 검색

exists 1116 <- 1116의 존재여부 검색 (1이면 존재, 0이면 존재하지 않음)

strlen 1111 <- key 1111의 value 길이

flushall <- 현재 저장되어 있는 모든 key 삭제

setex 1111 30 "JEAN CALM" <- EX는 expire(일정시간 동안만 저장)

ttl 1111 <- 현재 남은 시간 확인

mset 1113 "NoSQL User Group" 1115 "PIT" <- 여러 개 필드를 한번에 저장

mget 1113 1115 <- mset에 저장된 값을 한번에 다중 검색

mget 1113 1115 nonexisting <- nonexisting은 존재하지 않는 경워 nil 출력

mset seq_no 20231017 <- 연속번호 발행을 위한 key/value 저장

incr seq_no <- Incremental 증가값 +1

decr seq_no <- Decremental 증가값 -1

append 1115 " co." <- 현재 value에 value 추가

save <- 현재 입력한 key/value 값을 파일로 저장

info <- Redis 서버 설정 상태 조회 명령어

time <- 데이터 저장시간
```