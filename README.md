### English introduction
Please view [README-EN.md](https://github.com/hiddenhero47/tetris/blob/master/README-EN.md)

----
## Tetris made with React, Redux, and Immutable

----
Tetris has always been a classic game loved by developers to implement in various programming languages. There are many JavaScript implementations, but creating a good Tetris using React became my goal.

Click here: [https://github.com/hiddenhero47/tetris](https://github.com/hiddenhero47/tetris) to play!

----
### Effect Preview
![Effect Preview](https://img.alicdn.com/tps/TB1Ag7CNXXXXXaoXXXXXXXXXXXX-320-483.gif)

Recorded at normal speed, smooth experience.

### Responsiveness
![Responsiveness](https://img.alicdn.com/tps/TB1AdjZNXXXXXcCapXXXXXXXXXX-480-343.gif)

This does not only mean screen adaptation but also `responsive operations using the keyboard on PC and fingers on mobile`:

![Mobile](https://img.alicdn.com/tps/TB1kvJyOVXXXXbhaFXXXXXXXXXX-320-555.gif)

### Data Persistence
![Data Persistence](https://img.alicdn.com/tps/TB1EY7cNXXXXXXraXXXXXXXXXXX-320-399.gif)

What’s the most fearsome thing about playing offline games? Power failure. By subscribing to `store.subscribe`, the state is stored in localStorage, accurately recording all statuses. If the webpage closes, refreshes, crashes, or the phone runs out of battery, reopening the game will continue where you left off.

### Redux State Preview ([Redux DevTools extension](https://github.com/zalmoxisus/redux-devtools-extension))
![Redux State Preview](https://img.alicdn.com/tps/TB1hGQqNXXXXXX3XFXXXXXXXXXX-640-381.gif)

Redux is designed to manage all states that need to be saved, ensuring the persistence described above.

----
The game framework uses React + Redux and incorporates Immutable to handle Redux state. (For an introduction to React and Redux, refer to: [React Beginner Examples](http://www.ruanyifeng.com/blog/2015/03/react.html))

## 1. What is Immutable?
Immutable refers to data that, once created, cannot be modified. Any modification, addition, or deletion of an Immutable object will return a new Immutable object.

### First Encounter:
Let’s look at the following code:
``` JavaScript
function keyLog(touchFn) {
  let data = { key: 'value' };
  f(data);
  console.log(data.key); // Guess what it will print?
}
```

Without checking f, you can’t be sure what data.key will print. But if data is Immutable, you can be sure it will print value:
``` JavaScript
function keyLog(touchFn) {
  let data = Immutable.Map({ key: 'value' });
  f(data);
  console.log(data.get('key'));  // value
}
```

In JavaScript, Object and Array use reference assignment, where new objects simply reference the original object. Changing the new one also affects the old one:
``` JavaScript
foo = {a: 1};  bar = foo;  bar.a = 2;
foo.a // 2
```

While this approach saves memory, it can cause uncontrollable states in complex applications, negating the advantages of memory savings.

Immutable behaves differently:
``` JavaScript
foo = Immutable.Map({ a: 1 });  bar = foo.set('a', 2);
foo.get('a') // 1
```

Simplicity:
In Redux, the best practice is for each reducer to return a new object (or array). This often results in code like this:
``` JavaScript
// reducer
...
return [
   ...oldArr.slice(0, 3),
   newValue,
   ...oldArr.slice(4)
];
```

To return a new object (or array), this awkward structure is often unavoidable. It becomes even trickier with deeper data structures. Let’s see how Immutable simplifies this:
``` JavaScript
// reducer
...
return oldArr.set(4, newValue);
```

Isn’t that much simpler?

On ===:
We know that === comparison for Object and Array compares reference addresses, not values:

``` JavaScript
{a:1, b:2, c:3} === {a:1, b:2, c:3}; // false
[1, 2, [3, 4]] === [1, 2, [3, 4]]; // false
```

To compare values, you would have to use deepCopy or deepCompare, which is tedious and performance-heavy.

Immutable simplifies this:
``` JavaScript
map1 = Immutable.Map({a:1, b:2, c:3});
map2 = Immutable.Map({a:1, b:2, c:3});
Immutable.is(map1, map2); // true

List1 = Immutable.fromJS([1, 2, [3, 4]]);
List2 = Immutable.fromJS([1, 2, [3, 4]]);
Immutable.is(List1, List2); // true
```

Refreshing, isn’t it?

For Tetris, imagine the game board as a 2D array, with movable blocks being shapes (also 2D arrays) + coordinates. The combination of the board and blocks forms the final Matrix. In the game, these properties are all built using Immutable. Its comparison methods make implementing shouldComponentUpdate straightforward. Source code: /src/components/matrix/index.js#L35
