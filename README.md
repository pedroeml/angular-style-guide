# Angular Code Style Guidelines

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
    } else if (thing.otherThing) {
        return doOtherThing(thing);
    }

    return doSomethingElse(thing);
}

// BAD
public doSomething(thing: Thing): Something {
    return thing.oneThing === 1 ? doOneThing(thing) :
        thing.otherThing ? doOtherThing(thing) : doSomethingElse(thing);
}

// GOOD
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
// BAD
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

For injecting dependencies to a component (or any class annotated with `@Injectable`) constructors should be without access modifier. It's also recommended to its dependencies be `private` and `readonly`, thus there's now way to an external component overwrite another component providers.

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
