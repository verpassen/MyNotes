# Process control of laser metal deposition

\---

title: process control of laser metal deposition&#x20;

author: Yangbo Liu

\---

## 研究目標

研究並找到一個方式可以藉由控制熔池大小來穩定製程

並將過程拆成兩個步驟

1. 如何正確的量測到熔池的尺寸
2. 如何控制熔池大小

## 使用的設備

* 近紅外線相機 (monochrome NIR camera)

相機只會輸出灰階的影像，因此還需要real-time melt pool algorithm 去計算尺寸與調整threshold algorithm。目的在於可以正確地取得熔池的大小&#x20;

## Review&#x20;

```
LMD 的形式粗略的可以分成兩種，依據供粉的形式
1. 同軸
2. Coaxial

同軸的LMD 形式比Off-axial 的粉末利用率來的高，氧化狀況較低 
兩種形式最大的差別在粉末的分布情況。 
off-axial 可以使用較大的雷射功率與較大的粉末供給率

2. 過去對於熔池監控有介紹過很多種不同的做法。有光學、聲音、熱溫計去量測的。以這一篇最推薦的為以近紅外線相機 + 熱溫計校正。缺點是成本高，更不用談如果需要高動態範圍的相機
   
3. **目前的控制模型只有使用First-order system** , 因此相關性的部分，在調整參數後，雷射功率與光斑大小和熔覆特徵的關係，就會失去參考性。
```

## Modeling

```
 * linear parameter varying(LPV) model 描述雷射功率和熔池大小的關係(LP-MPS)
```

```
 * 利用 PIControl 與 MPC (Model predictive control) 來控制熔池大小 
```

```
 * 三個模擬的假設
    -  the substrate is thick enough (底板的質量 >> 供給粉末質量)
    - no significant surface area change 
    - no or low heat accumulation ( 需要有足夠的冷卻時間)
```



## 量測熔池

* Threshold calibration

Threshold 決定判斷擷取畫面熔池擷取的邊界，因此threshold 設定值決定了後續計算出來的熔池大小的正確性，重點在於如何調整threshold ，使得預測的熔覆寬度和觀察到的熔池大小的差距越小越好

* Intensity Calibration&#x20;

熔覆寬度應該為真實熔池大小的寬度 > 假設熔融的材料很快固化，且沒有熔融的材料沒有擴散



## 問題

* Recoil pressure
  * The role of recoil pressure in energy balance during laser materials processing

### CCD

* Pixel bit depth and well depth
* high dynamic range

## 參考資料

```
- [LPV](https://www.mathworks.com/help/control/ug/linear-parameter-varying-models.html)
- [MPC](https://en.wikipedia.org/wiki/Model_predictive_control)
```

