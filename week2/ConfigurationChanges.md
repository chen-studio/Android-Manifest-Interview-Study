## ConfigurationChanges 해결방법
- onSavedInstanceState() + onRestoreInstanceState()
- Manifest에 android:configChanges 사용 + onConfigurationChanged()
- Jetpack ViewModel 사용
- rememberSaveable 사용

### 커스텀뷰(XML)에서 ConfigurationChanges 대응 방법
- 관련 키워드 (BaseSavedState, Parcelable)
```.kt
import android.content.Context
import android.os.Parcel
import android.os.Parcelable
import android.util.AttributeSet
import android.widget.EditText
import androidx.customview.view.AbsSavedState

class MyCustomEditText @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = android.R.attr.editTextStyle
) : EditText(context, attrs, defStyleAttr) {

    // 커스텀 상태 저장용 데이터
    var customText: String = ""
        set(value) {
            field = value
            setText(value)
        }

    // 1️⃣ 상태 저장
    override fun onSaveInstanceState(): Parcelable? {
        val superState = super.onSaveInstanceState()
        return SavedState(superState).apply {
            savedText = customText
        }
    }

    // 2️⃣ 상태 복원
    override fun onRestoreInstanceState(state: Parcelable?) {
        if (state !is SavedState) {
            super.onRestoreInstanceState(state)
            return
        }
        super.onRestoreInstanceState(state.superState)
        customText = state.savedText ?: ""
    }

    // 3️⃣ BaseSavedState 상속 클래스 정의
    private class SavedState : AbsSavedState {
        var savedText: String? = null

        constructor(superState: Parcelable?) : super(superState)

        constructor(parcel: Parcel, loader: ClassLoader?) : super(parcel, loader) {
            savedText = parcel.readString()
        }

        override fun writeToParcel(out: Parcel, flags: Int) {
            super.writeToParcel(out, flags)
            out.writeString(savedText)
        }

        companion object CREATOR : Parcelable.ClassLoaderCreator<SavedState> {
            override fun createFromParcel(parcel: Parcel): SavedState = SavedState(parcel)

            override fun newArray(size: Int): Array<SavedState?> {
                return arrayOfNulls(size)
            }
        }
    }
}
```
