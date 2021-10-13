# shortcuts

## Install

```sh
$ npm install shortcutpm --save
$ # or 
$ yarn add shortcutpm
```

Then import/require the module:

```js
const { match } = require('shortcutpm');
import {match} from 'shortcutpm';
```

## Usage

#### Pattern matching

```js
import {match} from 'shortcutpm';
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
import {match} from 'shortcutpm';
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
import {match} from 'shortcutpm';
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
import {match} from 'shortcutpm';
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
import {match} from 'shortcutpm';
const matcher = 
    match('Ctrl+A', 'A')
    ('Ctrl+B', 'B')
    .else(() => console.log('no shortcut pattern matches'));
document.body.addEventListener('keydown', e => {
  matcher(e);
})
```

### Rxjs operator

```js
import { shortcut } from 'shortcutpm/rxjs';
import { fromEvent } from 'rxjs';

fromEvent(document.body, 'keydown')
.pipe(shortcut('Ctrl+A'))
.subscribe(e => console.log('Ctrl+A'))
```

### React hook

```jsx
import { useShortcut } from 'shortcutpm/react';

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


