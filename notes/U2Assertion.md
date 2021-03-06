Unit 2x: Assertion
===

>   幾乎百分百的確認，但在開發階段，我們還是會謹慎一點。

斷言是 java 中用來檢查程式中的假設與斷言。例如你寫一個程式判斷汽車（或火箭）的速度，不管怎麼快，也不可能比光速快。所以你可以下一個斷言：計算出來的速度必定小於光速。當這個斷言被違反了，表示你的程式可能出現問題。

除此以外，斷言也可以幫助提升程式的可閱讀。

### 語法

     assert Expression1 ;

或

     assert Expression1 : errMessage ; 
 
其中 Expression1 式一個 is a boolean 表示式. errMessage 式一個含有值的表示式，當斷言不成立時，會拋出 assertionErr 的例外，並把 errMessage 的值呈現出來。

### Enable and disable

為了讓 assertion 不會造成程式的負擔，你可以在編譯的時候決定是否要將 assertion 啟動（enable）。預設是不啟動的。To ensure that assertions are not a performance liability in deployed applications, assertions can be enabled or disabled when the program is started, and are disabled by default.

[Eclipse 中如何啟動 assertion](http://stackoverflow.com/questions/11415160/how-to-enable-the-java-keyword-assert-in-eclipse-program-wise)

### 什麼時候該用 Invariant?

#### 1. 方法內部的不變式 Internal Invariants

```
if (i % 3 == 0) {
   ...
} else if (i % 3 == 1) {
   ...
} else {
   assert i % 3 == 2 : i; //我斷言前面的條件若不滿足，則 i%2 一定會等於 2
   ...
}
```

#### 2. 類別的不變式 Class invariant

類別不變式表示該物件任何方法執行的前後都恆真的式子，例如一個 Stack 物件內部元素的個數一定介於 0 與最大值之間。

```
private boolean inv() {
  return (num >= 0 && num < capacity);
}
```

所以我們可以在執行任何動作之後呼叫 inv() 來檢查是否發生異常。

    assert inv();

right before each return.

#### 3. 不變的控制流 Control-Flow Invariants 

對控制流程的斷言，"我相信絕對不會到 05 行"
```
01 void foo() { 
02    for (...) {
03       if (...) return;
04    }
05    assert false; 
06 }
```

如此，萬一真的跑到 05, 系統就會拋出例外警告。

#### 4. 後置條件 Postconditions 

在一段複雜的運算後，你斷定會成為真的事情，用斷言來加強。

```
public BigInteger modInverse(BigInteger m) {
   if (m.signum <= 0)
      throw new ArithmeticException("Modulus not positive: " + m);
      ... // Do the computation
   assert this.multiply(result).mod(m).equals(ONE) : this;
   return result; 
}
```

### 什麼時候不該使用 Assertion?


#### 1. 不要使用 assertion 來做公開方法的參數檢查

> 公開方法對參數的檢查本來就應該做，不要用 assertion 來檢查。

公開的方法表示會有很多其他人會呼叫此方法，傳入各種不同的參數，為了避免他們傳錯，方法本來就必須測試這些參數，所以不該用斷言的方式來檢查。

You can use an assertion to test a nonpublic method's precondition that you believe will be true no matter what a client does with the class.

```
private void setRefreshInterval(int interval) {
   // Confirm adherence to precondition in nonpublic method
   assert interval > 0 && interval <= 1000/MAX_REFRESH_RATE : interval;
   ... // Set the refresh interval 
}
```

想反的，若是私有方法，其參數的約定式內部的，我們應該很清楚的知道不該有那樣不正確的參數傳入，所以可以用斷言。如果真的傳入奇怪的參數，就會拋出例外。

#### 2. 不要使用 assertion 來執行任何與程式邏輯相關的操作

不要把程式該做的工作放在 assert 中。因為 assert 以後可能會被移除。

```
// wrong! - action is contained in assertion
assert names.remove(null);

// right - action precedes assertion
boolean nullsRemoved = names.remove(null);
assert nullsRemoved; // Runs whether or not asserts are enabled
```


Lab
---

**Lab 1**: 學期成績由期中考、期末考、平成成績所組成，各佔 30%, 30%, 40%。計算出的成績應該小於 100 分，請用斷言來確保最後成績的正確。

**Lab 2**: 經過 Sort 後，我們斷言第一個元素一定比第二個元素小，請用 assert 來完成。

**Lab3**: 應用 assert 寫一個判斷三角形（是何種三角形）的程式。

**Lab 4**: 有一個 Tree 類別，一個 Node 類別。node1.add(node2) 可以把 node2 加到 node 的子節點。 tree.setRoot(node1) 可以設定 node1 為根節點。每個 node 內有一個數值，tree.getSum() 會傳回所有 node 的合。node.getSum() 會回傳該點和所有子節點的合。應用 assert 來寫這個程式。設計一些測試案例來測試此程式。

	

