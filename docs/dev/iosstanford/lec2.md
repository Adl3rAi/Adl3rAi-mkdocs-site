# Lecture 2

**Content View**

```swift
import SwiftUI

struct ContentView: View {
  var body: some View {
    ZStack{
      RoundedRectangle(cornerRadius: 20)
      	.stroke(lineWidth: 3)
      Text("Hello, World!")
    }
    .padding(.horizontal)
    .foregroundColor(.red)
  }
}

struct


struct ContentView_Preview: PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
```



