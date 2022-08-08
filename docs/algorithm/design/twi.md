# Design Twitter

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [355. Design Twitter](https://leetcode.com/problems/design-twitter/) |[355. Design Twitter](#355-design-twitter)      |



## 355. Design Twitter

Design a simplified version of Twitter where users can post tweets, follow/unfollow another user, and is able to see the `10` most recent tweets in the user's news feed.

Implement the `Twitter` class:

- `Twitter()` Initializes your twitter object.
- `void postTweet(int userId, int tweetId)` Composes a new tweet with ID `tweetId` by the user `userId`. Each call to this function will be made with a unique `tweetId`.
- `List<Integer> getNewsFeed(int userId)` Retrieves the `10` most recent tweet IDs in the user's news feed. Each item in the news feed must be posted by users who the user followed or by the user themself. Tweets must be **ordered from most recent to least recent**.
- `void follow(int followerId, int followeeId)` The user with ID `followerId` started following the user with ID `followeeId`.
- `void unfollow(int followerId, int followeeId)` The user with ID `followerId` started unfollowing the user with ID `followeeId`.

 

**Example 1:**

```
Input
["Twitter", "postTweet", "getNewsFeed", "follow", "postTweet", "getNewsFeed", "unfollow", "getNewsFeed"]
[[], [1, 5], [1], [1, 2], [2, 6], [1], [1, 2], [1]]
Output
[null, null, [5], null, null, [6, 5], null, [5]]

Explanation
Twitter twitter = new Twitter();
twitter.postTweet(1, 5); // User 1 posts a new tweet (id = 5).
twitter.getNewsFeed(1);  // User 1's news feed should return a list with 1 tweet id -> [5]. return [5]
twitter.follow(1, 2);    // User 1 follows user 2.
twitter.postTweet(2, 6); // User 2 posts a new tweet (id = 6).
twitter.getNewsFeed(1);  // User 1's news feed should return a list with 2 tweet ids -> [6, 5]. Tweet id 6 should precede tweet id 5 because it is posted after tweet id 5.
twitter.unfollow(1, 2);  // User 1 unfollows user 2.
twitter.getNewsFeed(1);  // User 1's news feed should return a list with 1 tweet id -> [5], since user 1 is no longer following user 2.
```

 

**Constraints:**

- `1 <= userId, followerId, followeeId <= 500`
- `0 <= tweetId <= 104`
- All the tweets have **unique** IDs.
- At most `3 * 104` calls will be made to `postTweet`, `getNewsFeed`, `follow`, and `unfollow`.

设计推特需要实现的API

```java
class Twitter {
  public void postTweet(int userId, int tweetId){}
  public List<Integer> getNewsFeed(int userId){}
  public void follow(int followerId, int followeeId){}
  public void unfollow(int followerId, int followeeId){}
}
```

API的具体用法

```java
Twitter twitter = new Twitter();
twitter.postTweet(1,5); // 用户1发送推特5
twitter.getNewsFeed(1); // return [5] 返回自己发的推特5
twitter.follow(1,2); // 用户1关注用户2
twitter.postTweet(2,6); // 用户2发送推特6
twitter.getNewsFeed(1); // return [6,5]
twitter.unfollow(1,2); // 用户1取关用户2
twitter.getNewsFeed(1); // return [5]
```

将每个用户各自的推文存储在链表中，每个链表节点存储`id`和时间戳`time`，并且这个链表是有时序的。如果某用户关注了`k`个用户，就可以合并`k`个有序链表的算法合并出有序的推文列表

首先需要一个`User`类，以储存`user`信息，还需要一个`Tweet`类，以储存推文信息，并且`Tweet`类要作为链表的节点

```cpp
class Twitter{
  private static int timestamp = 0;
  private static class Tweet {...}
  private static class User {...}
  public void postTweet(int userId, int tweetId) {}
  public List<Integer> getNewsFeed(int userId) {}
  public void follow(int followerId, int followeeId){}
  public void unfollow(int followerId, int followeeId){}
}
```

`Tweet`类的实现

每个`Tweet`实例需要记录`tweetId`和`time`，作为链表节点，还需要指向下一个节点的`next`指针

```java
class Tweet {
  private int id;
  private int time;
  private Tweet next;
  public Tweet(int id, int time) {
    this.id = id;
    this.time = time;
    this.next = null;
  }
}
```

`User`类的实现

一个用户储存的信息有`userId`，关注列表，该用户发过的推文列表

其中关注列表中的元素只有`0`和`1`两种状态，则应采用`set`来存

推文列表则使用链表来储存

并且，`follow(),unfollow(),postTweet()`都应该是`User`的行为

```java
class User {
  private int id;
  public Set<Integer> followed;
  public Tweet head;
  public User(int userId) {
    followed = new HashSet<>();
    this.id = userId;
    this.head = null;
    follow(id);
  }
  public void follow(int userId) {
    followed.add(userId);
  }
  public void unfollow(int userId) {
    if(userId != this.id) {
      followed.remove(userId);
    }
  }
  public void post(int tweetId) {
    Tweet twt = new Tweet(tweetId, timestamp);
    timestamp++;
    twt.next = head;
    head = twt;
  }
}
```

```java
class Twitter {
  private static int timestamp = 0;
  private static class Tweet {...}
  private static class User {...}
  // userMap将userId和User对象一一对应
  private HashMap<Integer, User> userMap = new HashMap<>();
  public void postTweet(int userId, int tweetId) {
    if(!userMap.containsKey(userId)) {
      userMap.put(userId, new User(userId));
    }
    User u = userMap.get(userId);
    u.post(tweetId);
  }
  public void follow(int followerId, int followeeId) {
    // follower不存在，则新建
    if(!userMap.containsKey(followerId)) {
      User u = new User(followerId);
      userMap.put(followerId, u);
    }
    if(!userMap.containsKey(followeeId)) {
      User u = new User(followeeId);
      userMap.put(followeeId, u);
    }
    userMap.get(followerId).follow(followeeId);
  }
  public void unfollow(int followerId, int followeeId) {
    if(userMap.containsKey(followerId)) {
      User flwer = userMap.get(followerId);
      flwer.unfollow(followeeId);
    }
  }
  public List<Integer> getNewsFeed(int userId) {
    List<Integer> res = new ArrayList<>();
    if(!userMap.containsKey(userId)) return res;
    Set<Integer> users = userMap.get(userId).followed;
    PriorityQueue<Tweet> pq = new PriorityQueue<>(users.size(), (a,b)->(b.time-a.time));
    for(int id : users) {
      Tweet twt = userMap.get(id).head;
      if(twt == null) continue;
      pq.add(twt);
    }
    while(!pq.isEmpty()) {
      if(res.size() == 10) break;
      Tweet twt = pq.poll();
      res.add(twt.id);
      if(twt.next != null) {
        pq.add(twt.next);
      }
    }
    return res;
  }
}
```

