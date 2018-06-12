# Minimap

> A tiny state machine to track menu interactions.

## Concept

Syncing the state of a menu (e.g. nav, dropdown, etc...) is difficult, especially if the menu is nested and/or supports multiple interaction types (e.g. hover, focus, etc...).

The idea for Minimap is to transform the menu (state) into a 2D matrix, and plots the items based on `x`/`y` coordinates.

This 2D matrix allows for:

- Fast position detection for current/next move
- Simple keyboard navigation calculation
- Nested menu

## States

The state values of the map are represented below:

| Value | Represents                             |
| ----- | -------------------------------------- |
| `0`   | Empty space, cannot move to this space |
| `1`   | Inactive item                          |
| `2`   | Active item                            |
| `3`   | Hovered item                           |
| `4`   | Focused item                           |
| `8`   | Linked item                            |
| `9`   | Disabled item                          |

## Example: Flat menu

A menu is list items:

```
[
  'apple',
  'banana',
  'cranberry'
]
```

In addition to the items, Minimap needs to know the state of each item, which may be represented like:

```
[
  ['apple', 'active]
  ['banana', '']
  ['cranberry' '']
]
```

This information gets transformed into a 2D coordinates + state:

```
[
  [0,0,2]
  [1,0,1]
  [2,0,1]
]
```

You can imagine the simplified menu like this:

```
[
  [2]
  [1]
  [1]
]
```

#### Explanation

Each item gets transformed into an `[x,y]` 2D array. The coordinates represent it's state and position on the matrix.

#### Matrix

The above menu will generate a simple matrix that looks like:

```
2
1
1
```

#### Movement

Moving down (e.g. pressing `down` on your keyboard), will shift the states, resulting in:

```
1
2
1
```

Pressing down again will result in:

```
1
1
2
```

**[Matrix movement demo](https://codepen.io/ItsJonQ/pen/qKreKK)**

## Example: Nested menu

A nested menu is list items:

```
[
  'apple',
  'banana',
  ['melons', ['honeydew', 'watermelon']
]
```

Nested menu state:

```
[
  ['apple', 'active]
  ['banana', '']
  ['melons', [
    ['honeydew', '']
    ['watermelon', '']
  ]]
]
```

This information gets transformed into a 2D coordinates + state:

```
[
  [0,0,2] <-- apple
  [1,0,1] <-- banana
  [2,0,1] <-- melons
  [2,1,1] <-- honeydew
  [3,1,1] <-- watermelon
]
```

#### Matrix

The above menu will generate a simple matrix that looks like:

```
2 0
1 0
1 1
0 1
```

#### Movement

Below details the movement of `down`, `down`, `right`, `down`:

`down`

```
1 0
2 0
1 1
0 1
```

`down`

```
1 0
1 0
2 1
0 1
```

`right`

```
1 0
1 0
1 2
0 1
```

`down`

```
1 0
1 0
1 1
0 2
```

# Example: Multi-nested menu

A nested menu is list items:

```
[
  ['apples', ['fuji', 'green']
  'banana',
  ['melons', ['honeydew', 'watermelon']
]
```

Nested menu state:

```
[
  ['apples', [
    ['fuji', 'active']
    ['green', '']
  ]]
  ['banana', '']
  ['melons', [
    ['honeydew', '']
    ['watermelon', '']
  ]]
]
```

This information gets transformed into a 2D coordinates + state:

```
[
  [0,0,2] <-- apples
  [0,1,1] <-- fuji
  [1,1,1] <-- green
  [2,0,1] <-- banana
  [3,0,1] <-- melons
  [3,1,1] <-- honeydew
  [4,1,1] <-- watermelon
]
```

#### Matrix

The above menu will generate a simple matrix that looks like:

```
2 1
8 1
1 0
1 1
0 1
```

- `8` represents a link. This allows the user to move between Apples (at `0,0`) to Banana (at `2,1`) with a single movement.

#### Movement

Below details the movement of `down`, `up`, `right`, `down`:

`down`

```
1 1
8 1
2 0
1 1
0 1
```

`up`

```
2 1
8 1
1 0
1 1
0 1
```

`right`

```
1 2
8 1
1 0
1 1
0 1
```

`down`

```
1 1
8 2
1 0
1 1
0 1
```

## Collision handling

Arrow movements are bound to the width/height of the matrix, and cannot move into `0` values.

## Encode/Decode

Functions will be provided to encode a collection of items into a Minimap matrix and vice versa.

## Observable

Events will be broadcasted on every state change.

## Inspiration/references

* Game map design
* Collision detection
* State machines
* Redux
