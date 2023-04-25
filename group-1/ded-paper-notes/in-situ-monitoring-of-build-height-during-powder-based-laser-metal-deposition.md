# In situ monitoring of build height during powder‑based laser metal deposition

## 名詞解釋

* measure omnidirectional height

height in different build directions relative to substrate

* convex position

相較起來較高一點的位置

* concave poisioin

相較起來比較低一點的位置

## Paper Review

主要影響成形的幾何尺寸正確性，需要考量到以下三點:

1. 在堆積過程中，製程參數的不穩定(例如:粉末)，會造成熔覆堆積高度的變化。 而高度的變化，又會造成雷射與粉末在不同的高度下成型，再影響到材料堆積的結果
2. 材料基板表面的平整性(不在這一篇的討論範圍內)
3. 移動軌跡的變化，造成雷射的移動加/減速，影響到粉末被熔池捕捉的比例， 再影響至整體的堆積高度值

**Other insights**:

* Powder stream focal plane should be slightly below than the substrate plane
* the layer spacing 每層高建議為0.4-0.7倍的熔覆高度，以維持較好的沉積品質

## 目的

This study focuses mainly on the online measurement of build height via in situ monitoring of the laser/powder defocusing distance to develop a precursor for a feedback control system for build height for LMD printers

## 實驗架構

硬體

* 4kW 二極體雷射 (LDF 4000-100)
* collimator 72 mm focal length
* focusing lens 300 mm focal length
* 光斑直徑2.5 mm
* top hat intensity distribution
* Powder feeder (Metco Twin 10-c, Sulzer)
* 6軸機械手(IRB2400 , ABB)
* 中碳鋼基板(150 X 100 X 100 mm^3)

粉末材料

* SS316L-5320, Hoganas AB

## 實驗方法

### 相機校正 (Camera calibration)

* effective image resolution (d\_des) : 0.42 mm/pixel

### 影像處理流程

* Otsu adaptive threshold method

1. 先對影像擷取ROI(Region of interest)
2. 將影像放大五倍，維持相同的resolution (by bicubic interpolation)
3. 利用初始的檻值進行二值化
4.  將圖片進行橢圓擬合，

    4.1 如果橢圓長軸 a < 經驗值 l，儲存 $$y_{center}^n(t)$$

    4.2 如果橢圓長軸 a> 經驗值 l ，增加門檻值，回到步驟三

<figure><img src="../../.gitbook/assets/image process flowchart.PNG" alt=""><figcaption></figcaption></figure>

\---

### 實驗結果

利用三角形與箭頭的輪廓作為測試的薄牆外觀測試件

* Triangle-shaped

## 結論

## 問題

* using trianagulation method for height monitoring
* how to choose band-pass filter wavelength 文中雷射波長為 980-1030 nm 帶通濾波 900 nm with 10 +/- 2 nm 半寬帶
* camera calibration 的流程，文中並沒有詳細說明，需要再找其他文章閱讀
