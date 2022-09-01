# Lecture 6

## `protocol`

A `protocol` is sort of a "stripped-down" `struct`/`class`

It has functions and vars, but no implementation(or storage)

Declaring a protocol looks very similar to struct or class

```swift
protocol Moveable {
  func move(by: Int)
  var hasMoved: Bool { get }
  var distanceFromStart: Int{ get set }
}
```

then other type can claim to implement Moveable

```swift
struct PortableThing: Moveable {
  // must implement move(by:), hasMoved and distanceFromStart here
}
// PortableThing conforms to Movealbe
```

and this is also legal

```swift
protocol Vehicle: Moveable {
  var passengerCount: Int {get set}
}
class Car: Vehicle, Impundable, Leasable {
  // must implement move(by:), hasMoved, distanceFromStart and passengerCount here
  // and must implement any funcs/vars in Impoundable and Leasable too
}
```

Very rarely, a protocol can be used as a type (in any other circumstance, a type can be used). Not all protocols can be used this way(`View` can't, nor `Equatable`, nor `Identifiable`)

```swift
func travelAround(using moveable: Moveable)
// can pass a Car or PortableThing in here
let foo = [Moveable]
// this Array can now contains Cars and also PortableThings
```

A much more common usage is to specify the behavior of a `struct`, `class` or `enum`

```swift
struct EmojiMemoryGameView: View
```

`EmojiMemoryGameView` became a very powerful `struct`

```swift
class EmojiMemoryGame: ObservableObject
```

Another use is turning "don't cares" into "somewhat cares"

```swift
struct MemoryGame<CardContent> where CardContent: Equatable
```

can also use protocols to restrict an `extension` to work only with certain thins

```swift
extension Array where Element: Hashable {...}
```

This `extension` will only be available for certain `Array`s, that elements conform to `Hashable`

can also use `protocol`s to restrict individual functions to work only with certain things

```swift
init(data: Data) where Data: Collection, Data.Element: Identifiable
```

`Data` is a don't care on whatever `struct` this `init` is in

You can't even create this thing unless the data you provide is a `Collection` of `Identifiables`

Occasionally a `protocol` is used to set up an agreement between two entities.

One of the most powerful uses of `protocol`s is to facilitate code sharing

Implementation can be added to a `protocol` by creating an `extension` to it.

An `extension` can also add a default implementation for a `func` or `var` in the `protocol`. (That's how `ObservableObject`s get `objectWillChange` for `free`)

```swift
func filter(_ isIncluded: (Element) -> Bool) -> Array<Element>
```

This function was written once by Apple.

And yet it works on Array and Range and String and Dictionary and others.

`filter` was added to the Foundation library as an `extension` to the Sequence protocol.

Array, Range, String, Dictionary all conform to the Sequence protocol

So they all get the filter function that was added to Sequence via that extension

Adding protocol implementation

```swift
struct Tesla: Vehicle {
  // Tesla is constrained to have to implement everything in Vehicle
  // but it gains all the capabilities a Vehicle has too
}
// Things that comform to Vehicle "gain capabilities" via an extension to the Vehicle protocol
extension Vehicle {
  func registerWithDMV() {/* actual implementation here*/}
}
// now Teslas (and all other Vehicles) have gained to ability to be registered with DMV
```

```swift
struct CardView: View {
  var body: some View { ... }
}
```

Here `CardView` is declaring that it implements the protocol `View`

Since `CardView` claims to implement this protocol ...

... it must(i.e. is constrained to) implement all of the `View` protocol's `func`s/`var`s

Luckily, the only thing that needs implementation in the `View` protocol is `body`

So once `CardView` implements `body`, it now fully "conforms" to `View`

And SwiftUI has added a whole ton of other `func/var`  to `View` via `extension`

So `CardView` gains the ability to respond to any of those `fun/var` too

```swift
protocol View {
  var body: some View
}
extension View {
  func foregroundColor(_ color: Color) -> some View {}
  func font(_ font: Font?) -> some View {}
  func blur(radius: CGFloat, opaque: Bool) -> some View {}
  ...
}
```

Generics + Protocols

```swift
protocol Identifiable {
  var id: ID {get}
}
```

The type `ID` here is a "don't care" for `Identifiable`

protocols can be generic too

however, protocols don't declare their "don't care" types in the same way as a `struct` does

```swift
protocol Identifiable {
  associatedtype ID
  var id: ID {get}
}
```

not `protocol Identifiable<ID>`

The type `ID` also has to be `Hashable`

```swift
protocol Identifiable {
  associatedtype ID where ID: Hashable
  var id: ID {get}
}
or, more simply
protocol Identifiable {
  associatedtype ID: Hashable
  var id: ID {get}
}
```

`Hashable` is a simple protocol

```swift
protocol Hashable {
  func hash(into hasher: inout Hasher)
}
struct Foo: Hashable {
  var i: Int
  var s: String
  func hash(into hasher: inout Hasher) {
    hasher.combine(i)
    hasher.combine(s)
  }
}
```

```swift
protocol Equatable {
  static func ==(lhs: Self, rhs: Self) -> Bool
}
```

The `Self` means "the actual type that is implementing Equatable"

When you implement Equatable in a struct, you replace Self with your struct's type. This kind of Self-referencing protocol cannot be used as a normal type.

In other word, `var x: [Equatable]` makes no sense.

if declare a `struct` to be `Equatable`

Then Swift will implement the static `==` func for you

```swift
struct Card: Identifiable, Equatable {
  var isFaceUp: Bool
  var isMatched: Bool
  var content: CardContent
  var id: Int
}
```

now `card1 == card2` will be legal

Shape is a protocol that inherits from `View`

all Shapes are also Views

By default, Shapes draw themselves by filling with the current foreground color. But we've already seen that this can be changed with `.stroke()` and `.fill()`

They return a View that draws the Shape in the specified way (by stroking or filling)

```swift
func fill<S>(_ whatToFillWith: S) -> some View where S: ShapeStyle
```

The `ShapeStyle` protocol turns a `Shape` into a `View` by apply some styling to it

Like, Color, ImagePaint, AngularGradient, LinearGradient

The Shape protocol (by extension) implements View's body var for you

```swift
func path(in rect: CGRect) -> Path {
  return a Path
}
```

`AspectGrid.swift`

```swift
import SwiftUI

struct AspectVGrid<Item, ItemView>: View where ItemView: View, Item: Identifiable {
    var items: [Item]
    var aspectRatio: CGFloat
    var content: (Item) -> ItemView
    // content的返回对象能不能是View呢？不能，View是一个protocol，是一个自我引用的类型
    // 能不能是some View呢？不能
    // 返回的对象必须是一个View的Generics
    
    init(items: [Item], aspectRatio: CGFloat, @ViewBuilder content: @escaping (Item) -> ItemView) {
        self.items = items
        self.aspectRatio = aspectRatio
        self.content = content
    }
    
    // 使用上述properties和View中的AspectVGrid()共同build文件AspectVGrid.swift中的body，如下
    var body: some View {
        GeometryReader { geometry in
            VStack {
                let width: CGFloat = widthThatFits(itemCount: items.count, int: geometry.size, itemAspectRatio: aspectRatio)
                LazyVGrid(columns: [adaptiveGridItem(width: width)], spacing: 0) {
                    // ForEach(Element)要求Element是Identifiable的
                    ForEach(items) { item in
                        content(item).aspectRatio(aspectRatio, contentMode: .fit)
                    }
                }
            }
            Spacer(minLength: 0)
        }
    }
    
    private func adaptiveGridItem(width: CGFloat) -> GridItem {
        var gridItem = GridItem(.adaptive(minimum: width))
        gridItem.spacing = 0
        return gridItem
    }
    
    private func widthThatFits(itemCount: Int, int size: CGSize, itemAspectRatio: CGFloat) -> CGFloat {
        var columnCount = 1
        var rowCount = itemCount
        repeat {
            let itemWidth = size.width / CGFloat(columnCount)
            let itemHeight = itemWidth / itemAspectRatio
            if CGFloat(rowCount) * itemHeight < size.height {
                break
            }
            columnCount += 1
            rowCount = (itemCount + (columnCount - 1)) / columnCount
        } while columnCount < itemCount
        if columnCount > itemCount {
            columnCount = itemCount
        }
        return floor(size.width / CGFloat(columnCount))
    }
}

//struct AspectVGrid_Previews: PreviewProvider {
//    static var previews: some View {
//        AspectVGrid()
//    }
//}
```

```swift
import SwiftUI

struct EmojiMemoryGameView: View {
    // view is just an agent for showing what in the model through the view model, should pass viewModel as an argument
    @ObservedObject var game: EmojiMemoryGame
    // @ObservedObject means that something changed plz rebuild entire body
    
    var body: some View {
//        ScrollView {
//            LazyVGrid(columns: [GridItem(.adaptive(minimum: 100))]){
//                ForEach(game.cards) { card in
//                    CardView(card: card)
//                        .aspectRatio(2/3, contentMode: .fit)
//                        .onTapGesture {
//                            // this is the intent of an user
//                            // intent is stored in ViewModel
//                            game.choose(card)
//                        }
//                }
//            }
//        }
        AspectVGrid(items: game.cards, aspectRatio: 2/3) { card in
            if card.isMatched && !card.isFaceUp {
                Rectangle().opacity(0)
            } else {
                CardView(card: card)
                    .padding(4)
    //                .aspectRatio(2/3, contentMode: .fit) AspectVGrid()里已经给了aspectRatio了
                    .onTapGesture {
                        game.choose(card)
                    }
            }
//            cardView(for: card)
        }
        .foregroundColor(.red)
        .padding(.horizontal)
        
    }
    
//    @ViewBuilder
//    private func cardView(for card: EmojiMemoryGame.Card) -> some View {
//        if card.isMatched && !card.isFaceUp {
//            Rectangle().opacity(0)
//        } else {
//            CardView(card: card)
//                .padding(4)
//                .onTapGesture {
//                    game.choose(card)
//                }
//        }
//    }
    // func cardView()并没有返回View，但需要其返回some View，所以要添加 @ViewBuilder告诉compiler
    
    
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
                    Pie(startAngle: Angle(degrees: 0-90), endAngle: Angle(degrees: 110-90))
                        .padding(5).opacity(0.5)
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
        static let cornerRadius: CGFloat = 10
        static let lineWidth: CGFloat = 3
        static let fontScale: CGFloat = 0.7
    }
}

// 右侧preview是什么样的由下面的代码决定
struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        let game = EmojiMemoryGame()
        game.choose(game.cards.first!)
        return EmojiMemoryGameView(game: game)
            .preferredColorScheme(.light)
    }
}
```

```swift
import SwiftUI

struct Pie: Shape {
    // startAngle和endAngle是var，是因为之后需要animate这两个property
    // View可以是let，而Shape可以animate所以是var
    var startAngle: Angle
    var endAngle: Angle
    var clockwise: Bool = false
    
    func path(in rect: CGRect) -> Path {
        
        let center = CGPoint(x: rect.midX, y: rect.midY)
        let radius = min(rect.width, rect.height) / 2
        let start = CGPoint(
            x: center.x + radius * CGFloat(cos(startAngle.radians)),
            y: center.y + radius * CGFloat(sin(startAngle.radians))
        )
        
        var p = Path()
        p.move(to: center)
        p.addLine(to: start)
        p.addArc(
            center: center,
            radius: radius,
            startAngle: startAngle,
            endAngle: endAngle,
            clockwise: !clockwise
        )
        p.addLine(to: center)
        
        return p
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
    
//    func index(of card: Card) -> Int? {
//        for index in 0..<cards.count {
//            if(cards[index].id == card.id) {
//                return index
//            }
//        }
//        return nil
//    }
    
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
        var isFaceUp = false
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
        MemoryGame<String>(numberOfPairsOfCards: 6) { pairIndex in
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

