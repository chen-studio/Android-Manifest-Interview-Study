## DeepLink 구성요소
- android:scheme: ex) myapp
- android:host: example.com
- android:pathPrefix: /deepLink (잘 사용안함)

### DeferredDeeplink
- 앱이 설치되지 않은 상태에서 링크 클릭 → Play Store로 이동 → 앱 설치 → 처음 실행 시 링크 정보 전달
- AppsFlyer의 OneLink 또는 Firebase의 DynamicLink
- OneLink SDK 또는 DynamicLink SDK 내부에 앱이 설치되었는지 판별하는 코드가 포함되어 있고 설치 완료 후 딥링크로 이동하도록 구현됨
