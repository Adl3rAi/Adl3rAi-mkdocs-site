# Lecture 5

## `@State`

Currently, all of View `struct`s are completely and utterly read-only

only `let` or computed `var` (which are read-only) make much sense on a View

The exception is property wrappers like `@ObservedObject` which must be marked `var`

Views are mostly supposed to be "stateless"(just drawing the Model all the time). They don't need any state of their own. So no need for them to be none-read-only.

You must mark any `var`s used for this temporary state with `@State`

```swift
@State private var somethingTemporary: SomeType // SomeType can be any struct
```

These are marked. `private` because no one else can access them anyway

`@State` is only used inside the View

Changes to this `@State` `var` will cause View to rebuild its body

It's sort of like an `@ObservedObject` but on a random piece of data instead of a ViewModel

```swift
@State private var somethingTemporary: SomeType
```

When read-only View gets rebuilt, the new version will continue to point to it. In other words, changes to your View(via its arguments) will not dump this state

---

## Property Observers

Property observers are essentially a way to "watch" a `var` and execute code when it changes

The syntax can look a lot like a computed `var`, but it is completely unrelated to that

```swift
var isFaceUp: Bool {
  willSet {
    if newValue {
      startUsingBonusTime()
    } else {
      stopUsingBonusTime()
    }
  }
}
```

Inside here, `newVal` is a special variable(the value it's going to get set to)

There's also a `didSet` (inside that one, `oldValue` is what the value used to be)

---

## Layout

Container Views "offer" space to the Views inside them

Views then choose what size they want to be

Container Views then position the Views inside of them

`HStack` and `VStack`

Stacks divide up the space that is offered to them and then offer that to the Vies inside.

It offers space to its "least flexible"(with respect to sizing) subviews first

`Image`(it wants to be a fixed size) is an "inflexible" View

After an offered Vies(s) takes what it wants, its size is removed from the space available

Then the stack moves on to the next "least flexible" Views

Very flexible views will share evenly(mostly)

Rinse and repeat

```swift
HStack {
  Text("Important").layoutPriority(100)
  Image(systemName: "arrow.up") // the default layout priority is 0
  Text("Uimportant")
}
```

`LazyHStack` and `LazyVStack`

These "lazy" versions of the stack don't build any of their Views that are not visible.

They also size themselves to fit their Views.

So they don't take up all the space offered to them even if they have flexible views inside.

You'd use these when you have a stack that is in a ScrollView

`ScrollView`

ScrollView takes all the space offered to it.

The views inside it are sized to fit along the axis your scrolling on.

---

`GeometryRead`



## `@ViewBuilder`

Any func or read-only computed `var` can be marked with `@ViewBuilder`

If so marked, the contents of that `func` or `var` will be interpreted as a list of `Views`

```swift
@ViewBuilder
func front(of card: Card) -> some View {
  let shape = RoundedRectangle(cornerRadius: 20)
  shape
  shape.stroke()
  Text(card.content)
}
```

can also use `@ViewBuilder` to mark a parameter of a function or an init.

That argument's type must be "a function that returns a View"

```swift
import SwiftUI

struct EmojiMemoryGameView: View {
    // view is just an agent for showing what in the model through the view model, should pass viewModel as an argument
    @ObservedObject var game: EmojiMemoryGame
    // @ObservedObject means that something changed plz rebuild entire body
    
    var body: some View {
        ScrollView {
            LazyVGrid(columns: [GridItem(.adaptive(minimum: 100))]){
                ForEach(game.cards) { card in
                    CardView(card: card)
                        .aspectRatio(2/3, contentMode: .fit)
                        .onTapGesture {
                            // this is the intent of an user
                            // intent is stored in ViewModel
                            game.choose(card)
                        }
                }
            }
        }
        .foregroundColor(.red)
        .padding(.horizontal)
    }

}

struct CardView: View {
    let card: EmojiMemoryGame.Card
    
    var body: some View {
//        ZStack {
//            let shape = RoundedRectangle(cornerRadius: 20)
//            if card.isFaceUp {
//                shape.fill().foregroundColor(.white)
//                shape.strokeBorder(lineWidth: 3)
//                Text(card.content).font(.largeTitle)
//            } else if card.isMatched {
//                shape.opacity(0)
//            }
//            else {
//                shape.fill()
//            }
//        }
        
        // The whole point of GeometryReader is it's offered some size,
        // it offers that to its Views and tells its View
        // what that size is via proxy(geometry)
        GeometryReader { geometry in
            ZStack {
                let shape = RoundedRectangle(cornerRadius: DrawingConstants.cornerRadius)
                if card.isFaceUp {
                    shape.fill().foregroundColor(.white)
                    shape.strokeBorder(lineWidth: DrawingConstants.lineWidth)
                    Text(card.content).font(font(in: geometry.size))
                } else if card.isMatched {
                    shape.opacity(0)
                }
                else {
                    shape.fill()
                }
            }
        }
    }
    
    private func font(in size: CGSize) -> Font {
        Font.system(size: min(size.width, size.height) * DrawingConstants.fontScale)
    }
    
    private struct DrawingConstants {
        static let cornerRadius: CGFloat = 20
        static let lineWidth: CGFloat = 3
        static let fontScale: CGFloat = 0.8
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        let game = EmojiMemoryGame()
        EmojiMemoryGameView(game: game)
            .preferredColorScheme(.light)
        EmojiMemoryGameView(game: game)
            .preferredColorScheme(.dark)
        
    }
}
```

```swift
import Foundation
// this is the model in mvvm
struct MemoryGame<CardContent> where CardContent: Equatable{
    private(set) var cards: Array<Card>
    
    // computed property
    private var indexOfTheOneAndOnlyFaceUpCard: Int? {
        get { cards.indices.filter({cards[$0].isFaceUp}).oneAndOnly }
        set { cards.indices.forEach({cards[$0].isFaceUp = ($0 == newValue)})
//            for index in cards.indices {
                // 不能在indexOfTheOneAndOnlyFaceUpCard中使用indexOfTheOneAndOnlyFaceUpCard
//                if index != indexOfTheOneAndOnlyFaceUpCard {
//                if index != newValue {
//                    cards[index].isFaceUp = false
//                } else {
//                    cards[index].isFaceUp = true
//                }
            
            }
    }
    
    
    // private var indexOfTheOneAndOnlyFaceUpCard: Int?
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
                cards[chosenIndex].isFaceUp = true
            } else {
                for index in cards.indices {
                    cards[index].isFaceUp = false
                }
                indexOfTheOneAndOnlyFaceUpCard = chosenIndex
            }
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
        cards = []
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
        var isFaceUp = true
        var isMatched = false
        let content: CardContent
        let id: Int
        // var id: Int makes it identifiable
    }
}
// above is the entire model in mvvm

extension Array {
    var oneAndOnly: Element? {
        if count == 1 {
            return first
        } else {
            return nil
        }
    }
}
```

```swift
// ViewModel is a part of UI, but not the part of View in MVVM
import SwiftUI
// view model in mvvm is a class, can inheritate

//func makeCardContent(index: Int) -> String {
//    return "😊"
//}

// ObservableObject tells the world that something is changed
class EmojiMemoryGame: ObservableObject {
    typealias Card = MemoryGame<String>.Card
    
    private static let emojis = ["🚗","🚕","🚙","🚌","🚎","🏎","🚓","🚑","🚒","🚐", "🛻","🚚","🚛","🚜"]
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
    private static func createMemoryGame() -> MemoryGame<String> {
        MemoryGame<String>(numberOfPairsOfCards: 5) { pairIndex in
            EmojiMemoryGame.emojis[pairIndex]
        }
    }
    
    // the order of these properties(like model, like emojis) being initialized is random
    // it is no way that you can have one of their initializers depending on another one already having been initialized
    // when we want to initialize here, emojis may not initialize
    
    // @Published means that "something changed" every time its model changes
    @Published private var model = EmojiMemoryGame.createMemoryGame()
//        MemoryGame<String>(numberOfPairsOfCards: 4) { pairIndex in
//            emojis[pairIndex]
//            // '=' is a property initializer
//        }
    
    // notice the func makeCardContent above, here to simplify the code
    
    // private(set) can look (from other class), but cannot change
    var cards: Array<Card> {
        model.cards
    }
    
    // MARK: - Intent(s)
    
    func choose(_ card: Card) {
        // model is the model
        model.choose(card)
    }
}
```

