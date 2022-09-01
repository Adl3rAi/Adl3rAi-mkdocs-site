# Lecture 4

## `enum`

Another variety of data structure in addition to `struct` and `class`

```swift
enum FastFoodMenuItem {
  case hamburger
  case fries
  case drink
  case cookie
}
```

An `enum` is a value type(like `struct`), so it is `copied` as it is passed around

```swift
enum FryOrderSize {
  case large
  case small
}
```

* Setting the value of an enum

```swift
let menuItem: FastFoodMenuItem = FastFoodMenu.hamburger(patties: 2)
var otherItem: FastFoodItem = FastFoodMenuItem.cookie
var yetAnotherItem = .cookie // this is illegal
```

* Checking an `enum`'s state

```swift
var menuItem = FastFoodMenuItem.hamburger(patties: 2)
switch menuItem {
  case .hamburger: break;
  case .fries: print("fries")
  case .drink: print("drink")
  case .cookie: print("cookie")
}
```

## `Optional`

An Optional is just an enum.

```swift
enum Optional<T> {
  case none
  case some(T)
}
```

Declaring something of type `Optional<T>` can be done with the syntax `T?`

You can then assign it the value `nil`(`Optional.none`)

Or you can assign it something of the type `T`(`Optional.some`)

```swift
var hello: String?
var hello: String? = "hello"
var hello: String? = nil
```

You can access the associated value either by force(with `!`)

```swift
let hello: String? = ...
print(hello!)
```

Or "safely" using `if let` and then using the safely-gotten associated value in `{}`

```swift
if let safehello = hello {
  print(safehello)
}
else {
  // do sth else
}
```

`??` "Optional Defaulting", It's called the "nil-coalescing operator"

```swift
let x: String? = ...
let y = x ?? "foo"

switch x {
  case .none: y = "foo"
  case .some(let data): y = data
}

let x: String? = ...
let y = x?.foo()?.bar?.z

switch x {
  case .none: y = nil
  case .some(let xval):
  	switch xval.foo() {
      case .none: y = nil
      case .some(let xfooval) {
        switch xfooval.bar {
          case .none: y = nil
          case .some(let xfbval): y = xfbval.z
        }
      }
    }
}
```

---

## `View`

```swift
import SwiftUI

struct ContentView: View {
    // view is just an agent for showing what in the model through the view model, should pass viewModel as an argument
    @ObservedObject var viewModel: EmojiMemoryGame
    // @ObservedObject means that something changed plz rebuild entire body
    
    var body: some View {
        ScrollView {
            LazyVGrid(columns: [GridItem(.adaptive(minimum: 65))]){
                ForEach(viewModel.cards) { card in
                    CardView(card: card)
                        .aspectRatio(2/3, contentMode: .fit)
                        .onTapGesture {
                            // this is the intent of an user
                            // intent is stored in ViewModel
                            viewModel.choose(card)
                        }
                }
            }
        }
        .foregroundColor(.red)
        .padding(.horizontal)
    }

}

struct CardView: View {
    let card: MemoryGame<String>.Card
    
    var body: some View {
        ZStack {
            let shape = RoundedRectangle(cornerRadius: 20)
            if card.isFaceUp {
                shape.fill().foregroundColor(.white)
                shape.strokeBorder(lineWidth: 3)
                Text(card.content).font(.largeTitle)
            } else if card.isMatched {
                shape.opacity(0)
            }
            else {
                shape.fill()
            }
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        let game = EmojiMemoryGame()
        ContentView(viewModel: game)
            .preferredColorScheme(.light)
        ContentView(viewModel: game)
            .preferredColorScheme(.dark)
        
    }
}
```

## `ViewModel`

```swift
// ViewModel is a part of UI, but not the part of View in MVVM
import SwiftUI
// view model in mvvm is a class, can inheritate

//func makeCardContent(index: Int) -> String {
//    return "😊"
//}

// ObservableObject tells the world that something is changed
class EmojiMemoryGame: ObservableObject {
    
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
        MemoryGame<String>(numberOfPairsOfCards: 3) { pairIndex in
            EmojiMemoryGame.emojis[pairIndex]
        }
    }
    
    // the order of these properties(like model, like emojis) being initialized is random
    // it is no way that you can have one of their initializers depending on another one already having been initialized
    // when we want to initialize here, emojis may not initialize
    
    // @Published means that "something changed" every time its model changes
    @Published private(set) var model: MemoryGame<String> = EmojiMemoryGame.createMemoryGame()
//        MemoryGame<String>(numberOfPairsOfCards: 4) { pairIndex in
//            emojis[pairIndex]
//            // '=' is a property initializer
//        }
    
    // notice the func makeCardContent above, here to simplify the code
    
    // private(set) can look (from other class), but cannot change
    var cards: Array<MemoryGame<String>.Card> {
        model.cards
    }
    
    // MARK: - Intent(s)
    
    func choose(_ card: MemoryGame<String>.Card) {
        // model is the model
        model.choose(card)
    }
}
```



## `Model`

```swift
import Foundation
// this is the model in mvvm
struct MemoryGame<CardContent> where CardContent: Equatable{
    private(set) var cards: Array<Card>
    
    private var indexOfTheOneAndOnlyFaceUpCard: Int?
    // This is a type that sometimes not set, which means that it should be an optional
    // 一般情况下一个optional被初始化为nil
    
    mutating func choose(_ card: Card) {
        // card.isFaceUp.toggle()是非法的，因为此处的card是指func choose(_ card: Card)中的card，作为function中的arrgument本身是一个let，无法改变
        
        // 设计一个函数func index(of card: Card)->Int，以得到card在cards中的index，并单独修改cards[index]中的isFaceUp，行吗？
        
//        let chosenIndex = index(of: card)
        
//        var chosenCard = cards[chosenIndex]
//        chosenCard.isFaceUp.toggle()
        // 事实证明是不行的
        // var chosenCard = cards[chosenIndex]得到的是一个copy，由于struct
        // 所以如果要修改，应当直接修改cards[chosenIndex]
        
//        cards[chosenIndex].isFaceUp.toggle()
       
        // Cannot use mutating member on immutable value: 'self' is immutable
        // 需要在func前添加mutating，从而function能够mutate这个struct，struct默认上是immutable的
        // 但是在UI中, cards[chosenIndex].isFaceUp仍没有改变
        
        // 当修改成为func index(of card: Card) -> Int?后，不论是修改为let chosenIndex = index(of: card)!还是cards[chosenIndex!].isFaceUp.toggle()，当index(of card: Card)返回nil时都会crash，所以要使用if let重写func choose(_ card: Card)
//        if let chosenIndex = index(of: card) {
//            cards[chosenIndex].isFaceUp.toggle()
//        }
        
        if let chosenIndex = cards.firstIndex(where: { $0.id == card.id}), !cards[chosenIndex].isFaceUp,
            !cards[chosenIndex].isMatched {
            if let potentialMatchIndex = indexOfTheOneAndOnlyFaceUpCard {
                if cards[chosenIndex].content == cards[potentialMatchIndex].content {
                    cards[chosenIndex].isMatched = true
                    cards[potentialMatchIndex].isMatched = true
                }
                indexOfTheOneAndOnlyFaceUpCard = nil
            } else {
                for index in cards.indices {
                    cards[index].isFaceUp = false
                }
                indexOfTheOneAndOnlyFaceUpCard = chosenIndex
            }
            
            cards[chosenIndex].isFaceUp.toggle()
        }
        // 此处code取代了if let chosenIndex = index(of: card)和func index(of card: Card) -> Int?
    }
    
    func index(of card: Card) -> Int? {
        for index in 0..<cards.count {
            if(cards[index].id == card.id) {
                return index
            }
        }
        return nil
    }
    
    init(numberOfPairsOfCards: Int, createCardContent: (Int) -> CardContent) {
        cards = Array<Card>()
        // add numberOfPairsOfCards * 2 cards to cards array
        for pairIndex in 0..<numberOfPairsOfCards {
            // maybe: var content: CardContent = ...?
            let content = createCardContent(pairIndex)
            cards.append(Card(content: content, id: pairIndex*2))
            cards.append(Card(content: content, id: pairIndex*2+1))
            // stop here, how to create CardContent?
        }
    }
    
    // how to make things Identifiable?
    // 直接添加Identifiable
    struct Card: Identifiable {
        // MemoryGame.Card(outside of this code)
        var isFaceUp: Bool = false
        var isMatched: Bool = false
        var content: CardContent
        var id: Int
        // var id: Int makes it identifiable
    }
}
// above is the entire model in mvvm
```

