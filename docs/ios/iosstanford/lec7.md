# Lecture 7

ViewModifier

The ViewModifier protocol has one function in it.

Its only job is to create a new View based on the thing passed to it.

```swift
protocol ViewModifier {
  typealias Content // the type of the View passed to body(content:)
  func body(content: Content) -> some View {
    return some View that almost certainly contains the View content
  }
}
```

When caa `.modifier` on a View, the `Content` passed to this function is that View

ViewModifier code looks a lot like View code(`func body(content:)` instead of `var body`), because `ViewModifier`s are Views



Animation

Only changes can be animated.

ViewModifier arguments

Shapes

The "existence" of a View in the UI

Animation is showing the user changes that have already happened

ViewModifiers are the primary "change agents" in the UI

A change to a ViewModifier's arguments has to happen after the View is initially put in the UI.

In other words, only changes in a ViewModifier's arguments since it joined the UI are animated.

Not all ViewModifier arguments are animatable, but most are.

A View coming on-screen is only animated if it's joining a container that is already in the UI

A View going off-screen is only animated if it's leaving a container that is staying in the UI.

Implicitly(automatically), by using the view modifier `.animation(Animation)`

Explicitly, by wrapping `withAnimation(Animation) {}`

By making Views be included or excluded from the UI



Explicit animation creates an animation transaction during which all eligible changes made as a result of executing a block of code will be animated together.

Explicit animations are almost always wrapped around calls to ViewModel Intent functions.

Do not override an implicit animation.



Transitions

Transitions specify how to animate the arrival/departure of Views

Only works for Views that are inside CTAAOS(Containers That Are Already On-Screen)

Unlike `.animation()`, `.transition()` does not get redistributed to a container's content Views. So putting `.transition()` on the `ZStack` above only works if the entire `ZStack` came/went. Group and ForEach do distribute `.transition()` to their content Views, however.







