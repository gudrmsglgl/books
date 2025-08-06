# Chapter 0 

목차 <br>
[Category 0: The Android Framework](#id-section1)
<br>
- [1. What is Intent?](#id-section2)<br>
- [2. What is the purpose of Pending Intent?](#id-section3)<br>
- [3. What are the differences between Serializable and Parcelable](#id-section4)<br>
- [4. What is Context and what types of Context exist?](#id-section5)<br>

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
