react-tween
===
Tween animation for React components

Demos
---
- [Animate opacity and color with `<Tween />`](http://codepen.io/mking-clari/pen/JRqzLN)
- [Animate bars with `<TransitionTween />`](http://codepen.io/mking-clari/pen/yadomz)

Usage
---
- `<Tween />` is used to animate a single value over time.
- `<TransitionTween />` is used to animate a list of items where items are being added and removed.

```javascript
import { Animations, TransitionTween, Tween } from 'react-tween';

// Tween is declarative.
// toValue defines the destination value.
// Whenever the destination changes, the style is animated to that
// destination value.
<Tween animation={Animations.timing({ toValue: { opacity: 1 } })}>
  {style => (
    <div style={{ opacity: style.opacity }}>
      Animate opacity
    </div>
  )}
</Tween>

// TransitionTween takes the place of TransitionGroup.
<TransitionTween
  animations={[
    {
      // The key is the child component key.
      key: '0',
      // The animation describes the set of values being animated.
      animation: Animations.timing({ toValue: { height: 100 } }),
      // The data is an arbitrary value associated with the key.
      data: '0',
    },
    {
      key: '1',
      animation: Animations.timing({ toValue: { height: 200 } }),
      data: '1',
    },
  ]}
  // The willEnter prop defines the style when an item is added.
  // By default, the enter style is given by the animations in the animations array.
  willEnter={style => ({ ...style, height: 0 })}
  // The willLeave prop defines the animation when an item is removed.
  // By default, the leave animation is a no-op.
  willLeave={style => Animation.timing({ toValue: { ...style, height: 0 } })}
>
  {styles => (
    <div className="bars">
      {styles.map(style => (
        <div
          className="bar"
          key={style.key}
          style={{ height: style.style.height }}
        />
      ))}
    </div>
  )}
</TransitionTween>

// Both Tween and TransitionTween support customized delay, duration, and
// easing.
import { easeSinInOut } from 'd3-ease';

<Tween
  animation={Animations.timing({
    toValue: { opacity: 1 },
    delay: 500,
    duration: 1000,
    easing: easeSinInOut,
  })}
>
  {/* ... */}
</Tween>

// Run animations in sequence with Animations.sequence
<Tween
  animation={Animations.sequence([
    // The color will first animate to green, then to red.
    Animations.timing({ toValue: { color: 'green' } }),
    Animations.timing({ toValue: { color: 'red' } }),
  ])}
>
  {/* ... */}
</Tween>

// Run animations in parallel with Animations.parallel
<Tween
  animation={Animations.parallel([
    Animations.timing({ toValue: { color: 'orange' } }),
    Animations.timing({ toValue: { opacity: 1 } }),
  ])}
>
  {/* ... */}
</Tween>

// Stagger animations with Animations.stagger
<Tween
  animation={Animations.stagger(500, [
    // First, the color will animate to orange.
    // When the color is halfway to orange, the opacity will begin animating to 1.
    Animations.timing({
      toValue: { color: 'orange' },
      duration: 1000,
    }),
    Animations.timing({
      toValue: { opacity: 1 },
      duration: 1000,
    }),
  ])}
>
  {/* ... */}
</Tween>
```

Implementation
---
- The structure of the `<Tween />` and `<TransitionTween />` components is based on [react-motion](https://github.com/chenglou/react-motion).
- The `Animations` API is based on [React Native's animation API](https://facebook.github.io/react-native/docs/animations.html).
- The animation is implemented using [d3's interpolation, easing, and timer](https://d3js.org). Any value d3 can interpolate, `react-tween` can interpolate.

Comparison to `react-motion`
---
Why not spring-based animation? I think spring-based animation is, in many cases, ideal. But sometimes it's not possible to use it. For example, the designer may specify a traditional easing-based animation, or the feature may require a controlled duration. In this case, it's better to use an easing-based animation rather than a spring-based one. `react-tween` is simply another tool in the frontend developer's toolbox.
