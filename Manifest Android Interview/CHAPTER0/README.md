# Chapter 0 

목차 <br>
[Category 0: The Android Framework](#id-section1)
<br>
- [1. What is Intent?](#id-section2)<br>
- [2. What is the purpose of Pending Intent?](#id-section3)<br>

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