# Otsu Threshold method

自動找出最佳的閥值，去除手動調整閥值的問題



Python 範例

```python
import cv2         
import numpy as np  

image1 = cv2.imread('test.jpg')
img = cv2.cvtColor(image1, cv2.COLOR_BGR2GRAY)
  
# applying Otsu thresholding as an extra flag in binary thresholding     
ret, thresh1 = cv2.threshold(img, 120, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)   

```

