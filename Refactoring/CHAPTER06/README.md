# 기본적인 리팩터링

[함수 추출하기](#id-section1)<br>
[함수 인라인하기](#id-section2)<br>
[변수 추출하기](#id-section3)<br>
[변수 인라인하기](#id-section4)<br>
[함수 선언 바꾸기](#id-section5)<br>
[변수 캡슐화하기](#id-section6)<br>
[변수 이름 바꾸기](#id-section7)<br>
[매개변수 객체 만들기](#id-section8)<br>
[여러 함수를 클래스로 묶기](#id-section9)<br>
[여러 함수를 변환 함수로 묶기](#id-section10)<br>
[단계 쪼개기](#id-section11)<br>



### 저수준 리팩터링
- **추출**은 결국 이름 짓기이며, 코드 이해도가 높아지다 보면 이름을 바꿔야 할 때가 많다.
- **함수 선언 바꾸기** 
    - 함수의 이름을 변경할 때 많이 쓰인다.
    - 함수의 인수를 추가 제거할 때 적용
- **함수 이름 바꾸기**
    - 바꿀 대상이 변수일 때 
    - 변수 캡슐화하기와 관련 깊다.
- **매개변수 객체 만들기**
    - 자주 함께 뭉쳐 다니는 인수 
	    - [x] **객체 하나로 묶기**

### 고수준 리팩터링
- 함수를 만들고 나면 
	- [x] 다시 **고수준 모듈로 묶어야** 한다.
- 함수를 그룹으로 묶을 때
	- [x] **여러 함수를 클래스로 묶기**를 이용
- 읽기 전용 데이터
	- [x] **여러 함수를 변환 함수**로 묶기 
- 모듈들의 작업 처리 과정을 명확한 단계로 구분 
    - [x] **단계 쪼개기**

<br>
<div id='id-section1'/>

## 6.1 함수 추출하기 Extract Function

```kotlin
fun printOwing(invoice: Invoice){
   printBanner()
   val outstanding = calculateOutstanding()
	
   // 세부 사항 출력 
   println("고객명: ${invoice.customer}")
   println("채무액: ${outstanding}")
}
```
**🔻&nbsp;&nbsp;함수 추출 후**
```kotlin
fun printOwing(invoice: Invoice){
   printBanner()
   val outstanding = calculateOutstanding()
   printDetail(invoice, outstanding)
}

fun printDetail(invoice: Invoice, outstanding: Int){
   println("고객명: ${invoice.customer}")
   println("채무액: ${outstanding}")
}
```
<br>

### 🔎 &nbsp;&nbsp;코드를 독립된 함수로 묶어야 할 때
 #### ⭐&nbsp;&nbsp;**목적과 구현을 분리**
- 코드를 보고 무슨 일을 하는지 파악하는 데 한참이 걸린다면 
	- [x] 함수로 추출
	- [x] '무슨 일'에 걸맞는 이름을 짓는다.
		- [x] 😊 함수를 아주 짧게, 단 몇 줄만 담도록 작성하는 습관 
		- [x] 💭 함수 호출이 많아져서 성능이 느려질까 걱정하지 말아라
		- [x] 😫 5~6줄 이상 냄새 풍김
#### 길이를 기준
   - 한 화면을 넘어가면 안 된다
#### 재사용성 기준
   - 두 번 이상 사용될 코드는 함수로 만들고, 한 번만 쓰이는 코드는 인라인 상태로 놔두는 것.
<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 함수를 새로 만들고 목적을 잘 드러내는 이름을 붙인다 ('어떻게' 아닌 '무엇을' 하는지가 드러나게)
```
* 대상 코드가 매우 간던하더라도 함수로 뽑아서 목적이 더 잘 드러나는 이름을 붙일 수 있다면 추출한다.
* 이름이 떠오르지 않는다면 함수로 추출하면 안 되는 신호.
* 추출하는 과정에서 좋은 이름이 떠오를 수도 있으니 처음부터 최선의 이름부터 짓고 시작할 필요 없다.
* 함수로 추출해서 사용해보고 효과가 크지 않다면 다시 원래 상태로 인라인해도 된다.
```
&emsp;⓶ 추출할 코드를 원본 함수에서 복사하여 새 함수에 붙여넣는다.<br>
&emsp;⓷ 추출한 코드 중 원본 함수의 지역 변수를 참조하거나 추출한 함수의 유효범위를 벗어나는 변수는 없는지 검사한다. 있다면 매개변수로 전달한다.
```
-> 함수에는 지역 변수와 매개변수가 있기 마련. 
   가장 일반적인 처리 방법은 이런 변수 모두를 인수로 전달.
-> 추출한 코드에서만 사용하는 변수가 추출한 함수 밖에 선언되어 있다면 추출한 함수 안에서 선언 하도록 수정
-> 추출한 코드 안에서 값이 바뀌는 변수 중에서 값으로 전달되는 것들은 주의해서 처리.
   이런 변수가 하나뿐이라면 추출한 코드를 질의 함수로 취급해서 그 결과(반환 값)을 해당 변수에 대입
-> 때로는 추출한 코드에서 값을 수정하는 지역 변수가 너무 많을 수 있다. 
   이럴 때는 함수 추출을 멈추고, 변수 쪼개기가 임시 변수를 질의 함수로 바꾸기와 같은 다른 리팩터링 적용해서 변수를 사용하는 코드를 단순하게 바꿔본다. 그런 다음 함수 추출을 다시 시도.      
```
 &emsp;⓸ 변수를 다 처리했다면 컴파일한다.<br>
 &emsp;⓹ 원본 함수에서 추출한 코드 부분을 새로 만든 함수를 호출하는 문장으로 바꾼다 (즉, 추출한 함수로 일을 위임)<br>
 &emsp;⓺ 테스트한다.<br>
 &emsp;⓻ 다른 코드에 방금 추출한 것과 똑같거나 비슷한 코드가 없는지 살핀다. 있다면 방금 추출한 새 함수를 호출하도록 바꿀지 검토한다. (인라인 코드를 함수 호출로 바꾸기)

<br>
<div id='id-section2'/>

## 6.2 함수 인라인하기 Inline Function
```kotlin
fun getRating(driver: Driver): Int{
   retrun moreThanFiveLateDeliveries(driver) ? 2: 1
}

fun moreThanFiveLateDeliveries(driver: Driver): Int {
   return driver.numberOfLateDeliveries > 5
}
```
**🔻&nbsp;&nbsp;함수 인라인**
```kotlin
fun getRating(driver: Driver): Int{
   retrun (driver.numberOfLateDeliveries > 5) ? 2: 1
}
```
<br>

### 🔎 &nbsp;&nbsp;함수를 인라인 할 때 
- 함수 본문이 **🔎 이름만큼 명확한 경우**
- 함수 본문 코드를 이름만큼 깔끔하게 리팩터링할 때
- 리팩터링 과정에서 잘못 추출된 함수들도 다시 인라인
- 간접 호출을 너무 과하게 쓰는 코드
- 다른 함수로 단순히 위임하기만 하는 함수들이 너무 많아서 **위임 관계가 복잡하게 얽힌 경우**

<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 다형 메서드(polymorphic method)인지 확인.
```
-> 서브클래스에서 오버라이드하는 메서드는 인라인하면 안 된다.
```
&emsp;⓶ 인라인할 함수를 호출하는 곳을 모두 찾는다.<br>
&emsp;⓷ 각 호출문을 함수 본문으로 교체한다.<br>
&emsp;⓸ 하나씩 교체할 때마다 테스트한다.<br>
```
-> 인라인 작업을 한 번에 처리할 필요는 없다.
인라인하기가 까다로운 부분이 있다면 일단 남겨두고 여유가 생길 때마다 틈틈이 처리한다.
```
&emsp;⓹ 함수 정의(원래 함수)를 삭제한다.

<br>
<div id='id-section3'/>

## 6.3 변수 추출하기 Extract Variable
```kotlin
return order.quantity * order.itemPrice -
	Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
	Math.min(order.quantity * order.itemPrice * 0.1, 100)
```
**🔻&nbsp;&nbsp;변수 추출**
```kotlin
val basePrice = order.quantity * order.itemPrice
val quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05
val shipping = Math.min(basePrice * 0.1, 100)
return basePrice - quantityDiscount + shipping
```

### 🔎 &nbsp;&nbsp;변수 추출할 때 
- 표현식이 너무 복잡한 경우
	- 이럴 때 지역 변수 활용 
	- 코드의 목적 훨씬 명확하게 드러낼 수 있음
	- 디버깅에도 도움이 됨. (중단점 지정, 상태 출력 문장 추가 가능)

- 변수 추출은 곧 이름을 붙이고 싶다.
	- 이름이 들어갈 문맥을 살피자.
	- 현재 함수 안에서만 의미가 있다면 변수로 추출
	- ~~함수를 벗어난~~ **넓은 문맥**에서까지 의미가 된다면 그 **넓은 범위에서 통용**되는 이름
		- ~~변수가 아닌 (주로)~~ **💫 함수로 추출**해야 한다.
		- 이름이 통용되는 문맥을 넓히면 다른 코드에서 사용할 수 있기 때문에 같은 표현식을 중복해서 작성하지 않아도 됨
		- 중복이 적으면서 의도가 잘 드러나는 코드를 작성 
<br>

### **ex) 변수 추출 시 함수를 벗어난 넓은 문맥에서 함수로 추출**<br>

```kotlin
class Order{
   val data
   val quantity
      get() = this.data.quantity
   val itemPrice
      get() = this.data.itemPrice
   val price
      get() = this.quantity * this.itemPrice - 
      Math.max(0, this.quantity - 500) * this.itemPrice * 0.05 +
      Math.min(this.quantity * this.itemPrice * 0.1, 100)
				
   constructor(record: Record){
      this.data = record
   }
}
```
**🔻&nbsp;&nbsp;이름이 가격을 계산하는 price() 메서드의 범위를 넘어 클래스 전체 적용<br> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-> 변수추출x, 함수추출o**

```kotlin
class Order{
   val data
   val quantity
      get() = this.data.quantity 
   val itemPrice
      get() = this.data.itemPrice
   val basePrice
      get() = this.quantity * this.itemPrice
   val quantityDiscount
      get() = Math.max(0, this.quantity - 500) * this.itemPrice * 0.05
   val shipping
      get() = Math.min(this.quantity * this.itemPrice * 0.1, 100)	
   val price
      get() = this.basePrice - this.quantityDiscount + this.shipping
				
   constructor(record: Record){
      this.data = record
   }
}
```
<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 추출하려는 표현식에 부작용은 없는지 확인<br>
&emsp;⓶ 불변 변수를 하나 선언하고 이름을 붙일 표현식의 복제본을 대입<br>
&emsp;⓷ 원본 표현식을 새로 만든 변수로 교체<br>
&emsp;⓸ 테스트한다. <br>
&emsp;⓹ 표현식을 여러 곳에서 사용한다면 각각을 새로 만든 변수로 교체. 교체할 때마다 테스트


<br>
<div id='id-section4'/>

## 6.4 변수 인라인하기 Inline Variable

```kotlin
val basePrice = anOrder.basePrice
return (basePrice > 1000)
```
**🔻 변수 인라인**
```kotlin
return anOrder.basePrice > 1000
```
<br>

### 🔎 &nbsp;&nbsp;변수를 인라인 할 때 
- 원래 표현식과 다를 바 없을 때 
- 변수가 주변 코드를 리팩터링하는 데 방해될 때 

<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 대입문의 우변(표현식)에서 부작용이 생기지 않는지 확인<br>
&emsp;⓶ 변수가 불변으로 선언되지 않았다면 불변으로 만든 후 테스트한다.<br>
```
-> 이렇게 하면 변수에 값이 단 한번만 대입되는지 확인할 수 있다.
```
&emsp;⓷ 이 변수를 가장 처음 사용하는 코드를 찾아서 대입문 우변의 코드로 바꾼다.<br>
&emsp;⓸ 테스트한다. <br>
&emsp;⓹ 변수를 사용하는 부분을 모두 교체할 때까지 이 과정을 반복한다.<br>
&emsp;⓺ 변수 선언문과 대입문을 지운다.<br>
&emsp;⓻ 테스트한다.

<br>
<div id='id-section5'/>

## 6.5 함수 선언 바꾸기 Change Function Declaration
```kotlin
fun circum(radius: Float){...}
```
**🔻 함수 선언 바꾸기**
```kotlin
fun circumference(radius: Float){...}
```
<br>

### 함수 선언
- 프로그램을 작은 부분으로 나누는 주된 수단
- 소프트웨어 시스템의 구성 요소를 조립하는 연결부 역할
- 잘 정의하면 새로운 부분을 추가가 쉬움, 잘못 정의하면 지속적인 방해 요인, 동작을 파악하기 어려워지고 요구사항이 바뀔 때 적절히 수정하기 어렵게 한다.

<br>

### 💫 &nbsp;&nbsp;중요한 것
- 함수 이름
```
* 이름이 좋으면 함수의 구현 코드를 살펴볼 필요 없이 호출물만 보고 무슨 일을 하는지 파악 가능
* 잘못된 함수를 발견하면 더 나은 이름이 떠오르는 즉시 바꾸자.
* 나중에 그 코드를 다시 볼 때 무슨 일을 하는지 '또' 고민하지 않게 됨.
```
> ##### 💡 좋은 이름을 떠올리는데 효과적인 방법<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;주석을 이용해 함수의 목적을 설명해보는 것. &nbsp;그러다 보면 주석이 멋진 이름으로 바뀌어 되돌아올 때가 있다.

<br>

- 매개변수 
```
* 함수가 외부 세계와 어우러지는 방식을 정의
* 다른 모듈과의 결합(coupling)을 제거
* 매개변수 올바르게 선택하는 것은 정답이 없고 어떻게 연결하는 것이 더 나은지 더 잘 이해하게 될 때마다 그에 맞게 코드를 개선할 수 있도록 함수 선언 바꾸기 친숙해져야 한다.
```

<br>

### 📍 &nbsp;&nbsp;간단한 절차
&emsp;⓵ 매개변수를 제거하려거든 먼저 **⚠️ 함수 본문에서 제거 대상 매개변수를 참조하는 곳**은 없는지 확인.<br>
&emsp;⓶ 메서드 선언을 원하는 형태로 바꾼다.<br>
&emsp;⓷ 기존 메서드 선언을 참조하는 부분을 모두 찾아서 바뀐 형태로 수정.<br>
&emsp;⓸ 테스트한다. <br>


<br>

### 📍 &nbsp;&nbsp;마이그레이션 절차
&emsp;⓵ 이어지는 추출 단계를 수월하게 만들어야 한다면 함수의 본문을 적절히 리팩터링.<br>
&emsp;⓶ 함수 본문을 새로운 함수로 추출한다.<br>
```
-> 새로 만들 함수 이름이 기존 함수와 같다면 일단 검색하기 쉬운 이름을 임시로 붙여둔다.
```
&emsp;⓷ 추출한 함수에 매개변수를 추가해야 한다면 '간단한 절차'를 따라 추가.<br>
&emsp;⓸ 테스트한다. <br>
&emsp;⓹ 기존 함수를 인라인한다. <br>
&emsp;⓺ 이름을 임시로 붙여뒀다면 함수 선언 바꾸기를 한 번 더 적용해서 원래 이름으로 되돌린다.<br>
&emsp;⓻ 테스트한다.

<br>

### **ex) 매개변수 추가하기**<br>
##### **요구사항: 예약 시 우선순위 큐를 지원하는 새로운 요구**

```kotlin
// Book 클래스..
fun addReservation(customer: Customer){
   this._reservations.push(customer)
}
```
&emsp;② 함수 본문을 새로운 함수로 추출한다.
```kotlin
// Book 클래스..
fun addReservation(customer: Customer){
   this._reservations.push(customer)
}

fun zz_addReservation(customer: Customer){
   this._reservations.push(customer)
}
```
&emsp;③ 새 함수의 선언문과 호출문에 원하는 매개변수를 추가한다.
```kotlin
// Book 클래스..
fun addReservation(customer: Customer){
   this.zz_addReservation(customer, false)
}

fun zz_addReservation(customer: Customer, isPriority: Boolean){
   assert(isPriority == true || isPriority == false) // 호출하는 곳에서 새로 추가한 매개변수를 실제로 사용하는지 확인
   this._reservations.push(customer)
}
```
&emsp;⑤ 기존 함수를 인라인한다. <br>
&emsp;⑥ 다 고쳤다면 새 함수의 이름을 기존 함수의 이름으로 바꾼다.

<br>

### **ex) 매개변수를 속성으로 바꾸기**<br>
##### **요구사항: 고객이 뉴잉글랜드에 살고 있는지 확인하는 함수**

```kotlin
fun inNewEngland(customer: Customer): Boolean{
   return ["MA","CT","ME","VT","NH","RI"].includes(customer.address.state)
}

val newEnglanders = someCustomers.filter{c -> inNewEngland(c)}
```
**🔻 함수 추출**

```kotlin
fun inNewEngland(customer: Customer): Boolean{
   val stateCode = customer.address.state
   return xxNewinNewEngland(stateCode)
}

fun xxNewinNewEngland(stateCode: StateCode): Boolean{
   return ["MA","CT","ME","VT","NH","RI"].includes(stateCode)
}
```
**🔻 함수 인라인 -> 기존 함수 호출문을 새 함수 호출문으로 교체**
```kotlin
val newEnglanders = someCustomers.filter{c -> xxNewinNewEngland(c.address.state)}
```
**🔻 함수 선언 바꾸기 -> 새 함수의 이름을 기존 함수의 이름으로 바꾼다.**
```kotlin
// 최상위
fun inNewEngland(stateCode: StateCode): Boolean{
   return ["MA","CT","ME","VT","NH","RI"].includes(stateCode)
}

// 호출문 
val newEnglanders = someCustomers.filter{c -> inNewEngland(c.address.state)}
```

<br>
<div id='id-section6'/>

## 6.6 변수 캡슐화하기 Encapsulate Variable

- 유효범위가 넓어질수록 다루기 어려워짐 -> 전역 데이터의 골칫거리 이유
- 접근할 수 있는 범위가 넓은 데이터를 옮길 때
	- 그 데이터로의 **💫 접근을 독점하는 함수를 만드는 식으로 캡슐화**하는 것이 가장 좋은 방법
	- 데이터 재구성이라는 어려운 작업을 함수 재구성이라는 더 단순한 작업으로 변환되는 것.
- 데이터를 변경하고 사용하는 코드를 감시하기 때문에 **데이터 변경 전 검증이나 변경 후 추가 로직을 쉽게** 끼워 넣을 수 있다. 
- 자주 사용하는 데이터에 대한 결합도가 높아지는 일을 막을 수 있다.
- private 유지(필드 캡슐화하기) -> 가시 범위 제한
- 불변 데이터는 가변 데이터보다 캡슐화할 이유가 적다. 데이터를 변경될 일이 없어서 갱신 전 검증 같은 추가 로직이 자리할 공간을 마련할 필요가 없기 때문.

<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 변수로의 접근과 갱신을 전담하는 캡슐화 함수들을 만든다.<br>
&emsp;⓶ 정적 검사를 수행한다.<br>
&emsp;⓷ 변수를 직접 참조하던 부분을 모두 적절한 캡슐화 함수 호출로 바꾼다. 하나씩 바꿀 때마다 테스트한다.<br>
&emsp;⓸ 변수의 접근 범위를 제한한다. <br>
```
-> 변수로의 직접 접근을 막을 수 없을 때도 있다. 그럴 때는 변수 이름을 바꿔서 테스트해보면 해당 변수를 참조하는 곳을 쉽게 찾아낼 수 있다.
```
&emsp;⓹ 테스트한다. <br>
&emsp;⓺ 변수 값이 레코드라면 레코드 캡슐화하기를 적용할지 고려.<br>

<br>

### **ex) 전역 변수에 중요한 데이터가 담겨 있는 경우**<br>

```kotlin
var defaultOwner = "마틴 파울러"

// 다음과 같은 참조하는 코드..
spaceship.owner = defaultOwner

// 갱신하는 코드 역시 있을 것...
defaultOwner = "레베카 파손스"
```
**1. 기본적인 캡슐화를 위해 가장 먼저 데이터를 읽고 쓰는 함수 정의**
```kotlin
fun getDefaultOwner() = this.defaultOwner
fun setDefaultOwner(arg: String){this.defaultOwner = arg}
``` 
**3. 그런 다음 defaultOwner를 참조하는 코드를 찾아서 방금 만든 게터 함수를 호출하도록 고친다.**
```kotlin
spaceship.owner = getDefaultOwner()
setDefaultOwner("레베카 파슨스")
``` 

<div id='id-section6-ex1'/>

**4. 모든 참조를 수정했다면 변수의 가시 범위를 제한한다.** <br>
**🙆‍♀️ 1. Function set & get (캡슐화)**

```kotlin
private var defaultOwner = "마틴 파울러"  // 👈 가시 범위 제한
fun getDefaultOwner() = this.defaultOwner
fun setDefaultOwner(arg: String){this.defaultOwner = arg}
``` 
**🙆‍♀️ 2. Property set & get (캡슐화)**

```kotlin
private var _defaultOwner = "마틴 파울러"
var defaultOwner: String
   set(value){	
      this._defaultOwner = value
   }
   get() = this._defaultOwner
``` 
<br>

### 💊 &nbsp;&nbsp;값 캡슐화하기
- 기본 캡슐화 기법은 데이터 항목을 참조하는 부분만 캡슐화한다.
- 변수뿐 아니라 변수에 담긴 내용을 변경하는 행위까지 제어할 수 있게 캡슐화하고 싶을 때 
- 두 가지 방법
	- 값을 바꿀 수 없게 만드는 것.
	- 게터가 데이터의 복제본을 반환하도록 수정

<br>
<div id='id-section7'/>

## 6.7 변수 이름 바꾸기 Rename Variable
```kotlin
val a = height * width
```
**🔻 변수 이름 바꾸기**

```kotlin
val area = height * width
```

- 명확한 프로그래밍의 핵심 이름짓기.
- 변수는 프로그래머가 하려는 일에 관해 많은 것을 설명해준다. ( 단, 이름을 잘 지었을 때 )
- 한 줄짜리 람다식(laumbda expression)에서 사용하는 변수는 대체로 쉽게 파악 -> 한 글자로 된 이름
- 간단한 함수의 매개변수 이름도 짧게 지어도 될 때가 많다.
- 동적 타입 언어라면 이름 앞에 타입을 드러내는 스타일도 좋다. 
- 함수 호출 한 번으로 끝나지 않고 값이 영속되는 필드라면 이름에 더 신경 써야 한다.
<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 폭넓게 쓰이는 변수라면 변수 캡슐화하기를 고려<br>
&emsp;⓶ 이름을 바꿀 변수를 참조하는 곳을 모두 찾아서, 하나씩 변경<br>
&emsp;⓷ 테스트한다.

### **ex) 함수 밖에서 참조할 수 있는 변수 (읽기, 쓰기)**<br>
👉 &nbsp;&nbsp;[캡슐화로 처리](#id-section6-ex1)


<br>
<div id='id-section8'/>

## 6.8 매개변수 객체 만들기 Introduce Parameter Object
```kotlin
fun amountInvoiced(startDate: String, endDate: String){...}
fun amountReceived(startDate: String, endDate: String){...}
fun amountOverdue(startDate: String, endDate: String){...}
```
**🔻 매개변수 객체 만들기**

```kotlin
fun amountInvoiced(dateRange: DateRange){...}
fun amountReceived(dateRange: DateRange){...}
fun amountOverdue(dateRange: DateRange){...}
```
<br>

### 🔎 &nbsp;&nbsp;매개변수 객체 만들 때 
- **💾 데이터 항목 여러 개**가 이 함수에서 저 함수로 함께 몰려다니는 경우
- 데이터 구조로 묶으면 데이터 사이의 관계 명확
- 매개변수 수 감소
- 모든 함수가 원소를 참조할 때 똑같은 이름을 사용하기 때문에 일관성 높임
- 코드를 더 근본적으로 바꿈
- 데이터 구조에 담길 데이터에 공통으로 적용되는 동작 추출
	- [x] 공용함수를 나열
	- [x] 이 함수들과 데이터를 합쳐 클래스로 만듬
<br>

### 📍 &nbsp;&nbsp;절차

&emsp;⓵ 적당한 데이터 구조가 아직 마련되어 있지 않다면 새로 만든다.
```
-> 클래스로 만드는걸 선호. 나중에 동작까지 함께 묶기 좋기 때문
저자는 주로 데이터 구조를 값 객체 Value Object 로 만든다.
```
&emsp;⓶ 테스트한다.</br>  
&emsp;⓷ 함수 선언 바꾸기로 새 데이터 구조를 매개변수로 추가한다.</br>  
&emsp;⓸ 테스트한다.</br>  
&emsp;⓹ 함수 호출 시 새로운 데이터 구조 인스턴스를 넘기도록 수정한다. 하나씩 수정할 때마다 테스트한다.</br>  
&emsp;⓺ 기존 매개변수를 사용하던 코드를 새 데이터 구조의 원소를 사용하도록 바꾼다.</br>  
&emsp;⓻ 다 바꿨다면 기존 매개변수를 제거하고 테스트한다. </br>  
 
<br>

### **ex) 온도 측정값(reading)에서 정상 작동 범위를 벗어난 것이 있는지 검사하는 코드**<br>
```kotlin
data class Reading(val temp: Int, val time: String)

data class Station(
   val name: String,
   val readings: List<Reading>
)
```

```kotlin
val station: Station = Station(
   name = "ZB1",
   readings = listOf(
      Reading(temp=47, time="2016-11-10 09:10"),
      Reading(temp=53, time="2016-11-10 09:20"),
      Reading(temp=58, time="2016-11-10 09:30"),
      Reading(temp=53, time="2016-11-10 09:40"),
      Reading(temp=51, time="2016-11-10 09:50")
   )
)

// 정상 범위를 벗어난 측정값을 찾는 함수
fun readingsOutsideRange(
   station: Station, 
   min: Int, 
   max: Int
) = station.readings
     .filter{r -> r.temp < min || r.temp > max}


// 호출문
val alerts = readingsOutsideRange(station, 
   operatingPlan.temperatureFloor, // 최저 온도
   operatingPlan.temperatureCeiling  // 최고 온도
) 
```
&emsp;⓵ 범위range라는 개념은 객체 하나로 묶어 표현 , 묶은 데이터를 표현하는 클래스 선언

```kotlin
// 동작까지 옮기는 더 큰 작업의 첫 단계로 수행되기 때문에 클래스로 선언 
data class NumberRange(val min: Int, val max: Int)
```

&emsp;⓷ 새로 만든 객체를 readingsOutsideRange()의 매개변수로 추가하도록 함수 선언 바꾼다.

```kotlin
fun readingsOutsideRange(
   station: Station, 
   min: Int, 
   max: Int,
   range: NumberRange  // 👈 매개변수 추가 
) = station.readings
     .filter{r -> r.temp < min || r.temp > max}



// ------------------------------------- 호출문
val range = NumberRange(           // 👈 NumberRange 추가
   operatingPlan.temperatureFloor,
   operatingPlan.temperatureCeiling
)

val alerts = readingsOutsideRange(
   station, 
   operatingPlan.temperatureFloor, 
   operatingPlan.temperatureCeiling,
   range                          // 👈 NumberRange 추가
) 

```
&emsp;⓺ 이제 기존 매개변수를 사용하는 부분을 변경. ✅ **하나씩 지우고 테스트** 하면서 진행 

```kotlin
fun readingsOutsideRange(
   station: Station, 
   //min: Int, 
   //max: Int,           // 👈 기존 매개변수 제거 
   range: NumberRange  
) = station.readings
     .filter{r -> r.temp < range.min || r.temp > range.max }



// ------------------------------------- 호출문
val range = NumberRange(           
   operatingPlan.temperatureFloor,
   operatingPlan.temperatureCeiling
)

val alerts = readingsOutsideRange(
   station, 
   //operatingPlan.temperatureFloor, 
   //operatingPlan.temperatureCeiling, // 👈 기존 매개변수 제거
   range                          
) 

```

#### **매개변수 객체 만들기 끝**<br>
### **진정한 값 객체로 거듭나기**<br>
- [x] 클래스로 만들어두면 관련 동작들을 이 클래스로 옮길 수 있다는 이점이 있음
- [x] 코드에 범위 개념이 필요함을 깨달았다면 범위 객체로 바꾸자.
```kotlin
data class NumberRange(val min: Int, val max: Int){
   
   fun contains(arg: Int) = (arg >= this.min && arg <= this.max)

}

fun readingsOutsideRange(
   station: Station, 
   range: NumberRange  
) = station.readings
     .filter{r -> !range.contains(r.temp) }

```


<br>
<div id='id-section9'/>

## 6.9 여러 함수를 클래스로 묶기 Combine Functions into Class

```kotlin
fun base(reading: Reading){...}
fun taxableCharge(reading: Reading){...}
fun calculateBaseCharge(reading: Reading){...}
```

**🔻 클래스로 묶기**

```kotlin
class Reading {
	base(){...}
	taxableCharge(){...}
	calculateBaseCharge(){...}
}
```

### 🔎 &nbsp;&nbsp;함수들을 클래스로 묶을 때 
- **공통 데이터를 중심**으로 긴밀하게 엮여 작동하는 🏭 **함수 무리**를 발견할 때 
- 함수들이 공유하는 공통 환경을 더 명확하게 표현, 각 함수에 적달되는 인수를 줄여서 객체 안에서의 함수 호출을 간결하게 만듬.
- 시스템의 다른 부분에 전달하기 위한 참조를 제공.
- 함수를 묶을 때는
	- 여러 함수를 변환 함수로 묶기도 있다. 
- 두드러진 장점
	- 클라이언트가 객체의 **핵심 데이터를 변경 가능**
	- 파생 객체들을 일관되게 관리

<br>

### 📍 &nbsp;&nbsp;절차

&emsp;⓵ 함수들이 공유하는 공통 데이터 레코드를 캡슐화한다.
```
-> 공통 데이터가 레코드 구조로 묶여 있지 않다면 
   먼저 매개변수 객체 만들기로 데이터를 하나로 묶는 레코드를 만든다.
```
&emsp;⓶ 공통 레코드를 사용하는 함수 각각을 새 클래스로 옮긴다.</br>  
```
-> 공통 레코드의 멤버는 함수 호출문의 인수 목록에서 제거한다
```
&emsp;⓷ 데이터를 조작하는 로직들은 함수로 추출해서 새 클래스로 옮긴다.</br>  

<br>

### **ex) 자동차 세금 계산서**<br>

```kotlin
val aReading = acquireReading()
val base = (baseRate(aReading.month, areading.year) * aReading.quantity)  // 👈 함수 추출의 필요성을 느껴야함
val taxableCharge = Math.max(0, base - taxThreshold(aReading.year))
```

```kotlin
val aReading = acquireReading()
val base = calculateBaseCharge(aReading)  

fun calculateBaseCharge(aReading){
  return baseRate(aReading.month, aReading.year) * aReading.quantity
}
```
&emsp;⓵ 공통 데이터 레코드를 캡슐화한다.

```kotlin
class Reading(private val data: rawReading){
   val customer
      get() = data.customer
   val quantity
      get() = data.quantity
   val month
      get() = data.month
   val year
      get() = data.year            
}
```

&emsp;⓶ 만들어져 있는 calculateBaseCharge() 클래스로 옮기자

```kotlin
class Reading(private val data: rawReading){
   val customer
      get() = data.customer
   val quantity
      get() = data.quantity
   val month
      get() = data.month
   val year
      get() = data.year
   val baseCharge           // 👈 기존 함수 클래스 옮기기
      get() = baseRate(this.month, this.year) * this.quantity               
}
```

```kotlin
val rawReading = acquireReading()
val aReading = Reading(rawReading)
val base = aReading.baseCharge // 👈 변수 인라인 하고 싶은 충동이 느껴져야 함
val taxableCharge = Math.max(0, base - taxThreshold(aReading.year))
```

```kotlin
val rawReading = acquireReading()
val aReading = Reading(rawReading)
//val base = aReading.baseCharge
val taxableCharge = Math.max(0, aReading.baseCharge - taxThreshold(aReading.year)) // 👈 변수 인라인을 하였지만 함수 추출을 하고 싶은 충동
```
```kotlin
// 함수 추출하기
fun taxableChargeFn(aReading: Reading) = 
   Math.max(0, aReading.baseCharge - taxThreshold(aReading.year))
```
&emsp;⓶ 만들어져 있는 taxableChargeFn() 클래스로 옮기자
```kotlin
class Reading(private val data: rawReading){
   ...
   val texableCharge           
      get() = Math.max(0, this.baseCharge - taxThreshold(this.year))               
}
```

- [x] 프로그램의 다른 부분에서 데이터를 갱신할 가능성이 꽤 있을 때는 클래스로 묶어두면 큰 도움

```kotlin
val rawReading = acquireReading()
val aReading = Reading(rawReading)
val taxableCharge = aReading.texableCharge
```


<br>
<div id='id-section10'/>

## 6.10 여러 함수를 변환 함수로 묶기 Combine Functions into Transform
```kotlin
fun base(reading: Reading){...}
fun taxableCharge(reading: Reading){...}
```

**🔻 변환 함수로 묶기**

```kotlin
fun enrichReading(argReading): Reading {
   val aReading = _.cloneDeep(argReading)
   aReading.baseCharge = base(aReading)
   aReading.taxableCharge = taxableCharge(aReading)
   return aReading
}
```

### 🔎 &nbsp;&nbsp;함수들을 변환함수로 묶을 때
> 원본 데이터를 입력받아서 필요한 정보를 모두 도출한 뒤, <br> 각각을 출력 데이터의 필드에 넣어 변환
- 정보가 사용되는 곳마다 같은 **도출 로직이 반복**될 때 
- 검색과 갱신을 일관된 장소에서 처리할 수 있고 로직 중복 방지
- 클래스 묶기와 변환함수 묶기
	<br>❗ 원본 데이터가 코드 안에서 **갱신될 때**는 **클래스로 묶는 편이 훨씬 낫다.**
- 데이터 구조와 이를 사용하는 함수를 쉽게 찾아 쓸 수 있다. 

<br>

### 📍 &nbsp;&nbsp;절차

&emsp;⓵ 변환할 레코드를 입력받아서 값을 그대로 반환하는 함수를 만든다.
```
-> 대체로 깊은 복사로 처리해야 한다. 
   변환 함수가 원본 레코드를 바꾸지 않는지 검사하는 테스트를 마련해두면 도움
```
&emsp;⓶ 묶을 함수 중 함수 하나를 골라서 본문 코드를 변환 함수로 옮기고, 처리 결과를 레코드에 새 필드로 기록한다. 그런 다음 클라이언트 코드가 이 필드를 사용하도록 수정.</br>  
```
-> 로직이 복잡하면 함수 추출하기부터 한다.
```
&emsp;⓷ 테스트한다.</br> 

<br>

<br>
<div id='id-section11'/>

## 6.11 단계 쪼개기 Split Phase


```kotlin
val orderData = orderString.split(/\s+/)
val productPrice = priceList[orderData[0].split("-")[1]]
val orderPrice = orderData[1].toInt() * productPrice
```

**🔻 단계 쪼개기**

```kotlin
val orderRecord = parseOrder(order)
val orderPrice = price(orderRecord, priceList)

fun parseOrder(str: String): Order{
   val values = str.split(/\s+/)
   return Order(
      productID = values[0].split("-")[1],
      quantity = values[1].toInt()
   )
}

fun price(order: Order, priceList: Array[Int]): Int{
   return order.quantity * priceList[order.productID]
}
```

### 🔎 &nbsp;&nbsp;단계 쪼개기
- **👫 서로 다른 두 대상을 한꺼번에 다루는 코드**를 발견하면 별개 모듈로 나누는 방법 모색
	- 수정할 때 하나에만 집중 가능
	- 다른 모듈의 상세 내용 기억 못해도 원하는 대로 수정 가능 
- 동작을 연이은 두 단계로 쪼개는 것.
	- [x] 입력값을 다루기 편한 형태로 가공 or 순차적인 단계들로 분리
	- [x] 각 단계는 서로 확연히 다른 일을 수행해야 함
- 다른 단계로 볼 수 있는 코드 영역들이 서로 다른 데이터와 함수를 사용할 때 

<br>

### 📍 &nbsp;&nbsp;절차
&emsp;⓵ 두 번째 단계에 해당하는 코드를 독립 함수로 추출한다.<br>
&emsp;⓶ 테스트한다.<br>
&emsp;⓷ 중간 데이터 구조를 만들어서 앞에서 추출한 함수의 인수로 추가한다.<br>
&emsp;⓸ 테스트한다. <br>
&emsp;⓹ 추출한 두 번째 단계 함수의 매개변수를 하나씩 검토한다. 그중 첫 번째 단계에서 사용되는 것은 중간 데이터 구조로 옮긴다. 하나씩 옮길 때마다 테스트한다. <br>
```
-> 간혹 두 번째 단계에서 사용하면 안 되는 매개변수가 있다. 이럴 때는 각 매개변수를 사용한 결과를 
중간 데이터 구조의 필드로 추출하고, 이 필드의 값을 설정하는 문장을 호출한 곳으로 옮긴다.
```
&emsp;⓺ 첫 번째 단계 코드를 함수로 추출하면서 중간 데이터 구조를 반환하도록 만든다.<br>
```
-> 이때 첫 번째 단계를 변환기 객체로 추출해도 좋다.
```
<br>

### **ex) 상품의 결제 금액 계산**<br>

```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val basePrice = product.basePrice * quantity
   val discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate
   val shippingPerCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = quantity * shippingPerCase
   val price = basePrice - discount + shippingCost
   return price
}
```
> 💁 &nbsp;&nbsp;앞의 몇 줄은 상품정보를 이용해서 결제 금액 중 '*상품 가격 계산* '<br> &emsp;&emsp;반면 뒤의 코드는 배송 정보를 이용하여 결제 금액 중 '*배송비를 계산 '*

&emsp;⓵ 먼저 배송비 계산 부분을 함수로 추출<br>
```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val basePrice = product.basePrice * quantity
   val discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate
   //val shippingPerCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   //val shippingCost = quantity * shippingPerCase
   //val price = basePrice - discount + shippingCost
   val price = applyShipping(basePrice, shippingMethod, quantity, discount)
   return price
}

fun applyShipping(
   basePrice: Int, 
   shippingMethod,
   quantity: Int,
   discount: Int
): Int{
   val shippingPerCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = quantity * shippingPerCase
   val price = basePrice - discount + shippingCost
   return price
}
```

&emsp;⓷ 첫 번째 단계와 두 번째 단계가 주고받을 중간 데이터 구조를 만든다.<br>

```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val basePrice = product.basePrice * quantity
   val discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate
   val priceData = Price() // 👈 중간 데이터 구조
   val price = applyShipping(priceData, basePrice, shippingMethod, quantity, discount)
   return price
}

fun applyShipping(
   priceData: Price, // 👈 중간 데이터 매개변수 추가
   basePrice: Int, 
   shippingMethod,
   quantity: Int,
   discount: Int
): Int{
   val shippingPerCase = (basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = quantity * shippingPerCase
   val price = basePrice - discount + shippingCost
   return price
}
```

&emsp;⓹ 매개변수를 살펴본다. 이중 basePrice는 첫 번째 단계를 수행하는 코드에서 생성.<br> 따라서 ***중간 데이터 구조로 옮기고 매개변수 목록에서 제거***
```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val basePrice = product.basePrice * quantity
   val discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate
   val priceData = Price(basePrice = basePrice) // 👈 중간 데이터 구조로 매개변수 옮김
   val price = applyShipping(priceData, /*basePrice,*/ shippingMethod, quantity, discount)
   return price
}

fun applyShipping(
   priceData: Price, 
   //basePrice: Int,  👈 중간 데이터로 매개변수 옮겼으니 제거
   shippingMethod,
   quantity: Int,
   discount: Int
): Int{
   val shippingPerCase = (priceData.basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = quantity * shippingPerCase
   val price = priceData.basePrice - discount + shippingCost
   return price
}
```

&emsp; ***다른 매개변수도 중간 데이터 구조로 옮기고 매개변수 목록에서 제거***
```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val basePrice = product.basePrice * quantity
   val discount = Math.max(quantity - product.discountThreshold, 0) * product.basePrice * product.discountRate
   val priceData = Price(basePrice = basePrice, quantity = quantity, discount = discount) // 👈 중간 데이터 구조로 매개변수 옮김
   val price = applyShipping(priceData, shippingMethod)
   return price
}

fun applyShipping(
   priceData: Price, 
   //basePrice: Int,  
   shippingMethod,
   //quantity: Int, 👈 중간 데이터로 매개변수 옮겼으니 제거
   //discount: Int             ""
): Int{
   val shippingPerCase = (priceData.basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = priceData.quantity * shippingPerCase
   val price = priceData.basePrice - priceData.discount + shippingCost
   return price
}
```

&emsp;⓺ 첫 번째 단계 코드를 ***함수로 추출하고 중간 데이터 구조를 반환*** 하도록 만든다.

```kotlin
fun priceOrder(
   product: Product,
   quantity: Int, 
   shippingMethod
): Int {
   val priceData = calculatePricaingData(product, quantity) // 👈 첫 번째 단계 -> 함수 추출 -> 중간 데이터 반환
   return applyShipping(priceData, shippingMethod)
}

fun calculatePricaingData(): Price{ // 👈 첫 번째 단계
   val basePrice = ...
   val disCount = ...
   return Price(basePrice = basePrice, quantity = quantity, discount = discount)
}

fun applyShipping( //👈 두 번째 단계
   priceData: Price, 
   shippingMethod
): Int{
   val shippingPerCase = (priceData.basePrice > shippingMethod.discountThreshold) ? shippingMethod.discountedFee : shippingMethod.feePerCase
   val shippingCost = priceData.quantity * shippingPerCase
   val price = priceData.basePrice - priceData.discount + shippingCost
   return price
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbODM5MzQ2NDk0LDE0NzEzNDA2MiwxNzE0ND
YyMTQyLC01Njk2NjMxOTAsMTA5NjI5ODkzOSwzMDQ2NTI0ODAs
MTQ1NDkyOTMxMiw3OTE0NTk0OTgsLTMzMTc1NDc3Myw2MTYyND
g3NjIsNDgyNzQ0ODQ1LDEyODg2MzgyMDgsNjA1MzU3MjgyLC0x
NDA1MTc3NjIzLDE5NjE1MTQ5MDMsLTIwMDMyOTk1NTIsLTg2MT
kwNTUxMiwxODIyNTA0NDg1LDE0NjY0NjcwNzAsMzQ2MzUzMDI3
XX0=
-->