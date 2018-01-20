---
layout: post
title : "How components communicate with each other in Angular."
categories: angular
---

The most confusing part with angular was interaction among components.
This is because I didn't spend the time to study fundamentals, sending data back and forth was just too confusing.
In the official Angular document, component interaction is explained in 5 ways.
First 4 are straightforward but the last one about the service is challenging. 
I'll get back to that after studying the service first.

Angular를 처음 사용했을 때 가장 어려웠던 것은 기초를 공부할 시간이 없이 바로 실무로 투입이 되다 보니..
간단한 컴포넌트를 만들어서 사용하는 것이 너무 어렵고 시간도 오래 걸렸다.
처음에는 컴포넌트를 왜 만드는지.. 조차 모르고 그냥 막 다 copy and paste 하느라 아주 힘들었던 기억이 난다.
그래서 지금이라도 기초를 다시 잡으려고 한다. 시간을 내서 doc을 읽어보니 왜 이해가 되는 거같지? 
도큐먼트에서는 component interaction을 할 수 있는 방법을 5가지를 설명한다.
여기서 첫 번째 4개는 금방 이해할 수 있었다. 하지만 5번째는 아직 100% 이해하지 못해서 서비스 쪽 개념을 다시 공부하고 마지막 것을 정리해야겠다.

<br />
<hr>
<br />

> 1 . Pass data from parent to child with input binding. 
> <br /> 부모 컴포넌트에서 자식 컴토넌트로 데이터를 보낼때는 input binding을 사용한다.

```ts
// Parent Component

@Component({
        selector : 'app-parent',
        /** left side 'hero' is the hero variable from the child component. 
        right side hero is the value inside this component */
        template : `<app-child 
        [hero]=“hero”       
        [master]='master'></app-child>` 
        /** []를 이용해서 자식 컴포넌트로 보낼 데이터 바인딩을 한다. 왼쪽에 있는 master 가 자식클라스에 
        @Input으로 선언한 변수이다. 오른쪽은 이 컴포넌트에 선언되어 있는 master를 뜻한다. 
        여기 선언되어 있는 master는 Batman이기 때문에 Batman 값을 자식 컴포넌트에 선언되어
        있는 master변수가 받는것이다. */
})
export class ParentComponent{
    
    public hero: string = Superman;       // A variable called hero which has a value of Superman
    public master: string = 'Batman';     // Master라는 변수를 선언하고 Batman 값을 변수에 넣는다.

}
```


```ts
// Child Component

@Component({
        selector : 'app-child',
        template : `<div>My name is {{hero}}, my best friend is {{master}}</div>`, 
        /** My name is Superman, my best friend is Batman. 
        부모 클라스에서 input binding으로 hero, master값을 받아 오기 때문에 위에처럼 찍히는 것이다. */
})
export class ChildComponent{
    
    /** with this input annotation you are telling angular that you are 
    getting this value from your parent component. */
    @Input()
    public hero: string;

    // @input을 선언하면 부모클라스에서 값을 받아온다는 뜻이다.
    @Input('master') 
    public masterName  : string;

    public _name: string ;

    /** input 으로 받아온 값을 가로채서 새로운 값을 주거나 trim 같은 메소드를 사용해서값을 변경할수 있다.
    Intercept input property changes with a setter */
    @Input() 
    set name(name: string) { // if some setting is needed for an input use set
        this._name = (name && name.trim()) || '<no name set>';
    }

}
```

<br />
<hr>
<br />

> 2 . Parent listens to child event
> <br /> 부모가 자식한테서 값을 받아올때는 자식에서 뱉는(?) 이벤트를 기다린다

```ts
//Parent Component

@Component({
        selector : 'app-parent',
        /**  Here heroChangeEvent in () is the variable in parent component with @output(). And the changeHeroName() is a method that is called when heroCHangeEvent is emitted. Here $event parameter is used to transfer data.
        heroChangeEvents는 부모 컴포넌트에서 만든 변수이다. heroChangeEvent.emit()을 하면 여기로 온뒤에 changeHeroName()을 콜하는 것이다. 여기서 파라미터로는 $event를 이용해서 데이터를 넘긴다. */
        template : `<app-child (heroChangeEvent)=changeHeroName($event)></app-child> 
                    <div>The hero has changed to {{ heroName }}</div>`  
                    // The hero has changed to WonderWoman 
})
export class ParentComponent{    

    public heroName: string;

    /** Child component 에서 emit 을 하면 여기로 연결해놨으니깐 거기서 보낸 name을 받아서 
    로컬 변수에 넣어서 화면에 보여지는 것이다 */
    public changeHeroName(name) {  

        this.heroName = name;

    }

}
```


```ts
// Child Component

@Component({
        selector: 'app-child',
 // When div is clicked change method is called. Div를 클릭하면 change()메소드를 탄다
        template : `<div (click)="change()">Click to change hero</div>`, 
})
export class ChildComponent{
    
    // with output annotation you are making custom event.
    @Output()                                                          

    // output annotation 으로 선언한 heroChangeEvent를 이용해서 부모 컴포넌트로 이벤트 전파
    public heroChangeEvent : new EventEmitter(); 


    /**  heroChangeEvent.emit() 은 Parent Component 에 선언되어있는 (heroChangeEvent)="changeHeroName()" 으로 가서 changeHeroName()를 부르게 되는 것이다
    
    heroChangeEvent.emit(and what you want to send to the parent component) calls  (heroChangeEvent)="changeHeroName()" this statement in parent component. 
    So when you emit changeHeroName method is called. */
    public change() { 
        this.heroChangeEvent.emit('Wonderwoman');
    }

}
```

<br />
<hr>
<br />

> 3 . Parent interacts with child with local variable
> <br /> 변수를 선언해서 부모클라스에서 자식 클라스와 interact 한다.

By creating a template reference variable for the child element and then reference that variable within the parent template.

```ts
// Parent Component

@Component({
        selector : 'app-parent',
        /** #childComponent is naming a local variable to the child Component. 
        Now with childComponent variable, parent component can get access to
        child components variables and methods.
        # 뒤에 오는 단어는 childComponent를 새로운 변수에 담는 것이다. 그래서 #childComponent라고 지었으면 childComponent.minute로  . 을 이용해서 자식 컴포넌트에 접근할 수 있다 */
        template : `<app-child #childComponent ></app-child> 
                    <div>{{childComponent.getCurrentDay}} {{childComponent.hour}}</div>`  
                    // Wednesday 50
})
export class ParentComponent{   

}

```


```ts
// Child Component

@Component({
        selector: 'app-child',
        template : ``, 
})
export class ChildComponent{
    
    public minute : number  = 3;
    public second : number = 60;
    public hour : number  = 50;

    public getCurrentDay() {

        return 'Wednesday';
    }

}

```

<br />
<hr>
<br />

> 4 . Parent calls a viewChild()
> <br /> ViewChild()를 이용해서 자식 컴포넌트에 접근한다.

The local variable approach is easy but it is only limited to the parent template. The parent component itself has no access to the child.
Inject the child component into the parent as a ViewChild when you need to read and write child component values or must call child component methods.

3번 같은 경우에 컴포넌트를 변수에 담아서 사용하면 쉽고 간단하지만 template안에서만 가능하기 때문에 사용성에 한계가 있다.
Component에 자식 컴포넌트를 주입해서 사용하는 방법이 viewChild()방법이다

```ts
@ViewChild(ChildComponent)
private childComponent : ChildComponent; 

public numberOfSeconds: number;

ngAfterViewInit() {
 
    // 3번이랑 비슷하게 위에 this.childComponent 를 이용해서 . 을 통해 child component에 접근할수 있다.
    this.numberOfSeconds = childComponent.seconds; 

}
```

<br />
<hr>
<br />

> 5 . Parent and children communicate via service
> <br /> 부모, 자식 컴포넌트는 서비스를 이용해서 서로에서접근한다.

Parent and children share a service whose interface enables bi-directional communication within the family.
The scope of the service instance is the parent component and its children. Components outside this component subtree have no access to the service or their communications.
 
--> 여기는 조금 후에 정리 예정















