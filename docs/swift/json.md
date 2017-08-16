# Swift/Json


Used SwiftyJSON.

[SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)
[Sample Project](https://github.com/mikejakobsen/CollegeFootball/)

Load 3 party plugins -> Just by dragging it in.

### Old School

```Swift
      func finishedDownloadingJSON(data: [Any]) {
          for player in data {
              if let player = player as? [String:Any],
                  let name = player["name"] as? String,
                  let college = player["college"] as? String,
                  let imageURL = player["image"] as? String {
                  let player = Player(name: name, college: college, imageURL: imageURL)
                  addEast(player: player)
              }
          }
          delegate?.dataIsReady()
      }
```

SwiftyJSON makes it more readable.



