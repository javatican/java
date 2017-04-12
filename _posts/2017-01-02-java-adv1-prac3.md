---
layout:	post
title:	Java基礎 - Practice 3
date:	2017-01-02
categories: java
---
## 3-1 練習以物件導向方式來分析問題

這個練習將以物件導向的方式來分析問題領域. 所要解決的問題如下:

一個足球聯盟需要一個軟體系統, 來追蹤球隊與選手的排名, 包括(1)比賽結果, (2)球隊排名, (3)每個球隊中的選手進球排名.
思考的方向可以從這個系統可能包含的物件(名詞), 每一個物件可能有的屬性(形容詞), 以及可能的行為(動詞)來開始. 
例如: 選手(Player)是一個名詞, 選手的姓名(形容詞), 以及進球(動詞).


### 系統中須包含的物件

1. 聯盟(League)
1. 球隊(Team)
1. 選手(Player)
1. 比賽(Match)
1. 進球(Goal)

### 物件屬性及行為

```
聯盟(League)
------------
名稱(name)
*隊伍列表(teams)
*比賽列表(matches)
------------
取得比賽結果(get_result)
取得球隊排名(get_ranking)
```

```
球隊(Team)
------------
代碼(id)
名稱(name)
*選手列表(players)
------------
取得選手進球排名(get_score_ranking)
```

```
選手(Player)
------------
代碼(id)
名稱(name)
背號(number)
*所屬球隊(team)
------------
取得所有進球(get_goals)
```

```
比賽(Match)
------------
代碼(id)
*球隊一(team1)
*球隊二(team2)
球隊一分數(score1)
球隊二分數(score2)
*進球(goal)
------------
取得比賽結果(get_result)
```

```
進球(Goal)
------------
代碼(id)
*球隊(team)
*選手(player)
時間(time)
```

## 3-2 練習以UML將上述的類別關係表示出來

1. 在命名時須注意:
  - 類別名稱以大寫字母開頭, 屬性及方法則用小寫字母開頭
  - 以上名稱如果使用多個英文字, 則應使用駝峰命名法.
  - 屬性若是代表集合資料, 名稱以[]結尾
  - 方法名稱以()結尾

2. 可以使用下載的UMLet工具, 或者線上的UML工具, 例如 [gliffy](https://www.gliffy.com)

<img src="{{site.baseurl}}/assets/soccer.png" alt="Sequence str common" style="width: 600px;" />

:sweat_smile:

