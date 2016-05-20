# TEventEmit

This is a simple EventEmitter based on the [eventemitter3](https://github.com/primus/eventemitter3) project. It is quite similar but has a few differences. I will list the main ones here, the others you can find on your own.

* Any Context sent into the `emit()` function should be apart of an array.
* When calling the `listeners()` function you must supply the `boolean` second argument. If you supply `true` the function will return if there are active listeners as a `boolean` value. If you supply `false` it will list the active listeners.
* When calling the `on()` function the third `once` argument must be supplied. It should be a `boolean` value which determines if the event should only be fired once and then removed
* Got rid of the `once()` function completely. If you want that functionality just supply `true` as the third argument on `on()`
* No more function aliases for `off()` or `addListener` just use `removeListener()` and `on()`
* Didn't even include `setMaxListeners()`
* `EventListeners` instance is always an array of `EventListener` and never a single object. When eventemitter3 would use something like `listener.fn` for a single listener, TEventEmit would use `listener[0].func` it keeps the type static

## How to compile

Compiling is pretty simple, the project is small so there isn't any weird stuff you need to do. I use the following flags when piping into gulp:

```javascript
'module': 'es6',
'target': 'es6',
'noImplicitAny': true,
'suppressImplicitAnyIndexErrors': true
```

That last one is required because of [this](https://github.com/Microsoft/TypeScript/issues/1232)

## Including in your project

Until I get around to adding this to NPM, just clone the repo, and include `TEventEmit.ts` in your project. The following examples assume you put the TEventEmit class in a folder called /TEventEmit/

```typescript
import TEventEmit from './TEventEmit/TEventEmit';

export default class Foo extends TEventEmit{
    constructor(){
        super();
    }
}
```

## Example usage

```typescript
import TEventEmit from './TEventEmit/TEventEmit';

export default class Foo extends TEventEmit
{
    constructor(){
        super();
    }

    public test(): void{
        // Emit an event
        this.emit('SampleEvent', ['hello', 'world']);
    }
}

function main(): number
{
    let Bar: Foo = new Foo();

    Bar.on('SampleEvent', (hello: string, world: string) => {
        console.log(`${hello} ${world}`);
    }, this, false);

    Bar.test();

    return 1;
}
```

Once this is run you will get the following output

`hello`

When passing the arguments in the `emit()` function remember their types so you can later specify their types in the `on()` function, because they are passed as type `any` leaving out the hinting will cause an error when using `'noImplicitAny': true`

## Finding listeners

```typescript
function main(): number
{
    let Bar: Foo = new Foo();

    Bar.on('SampleEvent', () => {
        i = 13;
    }, this, false);

    Bar.test();

    console.log(`First: ${Bar.listeners('SampleEvent', true)}`);
    console.log('Second:');
    console.log(Bar.listeners('SampleEvent', false));

    return 1;
}
```

Once this is run you will get the following output

`First: true`

`Second:`

`Array [ main/<() ]`

You get the results above becuase the first `listeners()` function was supplied with a truthful second argument, so it returns a `boolean` value indicating if there are listeners on the event. The second result is with a false second argument so you get an array of functions on the listener

## Removing listeners from an event

```typescript
function main(): number
{
    let Bar: Foo = new Foo();

    Bar.on('SampleEvent', () => {
        i = 13;
    }, this, false);

    Bar.test();

    Bar.removeAllListeners('SampleEvent');

    console.log(Bar.listeners('SampleEvent', true));

    return 1;
}
```

Once this is run you will get the following output

`false`

This is the case because you removed all listeners on the `SampleEvent` event.

## TODO:
* Write tests
* Improve code
* Find any buggy edge cases