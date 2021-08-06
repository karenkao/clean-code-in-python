## ch2 符合 python 風格的程式

在程式語言當中都有典型寫法(idiom)，他是為了執行特定工作而採取的特殊寫法，它是經常出現、不斷重複，且每次都使用同一個結構的東西，有人將它稱作模式(pattern)，但和設計模式不同。

典型寫法和程式語言有關，也就是每一個語言都有自己的表達風格，在python 當中稱為 pythonic。

有很多原因最好來遵循且編寫符合python 風格的程式，原因等等說，如同ch1所言可以讓開發團隊習慣使用相同的模式結構非常重要，也能協助他們專注在問題的本質，且避免犯錯，總之就是要pythonic 這樣寫就對了。

### 索引與切片:

  - 例子是: 
    
    ```
    a = (1,2,3,4,5,6,7,8,9,10)   
    a[-1] = 4
    a[2:4] = (3,4,5)
    a[:3] = (1,2,3)
    a[1:3:2] = (2) => 取得位置1和位置3間隔為2的所有元素
    
    # 上面那個也可以這樣寫
    interval = slice(1,3,2)
    a[interval] = (2)
    
    # 如果 slice 不寫數字進去就是 none
    interval = slice(None,3)
    a[interval] == a[:3] => 會 return True
    ```
    
    這裡的這個介紹是，優先使用內建的 slice 用法，取代 for 迴圈迭代，或是親手排除元素。
    
### 建立自己的序列 

如果想要自訂類別內的實作 __getitem__，必須考慮一些因素以遵守 python 的習慣作法。

例子是:
      
```
# 將傳入的數值包成list
>> class Items:
...    def __init__(self, *values):
...        self._values = list(values)
...    def __len__(self):
...        return len(self._values)
...    def __getitem(self, item):
...        return self._values.__getitem__(item)
...
>> guinea_pig = Items(5, 5, 6, 6)
>> len(guinea_pig)
4
>> guinea_pig[1:3]
[5, 6]
```
     
     
書中提到的是可以使用原本python 內建的魔法方法功能，也可以自己實作自己刻，但要符合python 風格

補充參考: https://www.huaweicloud.com/articles/12546698.html ，那多了解一些關於魔法方法:

什麼是Python魔法方法: 魔法方法就如同它的名字一樣神奇，總能在你需要的時候為你提供某種方法來讓你的想法實現。魔法方法是指Python內部已經包含的，初學者掌握Python的魔法方法也就變得尤為重要了。

為麼要使用Python魔法方法:使用Python的魔法方法可以使Python的自由度變得更高，當不需要重寫時魔法方法也可以在規定的默認情況下生效，在需要重寫時也可以讓使用者根據自己的需求來重寫部分方法來達到自己的期待。而且眾所周知Python是支持面向對象的語言Python的基本魔法方法就使得Python在面對對象方面做得更好。
     
- 這邊的舉例比較好懂:
![image](https://user-images.githubusercontent.com/76892468/126800913-fde1bddf-dc92-4a7a-a87b-2cfeee625773.png)

![image](https://user-images.githubusercontent.com/76892468/126800963-04084a56-6399-4b1d-9258-c7324a0b153a.png)
- 另外一個例子費式數列用__iter__做:

![image](https://user-images.githubusercontent.com/76892468/126801113-9c949007-8e56-4c9d-9398-e71cdec09fcf.png)

- 還有一個例子是調用不存在的方法或是屬性可以有預設值:

![image](https://user-images.githubusercontent.com/76892468/126801285-d33f230e-1f55-4378-9d29-cd34deee64f5.png)

![image](https://user-images.githubusercontent.com/76892468/126801408-15202a9b-dab5-4fe8-821f-a3f260243c5d.png)
     
  ### 環境管理器(context manager)
  
  這是 python 獨有的實用功能，可以對模式做正確的回應，模式是指我們執行某段程式時的每一種情況，它有先決條件也有後置條件，也就是執行主要動作之前與之後要做的東西。
  
  可以再資源管理程式附近看到環境管理器，例如: 當檔案被打開時，我們要處理它之後確保他們被關閉，或是當我們打開一個服務連結時也要相應的關閉它。
  
  這種情況下，通常要在關閉的時候釋出之前配置的所有資源，這是需要考慮的基本狀況，額外還要處理例外與錯誤，所以最常見的方式就是把清理放在 finally 區塊，確保不會遺漏它。
  - 例如:
  
      ```
      >> file = open(file_path)
      >> try:
      ...    process_file(file)
      ...finally:
      ...    file.close()
            
      
      - 後期有一個優雅進化的寫法 (with open):
      
      >> with open(file_path) as file:
      ...    process_file(file)

      ```
   環境管理器有兩個魔術方法: __enter__ 和 __exit__
   
   1. 剛剛的 with open 就是呼叫 __enter__然後把回傳的東西指派給as 後面的參數
   
   2. 裡面所有工作都執行完後會呼叫 __exit__ 然後安全的清理環境

   - 補充資料 : 有關環境管理器 https://blog.gtwang.org/programming/python-with-context-manager-tutorial/
   
   ![image](https://user-images.githubusercontent.com/76892468/126807902-8a18c37d-e5ec-4023-a301-bbb6d7570db4.png)
   
   ![image](https://user-images.githubusercontent.com/76892468/126807949-4aea4bda-a36b-4cd5-a7d5-391df3eee69e.png)
   
   ### 實作環境管理器
   
   實作剛剛範例的那種環境管理器，其實只要實作了魔術方法中的 __enter__ 和 __exit__ 的類別，就可以支援環境管理器協定了，但除了這個方法之外，還有其他的製作方式。
   
   1. 用 contextlib (可以參考: http://dokelung.me/category/python/dont-reinvent-sandwich-2/)
   
   ![image](https://user-images.githubusercontent.com/76892468/127176289-c545c21a-753b-40a7-a88c-5407b9982b52.png)
   
   ![image](https://user-images.githubusercontent.com/76892468/127176386-a3e65d03-2dbc-474c-b710-d85fa63f2b1c.png)

