# Lecture 3

## MVVM

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

---

## Varieties of Types

* `struct`

* `class`

  * Both `struct` and `class` have much exactly the same syntax

  * Both `struct` and `class` have `functions`

   ```swift
    // functions
    func multiply(operand: Int, by: Int)-> Int {
      return operand * by
    }
    multiply(operand: 5, by: 6)
    func multiply(_ operand: Int, by otherOperand: Int)->Int {
      return operand * otherOperand
    }
    multiply(5, by: 6)
   ```

  * Both `struct` and `class` have `init`ializers
  
	```swift
	struct MemoryGame {
    init(numberOfPairsOfCards: Int) {
      // create a game with that many pairs of cards
    }
  }
  ```
  
  * `struct` is a value type
  * Copied when passed or assigned
  * Copy on write, Functional programming
  * No inheritance
  *  "Free" `init` initializes ALL `var`s
  * Mutability must be explicitly stated 
  * Your "go to" data structure
  * Everything you've seen so far is a `struct`, except `View` is a protocol
  
  
  
  * `class` is a reference type
  * Passed around via pointers
  * Automatically reference counted
  * Object-oriented programming
  * Inheritance(single)
  * "Free" `init` initializes NO `var`s
  * Always mutable
  * Used in specific circumstances
  * `ViewModel` is always a `class`

* Generics

    ```swift
    struct Array<Element> {
      ...
      func append(_ element: Element) {...}
    }
    ```

* Functions

  ```swift
  (Int, Int) -> Bool 
  // take two Ints and return a Bool
  (Double) -> Void
  () -> Array<String>
  () -> Void
  var foo: (Double) -> Void
  func doSomething(what: ()->Bool)
  
  var operation: (Double) -> Double
  func square(operand: Double) -> Double {
    return operand * operand
  }
  operation = square
  let result1 = operation(4)
  // result1 == 16
  // sqrt is a build-in function in swift
  operation = sqrt
  let result2 = operation(4)
  // result2 == 2
  ```

---

## Model(`MemoryGame.swift`)

```swift
import Foundation

struct MemoryGame<CardContent> {
  private(set) var cards: Array<Card>
  
  func choose(_ card: Card) {
    
  }
  
  init(numberOfPairsOfCards: Int, createCardContent: (Int) -> CardContent) {
    cards = Array<Card>()
    for pairIndex in 0..<numberOfPairOfCards {
      let content = createCardContent(pairIndex)
      cards.append(Card(content: content))
      cards.append(Card(content: content))
    }
  }
  
  struct Card {
    var isFaceUp: Bool = false
    var isMatched: Bool = false
    var content: CardContent
  }
}
```

---

## ViewModel(`EmojiMemoryGame.swift`)

ViewModel is part of UI, but not part of View in MVVM

```swift
import SwiftUI

class EmojiMemoryGame {
    static let emojis = ["🚗","🚕","🚙","🚌","🚎","🏎","🚓","🚑","🚒","🚐", "🛻","🚚","🚛","🚜"]
    // put a static sign ahead, the emojis is really now the EmojiMemoryGame.emojis
    // static will solve the property initializers errors(make it 'global')
    
    // firstly, to creat ViewModel's own model
    // view model itself is the truth, stores the truth of the Model(MVVM)
    
//    private(set) var model: MemoryGame<String> =
//    MemoryGame<String>(numberOfPairsOfCards: 4, createCardContent: { (index: Int) -> String in // in seperate argument and function's content
//            return "😊"
//        })
    
//    private(set) var model: MemoryGame<String> =
//        MemoryGame<String>(numberOfPairsOfCards: 4) { _ in "😊" }
    static func createMemoryGame() -> MemoryGame<String> {
        MemoryGame<String>(numberOfPairsOfCards: 4) { pairIndex in
            EmojiMemoryGame.emojis[pairIndex]
        }
    }
    
    // the order of these properties(like model, like emojis) being initialized is random
    // it is no way that you can have one of their initializers depending on another one already having been initialized
    // when we want to initialize here, emojis may not initialize
    
    private(set) var model: MemoryGame<String> = EmojiMemoryGame.createMemoryGame()
//        MemoryGame<String>(numberOfPairsOfCards: 4) { pairIndex in
//            emojis[pairIndex]
//            // '=' is a property initializer
//        }
    
    // notice the func makeCardContent above, here to simplify the code
    
    // private(set) can look (from other class), but cannot change
    var cards: Array<MemoryGame<String>.Card> {
        return model.cards
    }
}
```







