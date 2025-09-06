<img width="519" height="451" alt="image" src="https://github.com/user-attachments/assets/785aa40a-4c75-4bd1-bb66-1cc2a63fb663" />

### HAL (Hardware abstraction layer (HAL)): 하드웨어 추상화 계층
- Hardware Abstraction Layer (HAL)은 안드로이드의 Java API 프레임워크를 기기 하드웨어에 연결하는 표준
인터페이스를 제공합니다. 이는 라이브러리 모듈로 구성되며, 각 모듈은 카메라나
Bluetooth와 같은 특정 하드웨어 구성 요소에 맞춰져 있습니다. 프레임워크 API가
하드웨어 접근을 요청하면, 안드로이드 시스템은 해당 HAL 모듈을 동적으로 로드하여 처리합니다

#### HAL 예시 (카메라)
```.c
// camera_hal.c (C언어 기반)

#include <hardware/hardware.h>
#include <hardware/camera.h>

// 카메라 장치 열기
static int camera_device_open(const hw_module_t* module,
                              const char* id,
                              hw_device_t** device) {
    // 여기서 실제 카메라 하드웨어 초기화 코드 작성
    // (센서 켜기, 리소스 확보 등)
    return 0;
}

// 카메라 HAL 모듈 정의
static struct hw_module_methods_t camera_module_methods = {
    .open = camera_device_open
};

// HAL 모듈 정보
camera_module_t HAL_MODULE_INFO_SYM = {
    .common = {
        .tag = HARDWARE_MODULE_TAG,
        .id = CAMERA_HARDWARE_MODULE_ID,
        .name = "My Camera HAL",
        .methods = &camera_module_methods,
    },
    // 카메라 개수 반환 함수
    .get_number_of_cameras = my_get_number_of_cameras,
    // 카메라 정보 가져오기 함수
    .get_camera_info = my_get_camera_info,
};

```

-앱에서 Camera API 호출 → 안드로이드 프레임워크가 요청을 전달
- Camera Service가 HAL 모듈을 로드
- camera_device_open() 이 실행되면서 실제 카메라 하드웨어 제어 시작
- HAL이 드라이버를 통해 카메라 센서를 켬

### 안드로이드 런타임 및 코어 라이브러리 (Android Runtime (ART) 및 Core Libraries)
- Android Runtime (ART)은 Kotlin이나 Java에서 컴파일된 바이트코드를 사용하여 애플리케이션을 실행합니다.
- ART는 최적화된 성능을 위해 Ahead‑of‑Time (AOT) 및 Just‑in‑Time (JIT) 컴파일을 지원합니다. 핵심 라이브러리는 데이터 구조, 파일 조작,
스레딩 등을 위한 필수 API를 제공하여 앱 개발을 위한 포괄적인 환경을 제공합니다.
#### 관련 키워드 (ProfileGuidedOptimization, Cloud Profile, Baseline Profile, 앱 실행속도 단축방법 어떤것들이 있을지?)

### 안드로이드 시스템은 암시적 인텐트를 처리할 앱을 어떻게 결정하며, 적합한 애플리케이션을 찾지 못하면 어떻게 되나요?
- 여러개인 경우 Chooser를 통해 실행할 적절한 앱을 직접 선택
- 없는 경우 `ActivityNotFoundException` 발생


### PendingIntent의 플래그
- PendingIntent는 동작 방식과 시스템 또는 다른 컴포넌트와의 상호 작용 방식을 제어하는다양한 플래그를 지원합니다.

• FLAG_UPDATE_CURRENT: 기존 PendingIntent를 새 데이터로 업데이트합니다.

• FLAG_CANCEL_CURRENT: 새 PendingIntent를 만들기 전에 기존 PendingIntent를 취소합니
다.

• FLAG_IMMUTABLE: PendingIntent를 변경 불가능하게 만들어 수신자가 수정하는 것을
방지합니다. (Android 12+ 에서 중요)

• FLAG_ONE_SHOT: PendingIntent가 한 번만 사용될 수 있도록 보장합니다.

#### 사용사례
- FLAG_UPDATE_CURRENT: 메신저 앱에서 Notification 최신 메시지 내용 갱신 시 인텐트에 담은 extra만 새 값으로 변경
- FLAG_CANCEL_CURRENT: 기존 PendingIntent 취소 (AlarmManager 기존 알림 완전 취소 후 새로 생성)
- FLAG_IMMUTABLE: 수신자가 인텐트를 임의로 바꿀 수 없음 Android 12(API 31)+에서 강력 권장, 보안상 이유
- FLAG_ONE_SHOT: 확인, 취소 같은 한 번만 실행되어야 하는 동작 (SMS인증, OTP 열기 버튼 등)

### Parcel
- Parcelable을 사용해 직렬화, 역직렬화를 수행할 때 Parcel이란 객체를 볼 수 있음
- 안드로이드에서 객체를 직렬화(Serialization) 하기 위한 고성능 컨테이너
- 데이터를 바이트 형태로 변환해서 다른 프로세스나 컴포넌트에 전달시에 사용
- IBinder를 통해 보낼 수 있는 메시지의 컨테이너
- 안드로이드는 프로세스간 데이터 전달 (IPC)에서 바인더를 통해 Parcel 객체를 전달
- Parcelable은 Parcel에 객체를 write/read 할 수 있도록 해준다

### ContextWrapper
- ContextWrapper는 Context를 상속받고 있는 클래스로, Context 객체를 감싸서(wrapping) 래핑된 Context에 대한 호출을 위임하는 기능을 제공합니다.
- 이는 원본 Context의 동작을 수정하거나 확장하기 위한 중간 계층 역할을 합니다.
-  ContextWrapper를 사용하면 Context와 직접적인 소통을 하지 않고도 특정 기능을 커스텀할 수 있습니다.
#### ContextWrapper의 목적
- ContextWrapper는 기존 Context의 특정 동작을 개선시키거나 재정의해야 할 때 사용됩니다.
- Context에 대한 호출을 중개하고 추가 기능이나 커스텀 동작을 제공하는 목적으로 많이 사용됩니다.
사용 사례
1. 커스텀 컨텍스트: 앱 전체에 다른 테마를 적용하거나 리소스를 특수한 방식으로 처리
하는 등 특정 목적을 위한 커스텀 Context를 생성해야 하는 경우.
2. 동적 리소스 처리: 문자열, 치수(dimension) 또는 스타일(style)과 같은 리소스를
동적으로 제공하거나 수정하기 위해 Context를 래핑하는 경우.
3. 의존성 주입: Dagger나 Hilt와 같은 라이브러리는 의존성 주입을 위해 커스텀
ContextWrapper를 생성하고, 컴포넌트에 해당 ContextWrapper를 Context 타입으로 제공합니다

#### Custom Context Wrapper 예시
```.kt
class CustomThemeContextWrapper(base: Context) : ContextWrapper(base) {
  private var theme: Resources.Theme? = null

  override fun getTheme(): Resources.Theme {
    if (theme == null) {
      theme = super.getTheme()
      theme?.applyStyle(R.style.CustomTheme, true) // 커스텀 테마 적용
    }
    return theme!!
}

  override fun setTheme(themeResId: Int) {
    theme = null
    super.setTheme(themeResId)
  }
```

### Activity A, B 생명주기 흐름 순서
Activity A와 Activity B의 생명주기 흐름 순서

• Activity A의 초기 실행

– Activity A: 처음에 실행될 때 onCreate() ‑> onStart() ‑> onResume() 순서로 호출 되고, 사용자가 Activity A와 상호 작용할 수 있습니다.

• Activity A에서 Activity B로 이동

– Activity A: onPause(), UI를 일시 중지하고 시각적으로 보이는 상태 관련 리소스를해제합니다.

– Activity B: onCreate() ‑> onStart() ‑> onResume(), 포커스를 가져오고 포그라운드 Activity가 됩니다.

– Activity A: onStop(), Activity B가 Activity A를 완전히 오버레이 하는 순간호출됩니다.

• Activity B에서 Activity A로 돌아오는 경우

– Activity B: onPause()

– Activity A: onRestart() ‑> onStart() ‑> onResume(), 포커스를 다시 얻고 포그라운드로 돌아옵니다.

– Activity B: onStop() ‑> onDestroy()

#### Activity 재생성시 생명주기에 대해 대답해보세요
#### 재생성 시 데이터를 유지하는 방법
#### 앱이 백그라운드에서 오래 존재하여 프로세스 우선순위에서 밀려 종료되는 경우, 다시 실행시 해당 데이터는 어떻게 복구될 수 있나요? 프로세스가 종료된것이 아닌가요?
#### ActivityManagerService에 대해 아시나요?
#### 앱을 처음 실행할때, 디바이스에서 수행되는 동작을 설명해보세요
