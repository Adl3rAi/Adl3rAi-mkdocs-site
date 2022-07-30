# Lecture 2

<img src="/Users/huqingyuan/Desktop/mkdocs-site/docs/pic/iosdev/gif1.gif" alt="gif1" style="zoom:45%;" />



**Content View**

```swift
import SwiftUI

struct ContentView: View {
  var emojis = ["🚗","🚕","🚙","🚌","🚎","🏎","🚓","🚑","🚒","🚐", "🛻","🚚","🚛","🚜"]
  @State var emojiCount = 3
  var body: some View {
  	VStack{
      ScrollView {
        LazyVGrid(columns: [GridItem(.adaptive(minimum: 100))]) {
          ForEach(emojis[0..<emojiCount], id: \.self) {
            emoji in
            CardView(content: emoji).aspectRatio(2/3, contentMode: .fit)
          }
        }
      }
    }
    .foregroundColor(.red)
    Spacer()
    HStack {
      remove
      Spacer()
      add
    }
    .font(.largeTitle)
    .padding(.horizontal)
  }
  .padding(.horizontal)
}

var remove: some View {
  Button(action: {
    if emojiCount > 1 {
      emojiCount -= 1
    }
  }){
    Image(systemName: "minus.circle")
  }
}

var add: some View {
  Button(action: {
    if emojiCount < emojis.count {
      emojiCount += 1
    }
  }) {
    Image(systemName: "plus.circle")
  }
}

struct CardView: View {
  var content: String
  @State var isFace: Bool = true
  var body: some View {
    ZStack {
      let shape = RoundedRectangle(cornorRadius: 5)
      if isFaceUp {
        shape.fill().foregroundColor(.white)
        shape.strokeBorder(lineWidth: 3)
        Text(content).font(.largeTitle)
      }
      else {
        shape.fill()
      }
    }
    .onTapGesture {
      isFaceUp = !isFaceUp
    }
  }
}

struct ContentView_Preview: PreviewProvider {
  static var previews: some View {
    ContentView()
    	.preferredColorScheme(.dark)
    ContentView()
    	.preferredColorScheme(.light)
  }
}
```



