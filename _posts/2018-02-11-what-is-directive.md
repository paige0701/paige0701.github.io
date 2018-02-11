---
layout : post
title : What is directive in angular ?
categories : angular
--- 
<br />
Directive is a custome HTML element that is used to extend
the power of HTML.
<br />
Directive allow you to attach behaviour to elements in DOM.
<br />
디렉티브는 돔 엘리먼트에 이렇게 해라 ~ 저렇게 해라 라고 말해주는 역할을 하는 것 같다.

<br />
<br />
There are three types of directives in angular
<br />
Angular에서 directive는 3가지로 나뉜다.


1. Attribute directive
Change the appearance or behaviour of an element

2. Structural directive
Change the DOM layout by adding or removing DOM elements

3. Component directives
Directives that have template


<br />
<hr>
<br />

> 1 - Structural Directives

<br>
Structural directive are prefixed with an astericks. Most popular ones are *ngIf & *ngFor.
<br />
Structural directive는 * 별표를 앞에 붙인다. 가장 많이 사용하는 것은 *ngIf 와 *ngFor 이다.

ngIf will remove the element depending on whether the assigned expression returns false or true.
<br />
ngIf는 ngIf 뒤에 붙는 expression이 true라면 element를 추가하고 false라면 아예 DOM에서 지워버린다.

```html
<!-- test.component.html -->

<div>
	<span *ngIf="booleanValue">False</span> // NOT
	<span *ngIf="booleanValue2">True</span> // Show
</div>

```


```ts
// test.component.ts

public booleanValue : boolean = false;
public booleanValue2 : boolean = true;


```


ngFor adds or removes elements in DOM depending on how many elements are there in iterable.

```html
<!-- test.component.html -->

<div>
	<ul>
		<li *ngFor ="let item of testList">{{item}}</li>
	</ul>
</div>

```


```ts
// test.component.ts

public testList = ['a','b','c','d'];


```


ngIf will completely remove the element from the DOM. But there are time where you don't want to remove the element but rather just hide it. Here comes in the attribute directive.
<br />
<br />
ngIf 같은 경우에 element를 DOM에서 지워버린다. 개발을 하다 보면 element를 아예 없애버리면 undefined 에러가 날때가 있다. 그냥 없애기 보다는 잠깐 화면에서 감추고 싶을 때가 있다. 그럴때는 attribute directive를 사용한다.


<br />
<br />

> 2 - Attribute Directives

<br>
Attribute directive are surrounded by brackets. Most widely used ones are [hidden], [ngClass], [ngStyle]
<br />
Attribute directive는 [] 에 쌓여 있다. 가장 많이 사용되는 것은 hidde, ngClass, ngStyle 이다.


```html
<!-- test.component.html -->

<div>
	<span [hidden]=true>This span is hidden ! </span>	
</div>

<div>
	<span [ngClass]={'label-colour':true}>This span now has a class name label-colour ! </span>	
	<span [ngStyle]={'background-color': 'yellow'}>Now this span has a background colour of yellow</span>
</div>


```














