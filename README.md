# backbone
# 基本事件方法

## 綁定 on 方法
使用 on 方法可以給一個對象的自定義事件綁定觸發該事件時執行的函數，當自定義的事件觸發時，綁定的函數將會被執行。其調用格式如下所示：

```javascript
obj.on(eventName,function,[context]);
```

其中，參數 Obj 表示對象本身；eventName 表示自定義的事件名；function 表示當事件觸發時，被執行的函數；可選參數 context 表示上下文對象，用於對象級事件的監聽，即當一個對象需要監聽另一個對象的事件時，可以使用該參數。
使用 on 方法不僅可以綁定用戶的自定義事件，還可以監聽對象自帶的一些事件，接下來通過簡單事例來進一步瞭解該方法的使用過程。

### 範例 1 使用 on 方法監聽默認事件

#### 1. 功能描述
構建一個名為 「person」 的模型類，在構建時，通過 defaults 設置對象的默認屬性名稱，使用 on 方法綁定對象默認屬性的 change 事件。當重置對象默認屬性值時觸發該事件，並在瀏覽器的控制台中輸出 「對象的默認值發生了變化」 的字樣。

#### 2. 實現代碼

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:""  
    }  
});  
var man = new person();  
man.on("change",function(){  
    console.log("對象的默認值發生了變化");  
});  
man.set("name:","皮卡丘");  
```

#### 3. 源碼分析
在上述代碼中，首先定義一個名為 person 的數據模型類。在定義時，通過 defaults 方法設置兩個名稱為 「name」 和「sex」的默認數據項，並將它們的值設置空。然後，實例化一個名為 man 的模型類對象，並使用 on 方法向該對象綁定觸發 change 事件時執行的函數。在該函數中，將在瀏覽器的控制台輸出 「對象的默認值發生了變化」 字樣。即只要對象的屬性值發生了變化，將觸發 change 事件，便執行事件綁定的函數。最後，使用對象的 set 方法，重置一個 name 屬性值，一旦修改對象的屬性值，將會觸發綁定的 change 事件。因此，當執行重置屬性值代碼之後，將會在瀏覽器的控制台輸出通知文字。

### 範例 2 使用 on 方法監聽默認事件
在使用 on 方法監聽對象的 change 事件時，還可以監聽到某個屬性值的變化事件，即屬性事件。其調用格式如下。

```javascript
obj.on(eventName:attrName,function(model,value))
```

其中，參數 attrName 表示對象中的屬性名稱；冒號（：）是過濾符，表示它是一個只針對某屬性變化時觸發的事件。在觸發事件後的回調函數中，參數 model 是當前的數據模型對象，value 表示名為 attrName 的屬性修改後的值。接下來通過簡單的代碼來說明屬性事件的觸發過程。

#### 1. 功能描述
在範例 1 的基礎之上，使用 on 方法綁定對象中 sex 屬性的 change 事件，當 sex 屬性值發生變化時觸發該事件，並在瀏覽器的控制台中輸出變化後的最新值。

#### 2. 實現代碼

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女"  
    }  
});  

var man = new person();  
man.on("change",function(){  
    console.log("對象的默認值發生了變化");  
});  
man.on("change:sex",function(model,value){  
    console.log("您修改了性別屬性值, 最新值是:"+value);  
});  
man.set("sex","男");  
```
 
#### 3. 源碼分析
在上述代碼中，分別給對象 man 綁定了兩個事件，一個是默認事件 change，另一個是屬性事件 change:sex，即 sex 屬性變化事件。在屬性變化事件的回調函數中，通過回傳的 value 參數獲取最新修改後的屬性值，並顯示在瀏覽器的控制台中。
最後執行對象的 set 方法時，在 Backbone 內部會對已變化的值進行檢測，當屬性值與上次的值不同時，將會觸發設置的屬性事件和默認的 change 事件。

注意：從上可以看出，當修改對象的某個屬性值時，其事件觸發的順序是：先觸發屬性事件，然後才觸發默認的 change 事件，這一點需要注意。

### 範例 3 使用 on 方法獲取屬性修改前的值
在使用 on 方法綁定 change 和 change 屬性事件時，還可以通過回調函數中的 model 對象獲取屬性修改前的所有值，獲取方法如下代碼所示：

```javascript
model.previous("attrName")
model.previousAttributes()
```

在上述代碼中，previous() 方法是獲取對象中某個屬性的原有值，previousAttributes() 方法則返回一個對象，對象中包含上一個保存狀態所有屬性的原有值。這兩個方法只用於模型對象的 change 和 change 屬性事件中獲取上一個保存狀態的屬性值。接下來通過一個簡單的範例來演示這兩個方法的使用過程。

#### 1. 功能描述
分別綁定 「分數」、「年齡」 屬性的 change 事件。在事件中，分別將屬性重置前後的值進行對比，並根據對比值的不同在瀏覽器的控制台中分別輸出不同的內容。

#### 2. 實現代碼

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
man.on("change:score",function(model,value){  
    var oldScore = model.previous("score");// 獲得修改前的指定屬性   
    if(value>oldScore){  
        console.log("您比上次進步了"+(value-oldScore)+"分");  
    }else if(oldScore>value){  
        console.log("您比上次落後了"+(oldScore-value)+"分");  
    }else{  
        console.log("您的成績沒有變化");  
    }  
});  

man.on("change:age",function(model,value){  
    var objAttr = model.previousAttributes();// 獲得修改前的對象   
    var oldAge = objAttr.age;  
    if(value>oldAge){  
        console.log("您又年長了"+(value-oldAge)+"歲");  
    }else if(oldAge>value){  
        console.log("您又年輕了"+(oldAge-value)+"歲");  
    }else{  
        console.log("您的年齡沒有變化");  
    }  
});  

man.set({"age":36,"score":200});  
```

#### 3. 源碼分析
在上述代碼中，通過使用 on 方法分別綁定對象 man 的 change:score 和 change:age 兩個屬性事件。在第一個屬性事件 change:score 中，通過回調函數中 model 模型對象的 previous() 方法，獲取上一次保存的 score 屬性值，並保存至變量 oldScore 中；將 oldScore 變量與最新修改的 score 屬性值 value 進行比較，根據比較的結果，在瀏覽器的控制台中輸出不同的文字。在第二個屬性事件 change:age 中，通過回調函數中 model 模型對象的 previousAttributes() 方法，獲取上一次保存結果的對象，並將該對象保存至變量 objAttr 中；再通過訪問對象變量 objAttr 屬性的方式，獲取上一次保存的 age 屬性值，並保存至 oldAge 中。接下來的代碼功能與 change:score 屬性事件相同，不再贅述。綁定兩個屬性事件之後，通過調用 man 對象的 set 方法，重置了 age，score 兩個屬性值，導致了這兩個事件的觸發。從上可以看出，默認 age 屬性值為 32，而重置時的屬性值為 36，它們之間相差 4 歲，所以在瀏覽器的控制台中顯示 「您又年長了 4 歲」。score 屬性與 age 屬性事件過程基本一樣，不再贅述。

### 範例 4 使用 on 方法綁定多個事件
#### 1. 功能描述
在 Backbone 中，除了使用 on 方法綁定單個對象的事件，還可以使用該方法同時綁定多個對象的事件。綁定的格式有兩種，第一種為各個事件使用空格隔開，格式如下：

```javascript
obj.on(eventName1 eventName2,function);
```

其中，使用空格隔開的參數 eventName1 和 eventName2 表示被綁定的多個事件名稱，function 表示所有被綁定事件都要執行的自定義函數。接下來通過一個簡單的範例來說明如何獲取重置時的原值與重置後的新值，並將它們輸出至瀏覽器的控制台中。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"張三",  
        sex:"女",  
        age:20,  
        score:30  
    }  
});  

var man = new person();  
man.on("change:score change:age",function(model,value){  
    var oldAge = model.previous("age");  
    var newAge = model.get("age");  
    if(oldAge!=newAge){  
        console.log("age 原值:"+oldAge+", 新值:"+newAge);  
    }  
      
    var oldScore = model.previous("score");  
    var newScore = model.get("score");  
    if(oldScore!=newScore){  
        console.log("score 原值:"+oldScore+", 新值:"+newScore);  
    }  
});  

man.set("age",36);  
man.set("score",200);  
```

#### 2. 源碼分析
在上述代碼中，使用 on 方法分別綁定了對象 man 的兩個屬性事件 change:score 和 change:age，在綁定事件執行的函數中，首先通過模型對象的 previous 和 get 方法獲取屬性修改前後的值，然後通過比較這兩個屬性值是否發生了變化，如果發生了變化，則在瀏覽器的控制台中輸出該屬性的原值和新值。最後，通過調用 man 對象的 set 方法重置 age 和 score 屬性值。
從上中 in 個，可以看出由於是按照先後順序對 man 對象的屬性值進行重置，因此，在控制台顯示時，也是按照順序進行顯示的，如果不是按照先後順序，而是同時對兩個屬性值進行重置，重置屬性值代碼修改如下：
	
```javascript
man.set({"age":36,"score":200})
```

執行上述重置屬性值時，將會在控制台輸出兩次一樣的結果，最終在 Chrome 瀏覽器控制台輸出效果如下所示。

上重複輸出的原因在於，同時重置屬性值，導致在兩個屬性事件分別觸發的過程中，這兩個屬性的原值與新值都是不相同的，因此符合輸出顯示的條件。因為綁定兩個事件，所以出現了兩次重複的輸出。

在使用 on 方法綁定事件中，有兩種格式可以綁定多個事件，除第一種使用空格之外，第二種方法為使用對象方式綁定多個事件，其調用格式如下：
	
```javascript
var ObjEvent = {
       eventName1:function1,
       eventName2:function2
       ...
}

Obj,on(ObjEvent);
```

上述代碼中，首先定義一個 ObjEvent，並以 ket/value 的方式向該對象批量添加各個事件名稱和執行事件的函數，即參數 eventName1 和 function1；然後通過使用 on 方法綁定該哈希對象即可。
接下來將第一種使用空格方式綁定多個事件的代碼修改成使用哈希對象綁定多個事件的功能，修改代碼如下所示：

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"張三",  
        sex:"女",  
        age:20,  
        score:30  
    }  
});  

var man = new person();  
var objEvent = {  
    "change:score":score_change,  
    "change:age":age_change,  
}  

man.on(objEvent);  

function score_change(model,value){  
    var oldScore = model.previous("score");  
    var newScore = model.get("score");  
    if(oldScore!=newScore){  
        console.log("score 原值:"+oldScore+", 新值:"+newScore);  
    }  
}  
  
function age_change(model,value){  
    var oldAge = model.previous("age");  
    var newAge = model.get("age");  
    if(oldAge!=newAge){  
        console.log("age 原值:"+oldAge+", 新值:"+newAge);  
    }  
}   
  
man.set({"age":36,score:200});  
```

在上述代碼中，首先定義一個哈希對象 objEvent，在該對象中批量添加了模型對象 man 的兩個屬性事件 change:score 和 change:age，並指定這兩個屬性事件執行的自定義函數分別為 score_change 和 age_change，代碼如下所示：

```javascript
var objEvent = {
     "change:score":score_change,
     "change:age":age_change
}
```


然後，調用模型對象 man 的 on 方法綁定 objEvent，從而完成對象多事件的綁定。最後，調用模型對象 man 的 set 方法重置對象的 age 和 score 兩個屬性值，導致對應綁定屬性事件的觸發，其最終在 Chrome 瀏覽器中執行的結果與上完全一致。

注意：
雖然使用上述兩種方式都可以綁定對象的多個事件，但這兩種方式也各具特點。在使用空格方式綁定多個事件時，需要考慮事件執行時的順序，有重複執行的可能性，因此適合多個事件執行一個函數的場景；而使用 objEvent 的方式綁定多個事件時，每個事件所執行的函數都是一一對應的關係，代碼機構非常清晰，因此適合不同事件執行不同函數的需求。

## 綁定一次 once 方法
在 Backbone 中，使用 on 方法可以綁定對象的事件之外，還可以使用 once 方法完成對象事件的綁定，只不過 once 方法綁定的事件只執行一次，之後即使觸發也不執行。其調用的格式如下。
Obj.once(eventName,function,[context])
其中，參數 Obj 表示對象本身，eventName 表示自定義的事件名，function 表示當前事件觸發時被執行的函數。接下來通過一個簡單範例介紹它的使用方法。

### 範例 5 使用 once 方法綁定事件
#### 1. 功能描述
先使用模型對象的 once 對象綁定 change 事件。在該事件中，通過變量累計事件執行的次數，並將該次數內容輸出至瀏覽器的控制台中，然後使用 set 方法重置二次模型對象的屬性。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var intNum = 0;  
man.once("change",function(){  
    intNum++;  
    console.log("事件觸發的次數為"+intNum);  
});  
man.set("age",36);  
man.set("age",37);  
```

#### 2. 源碼分析
在上述代碼中，首先定義數據模型並實例化一個模型對象 man，定義一個用於記錄執行次數的全局變量 intNum，該變量的初始值為 0；然後通過 man 對象調用 once 方法，綁定對象的 change 事件。在該事件執行的函數中，先累加變量 intNum 的值，即記錄該事件觸發的次數，並且在控制台中輸出該累加的變量 intNum 值。最後，兩次調用 man 對象的 set 方法對 age 屬性進行重置，
從上看到，雖然執行了兩次的屬性重置操作，但綁定的 change 事件僅顯示一次，這是由於綁定事件時，使用了 once 方法，而非 on 方法，當執行 once 方法綁定對象的事件後，將會自動調用內部的_once() 函數，僅執行一次對象綁定的事件，因此輸出的執行次數為 1. 針對 once 方法的這種特點，常常將該方法用於綁定對象的一些初始化事件中。

## 觸發事件 trigger 方法
前面介紹了如何使用 on 或 once 方法綁定對象事件的過程，而這些事件都是對象本身自帶的系統事件，如 change、change:age、change:score，它們的觸發都必須滿足相應的條件。例如 change 事件，當對象中的屬性值發生了變化時，才能觸發，其實也能通過調用一個方法來手動觸發某個事件，而這個方法就是 trigger。
trigger 也是 Backbone 事件 API 中的一個重要方法，它的功能是觸發對象的某一個事件，調用格式如下所示：

```javascript
Obj.trigger(eventName)
```

其中，參數 Obj 表示對象本身，eventName 表示自定義的事件名，即需要手動觸發的事件名稱。
使用 trigger 方法可以手動觸發對象的任何事件，不僅是自帶的系統事件，還可以是自定義的事件，接下來通過一個簡單的實例介紹 trigger 方法的事件。
	
### 範例 6 使用 trigger 方法觸發事件
#### 1. 功能描述
先使用 on 方法綁定模型對象的自定義事件 change_age_sex 和 age 屬性的 change 事件，然後分別調用 trigger 方法手動觸發綁定的事件。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});

var man = new person();  
man.on("change_age_sex",function(){  
    console.log("您手動觸發了一個自定義事件");  
});  

man.on("change:age",function(model,value){  
    if(value!=undefined){  
        console.log("您修改後的年齡為"+value);  
    }else{  
        console.log("您手動觸發了一個年齡修改事件");  
    }  
});  

man.trigger("change_age_sex");  
man.trigger("change:age");  
man.set("age",37);  
```


#### 2. 源碼分析
在上述代碼中，首先實例化了一個數據模型對象 man，然後通過調用 on 方法為 man 對象綁定了 change_age_sex 自定義事件和 change:age 系統自帶事件。在 change_age_sex 自定義事件中，將通過瀏覽器的控制台輸出一段說明文字；而在系統自帶的 change:age 事件中，將根據回調函數 value 的值來判斷事件是否手動執行還是自動觸發。如果是手動執行，在控制台中輸出一段說明文字，否則輸出修改後的 age 屬性值。最後，分兩次調用 man 對象的 trigger 方法手動觸發自定義事件 change_age_sex 和系統自帶事件 change:age，同時使用 set 方法重置 man 對象的 age 屬性值，導致 change:age 的自動觸發。

從上中看到，使用 trigger 方法手動觸發 change_age_sex 事件時，在瀏覽器的控制台輸出對應的說明文字。同樣，在手動觸發 change:age 事件時，在執行事件的代碼中已通過回調函數 value 值檢測出是否手動觸發，因此只在瀏覽器的控制台輸出對應的說明文字。然後，第三次通過 set 方法自動觸發 change:age 事件時，在瀏覽器的控制台中則輸出了修改後的 age 屬性值 37。

## 移除事件 off 方法
在 Backbone 中，與綁定事件的 on 方法相對應的是移除事件的 off 方法，該方法的功能是移除對象中已綁定的某個、多個或全部的事件。其調用格式如下。

```javascript
Obj.off(eventName,function,[context])
```

其中，參數 eventName 表示被移除的綁定事件名稱，該名稱可以是單個也可以是多個，如果要移除多個事件，則用空格將事件名稱隔開。除了移除對象的綁定事件之外，還可以移除事件執行的函數。即通過參數 function 來表示被移除的事件函數名。另外，如果在調用 off 方法時不帶任何參數，表示移除對象的全部綁定事件。接下來通過簡單範例逐一進行介紹。

### 範例 7 使用 off 方法移除對象的某個或多個綁定事件
在 Backbone 中，如果要移除對象的某個綁定事件，可以調用對象的 off 方法，指定需要移除的事件名稱；如果有多個事件名稱，則用空格隔開。

#### 1. 功能描述
先自定義兩個函數，用於模型對象的 on 方法綁定自定義事件時調用。先使用 on 方法綁定自定義的事件，又調用 off 方法移除已綁定的事件。最後，使用 trigger 方法手動觸發事件，觀察事件觸發時瀏覽器控制台中輸出的內容。


```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var m = 0;  
var n = 0;  
var callback_a = function(){  
    m++;  
    console.log("您執行 a 事件的次數為:"+m);  
}  
var callback_b = function(){  
    n++;  
    console.log("您執行 b 事件的次數為:"+n);  
}  

man.on("event_a",callback_a);  
man.on("event_b",callback_b);  
man.off("event_a");  
man.trigger("event_a event_b");  
man.off("event_a event_b");  
man.trigger("event_a event_b");  
```


#### 2. 源碼分析
在上述代碼中，實例化一個 man 對象之後，首先定義兩個變量 m、n，分別用於記錄執行不同事件的次數。其次自定義兩個事件執行函數 callback_a 和 callback_b。在這兩個函數中，分別對 m、n 的值進行累加，並在瀏覽器的控制台中輸出事件的名稱和累加後的值。

最後，兩次使用對象的 on 方法分別綁定 event_a 和 event_b 事件，並首次調用對象的 off 方法移除綁定的 event_a 事件。接下來，首次調用對象的 trigger 方法觸發 event_a 和 event_b 事件，當完成 trigge 方法調用後，再次使用對象的 off 方法移除綁定的 event_a 和 event_b 事件。此時，對象 man 所綁定的兩個事件已經全部移除。

從上中看到，雖然在代碼中使用 on 方法綁定了 event_a 和 event_b 兩個自定義的事件，但是在第一次觸發這兩個事件之前，先通過 off 方法移除了 event_a 事件。因此，在第一次觸發已綁定的兩個事件時，實際上僅有 event_b 事件執行。

在第二次調用 trigger 方法再次觸發這兩個事件之前，又通過 off 方法移除了 event_a 和 event_b 事件，因此，在第二次觸發兩個事件時，已經沒有任何事件被執行，最終只有 event_b 事件被執行一次。

### 範例 8 使用 off 方法移除綁定事件的某個函數
在 Backbone 中，不僅可以調用對象的 off 方法移除已綁定的一個或多個事件，還可以移除綁定事件執行的某個函數。( 一個事件可以綁定多個函數 )

#### 1. 功能描述
在範例 7 的基礎之上，使用 off 方法移除已綁定的某個函數，並觀察在觸發綁定事件時瀏覽器的控制台中輸出的內容。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  
var man = new person();  
var m = 0;  
var n = 0;  
var callback_a = function(){  
    m++;  
    console.log("您執行 a 事件的次數為:"+m);  
}  
var callback_b = function(){  
    n++;  
    console.log("您執行 b 事件的次數為:"+n);  
}  
man.on("event_a",callback_a);  
man.on("event_a",callback_b);  
man.trigger("event_a");  
man.off("event_a",callback_b);  
man.trigger("event_a");  
```

#### 2. 源碼分析
首先要明白一點，一個事件可以綁定多個 function。

由於 event_a 最開始綁定了 callback_a 和 callback_b，所以在觸發 evant_a 的時候 callback_a 和 callback_b 函數都會觸發。
當執行 man.off("event_a",callback_b); 的時候表示移除事件 event_a 裡的指定函數 callback_b，此時 event_a 裡只有綁定了函數 callback_b 。所以再次執行事件 event_a 的時候只執行了 callback_b。

### 範例 9 使用 off 方法移除對象的全部綁定事件
在 Backbone 中，對象的 off 除了可以移除某個或多個事件、事件執行的函數外，還可以通過不帶參數的方法移除全部已綁定的事件，調用格式如下所示：

```javascript
Obj.off();
```

#### 1. 功能描述
在範例 7 的基礎之上，先調用 off 方法移除全部已綁定的事件，然後調用 trigger 方法手動觸發這些事件，觀察瀏覽器控制台中內容輸出的變化。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var m = 0;  
var n = 0;  

var callback_a = function(){  
    m++;  
    console.log("您執行 a 事件的次數為:"+m);  
}  

var callback_b = function(){  
    n++;  
    console.log("您執行 b 事件的次數為:"+n);  
}  

man.on("event_a",callback_a);  
man.on("event_a",callback_b);  
man.off();  
man.trigger("event_a event_b");  
```

#### 2. 源碼分析
執行修改的代碼後在控制台中不會輸出任何結果信息。這是因為雖然使用對象的 on 方法綁定 event_a 和 event_b 這兩個事件，但在觸發這兩個綁定的事件之前，使用無參的方式調用了對象的 off 方法，將對象全部綁定的事件移除。因此，在執行時不會觸發任何事件。所以在控制台中沒有任何信息輸出。


## 監聽事件 listenTo 方法
Backbone 的 1.0 版本中提供了監聽方法，在前面章節中提高的 on 等事件綁定方法，其實也是監聽方法的一種，只不過監聽的對象不同，對象的 on 方法用於監聽對象某事件的觸發，即對象觸發了這個事件，便執行相應的代碼。

相對於對象的 on 方法而言，listenTo 方法的監聽效果更為突出，它是一個對象監聽另一個對象的事件，如果被監聽的對象出發了被監聽的事件，執行相應的回調函數或代碼塊。例如，view 對象要監聽 mode 對象的 change 事件，如果 mode 對象觸發了 change 事件，則需要刷新當前 view 對象，即執行下列監聽方法的代碼。

```javascript
view.listenTo(model,'change',view.render);
```

上述監聽方法也等價於如下代碼。

```javascript
model.on('change',view.render,view);
```

其中，第三個參數為上下文環境對象，此時它的值為 view，即 model 對象在觸發 change 事件時，關聯 view 對象進行執行 view.render 動作。

通過上述對 listenTo 方法的簡單介紹，我們知道它是一個對象級別的事件監聽方法，即在執行該方法時，必須具有兩個對象，其調用格式如下。

```javascript
Obj1.listenTo(Obj2,EventName,function);
```

其中，參數 Obj1、Obj2 都為對象，參數 EventName 是 Obj2 對象觸發的事件名稱，參數 function 為當 Obj2 觸髮指定的 EventName 事件時，Obj1 所執行的自定義函數。接下來通過一個簡單的範例來演示 listenTo 方法的使用。

### 範例 10 使用 listenTo 方法監聽事件

#### 1. 功能描述

先調用 listenTo 方法綁定模型對象 age 屬性的 change 事件。在該事件中，將會在瀏覽器的控制台輸出重置 age 屬性時的原值與新值內容，然後調用 set 方法重置對象的 age 屬性。在瀏覽器控制台輸出變化時的原值與新值內容。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var obj = _.extend({},Backbone.Events);  
obj.listenTo(man,"change:age",function(model,value){  
    var oldage = model.previous("age");  
    var newage = model.get("age");//value 也是 age 最新值的意思   
    if(oldage!=newage){  
        console.log("age 原值:"+oldage+", 新值:"+newage);  
    }  
});  
man.set("age",37);  
```

#### 2. 源碼分析
在上述代碼中，首先實例化了一個 model 對象 man 外，並定義了另一個事件對象 obj。然後調用 obj 對象的 listenTo 方法，監聽 man 對象的 change:age 事件。當觸發該事件時，獲取事件觸發前後的 age 屬性值，並進行比較。如果發生了 變化，則在瀏覽器的控制台輸出原值和新值。最後調用 man 對象的 set 方法重置 age 屬性值，用於觸發 change:age 事件。

從上中看到，在一個對象使用 listenTo 方法監聽另一個對象的事件時，與對象本身調用 on 方法基本相同，同樣可以用過回調函數獲取 model 對象和 value 新值，並通過比較是否變化後輸出在瀏覽器的控制台中。

## 監聽一次 listenToOnce 方法
在 Backbone 中，除了使用 listenTo 方法進行對象級別事件的監聽外，還可以使用 listenToOnce 方法進行對象級別事件的監聽。這點類似於對象的 on 方法與 once 方法。對象的 listenTo 和 listenToOnce 方法最大的不同之處在於，前者是一個對象一直監聽另一個對象事件的觸發，而後者是僅監聽一次。其調用格式如下所示：

```javascript
Obj1.listenToOnce(Obj2,EventName,function);
```

其中，參數的說明與 listenTo 完全一樣，不再贅述。接下來通過一個簡單範例來演示調用 listenToOnce 方法綁定對象的屬性事件。


### 範例 11 使用 listenToOnce 方法監聽事件

#### 1. 功能描述
首先使用 listenToOnce 方法綁定對象 age 屬性的 change 事件。在該事件中，累計事件執行的次數，並將該次數的內容輸出至瀏覽器的控制台中，然後調用 set 方法兩次重置 age 屬性值，觸發對應的 change 事件，觀察瀏覽器控制台輸出內容的變化。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var obj = _.extend({},Backbone.Events);  
var intNum = 0;  

obj.listenToOnce(man,"change:age",function(model,value){  
    intNum++;  
    console.log("事件觸發的次數為:"+intNum);  
});  

man.set("age",37);  
man.set("age",38);  
```

#### 2. 源碼分析
在上述代碼中，首先定義兩個用於監聽的對象 man 和 obj，同時定義了一個名為 intNum 的變量，用於記錄事件觸發的次數。然後，調用 obj 對象的 listenToOnce 方法監聽 man 對象的 change:age 事件，當該事件觸發時，intNum 變量累加一次值，在瀏覽器的控制台中輸出累加後變量 intNum 的值。最後，再次調用 man 對象的 set 方法，重置 man 對象的 age 屬性值。
從上中看到，雖然在代碼中兩次調用 man 對象執行 set 方法重置 age 屬性值，但是，在瀏覽器的控制台中，事件觸發的次數顯示為 1. 這是由於 obj 對象使用了 listenToOnce 方法監聽 man 對象的 change:age 事件，而 listenToOnce 方法的功能是只執行首次的監聽操作，後續事件即使觸發也不會監聽，因此瀏覽器的控制台中顯示的事件觸發次數為 1。


## 停止監聽 stopListening 方法
在 Backbone 中，與單個對象的 off 方法相同，對象級別的事件監聽也有停止方法，即 stopListening 方法，其調用格式如下。

```javascript
Obj1.stopListening(Obj2,EventName,function);
```

其中，參數 Obj1 和 Obj2 分別為監聽發起對象和被監聽對象，EventName 參數為被監聽對象所促發的事件名稱，如果要停止監聽多個事件，可以在這個參數中使用空格號隔開。另一個參數 function 的作用是，當觸發監聽事件時，監聽發起對象執行的自定義函數名稱。在 stopListening 方法中，既可以停止被監聽對象的某個或多個事件的監聽，也可以通過參數 function 在被監聽對象的停止監聽事件觸發後，發起對象執行的自定義函數。此外，如果該方法不調用任何參數，表示停止監控全部已監控的對象事件。接下來通過一個簡單的範例來演示 stopListening 方法的使用。

### 範例 12 使用 stopListening 方法停止監聽
#### 1. 功能描述
首先使用 ListenTo 方法，分別綁定對象 name、age、score 屬性的 change 事件，然後調用 stopListening 方法分別停止某個事件綁定的監聽，最後調用 set 方法重置屬性值時觸發綁定的事件，觀察瀏覽器控制台輸出內容的變化。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  
var obj = _.extend({},Backbone.Events);  

obj.listenTo(man,"change:name",function(model,value){  
    console.log("name 修改後的值為:"+value);  
});  

obj.listenTo(man,"change:age",function(model,value){  
    console.log("age 修改後的值為:"+value);  
});  

obj.listenTo(man,"change:score",function(model,value){  
    console.log("score 修改後的值為:"+value);  
});  

// 停止監聽某一個事件   
obj.stopListening(man,"change:name");  
man.set("name","張三");  
man.set("age",35);  
man.set("score",600);  

// 停止監聽兩個事件   
obj.stopListening(man,"change:name change:age");  
man.set("name","李四");  
man.set("age",36);  
man.set("score",601); 
 
// 停止監聽全部事件   
obj.stopListening();  
man.set("name","王二");  
man.set("age",37);  
man.set("score",602);  
```

#### 2. 源碼分析
在上述代碼中，首先定義了兩個對象 man 和 obj，前者為被監聽對象，後者為監聽發起對象。然後，通過 listenTo 方法分別監聽 man 對象的 change:age、change:age、change:score 事件，當這些事件被觸發時，將在瀏覽器的控制台輸出變化後的新值。最後，三次重置 man 對象的 name、age、score 屬性值。在第一次重置前，調用對象的 stopListening 方法停止監聽 change:name 事件；在第二次重置前，調用對象的 stopListening 方法停止監聽 change:name 和 change:age 事件；在第三次重置前，調用對象的 stopListening 方法停止全部監聽的事件。

從上看到，當第一次重置 man 對象的 name、age、score 屬性值前，因為調用對象的 stopListening 方法停止監聽 change:name 事件，在瀏覽器的控制台中顯示了 age、score 修改後的值 35 和 600；而在第二次重置 man 對象的三個屬性值前，因為調用對象的 stopListening 方法停止監聽 change:name 和 change:age 事件，在瀏覽器的控制台中顯示了 score 修改後的值 601；而在第三次重置 man 對象的三個屬性值前，因為調用對象的 stopListening 方法停止全部的監聽事件，沒有在瀏覽器中輸出任何信息。


## 其他事件
在 Backbone 的事件 API 中，除了上述方法之外，還有一個比較特殊的事件——all，該事件無論在對象觸發任何自身事件或執行自定義函數時，都會執行。此外，在整個 Backbone 中 Events 佔有非常重要的地位，它以最小化模塊的方式整合到 Model、Collection、View 類中，實現可以在這些類中自定義事件的功能，接下來詳細介紹這兩方面的知識。

## 特殊事件 all 的使用
在 Backbone 中，all 是一個很特殊的事件，因為該事件在對象的任何事件被觸發時，它都會自動觸發，可以說是一個全局性的事件。此外，既然它是一個事件，也可以通過調用對象的 trigger 方法進行手動觸發；在觸發該事件時，還可以在回調的函數中通過 value 參數獲取當前正在觸發的事件名稱。其調用的格式如下：

```javascript
Obj.on("all",function);
```

其中，使用 on 方法綁定了對象的 on 事件，當該事件觸發時，將執行自定義的回調函數 function。在該函數中，可以添加一個 value 參數，通過該參數獲取當前正在觸發事件的名稱。接下來通過一個簡單的範例來演示 all 事件的運用。

### 範例 13 all 事件的使用

#### 1. 功能描述
首先使用 on 方法綁定對象 name、age 屬性的 change 事件和特殊的 all 事件，在這些事件中，都會向瀏覽器的控制台輸出不同的內容。然後，調用 set 方法重置對象的屬性值，觸發對應的 change 事件，觀察瀏覽器控制台輸出內容的變化。

```javascript
var person = Backbone.Model.extend({  
    defaults:{  
        name:"",  
        sex:"女",  
        age:32,  
        score:120  
    }  
});  

var man = new person();  

man.on("change:age",function(model,value){  
    console.log("您觸發了 change:age 事件");  
});  

var event_fun = function(){  
    console.log("您觸發了 change:name 事件");  
}  

man.on("change:name",event_fun);  
man.on("all",function(value){  
    console.log("您觸發了 all 事件中"+value);  
});  

man.set("name","皮卡丘");  
man.set("age",35);  
```

#### 2. 源碼分析
在上述代碼中，首先實例化一個名為 man 的數據模型對象，然後調用對象的 on 方法綁定了 change:age 和 change:name 事件，並且還綁定了 all 事件。在綁定 all 事件中，通過回調函數中的 value 參數獲取正在觸發的事件名稱，並在瀏覽器的控制台中顯示該事件名稱；而在 change:age 和 change:name 事件中，分別在瀏覽器的控制台中顯示相應觸發事件的名稱。最後，調用 man 對象的 set 方法分別重置對象自身的 name、age 屬性值。

從上中看到，雖然只調用 man 方法重置了兩個屬性，但 all 事件卻被觸發了 4 次，這是由於每次重置對象屬性時，不僅觸發屬性事件，還要觸發 change 事件，這一點在到前面章解介紹 change 事件時已經介紹過。因此，當重置一個屬性值時，實際上觸發了兩個事件，由於每個事件觸發之後都會觸發 all 事件，所以 all 事件被觸發了 4 次。

從上中還可以看出，all 事件與其他事件在觸發上的順序，都是當其他事件觸發之後才觸發 all 事件。如果在一個事件中綁定了多個執行的自定義函數，而綁定的 all 事件觸發的順序也將是按照函數綁定時的順序依次觸發的。

前面也提到過，作為一個事件同樣可以調用對象的 trigger 方法來觸發，而它觸發後輸出什麼內容呢？接下來，將代碼範例 13 中最後兩句對象重置的代碼修改為如下：

```javascript
man.trigger("all");
```

從上中看到，如果在沒有任何事件觸發的前提下，手動調用對象的 trigger 方法觸發 all 事件，同樣也會執行兩次。這是由於當調用 trigger 方法時，會調用兩次 triggerEvents，一次為參數傳遞來的指定事件，另一次為固定的 all 事件。當沒有任何事件名稱傳遞過來時，其值為 undefined，在 Backbone 框架內部的代碼中，與 all 事件相關代碼如下所示：
	
```javascript
var events = this._events[name];
var allEvents = this._events.all;
if(events) triggerEvents(events,args);
if(allEvents) triggerEvents(allEvents,arguments);
```
