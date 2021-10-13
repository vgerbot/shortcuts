# shortcuts

## Install

```sh
$ npm install shortcuts --save
$ # or
$ yarn add shortcuts
```

Then import/require the module:

```js
const { match } = require('@shortcuts/core');
// or
import { match } from '@shortcuts/core';
```

## Usage

### Pattern matching

```js
import { match } from '@shortcuts/core';
const matcher = match
    .case('Ctrl+A', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}`)) // output when matching: Pressed 'Ctrl+A'
    .case('Mod+Z', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}'`)) // output when matching: Mac: `Pressed 'Meta+A'`, Win,Linux:`Pressed 'Ctrl+A'`
    .case('Ctrl+P,Shift+Ctrl+P', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}`)) // output when matching: `Pressed 'Ctrl+P'` or `Pressed 'Shift+Ctrl+P'`
    ;
document.body.addEventListener('keydown', e => {
    matcher(e);
})
```

You may omit `.case`.

```js
import { match } from '@shortcuts';
const matcher = match('Ctrl+A', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}`)) // output when matching: Pressed 'Ctrl+A'
    ('Mod+Z', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}'`)) // output when matching: Mac: `Pressed 'Meta+A'`, Win,Linux:`Pressed 'Ctrl+A'`
    ('Ctrl+P,Shift+Ctrl+P', (shortcutEvent) => console.log(`Pressed '${shortcutEvent.shortcut}`)) // output when matching: `Pressed 'Ctrl+P'` or `Pressed 'Shift+Ctrl+P'`
    ;
document.body.addEventListener('keydown', e => {
  matcher(e);
})
```

The result of the callback will be returned from matcher.

```js
import { match } from '@shortcuts';
const matcher =
    match('Ctrl+A', () => 'A')
    ('Ctrl+B', () => 'B')
document.body.addEventListener('keydown', e => {
  const result = matcher(e);
  console.log(result); // If there is no match, `undefined` will be returned.
})
```

If you provide a value instead of a function, that value will be returnd

```js
import { match } from '@shortcuts';
const matcher =
    match('Ctrl+A', 'A')
    ('Ctrl+B', 'B')
document.body.addEventListener('keydown', e => {
  const result = matcher(e);
  console.log(result);  // If there is no match, `undefined` will be returned.
})
```

You may use the `.else` to define a callback  if no shortcut pattern matches.

```js
import { match } from '@shortcuts';
const matcher =
    match('Ctrl+A', 'A')
    ('Ctrl+B', 'B')
    .else(() => console.log('no shortcut pattern matches'));
document.body.addEventListener('keydown', e => {
  matcher(e);
})
```

### Shortcut context

```js
import { context } from '@shortcuts/core';

// Create root context
const root = context()

// These will be execute in all contexts.
root.keydown('Ctrl+C', () => { });
root.keydown('Ctrl+C', () => { });

// Setup handlers for context
root.setup('foo', ctx => {
    ctx.keydown('Ctrl+A', (shortcutEvent) => {});
    ctx.keyup('Ctrl+A', (shortcutEvent) => {});
});
root.setup('bar', ctx => {
    ctx.keydown('Ctrl+B', (shortcutEvent) => {});
    ctx.keyup('Ctrl+B', (shortcutEvent) => {});
});

// Activate the contexts when appropriate
mRouter.on('/foo', () => {
    root.activate('foo');
})
// or
dialog.on('shown', () => {
    root.activate('bar');
})
```

fallback context

```js
import { fallback, context } from '@shortcuts/core';
const root = context();

// Setup handlers for context
root.setup('foo', ctx => {
    ctx.keydown('Ctrl+A', (shortcutEvent) => {
        console.log(`[foo] Ctrl+A`);
    });
});
root.setup('bar', ctx => {
    ctx.keydown('Ctrl+B', (shortcutEvent) => {
        console.log(`[bar] Ctrl+B`);
    });
});
root.setup('baz', ctx => {
    ctx.keydown('Ctrl+C', (shortcutEvent) => {
        console.log(`[baz] Ctrl+C`);
    });
});

fallback(root, 'baz')('bar') // fallback shortcut events from baz to bar
fallback(root, 'bar')('foo') // fallback shortcut events from bar to foo
```

If a shortcut key event listener it not found in current activated context, by default, the `shortcuts` will look for same shortcut key in root context, but if you specified a fallback context, the `shortcts` will look for that *fallback context* first.

```js
root.activate('baz');
// User pressed Ctrl+A
// Console outputs: [foo] Ctrl+A
```

### Integration with thirdparty framework/library

#### Rxjs

```js
import { shortcut } from '@shortcuts/rxjs';
import { fromEvent } from 'rxjs';

fromEvent(document.body, 'keydown')
.pipe(shortcut('Ctrl+A'))
.subscribe(e => console.log('Ctrl+A'))
```

#### React.js

```jsx
import { useShortcut } from '@shortcuts/react';

export const ExampleComponent = () => {
    const [count, setCount] = useState(0);
    useShortcut('Ctrl+K', () => {
        setCount(prev => prev + 1)
    })
    return (
        <span>Pressed {count} times</span>
    );
}

```

The hook takes care of all the binding and unbinding for you. As soon as the component mounts into the DOM, the key stroke will be listened to.When the component unmounts, it will stop listening.

#### Vue.js

```vue
<template>
    <my-component  @shortcut="{'ctrl+alt+o': doTheAction}">
</template>
<script>
    import shortcuts_vue from '@shortcuts/vue';
    Vue.use(shortcuts_vue);
<script>
```

You can define all shortcut key mapping in the global method.

```vue
<template>
    <my-component @shortcut="{'action1': doTheAction}">
</template>
<script>
    import shortcuts_vue from '@shortcuts/vue';
    Vue.use(shortcuts_vue, {
        keymap: {
            'action1': 'ctrl+alt+o'
        }
    });
</script>
```

#### Angular
