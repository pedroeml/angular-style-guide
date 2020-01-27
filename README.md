# Angular Code Style Guidelines

## Table of Contents

1. [HTML](#html)
2. [TypeScript](#typescript)
3. [Unit testing](#unit-testing)
4. [RxJs](#rxjs)

## HTML

### Properties

The usual HTML properties come before Angular's.

#### ngIf

The condition must be visible on the first line.

```html
// BAD
<app-component class="my-class" id="my-id" *ngIf="condition" [input]="myInput" (output)="handle($event)">
</app-component>

// BAD
<app-component
    class="my-class"
    id="my-id"
    *ngIf="condition"
    [input]="myInput"
    (output)="handle($event)">
</app-component>

// GOOD
<app-component *ngIf="condition"
    class="my-class"
    id="my-id" 
    [input]="myInput"
    (output)="handle($event)">
</app-component>
```

#### ngSwitch

The `ngSwitch` and `*ngSwitchCase` must be visible on the first line.

```html
// BAD
<app-modal #myModal [ngSwitch]="stage">
    <app-component
        *ngSwitchCase="myEnum.STAGE_A"
        class="my-class-a"
        [input]="myInputA"
        (output)="handleA($event)">
    </app-component>

    <app-component
        *ngSwitchCase="myEnum.STAGE_B"
        class="my-class-b"
        [input]="myInputB"
        (output)="handleB($event)">
    </app-component>
</app-modal>

// GOOD
<app-modal #myModal [ngSwitch]="stage">
    <app-component *ngSwitchCase="myEnum.STAGE_A"
        class="my-class-a"
        [input]="myInputA"
        (output)="handleA($event)">
    </app-component>

    <app-component *ngSwitchCase="myEnum.STAGE_B"
        class="my-class-b"
        [input]="myInputB"
        (output)="handleB($event)">
    </app-component>
</app-modal>
```

### Input & Output

```html
// BAD
<app-component [input]="myInput" 
               (output)="handle($event)">
</app-component>

// BAD
<app-component
    [input]="myInput" 
    (output)="handle($event)"
></app-component>

// BAD
<app-component
    [input]="myInput" 
    (output)="handle($event)"></app-component>

// BAD
<app-component [input]="myInput" 
    (output)="handle($event)">
</app-component>

// BAD
<app-component [input]="myInput" (output)="handle($event)">
</app-component>

// GOOD
<app-component
    [input]="myInput"
    (output)="handle($event)">
</app-component>

// GOOD (only one property)
<app-component [input]="myInput"></app-component>
```

### DOM Element Variable

If you're making an element accessible through a variable, make sure you left its declaration right at the beginning so others will know it's available in its component class' scope.

```html
// BAD
<app-component *ngIf="condition" #myComponent
    class="my-class" 
    [input]="myInput"
    (output)="handle($event)">
</app-component>

// GOOD
<app-component #myComponent *ngIf="condition"
    class="my-class"
    [input]="myInput"
    (output)="handle($event)">
</app-component>
```

## TypeScript

### Method call signature

```javascript
// BAD
doSomething(thingA, thingB, thingC, thingD,
    thingE, thingF);

// BAD
doSomething(thingA, thingB, thingC, thingD,
            thingE, thingF);

// BAD
doSomething(
    thingA,
    thingB,
    thingC,
    thingD,
    thingE);

// GOOD
doSomething(thingA, thingB, thingC, thingD, thingE, thingF);
```

### Multiple return statements

```javascript
// BAD
public doSomething(thing: Thing): Something {
    if (thing.oneThing === 1) {
        return doOneThing(thing);
    }
    if (thing.otherThing) {
        if (thing.otherThing.oneThing !== 1) {
            return doOneThing(thing.otherTing.oneThing);
        }
        return doOtherThing(thing);
    }

    return doSomethingElse(thing);
}

// BAD
public doSomething(thing: Thing): Something {
    return thing.oneThing === 1 ? doOneThing(thing) :
        thing.otherThing ? doOtherThing(thing) : doSomethingElse(thing);
}

// GOOD (only if each block has 1 or 2 lines)
public doSomething(thing: Thing): Something {
    if (thing.oneThing === 1) {
        return doOneThing(thing);
    } else if (thing.otherThing) {
        return doOtherThing(thing);
    }

    return doSomethingElse(thing);
}

// GOOD (only if the method scope doesn't have many ifs)
public doSomething(thing: Thing): Something {
    let something: Something;

    if (thing.oneThing === 1) {
        something = doOneThing(thing);
    } else if (thing.otherThing) {
        something = doOtherThing(thing);
    } else {
        something = doSomethingElse(thing);
    }

    return something;
}
```

### Return boolean expression

```javascript
// BAD
public canDoSomething(thing: Thing): boolean {
    return !!thing && thing.oneThing === 1 && !!thing.otherThing && thing.otherThing.oneThing === 1;
}

// BAD
public canDoSomething(thing: Thing): boolean {
    return !!thing &&
        thing.oneThing === 1 &&
        !!thing.otherThing &&
        thing.otherThing.oneThing === 1;
}

// GOOD
public canDoSomething(thing: Thing): boolean {
    return !!thing
        && thing.oneThing === 1
        && !!thing.otherThing
        && thing.otherThing.oneThing === 1;
}
```

### Nullable params

If your method may receive a `null`/`undefined` parameter and you're implemeting something to handle that inside of its scope, you should also make it explicit on the method's signature that it may receive a nullable type.

```javascript

// BAD
public doSomething(thing: Thing): Something {
    if (!thing) {
        ...
    }
    ...
}

// GOOD
public doSomething(thing?: Thing): Something {
    if (!thing) {
        ...
    }
    ...
}
```

### Mapper methods

If your code make use of some mapper methods and some values in your code may be `null`/`undefined`, the flow control must be outside of the mapper methods. Thus you'll avoid some bugs hard to trace and won't add more responsabilities to a mapper method.

```javascript
// BAD
public static mapToSomething(thing?: Thing): Something {
    if (!thing) {
        return undefined;   // or null
    }
    ...
}

public static doSomething(things: Thing[]): void {
    const somethingList: Something[] = things.map(thing => mapToSomething(thing));
    ...
}

// GOOD
public static mapToSomething(thing: Thing): Something {
    ...
}

public static doSomething(things: Thing[]): void {
    const somethingList: Something[] = things
        .filter(thing => !!thing)
        .map(thing => mapToSomething(thing));
    ...
}
```

### Arrow Functions

```javascript
// BAD
public static doSomething(things: Thing[]): void {
    const somethingList: Something[] = things.filter(thing => {
        return !!thing && !!thing.oneThing && thing.oneThing === 1;
        }).map(thing => mapToSomething(thing));
    ...
}

// BAD
public static doSomething(things: Thing[]): void {
    const somethingList: Something[] = things.filter(thing =>
        !!thing && !!thing.oneThing && thing.oneThing === 1
        ).map(thing => mapToSomething(thing));
    ...
}

// GOOD
public static doSomething(things: Thing[]): void {
    const somethingList: Something[] = things
        .filter(thing => !!thing && !!thing.oneThing && thing.oneThing === 1)
        .map(thing => mapToSomething(thing));
    ...
}
```

### Import formatting

Try to keep everything you need from one file in one line. If one line with between 150 to 200 chars is not enough, maybe it you should consider refactoring the class you're implementing breaking it into smaller ones.

```javascript
// BAD (double quotes)
import {MyService} from "./../my-module/my-service.service.ts";

// BAD
import {
    MyService
} from './../my-module/my-service.service.ts';

// BAD
import { MyService } from './../my-module/my-service.service.ts';

// GOOD
import { MyService } from '../my-module/my-service.service.ts';
```

### Dependecy injection via constructor method 

For injecting dependencies to a component (or any class annotated with `@Injectable`) constructors should be without access modifier. It's also recommended to its dependencies be `private` and `readonly`, thus there's no way to an external component overwrite another component providers.

```javascript
// BAD (bad line breaking)
public constructor(public myMapper: MyMapper,
    public myService: MyService) { }

// BAD (no comma)
constructor(
    public myService: MyService
) { }

// BAD (empty line in constructor scope)
constructor(
    public myService: MyService
) {

}

// BAD
constructor() { }


// GOOD (for git diffs)
constructor(
    private readonly myMapper: MyMapper,
    private readonly myService: MyService,
) { }

// GOOD (for avoiding unnecessary lines)
constructor(
    private readonly myMapper: MyMapper,
    private readonly myService: MyService) { }
```

### Input & Output
```javascript
// BAD
@Component({
    ...
})
export class MyComponent {
    @Input() something: Something;
    @Output() didSomething: EventEmitter<Something> = new EventEmitter<Something>();
    ...
}
// GOOD
@Component({
    ...
})
export class MyComponent {
    @Input()
    public something: Something;
    @Output()
    public readonly didSomething: EventEmitter<Something>;

    constructor() {
        didSomething = new EventEmitter<Something>();
    }
    ...
}
```

## Unit testing

### `spyOn` method without Stub class

```javascript
// BAD
describe('When the method [doSomething] is called', () => {
    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callFake(() => {
                return of(new Something());
            });
            ...
        });
        ...
    });
});

// GOOD
describe('When the method [doSomething] is called', () => {
    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.returnValue(of(new Something()));
            ...
        });
        ...
    });
});
```

### `spyOn` method with Stub class
```javascript
// GOOD
// my-service.service.stub.ts
export class MyServiceStub {
    ...
    public getSomething(): Observable<Something> {
        return of(new Something());
    }
}
// my-component.component.spec.ts
beforeEach(async(() => {
    TestBed.configureTestingModule({
        providers: [
            { provide: MyService, useClass: MyServiceStub },
            ...
        ],
        ...
    }).compileComponents().then(() => {
        ...
    });
}));

describe('When the method [doSomething] is called', () => {
    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callThrough();
            ...
        });
        ...
    });
});
```

### Running the methods you're testing outside `beforeEach`

Besides the code duplication problem, you may be prompt to be calling other method instead accidently or passing different params to the method.

```javascript
// BAD
describe('When the method [doSomething] is called', () => {
    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callThrough();

            myComponent.isChecked = true;
            myComponent.isAbleToDoSomething = true;
        });

        it('Should have called [getSomething] from MyService', () => {
            myComponent.doSomething(123);
            expect(myService.getSomething).toHaveBeenCalledWith(123);
        });

        it('Should have changed [isChecked] to false', () => {
            myComponent.doSomething(123);
            expect(myComponent.isChecked).toBeFalsy();
        });
    });
});

// GOOD
describe('When the method [doSomething] is called', () => {
    const code: number = 123;

    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callThrough();

            myComponent.isChecked = true;
            myComponent.isAbleToDoSomething = true;

            myComponent.doSomething(code);
        });

        it('Should have called [getSomething] from MyService', () => {
            expect(myService.getSomething).toHaveBeenCalledWith(code);
        });

        it('Should have changed [isChecked] to false', () => {
            expect(myComponent.isChecked).toBeFalsy();
        });
    });
});
```

### `beforeEach` scope duplication

```javascript
// BAD
describe('When the method [doSomething] is called', () => {
    const code: number = 123;

    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callThrough();

            myComponent.isChecked = true;
            myComponent.isAbleToDoSomething = true;
            myComponent.doSomething(code);
        });

        ...
    });

    describe('When the class property [isChecked] is false', () => {
        beforeEach(() => {
            spyOn(myService, 'getSomething').and.callThrough();

            myComponent.isChecked = false;
            myComponent.isAbleToDoSomething = true;
            myComponent.doSomething(code);
        });

        ...
    });
});

// GOOD
describe('When the method [doSomething] is called', () => {
    const code: number = 123;

    beforeEach(() => {
        spyOn(myService, 'getSomething').and.callThrough();

        myComponent.isAbleToDoSomething = true;
    });

    describe('When the class property [isChecked] is true', () => {
        beforeEach(() => {
            myComponent.isChecked = true;
            myComponent.doSomething(code);
        });

        ...
    });

    describe('When the class property [isChecked] is false', () => {
        beforeEach(() => {
            myComponent.isChecked = false;
            myComponent.doSomething(code);
        });

        ...
    });
});
```

### `it` scope duplication

```javascript
// BAD
describe('When the method [doSomething] is called', () => {
    const code: number = 123;
    ...

    describe('When the class property [isChecked] is true', () => {
        ...

        it('Should have called [getSomething] from MyService', () => {
            expect(myService.getSomething).toHaveBeenCalledWith(code);
        });

        it('Should have changed [isChecked] to false', () => {
            expect(myComponent.isChecked).toBeFalsy();
        });
    });

    describe('When the class property [isChecked] is false', () => {
        ...

        it('Should have called [getSomething] from MyService', () => {
            expect(myService.getSomething).toHaveBeenCalledWith(code);
        });

        it('Should have changed [isChecked] to true', () => {
            expect(myComponent.isChecked).toBeTruthy();
        });
    });
});

// GOOD
const doSomethingBaseCases = (code: number, isChecked: boolean) => {
    it('Should have called [getSomething] from MyService', () => {
        expect(myService.getSomething).toHaveBeenCalledWith(code);
    });

    it(`Should have changed [isChecked] to ${isChecked}`, () => {
        expect(myComponent.isChecked).toBe(isChecked);
    });
};

describe('When the method [doSomething] is called', () => {
    const code: number = 123;
    ...

    describe('When the class property [isChecked] is true', () => {
        ...

        describe('When the in common base cases are checked', () => {
            doSomethingBaseCases(code, false);
        });
    });

    describe('When the class property [isChecked] is false', () => {
        ...

        describe('When the in common base cases are checked', () => {
            doSomethingBaseCases(code, true);
        });
    });
});
```

## RxJs

```javascript
// TODO
```
