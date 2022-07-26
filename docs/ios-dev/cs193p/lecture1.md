# Lecture 1

**Content View**

```swift
import SwiftUI

struct ContentView: View {
  var body: some View {
  	ZStack {
      RoundedRectangle(cornerRadius: 5)
      	.stroke(lineWidth: 3)
      Text("Hello, World!")
    }
    .padding(.horizontal)
    .foregroundColor(.red)
  }
}

struct ContentView_Preview: PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
```

