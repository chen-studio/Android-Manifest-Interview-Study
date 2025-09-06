## system_server process
- OS(안드로이드 인스턴스) 당 1개만 존재.
- 안드로이드가 부팅될 때 Zygote 프로세스가 fork() 해서 system_server를 생성
- 이후 system_server 안에서 모든 프레임워크 레벨 시스템 서비스들이 실행됨
- 예: ActivityManagerService, PackageManagerService, WindowManagerService, PowerManagerService 등

### ActivityManagerService
- Android 시스템의 핵심 서비스 중 하나
- 각 애플리케이션 프로세스 관리, Task, Stack 관리 기능 수행
- 각 애플리케이션의 Activity, Service, BroadcastReceier등의 생명 주기 관리
- Android 8.0부터 백그라운드 제한 정책 수행 담당
- Android 시스템에서 사용 가능한 리소스를 모니터링 하고 메모리 부족시 Low Memory Killer를 통해 프로세스 종료
- ANR 감지 및 처리

### AMS 주요 구성 요소
- ProcessRecord: 각 애플리케이션의 프로세스 상태 정보를 담고 있으며 프로세스 UID, PID, 실행중인 Activity 목록 등 관리
- ActivityStackSupervisor: 여러 개의 ActivityStack관리, 실행 중인 Activity의 상태 추적 및 전환
- TaskRecrod: Task 단위의 실행 정보를 담고 있는 클래스, 여러개의 Activity를 그룹화 하여 관리
- BroadcastQueue: 브로드캐스트 메시지를 관리하는 큐. AMS는 시스템 및 앱에서 발생하는 브로드캐스트를 Foreground, Background 큐를 통해 관리
- OomAdjuster: 메모리 관리 및 OOM 조정 담당

### Activity Destroy 후 재생성시 데이터가 복구되는 원리
- Bundle 형태의 데이터는 내부적으로 바이트 스트림 형태로 직렬화되어 Parcel 객체 형태에 쓰임
- Binder API를 호출하여 시스템콜로 커널 Binder Driver에 데이터를 커널 메모리에 보관
- 커널 영역에서 system_server process로 해당 데이터 복사 (커널이 두 프로세스 사이에서 안전한 데이터 복사를 보장하기 위해 커널 영역을 거치게됨)
- AMS가 Parcel 데이터를 다시 Bundle로 역직렬화 하여 [ActivityRecord](https://cs.android.com/android/platform/superproject/+/android-latest-release:frameworks/base/services/core/java/com/android/server/wm/ActivityRecord.java;l=386?q=ActivityRecord&sq=) 객체 안에 보관
- system_server process는 앱 프로세스가 kill되어도 유지되기 때문에 데이터 저장 가능

