---
layout : post
title : Generators and lazy calls in javascript | 자바스크립트에서 제너레이터와 lazy 호출
categories : javascript
--- 


Q: 아래 두 라인의 코드가 동시에 실행 될 수 있나?
<br/>
A: 아니다 왜냐하면 두번쨰 라인은 x 가 첫번쨰 라인에 데펜건시가 걸려있다. x값이 정해저야 x + 10을 할 수 있기 때문이다. 즉 두번쨰 라인은 첫번째 라인이 실행 된 후 실행될 수 있다.
```
const x = 10;
const z = x + 10;
```
<br/>
<br/>

Q: 아래 두 라인의 코드가 동시에 실행 될 수 있나?
A: 
```
const e = () => 10;
const z = e() + 10;
```
