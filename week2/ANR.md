## ANR이란
- ANR (Application Not Responding) 은 앱의 메인 스레드(UI 스레드)가 너무 오랫동안, 통상적으로 5초 이상 차단(blocking)될 때 발생하는 안드로이드의 시스템 오류
- 메인 스레드에서 5초 이상 걸리는 무거운 작업
- 장시간 실행되는 네트워크 또는 데이터베이스 등의 I/O 작업
- UI 스레드 차단 작업 (가령, UI 스레드에서의 동기 작업 등)

### ANR 예방방법
- 무거운 작업을 메인 스레드 밖으로 이동 (코루틴 Dispatcher IO, Default 등)
- 백그라운드 작업에 WorkManager 사용
- 데이터 불러오기 최적화 (Paging처리 등)
- Android Studio로 모니터링 및 프로파일링
- 블로킹(blocking) 호출 피하기
- 가벼운 지연작업에 코루틴 delay 또는 Handler.postDelayed 사용

#### Coroutine delay 원리?
#### postDelayed 동작 원리? (MessageQueue, Looper와 연관지어 설명)

#### 특정 앱이 5초이상 응답이 없을 때, 이 앱의 프로세스를 kill 하는 동작은 누가 수행하는지?
