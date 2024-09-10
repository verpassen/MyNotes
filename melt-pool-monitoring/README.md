# Melt pool monitoring

## Why we need it ?

> 能夠達到一個穩定可控制的系統 ,才有辦法進行長時間的製程或是完成一個薄且高的物件

#### 如何去控制以下的參數:

* 粉末流量的控制 Powder flow rate
* 融池溫度 temperatur of the melt pool
* 融池大小 geometry of the melt pool
* 熔覆層高 height
* 雷射功率 Laser power



## The Approach of monitoring

&#x20;

### 1.  同軸影像

使用儀器: 同軸CCD

### 2. 熔池溫度

使用儀器: Pyrometer , 紅外線攝影機



### 3. 熔池發射出的電子/電流

> 原理 : 溫度夠高的時候，金屬的電子會脫離金屬表層散逸。

$$
\begin{align*} J &= A_G T^2 e^{-\frac{w}{k_B T}} \\[4mm] 其中& \\ &J 代表 發散的電流密度\\ &k_B 波茲曼常數 \\ &A_G 不同材料校準常數 \\ &W 材料的工作函數，通常溫度要超過2000K 才有明顯可偵測的熱發射數量 \end{align*}
$$



## 參考

* [Monitech Weld Monitoring systems](https://www.youtube.com/watch?v=2JfAbtnZWHM)&#x20;
* [Monitoring of the laser welding process with the Laser Welding Monitor LWM](https://www.youtube.com/watch?v=LRPpoZLZLvM\&t=9s) -[Weld depth monitoring](https://www.youtube.com/watch?v=qqNRbLRYWqI\&t=2s)
* [AM 閉迴路控制](https://sigmaadditive.com/wp-content/uploads/2018/09/process-mapping-metal-additive-manufacturing.pdf)
* [melt pool overview](https://www.sciencedirect.com/topics/engineering/melt-pool)
* [GTV Monitoring system](https://www.gtv-mbh.de/en/equipment-technology/process-monitoring.html)



















