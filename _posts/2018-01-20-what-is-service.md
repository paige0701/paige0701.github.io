---
layout : post
title : Services & Injectables in Angular
categories : angular
---

So continuing on from the post "How components communicate with each other in Angular.",
services are a great way to transfer or communicate data among components that do not know each other.

"How components communicate with each other in Angular." 에서 마지막인 5번째 방법은 여기서 이어서 쓰려고 한다.
서비스는 서로 연결되어 있는 않은 컴포넌트끼리 통신하기 좋은 방법 중 하나이다.

```ts

// generally services look like this.
// 서비스는 이렇게 생겼다.
import { Injectable } from '@angular/core';


/** @Injector() annotation tells angular that AnimalService might get Injected in some component.
But if you want to inject AnimalService into a component, you must first provide it. */
/** @Injector()라는 것을 서비스 위에 써줌으로써 앵규러에게 어떤 컴포넌트에 주입해서 사용할 수 있게 하는 것이다. 
컴포넌트에 주입하기 이전에 먼저 provide를 해야 사용할 수 있다. */
@Injectable()
export class AnimalService {

	public name : string = 'Paige';

	public getName (name : string) {

		return `My name is {name}`;
	}

	constructor() {

	}
}


```



> You can provide a service in a module or in a component.
> Module 이나 component 안에다가 provide를 선언할 수 있다.

```ts
// in module animal.module.ts
@NgModule({

	providers : [AnimalService] // provide in module // module 안에서 provider 설정
	declarations: [ RabbitComponent, TigerComponent ], // Components in Animal Module

})
export class AnimalModule{

}

// in component rabbit.component.ts
@Component({
	selector : 'app-rabbit',
	template : ``,
	providers : [AnimalService] // provide in component // component안에서 provider 설정

})
export class RabbitComponent {


}

```


Difference between providing a service in module and component is the the area or the scope the service covers.
If you provide the AnimalService in the module, you can share the service among all the components inside the AnimalModule.
If you provide it in the RabitComponent, the service is only available in the RabbitComponent and its child components.
When you have provided the service, you are ready to inject it into a component.

Service를 component 안에 provide하는 것과 module안에서 provide하는 것은 차이가 있다.
서비스를 어디까지 사용할 수 있냐가 어디에 provide하느냐에 따라서 달라진다.
AnimalModule은 RabbitComponent와 TigerComponent를 둘 다 갖고 있다. AnimalModule안 declaration에
RabbitModule, TigerModule을 넣었다. 그러니 이 두 개에서 AnimalService를 쓰고 싶으면 AnimalModule에 넣어서 사용하면 된다.
하지만 RabbitComponent에서만 AnimalService를 사용하고 싶으면 RabbitComponent에 추가하면 된다. 
그러면 RabbitComponent 와 RabbitComponent의 자식 component까지만 AnimalService에 접근 가능하다.
TigerComponent는 AnimalService사용 불가이다.


```ts
// rabbit.component.ts

import { AnimalService } from ~

@Component({
	selector : 'app-rabbit'
	template : `<h3>Name  =  { { nameFromService } }</h3>
	<div (click) = "changeName('Jenny')" ></div>` 
})		
export class RabbitComponent {


	public nameFromService : string;

	// 서비스는 생성자를 통해서 component에 주입한다. 그러면 this.animalService 로 서비스 접근 가능
	constructor(private animalService : AnimalService) {

		/**  여기 안에서 만든 변수 nameFromService 에 서비스에서 받아온 이름을 넣고 화면에 nameFromService을 찍으면 Jenny가 찍힌다.*/
		this.nameFromService = this.animalService.name; 
	}


	/** template안에 div 를 클릭하면 아래 method를 타게 되어있다. 
	이름을 Jenny로 바꾸면 서비스 안에 있는 name의 값은 Paige에서 Jenny로 바뀐다. */
	public changeName(name : string) {

		this.animalService.name = name;
	}

}

```


```ts
// tiger.component.ts

import { AnimalService } from ~

@Component({
	selector : 'app-tiger',
	template : ``

})
export class TigerComponent implements OnInit {

	constructor(private animalService : AnimalService) { // Inject animal service

	}

	ngOnInit() {

		// My name is Paige 
		// 서비스에 있는 메소드 호출
		console.info(this.animalService.getName('Paige'));  


		console.info(this.animalService.name); // Jenny 
		/** if rabbitComponent has been executed before this component. 
		name from AnimalService has been change from Paige to Jenny  */

		/** 현재 컴포넌트가 RabbitComponent 보다 후에 열린다면 this.animalService.name은
		Paige가 아닌 Jenny로 바뀌었을 것이다. RabbitComponent에서 이름을 바꿨기 때문에 !  */

	}

}


```



There are some drawbacks to using service.
We don't know when the value has changed.
This is because we are not firing an event when the value is changed.
Also, you must init the value if you are finished with it. Or else the service keeps the value forever.

서비스를 이용해서 데이터를 컴포넌끼리 공유할 때 문제점도 있다.
값이 변경되는 시점을 알 수 없고 초기화 시키지 않으면 그 값은 계속 유지되기 때문에 조심해야 한다.









