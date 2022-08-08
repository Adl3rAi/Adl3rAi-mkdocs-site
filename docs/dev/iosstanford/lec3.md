# Lecture 3

**MVVM**

* Model
  * UI Independent
  * Data + Logic
  * The information itself
    * <u>notices changes</u>
* ViewModel
  * Binds View to Model
  * Interpreter
  * Gatekeeper
  * Process Intent
    * <u>publishes "something changed"</u>
    * `ObservableObject`
    * `@Published`
    * `objectWillChange.send()`
    * <u>modifies the Model</u>
* View
  * Reflects the Model
  * Stateless(`@State`)
  * Declared
  * Reactive
    * <u>automatically observes publications, pulls data and rebuilds</u>
    * `@ObservedObject`
    * `@Binding`
    * `.onReceive`
    * `@EnvironmentObject`
    * `.environmentObject()`
    * <u>calls Intent function</u>

