## ContentProvider
- 애플리케이션 간 데이터 공유를 위한 표준화된 인터페이스를 제공하는 컴포넌트
- 다른 앱이나 컴포넌트가 데이터를 삽입, 삭제하는데 사용할 수 있는 중앙 저장소 역할
- 내부 저장소 구조를 노출하지 않고 다른 앱에 데이터를 제공하려는 경우 유용함

### ContentProvider 목적
- 데이터 접근 로직을 캡슐화 하여 앱 간 데이터 공유를 더 쉽고 안전하게 만듬
- SQLite 기반

### 각 ContentProvider는 식별자(Authority)와 Path, ID로 구성됨
- ContentProvider를 구현할 땐 아래 메서드를 구현해야 함
#### query(): Cursor?
- 데이터 검색용, 요청한 데이터셋을 행(row)/열(column) 형태로 담은 Cursor를 반환
- null 반환: 데이터가 없거나, URI가 잘못되었을 때
#### insert(): Uri?
- 새로 추가된 행(row)의 고유 URI를 반환
- 일반적으로 Uri.withAppendedPath(baseUri, newRowId.toString()) 형태, null시 추가 실패
#### update(): Int
- 실제로 수정된 행(row)의 개수를 반환
- 0 반환: 조건에 맞는 데이터가 없거나 실패
#### delete(): Int
- 실제로 삭제된 행(row)의 개수를 반환
- 0 반환: 조건에 맞는 데이터가 없거나 실패
#### getType(): String?
- URI가 가리키는 데이터의 MIME 타입 반환
- 규칙은 보통 두 가지:
- 여러 행일 때: "vnd.android.cursor.dir/vnd.<authority>.<path>"
- 단일 행일 때: "vnd.android.cursor.item/vnd.<authority>.<path>"
- null 반환: URI가 잘못되었을 때

### ContentProvider 사용 사례
- 다른 애플리케이션 간 데이터 공유
- 앱 시작 시 컴포넌트 또는 리소스 초기화
- 연락처, 미디어 파일 또는 앱별 데이터와 같은 구조화된 데이터에 대한 접근 제공
- 연락처 앱이나 파일 선택기와 같은 안드로이드 시스템 기능과의 통합 활성화
- 세분화된 보안 제어를 통한 데이터 접근 허용

## ContentResolver
- 이렇게 작성된 ContentProvider를 다른앱 또는 앱 내부에서 사용하려면 ContentResolver를 사용함
- 대부분 실제 앱에서 ContentProvider 작성 보다는 ContentResolver를 활용하여 데이터를 접근하는 사례가 많음

```.kt
val values = ContentValues().apply {
  put("name" ,"John Doe")
  put("email", "johndoe@example.com")
}
contentResolver.insert(Uri.parse("content://com.example.myapp.provider/users"), values)
```

