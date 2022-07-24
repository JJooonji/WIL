# 2022.07.24의 WIL

## JavaScript의 ES란?
- ES는 ECMA Script의 약자 <br>-> 자바스크립트는 1990년대 Netscape 회사의 Brendan Eich 라는 사람에 의해 최초 개발된 언어임. 자바스크립트가 잘 되자, MS에서 Jscript라는 언어를 개발해 IE에 탑재하였는데, 이 두 스크립트가 너무 제각각이라, 표준이 필요하게 됨.<br>
이 표준을 위해 자바스크립트를 ECMA( European Computer Manufacturers Association ) 라는 정보와 통신시스템의 비영리 표준 기구에 제출하였고 표준에 대한 작업을 ECMA-262란 이름으로 1996년 11월에 시작해 1997년 6월에 채택됨.

<br>

## ES5/ES6 문법 차이
---
> ES 뒤에 붙은 숫자에 따라 버전의 차이가 있음.
#### 1. ES3 (1999)
- 우리가 Javascript라고 하는 언어는 1997년 6월 표준화된 이후 2년이 지난 이 시점에 표준화된 언어라 할 수 있음.
#### 2. ES5 (2009)
  - 배열에 forEach, map, filter, reduce, some, every와 같은 메소드 지원
  - object에 대한 getter / setter 지원
  - 자바스크립트 strict 모드 지원 (더 엄격하게 문법 검사를 함.)
  - JSON 지원 ( 과거에는 XML을 사용하다가, json이 뜨면서 지원하게 됨 )
#### 3. ES6(=ES 2015)
<br>- 같은 버전으로 불리는 이름만 다른 것!

-  let, const 키워드 추가(Hoisting의 개선)
   - 기존의 변수는 함수 scope를 가진 var 키워드를 이용하여 선언함. 때문에 호이스팅이 일어났는데, 이에 대한 방안으로 block scope 를 가진 let과 const 키워드를 추가함.

- arrow 문법 지원
   - arror 문법으로 편하고 간결해진 코드를 작성할 수 있게 되었고, this 를 바인딩하지 않음 (this는 해당 scope의 this와 같음)
- iterator와 generator 추가
- module의 import/export 추가
- Promise 도입 
   - 자바스크립트의 비동기 언어로 인한 callback 지옥을 해결해줄 기법이 추가됨

<br>

---
> 이번주를 정리해보자.
### 알고리즘ㅠ
#### - 항해99 2주 알고리즘 주차를 지나오면서 정말 많은 자바스크립트 언어들을 보고 다양한 코드들을 작성해본 것 같다..
#### 아직은 코드를 간결하게 쓰지 못해 for문과 if문만 계~~속 돌려서 결과를 도출해내려고 했는데, 한번은 코드가 너무 길어 프로그래머스에서 실행이 오래 걸린다며 결과를 보여주지 않았다ㅎㅎ
#### 알고리즘 테스트 준비를 위해 모의고사를 먼저 치르며 문제를 해결하지 못해