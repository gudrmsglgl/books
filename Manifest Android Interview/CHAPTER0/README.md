# Chapter 0 

목차 <br>
[Category 0: The Android Framework](#id-section1)
<br>
- [1. What is Intent?](#id-section2)<br>
- [2. What is the purpose of Pending Intent?](#id-section3)<br>
- [3. What are the differences between Serializable and Parcelable](#id-section4)<br>
- [4. What is Context and what types of Context exist?](#id-section5)<br>
- [5. What is Application class?](#id-section6)<br>
- [6. What is the purpose of the AndroidManifest file?](#id-section7)<br>
- [7. Describe the Activity lifecycle](#id-section8)<br>

<div id='id-section1'/>
  
## Category 0: The Android Framework

Android SDK, Activity, Fragment, Android UIs, Jetpack libraries 를 알아보는 섹션

현업에서 실질적으로 개발 시 필요한 요소들을 배울 수 있음


<br>
<div id='id-section2'/>
  
### 1. What is Intent?

**Intent(인텐트)** 는 </br>
안드로이드에서 수행할 작업에 대한 추상적인 설명

인텐트는 
- 액티비티
- 서비스
- 브로드캐스트 리시버 등 

컴포넌트 간의 통신을 가능하게 하는 메시지 객체로 사용



#### 인텐트의 종류


**📌 명시적 인텐트 (Explicit Intent)**
   - **정의:** 실행할 컴포넌트(예: 액티비티, 서비스)를 명확하게 지정하는 인텐트
   - **사용 예시:** 앱 내에서 특정 액티비티로 이동할 때 사용
   - **시나리오:** 같은 앱 내에서 한 액티비티에서 다른 액티비티로 전환할 때 명시적 인텐트를 사용

```kotlin
val intent = Intent(this, TargetActivity::class.java)
startActivity(intent)
```


**📌 암시적 인텐트 (Implicit Intent)**
   - **정의:** 실행할 컴포넌트를 명확하게 지정하지 않고, 수행할 작업(액션)만 선언하는 인텐트
   - **사용 예시:** 다른 앱이나 시스템 컴포넌트가 처리할 수 있는 작업을 요청할 때 사용
   - **시나리오:** 웹 페이지를 브라우저로 열거나, 다른 앱과 콘텐츠를 공유할 때 암시적 인텐트를 사용. 시스템이 해당 작업을 처리할 수 있는 앱을 찾아 실행함

```kotlin
val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://www.example.com"))
startActivity(intent)
```

#### 정리

- **명시적 인텐트**는 앱 내부에서 대상 컴포넌트가 명확할 때(예: 특정 액티비티로 이동) 사용
- **암시적 인텐트**는 외부 앱이나 시스템의 다른 컴포넌트가 처리할 수 있는 작업(예: 웹페이지 열기, 콘텐츠 공유 등)에 사용하며, 대상 컴포넌트를 직접 지정하지 않음



#### 📌 인텐트 필터 (Intent Filter)

- **정의:** 인텐트 필터는 안드로이드에서 특정 인텐트(예: 링크 열기, 브로드캐스트 수신 등)에 앱 컴포넌트(액티비티, 서비스, 브로드캐스트 리시버)가 어떻게 반응할지 정의 
AndroidManifest.xml 파일에 선언하며, 각 인텐트 필터는 처리할 수 있는 액션, 카테고리, 데이터 타입 등을 지정

- **동작 방식:** 암시적 인텐트가 발생하면, 시스템은 인텐트의 속성과 앱에 정의된 인텐트 필터를 비교하여 적합한 컴포넌트를 찾고 -> 여러 컴포넌트가 일치할 경우, 사용자가 원하는 앱을 선택할 수 있도록 선택 창(chooser dialog)이 표시
- **의의:** 인텐트 필터를 올바르게 정의하면, 앱이 다른 앱 및 시스템 컴포넌트와 원활하게 상호작용할 수 있어 기능성이 향상

---

#### 실전 질문 (Practical Questions)

**Q) 명시적 인텐트와 암시적 인텐트의 핵심 차이점은 무엇이며, 각각 어떤 상황에서 사용하나요?**  
- **명시적 인텐트**는 실행할 컴포넌트를 명확하게 지정할 때 사용합니다. 예를 들어, 같은 앱 내에서 특정 액티비티로 이동할 때 사용합니다.  
- **암시적 인텐트**는 실행할 컴포넌트를 지정하지 않고, 시스템이나 다른 앱이 처리할 수 있는 작업을 요청할 때 사용합니다. 예를 들어, 웹페이지 열기, 사진 공유 등 외부 앱과의 연동이 필요한 경우에 사용합니다.

**Q) 안드로이드 시스템은 암시적 인텐트를 어떤 방식으로 처리하며, 적합한 앱이 없을 때는 어떻게 되나요?**  
- 시스템은 암시적 인텐트가 발생하면, 인텐트의 속성(액션, 카테고리, 데이터 등)과 앱에 등록된 인텐트 필터를 비교하여 처리 가능한 컴포넌트를 찾습니다.  
- 여러 앱이 해당 인텐트를 처리할 수 있으면, 사용자에게 선택 창(chooser dialog)을 띄워 원하는 앱을 선택하게 합니다.  
- 만약 처리할 수 있는 앱이 하나도 없다면, ActivityNotFoundException이 발생하며, 인텐트를 처리할 수 없다는 메시지가 표시됩니다.


<br>
<div id='id-section2'/>
  
### 2. What is the purpose of Pending Intent?

**PendingIntent(펜딩 인텐트)** 는 </br>
다른 앱이나 시스템 컴포넌트가 미리 정의된 인텐트를 나중에 대신 실행할 수 있도록 <br>**✌🏻권한을 위임✌🏻**하는 특별한 인텐트 객체

앱의 생명주기와 무관하게 알림(Notification)이나 서비스와의 상호작용 등, <br>
앱 외부에서 특정 작업을 예약하거나 실행해야 할 때 주로 사용

- **주요 사용 예시:**  
  - 알림(Notification)에서 사용자가 버튼 클릭 > 특정 액티비티나 브로드캐스트를 실행
  - 백그라운드 서비스에서 작업 예약
  - 앱 외부에서 앱의 특정 기능을 실행해야 할 때

```kotlin
val pendingIntent = 
PendingIntent.getActivity(
    context, 
    0, 
    Intent(context, TargetActivity::class.java), 
    PendingIntent.FLAG_UPDATE_CURRENT
)
```

#### 주요 특징 (Key Features of PendingIntent)

- **PendingIntent**는 일반 인텐트를 감싸는 래퍼 역할을 하며, 앱의 생명주기와 무관하게 인텐트가 유지될 수 있도록 해준다.
- 인텐트 실행 권한을 다른 앱이나 시스템 서비스에 위임할 수 있다.
- 액티비티, 서비스, 브로드캐스트 등 다양한 형태로 생성할 수 있다.
- 생성은 `PendingIntent.getActivity()`, `PendingIntent.getService()`, `PendingIntent.getBroadcast()` 등의 팩토리 메서드를 사용한다.

```kotlin
val intent = Intent(this, MyActivity::class.java)
val pendingIntent = PendingIntent.getActivity(
    this,
    0,
    intent,
    PendingIntent.FLAG_UPDATE_CURRENT
)
val notification = NotificationCompat.Builder(this, CHANNEL_ID)
    .setContentTitle("Title")
    .setContentText("Content")
    .setSmallIcon(R.drawable.ic_notification)
    .setContentIntent(pendingIntent) // 알림 클릭 시 실행
    .build()
NotificationManagerCompat.from(this).notify(NOTIFICATION_ID, notification)
```

- PendingIntent는 다양한 플래그로 동작 방식을 제어할 수 있다.
  - `FLAG_UPDATE_CURRENT`: 기존 PendingIntent가 있으면 데이터를 갱신
  - `FLAG_CANCEL_CURRENT`: 기존 PendingIntent를 취소 후 새로 생성
  - `FLAG_IMMUTABLE`: PendingIntent를 불변으로 만들어 수신자가 수정 불가
  - `FLAG_ONE_SHOT`: 한 번만 사용할 수 있도록 제한

**주요 사용 예시**
- **알림(Notification):** 사용자가 알림을 클릭하면 특정 액티비티 실행
- **알람(Alarm):** AlarmManager를 통해 예약 작업 실행
- **서비스/브로드캐스트:** ForegroundService나 BroadcastReceiver에 작업 위임

#### 보안 고려사항 (Security Considerations)

- PendingIntent를 생성할 때는 항상 `FLAG_IMMUTABLE` 플래그를 설정하는 것이 권장
- 악의적인 앱이 내부 인텐트를 수정하는 것을 방지
- 특히 Android 12(API 31) 이상에서는 일부 상황에서 `FLAG_IMMUTABLE`이 필수

---

#### 요약 (Summary)

- PendingIntent는 앱이 실행 중이 아니더라도 시스템 컴포넌트나 다른 앱과 안전하게 통신할 수 있게 해주는 안드로이드의 핵심 메커니즘
- 플래그와 권한을 적절히 관리하면, 예약 작업이나 백그라운드 작업을 안전하고 효율적으로 처리 가능

---

#### 실전 질문 (Practical Questions)

**Q) PendingIntent란 무엇이며, 일반 Intent와의 차이점은? 그리고 PendingIntent가 꼭 필요한 시나리오를 예시로 들어 설명하라.**  
- **PendingIntent**는 일반 인텐트를 감싸는 래퍼 객체로, 다른 앱이나 시스템이 나중에 대신 실행할 수 있도록 권한을 위임하는 역할을 한다.  
- 일반 Intent는 즉시 실행되지만, PendingIntent는 예약해뒀다가 외부에서 필요할 때 실행할 수 있다.
- **예시:** 알림(Notification)에서 사용자가 클릭했을 때 특정 액티비티를 실행하려면 PendingIntent가 필요하다. 앱이 백그라운드에 있거나 종료된 상태여도 시스템이 PendingIntent를 통해 지정된 작업을 대신 실행해준다.


<br>
<div id='id-section4'/>
  
### 3. What are the differences between Serializable and Parcelable

#### Serializable
- **자바 표준 인터페이스:** 객체를 바이트 스트림으로 변환해 액티비티 간 전달하거나 파일로 저장할 때 사용
- **리플렉션 기반:** 런타임에 클래스와 필드를 동적으로 검사해 직렬화
- **성능:** 리플렉션 때문에 느리고, 임시 객체가 많이 생성되어 메모리 오버헤드가 큼
- **사용 예시:** 성능이 중요하지 않거나, 안드로이드가 아닌 자바 코드에서 데이터 전달할 때

#### Parcelable
- **안드로이드 전용 인터페이스:** 안드로이드 컴포넌트 간 고성능 IPC(프로세스 간 통신)를 위해 설계됨
- **성능:** 리플렉션을 사용하지 않아 빠르고, 임시 객체 생성이 적어 GC 부담이 적음
- **사용 예시:** 액티비티, 서비스 등 안드로이드 컴포넌트 간 데이터 전달 시 성능이 중요할 때 우선적으로 사용

</br>

최신 안드로이드 개발에서는 **kotlin-parcelize** 플러그인을 사용하면 Parcelable 구현이 자동으로 생성
- 클래스에 `@Parcelize` 어노테이션만 붙이면, 필요한 Parcelable 코드가 자동으로 만들어진다.
- `writeToParcel`이나 `CREATOR` 구현 없이도 바로 사용할 수 있어, 코드가 깔끔하고 가독성이 높아진다.

```kotlin
import kotlinx.parcelize.Parcelize
import android.os.Parcelable

@Parcelize
class User(val firstName: String, val lastName: String, val age: Int) : Parcelable
```

💡 추가 팁:  

클래스에 `@Parcelize`를 사용할 때, <br>
프로퍼티가 기본 타입이 아니거나 이미 `@Parcelize`가 적용된 타입이 아니라면  
```
"Type is not directly supported by ‘Parcelize’. Annotate the parameter type with ‘@RawValue’ if you want it to be serialized using ‘writeValue()’."
```  
라는 에러가 발생할 수 있다.

이유는 Parcelize 컴파일러가 모든 프로퍼티를 직렬화할 때, <br> 
지원하지 않는 타입은 직접 처리해야 하기 때문.  
해결 방법은  
- 복잡하거나 커스텀 타입은 직접 `@Parcelize`를 적용하거나  
- `@RawValue`로 명시해 수동 직렬화를 하도록 표시하면 된다.

---

#### 실전 질문 (Practical Questions)

**Q) 안드로이드에서 Serializable과 Parcelable의 주요 차이점은 무엇이며, 컴포넌트 간 데이터 전달에 왜 Parcelable이 더 선호되는가?**

- **Serializable**은 자바 표준 인터페이스로, 객체를 바이트 스트림으로 변환할 때 리플렉션을 사용해 느리고 메모리 오버헤드가 크다.
- **Parcelable**은 안드로이드 전용 인터페이스로, 리플렉션 없이 빠르게 동작하며 임시 객체 생성이 적어 성능이 뛰어나다.
- 컴포넌트(Activity, Service 등) 간 데이터 전달 시 성능이 중요하므로, 대부분의 안드로이드 개발에서는 **Parcelable**이 선호된다.
- 최신 개발에서는 `@Parcelize`를 활용해 Parcelable 구현을 자동화할 수 있어, 코드가 간결하고 유지보수가 쉬워진다.

---

💡 Pro Tips for Mastery: Parcel과 Parcelable

- **Parcel**은 안드로이드에서 컴포넌트(Activity, Service, BroadcastReceiver 등) 간 고성능 IPC(프로세스 간 통신)를 위해 사용되는 컨테이너 클래스다.
- Parcel은 데이터를 평탄화(marshaling)해서 IPC 경계 너머로 전달하고, 다시 복원(unmarshaling)할 수 있게 해준다.
- 평탄화된 데이터와 IBinder 객체 참조를 함께 전달할 수 있으며, 빠른 IPC 전송에 최적화되어 있다.
- Parcel은 일반적인 직렬화 용도가 아니라, 일시적인 데이터 전달에만 사용해야 한다. 내부 구현이 변경될 수 있으므로 영구 저장에는 적합하지 않다.
- 다양한 API로 기본 타입, 배열, Parcelable 객체를 읽고 쓸 수 있다. Parcelable 객체는 클래스 정보를 쓰지 않으므로, 읽는 쪽에서 타입을 미리 알아야 효율적으로 처리 가능하다.

- **Parcelable**은 안드로이드 전용 인터페이스로, 객체를 Parcel에 평탄화/복원할 수 있게 해준다.  
  컴포넌트 간 복잡한 데이터 전달에 적합하며, Parcel과 함께 사용해 빠르고 효율적인 IPC를 구현할 수 있다.



<br>
<div id='id-section5'/>

### 4. What is Context and what types of Context exist?

**Context**
- 앱의 환경이나 상태 나타냄
- 리소스·클래스·시스템 서비스 등에 접근할 수 있게 해주는 안드로이드의 핵심 객체
- 컴포넌트가 시스템과 연결되어 리소스, 데이터베이스, 서비스 등을 사용할 때 반드시 필요


**Application Context**
- 앱 전체 생명주기에 연결된 컨텍스트
- `getApplicationContext()`로 얻을 수 있음
- 전역적으로 오래 살아야 하는 객체
- 라이브러리 초기화
- 앱 전체에서 사용하는 리소스 접근
- 브로드캐스트 리시버 등록 등에 사용

**Activity Context**
- 액티비티의 생명주기에 연결된 컨텍스트 (`this`로 참조)
- UI 생성/수정, 다른 액티비티 시작
- 액티비티에 종속된 리소스·테마 접근 등에 사용


**Service Context**
- 서비스의 생명주기에 연결된 컨텍스트
- 백그라운드 작업(네트워크, 음악 재생 등)에 사용
- 서비스에서 필요한 시스템 서비스 접근에 활용

**Broadcast Context**
- 브로드캐스트 리시버가 호출될 때 제공되는 컨텍스트
- 수명이 짧고, 특정 브로드캐스트에 응답할 때 사용
- 오래 걸리는 작업에는 사용하지 않는 것이 좋음

#### Context의 주요 활용 예시

- **리소스 접근:** getString(), getDrawable() 등으로 문자열, 이미지, 크기 등 리소스에 접근
- **레이아웃 인플레이트:** LayoutInflater를 통해 XML 레이아웃을 뷰로 변환
- **액티비티/서비스 시작:** startActivity(), startService() 등 컴포넌트 실행에 필요
- **시스템 서비스 접근:** getSystemService()로 ClipboardManager, ConnectivityManager 등 시스템 서비스 사용
- **DB/SharedPreferences 접근:** SQLite 데이터베이스, SharedPreferences 등 영속 저장소 접근에 사용

---

#### 실전 질문 (Practical Questions)

**Q) 안드로이드에서 올바른 타입의 Context를 사용하는 것이 왜 중요한가? 그리고 Activity Context를 오래 참조할 때 발생할 수 있는 위험은?**

- 올바른 Context를 사용하지 않으면 리소스 접근 오류, 앱 크래시, 메모리 누수 등 다양한 문제가 발생할 수 있다.
- 특히 Activity나 Fragment Context를 생명주기보다 오래 참조하면, 해당 컴포넌트가 종료되어도 메모리가 해제되지 않아 메모리 누수가 발생한다.
- 전역적으로 오래 사용하는 객체에는 Application Context를 사용해야 하며, UI나 액티비티에 종속된 작업에는 Activity Context를 사용해야 한다.

---

💡 Pro Tips for Mastery: Context 사용 시 주의점

- Context를 잘못 사용하면 메모리 누수, 크래시, 리소스 관리 실패 등 심각한 문제가 생길 수 있다.
- 가장 흔한 실수는 Activity나 Fragment Context를 싱글톤, static 변수, 오래 살아있는 객체에 저장하는 것.  
  이렇게 하면 해당 컴포넌트가 종료되어도 Context가 해제되지 않아 메모리 누수가 발생한다.

**예시: 아래 코드는 메모리 누수를 유발한다**


- 싱글톤이나 오래 살아있는 객체에 Activity Context를 저장하면 메모리 누수가 발생한다.
```kotlin
object Singleton {
    var context: Context? = null // 이렇게 하면 메모리 누수 발생
}
```
- 오래 참조해야 하는 경우에는 Application Context를 사용해야 한다.
```kotlin
object Singleton {
    lateinit var applicationContext: Context
}
```

- Context 타입을 잘못 사용하면 예기치 않은 동작이 발생할 수 있다.
  - **UI 관련 작업(레이아웃 인플레이트, 다이얼로그 등)** 에는 Activity Context를 사용
  - **앱 전체에서 사용하는 작업(라이브러리 초기화 등)** 에는 Application Context를 사용

- 잘못된 Application Context 사용 예시:
```kotlin
val dialog = AlertDialog.Builder(context.getApplicationContext()) // 잘못된 사용
```
- 올바른 Activity Context 사용 예시:
```kotlin
val dialog = AlertDialog.Builder(activityContext) // 올바른 사용
```

- 컴포넌트가 종료된 후 해당 Context를 사용하면 크래시나 예측 불가한 동작이 발생할 수 있다.
```kotlin
val button = Button(activity)
activity.finish() // 액티비티가 종료된 뒤에도 버튼이 참조를 유지하면 위험
```

- Context는 주로 메인 스레드에서 사용해야 하며, 백그라운드 스레드에서 UI 관련 리소스에 접근하면 크래시가 발생할 수 있다.
```kotlin
viewModelScope.launch {
    val data = fetchData()
    withContext(Dispatchers.Main) {
        Toast.makeText(context, "Data fetched", Toast.LENGTH_SHORT).show()
    }
}
```
--- 

<br>

💡 Pro Tips for Mastery: ContextWrapper란?

- **ContextWrapper**는 기존 Context 객체를 감싸서, 모든 호출을 내부 Context로 위임하는 안드로이드의 기본 클래스다.
- 중간 레이어 역할을 하며, 원래 Context의 동작을 수정하거나 확장할 수 있다.
- ContextWrapper를 사용하면 원본 Context를 변경하지 않고 기능을 커스터마이즈할 수 있다.

#### ContextWrapper의 목적

- 기존 Context의 특정 동작을 확장하거나 오버라이드할 때 사용
- 원본 Context로의 호출을 가로채서, 추가 기능이나 커스텀 동작을 제공할 수 있음

#### 주요 활용 예시

- **커스텀 Context:** 특정 목적(테마 변경, 리소스 처리 등)을 위해 커스텀 Context를 만들 때
- **동적 리소스 처리:** Context를 감싸서 문자열, 크기, 스타일 등 리소스를 동적으로 제공/수정할 때
- **의존성 주입:** Dagger, Hilt 같은 라이브러리에서 컴포넌트에 커스텀 Context를 붙일 때 ContextWrapper를 활용


#### ContextWrapper 예시

아래 코드는 ContextWrapper를 활용해 커스텀 테마를 적용하는 방법을 보여준다.

```kotlin
class CustomThemeContextWrapper(base: Context) : ContextWrapper(base) {
    override fun getTheme(): Resources.Theme {
        val theme = super.getTheme()
        theme.applyStyle(R.style.CustomTheme, true) // 커스텀 테마 적용
        return theme
    }
}
```

이 커스텀 ContextWrapper를 액티비티에서 사용할 수 있다:

```kotlin
class MyActivity : AppCompatActivity() {
    override fun attachBaseContext(newBase: Context) {
        super.attachBaseContext(CustomThemeContextWrapper(newBase))
    }
}
```

이 예시에서 CustomThemeContextWrapper는 액티비티에 특정 테마를 적용해, 기본 Context의 동작을 오버라이드한다.

**주요 장점**
- **재사용성:** 커스텀 로직을 래퍼 클래스에 캡슐화해 여러 컴포넌트에서 재사용 가능
- **캡슐화:** 원본 Context 구현을 변경하지 않고 동작을 확장/수정
- **호환성:** 기존 Context 객체와 문제없이 동작하며, 하위 호환성 유지

---
<br>

💡 Pro Tips for Mastery: Activity에서 this와 baseContext의 차이

**this (Activity에서)**
- 현재 액티비티 인스턴스를 가리킴
- Activity는 ContextWrapper의 서브클래스이므로, this는 액티비티의 컨텍스트 역할을 함
- 생명주기 관리, UI와의 상호작용 등 액티비티에 종속된 작업에 사용
- 예시: 액티비티 시작, 다이얼로그 표시 등
```kotlin
val intent = Intent(this, AnotherActivity::class.java)
startActivity(intent)

val dialog = AlertDialog.Builder(this)
    .setTitle("Example")
    .setMessage("This dialog is tied to this Activity instance.")
    .show()
```

**baseContext (Activity에서)**
- 액티비티가 기반하는 "베이스" 컨텍스트
- ContextWrapper의 getBaseContext()로 접근
- 주로 커스텀 ContextWrapper 구현이나 원본 컨텍스트가 필요할 때 사용
- 예시: 시스템 서비스 접근 등
```kotlin
val systemService = baseContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE)
```

**주요 차이점**
- **범위:** this는 액티비티 인스턴스와 생명주기를 포함, baseContext는 액티비티가 감싸고 있는 하위 컨텍스트
- **용도:** this는 UI/생명주기 관련 작업에, baseContext는 Context의 코어 기능이나 커스텀 ContextWrapper에서 사용
- **계층:** baseContext는 액티비티의 기반이 되는 컨텍스트로, this의 추가 기능(생명주기, UI 등)을 우회함

---

#### 요약 (Summary)

- Activity에서 `this`는 현재 액티비티 인스턴스를 가리키며, 생명주기와 UI 관련 기능이 포함된 고수준 컨텍스트를 제공한다.
- `baseContext`는 액티비티가 기반하는 하위 컨텍스트로, 주로 커스텀 ContextWrapper 구현 등 고급 상황에서 사용된다.
- 일반적으로 안드로이드 개발에서는 `this`를 많이 사용하지만, `baseContext`의 개념을 이해하면 디버깅이나 모듈화, 재사용성 높은 컴포넌트 개발에 도움이 된다.

<br>
<div id='id-section6'/>

### 5. What is Application class?

**Application 클래스**는 <br>
안드로이드에서 앱의 전역 상태와 생명주기를 관리하는 기본 클래스

- 앱의 진입점 역할<br>
- 액티비티·서비스·브로드캐스트 리시버보다 먼저 초기화
- 앱 전역 컨텍스트를 제공 > 공용 리소스 초기화에 적합하다.

#### Application 클래스의 목적

- 앱 전체에서 사용할 전역 상태 관리
- 앱 시작 시점에 라이브러리, 의존성, 공용 리소스 초기화
- 여러 액티비티/서비스에서 공유해야 하는 데이터나 객체 관리

#### 주요 메서드

- **onCreate()**  
  앱 프로세스가 생성될 때 호출.  
  데이터베이스, 네트워크 라이브러리, <br>분석 툴 등 앱 전체에서 필요한 의존성 초기화에 사용  
  앱 생명주기 동안 한 번만 호출됨

- **onTerminate()**  
  에뮬레이터 환경에서 앱이 종료될 때 호출.  
  실제 디바이스에서는 호출되지 않으므로, 종료 처리 로직에 의존하면 안 됨

- **onLowMemory() / onTrimMemory()**  
  시스템에서 메모리 부족을 감지하면 호출  
  onLowMemory()는 구버전 API에서 사용<br>onTrimMemory()는 앱의 메모리 상태에 따라 더 세밀하게 대응 가능

#### 참고

- 기본적으로 모든 앱은 Application 클래스를 사용하며, AndroidManifest.xml에 커스텀 클래스를 지정하면 직접 구현 가능

#### Application 클래스의 주요 활용 예시

- **글로벌 리소스 관리:** 데이터베이스, SharedPreferences, 네트워크 클라이언트 등 앱 전체에서 사용할 리소스를 한 번만 설정해 재사용
- **컴포넌트 초기화:** Firebase Analytics, Timber 등 앱 전역에서 필요한 라이브러리나 툴을 앱 시작 시점에 초기화
- **의존성 주입:** Dagger, Hilt 같은 프레임워크를 앱 전체에 적용해 의존성 관리

#### Best Practices

- onCreate()에서 오래 걸리는 작업은 피해서 앱 실행 지연을 방지
- Application 클래스를 불필요한 로직의 집합소로 사용하지 말고, 글로벌 초기화와 리소스 관리에만 집중
- 공유 리소스 관리 시 스레드 안전성(thread safety) 확보

---

#### 요약 (Summary)

- Application 클래스는 앱 전체에서 사용할 리소스와 설정을 초기화·관리하는 중앙 역할을 한다.
- 전역 설정에만 집중해 불필요한 복잡성을 피하는 것이 좋다.

---

#### 실전 질문 (Practical Questions)

**Q) Application 클래스의 목적은 무엇이며, 생명주기와 리소스 관리 측면에서 Activity와 어떻게 다른가?**

- Application 클래스는 앱 전체에서 사용할 리소스와 의존성을 초기화하고, 전역 상태를 관리하는 역할을 한다.
- Application은 앱 프로세스가 시작될 때 한 번만 생성되어 앱이 종료될 때까지 유지된다. 반면, Activity는 화면 단위로 생성·소멸이 반복된다.
- Application 컨텍스트는 앱 전체에서 사용 가능하며, Activity 컨텍스트는 UI와 생명주기에 종속된다.
- Application 클래스는 글로벌 리소스 관리와 초기화에 집중하고, Activity는 UI와 사용자 상호작용에 집중한다.



<br>
<div id='id-section7'/>

### 6. What is the purpose of the AndroidManifest file?

**AndroidManifest.xml**는 안드로이드 프로젝트에서 앱과 운영체제(OS) 사이의 연결고리 역할을 하는 핵심 설정 파일이다.  
앱의 구성 요소, 권한, 요구 기능, 메타데이터 등 앱 실행에 필요한 정보를 시스템에 알려준다.

#### 주요 역할

- **앱 컴포넌트 선언:**  
  액티비티, 서비스, 브로드캐스트 리시버, 콘텐츠 프로바이더 등 앱의 주요 컴포넌트를 등록해 시스템이 실행/연동할 수 있도록 함

- **권한 선언:**  
  앱이 필요한 권한(INTERNET, ACCESS_FINE_LOCATION, READ_CONTACTS 등)을 명시해, 사용자에게 리소스 접근 여부를 알리고 승인받음

- **하드웨어/소프트웨어 요구사항:**  
  카메라, GPS, 특정 화면 크기 등 앱이 필요로 하는 기능을 지정해, 플레이스토어에서 지원되지 않는 기기를 필터링

- **앱 메타데이터:**  
  패키지명, 버전, 최소/타겟 API 레벨, 테마, 스타일 등 앱 설치와 실행에 필요한 기본 정보 제공

- **인텐트 필터:**  
  컴포넌트가 ✌🏻어떤 인텐트에 반응✌🏻할지 정의(예: 링크 열기, 콘텐츠 공유 등), 다른 앱과의 상호작용 가능

- **앱 설정 및 구성:**  
  메인 런처 액티비티 지정, 백업 설정, 테마 지정 등 앱의 동작 방식과 UI를 제어하는 설정 포함


--- 

#### 실전 질문 (Practical Questions)

**Q) AndroidManifest의 인텐트 필터가 앱 상호작용을 어떻게 가능하게 하며, 액티비티 클래스가 AndroidManifest에 등록되지 않으면 어떻게 되는가?**

- **인텐트 필터**는 AndroidManifest.xml에 컴포넌트(액티비티, 서비스 등)가 어떤 인텐트에 반응할지 정의한다.  
  이를 통해 외부 앱이나 시스템이 해당 컴포넌트를 호출할 수 있어, 앱 간 상호작용(예: 링크 열기, 공유 등)이 가능해진다.
- 인텐트 필터가 없으면 암시적 인텐트로 해당 컴포넌트를 실행할 수 없고, 앱의 기능 확장성이 제한된다.
- **액티비티 클래스가 AndroidManifest에 등록되지 않으면**  
  시스템이 해당 액티비티를 인식하지 못해, 인텐트로 액티비티를 실행하려고 할 때 `ActivityNotFoundException`이 발생한다.  
  즉, 앱에서 해당 액티비티를 정상적으로 실행할 수 없다.


<br>
<div id='id-section8'/>

### 7. Describe the Activity lifecycle


안드로이드의 Activity 생명주기는 <br>
액티비티가 생성부터 소멸까지 거치는 여러 상태를 의미한다.  
각 상태를 이해하면 리소스 관리, 사용자 입력 처리, 안정적인 앱 동작에 도움이 된다.

#### 주요 단계

- **onCreate()**
  - 액티비티가 **✌🏻처음 생성✌🏻**될 때 호출
  - UI 초기화, 컴포넌트 설정, 저장된 상태 복원 등
  - 생명주기 동안 한 번만 호출(단, 액티비티가 완전히 소멸 후 재생성되면 다시 호출)

- **onStart()**
  - 액티비티가 사용자에게 **✌🏻보이기 시작✌🏻**할 때 호출(not interaction)
  - onCreate() 이후, onResume() 이전에 실행

- **onRestart()**
  - 액티비티가 중지(onStop)됐다가 다시 시작될 때 호출
  - onStart() 전에 실행

- **onResume()**
  - 액티비티가 포그라운드에 올라와 사용자와 상호작용 가능한 상태
  - UI 업데이트, 애니메이션, 입력 리스너 재개 등

- **onPause()**
  - 다른 액티비티나 다이얼로그가 화면을 일부 가릴 때 호출
  - 액티비티는 **✌🏻여전히 보이✌🏻**지만 포커스를 잃음
  - 애니메이션, 센서 업데이트, 데이터 저장 등 일시 중지 작업에 사용

- **onStop()**
  - 액티비티가 더 이상 사용자에게 보이지 않을 때 호출
  - 백그라운드 작업, 무거운 리소스 해제 등

- **onDestroy()**
  - 액티비티가 완전히 소멸되기 직전에 호출
  - 모든 리소스 정리 및 최종 해제 작업

--- 
#### 실전 질문 (Practical Questions)

**Q) onPause()와 onStop()의 차이점은 무엇이며, 리소스 집약적인 작업을 처리할 때 각각 어떤 상황에서 사용해야 하나?**

- **onPause()** 는 액티비티가 여전히 화면에 보이지만 포커스를 잃었을 때 호출된다.  
  예를 들어, 다이얼로그가 뜨거나 다른 액티비티가 부분적으로 화면을 가릴 때 발생한다.  
  UI 업데이트, 센서, 애니메이션 등 일시적으로 중단해야 하는 작업에 적합하다.

- **onStop()** 은 액티비티가 완전히 화면에서 사라질 때 호출된다.  
  백그라운드 작업, 네트워크 연결, 무거운 리소스(예: 카메라, 위치 서비스 등) 해제 등  
  더 오래 중단하거나 해제해야 하는 리소스 집약적 작업에 사용한다.

**정리:**  
- 짧게 중단할 작업(애니메이션, 센서 등)은 onPause()에서 처리  
- 화면에서 완전히 사라질 때 해제해야 하는 리소스(네트워크, DB, 서비스 등)는 onStop()에서 처리

--- 

**💡 Pro Tips for Mastery: 여러 Activity 간의 Lifecycle 변화**

두 개의 액티비티(A, B) 사이에서 화면 전환이 일어날 때, 각 액티비티의 생명주기 콜백이 순서대로 호출된다.

#### 순차적 생명주기 흐름

**1. Activity A 최초 실행**
- Activity A: `onCreate()` → `onStart()` → `onResume()`
- 사용자가 Activity A와 상호작용

**2. Activity A에서 Activity B로 이동**
- Activity A: `onPause()` (UI 일시 중지, 리소스 해제)
- Activity B: `onCreate()` → `onStart()` → `onResume()` (포커스 획득)
- Activity A: `onStop()` (Activity B가 완전히 화면을 덮으면 호출)

**3. Activity B에서 다시 Activity A로 복귀**
- Activity B: `onPause()`
- Activity A: `onRestart()` → `onStart()` → `onResume()` (포커스 재획득)
- Activity B: `onStop()` → `onDestroy()` (완전히 종료)

#### 요약

- 포그라운드에 있던 액티비티는 항상 `onPause()`를 거쳐 백그라운드로 이동
- 새로 실행되는 액티비티는 `onCreate()`부터 생명주기가 시작됨
- 이전 액티비티로 돌아오면 `onRestart()` 또는 `onResume()`을 통해 다시 활성화
- 나가는 액티비티는 상황에 따라 `onStop()` 또는 `onDestroy()`로 종료됨

이 흐름을 이해하면 리소스 관리와 사용자 경험을 최적화할 수 있다.

--- 
**💡 Pro Tips for Mastery: Activity의 lifecycle 인스턴스란?**

모든 Activity에는 Jetpack Lifecycle 라이브러리의 Lifecycle 인스턴스가 연결
- ComponentActivity 에서 Lifecycle 인스턴스 가져올 수 있음

이 인스턴스를 통해 직접 오버라이드하지 않고
(LifecycleObserver, DefaultLifecycleObserver)를 등록해 <br>
구조적으로 관리할 수 있다.

#### 사용 방법

- Lifecycle 인스턴스에 Observer를 추가해, 특정 생명주기 상태에서 원하는 작업을 수행할 수 있다.

```kotlin
class MyObserver : DefaultLifecycleObserver {
    override fun onStart(owner: LifecycleOwner) { /* ... */ }
    override fun onStop(owner: LifecycleOwner) { /* ... */ }
}

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        lifecycle.addObserver(MyObserver())
    }
}
```

#### 주요 장점

- **라이프사이클 인식:** 컴포넌트가 액티비티의 현재 상태를 인식해, 불필요하거나 잘못된 작업을 방지
- **관심사 분리:** 생명주기 의존 로직을 액티비티 밖으로 분리해 코드 가독성·유지보수성 향상
- **Jetpack 라이브러리 연동:** LiveData, ViewModel 등과 쉽게 통합해, 반응형 프로그래밍과 효율적 리소스 관리 가능

#### 요약

- Lifecycle 인스턴스는 현대 안드로이드 아키텍처의 핵심으로,  
  구조적이고 재사용 가능한 방식으로 생명주기 이벤트를 처리할 수 있게 해준다.
