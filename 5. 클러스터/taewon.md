# 9.엘라스틱서치와 루신 이야기
## 9.3.4 루씬 Flush, Commit, Merge
- 루씬 내부의 메모리에 전달된 데이터가 쌓임
- 일정 주기에 한번씩 세그먼트를 생성하고 디스크에 동기화 하는 작업이 Flush
- 실제로 데이터를 디스크에 쓰는 작업이 Commit
- 다수의 세그먼트를 하나로 합치는 작업이 Merge ([blog](http://blog.mikemccandless.com/2011/02/visualizing-lucenes-segment-merges.html))

## 9.3.5 엘라스틱서치 Refresh, Flush, Optimize API
### Refresh
- 루씬에서 Flush 하는 작업을 엘라스틱에서는 Refresh 라고 함
- default는 1초
- 대용량의 데이터를 색인할 경우에는 잠시 꺼두는 편이 이득
### Flush
- 루씬의 Commit을 실행한 후 새로운 Translog를 시작한다는 의미
- default는 5초에 한번
### Optimize API(force merge)
- 파편화 되어있는 세그먼트들을 하나로 합치는 작업

## 9.4 Translog
- 샤드에 변경사항이 생길 경우 그 내역을 기록하는 파일
- 엘라스틱서치의 Refresh가 발생되어도 그 내역은 게속 남아있음
- 엘라스틱서치의 Flush 가 일어나 관련 내용을 디스크에 쓴 이후에 Translog 의 내용이 지워짐
- Translog의 가장 큰 존재 이유는 장애 복구를 위함


# 12. 안정적인 클러스터 운영 노하우
## 12.1 노드 부트스트랩 과정의 이해
- 엘라스틱서치가 실행될 경우 설정 값이 이상있는지에 대한 검사를 수행함
- 개발모드, 운영모드 두가지 모드를 제공
### 개발모드
- 테스트 용도로 사용하기 위한 설정
- 부트스트랩 체크 과정이 무시
- 다른 노드와 클러스터를 구성할 수 없음
### 운영모드
- 실제 운영하는 클러스터를 위한 모드
- 부트스트랩 체크 과정 포함

## 12.4.1 클러스터 상태 측정
- _cat/health 를 이용하여 클러스터의 상태 확인 가능
- yellow 일 경우 하나 이상의 레플리카가 할당되지 않았거나 누락되었음
- red 일 경우 하나 이상의 프라이머리 샤드에 손상이 발생됨