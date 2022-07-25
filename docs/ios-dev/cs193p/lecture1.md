# Lecture 1

<img src="/Users/huqingyuan/Pictures/Photos Library.photoslibrary/originals/2/2AB1C801-F4B4-41BC-949F-0C6267883B56.jpeg" alt="2AB1C801-F4B4-41BC-949F-0C6267883B56" style="zoom:30%;" />

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

