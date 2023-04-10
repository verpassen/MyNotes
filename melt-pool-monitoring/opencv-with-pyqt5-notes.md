# OpenCv with PyQt5 notes

## 測試1

將圖片做color mapping&#x20;

原本灰階 8 bit 的圖片，依據grey value mapping 成不同色階的顯示

```python
import cv2 
import numpy as np 
import matplotlib.pylab as plt 

file_path = r'D:\\WorkTmp\\' # 路徑
name = 'test.jpg' # 檔名

def get_cmap(cmap_name):
    cmap = plt.get_cmap(cmap_name)
    # Initialize the matplotlib color map
    sm = plt.cm.ScalarMappable(cmap=cmap)
    # Obtain linear color range
    color_range = sm.to_rgba(np.linspace(0, 1, 256), bytes=True)[:,2::-1]
    return color_range.reshape(256, 1, 3)


gray = cv2.imread(file_path+name , cv2.IMREAD_GRAYSCALE)
img_bgr = cv2.applyColorMap(gray,get_cmap('jet'))
cv2.imshow('org',gray)

cv2.imshow('test',img_bgr)
cv2.waitKey(0)
```

<figure><img src="../.gitbook/assets/colormapping ex1.png" alt=""><figcaption></figcaption></figure>

## 延伸測試

如果要轉成contour ，利用matplot 畫圖

(目前的問題，因為contourf 會和影像的xy 座標原點不同，需要做軸向的轉換)

```python
x = np.arange(0,gray.shape[1])
y = np.arange(0,gray.shape[0])
xx,yy= np.meshgrid(x,y)
plt.contourf(xx,yy,gray,cmap='RdGy')
plt.colorbar()
plt.show()
```

<figure><img src="../.gitbook/assets/colormapping ex2.png" alt=""><figcaption></figcaption></figure>

## 測試2&#x20;

### 列出照片中沿著某條線的 GREY INTENSITY



```python
import cv2
import numpy as np 
import matplotlib.pyplot as plt 
img = cv2.imread(r'test 600_Color_Color.png',cv2.IMREAD_GRAYSCALE)
 
def on_click(event, x, y, flags, param):
    global mouseX, mouseY
    if event == cv2.EVENT_LBUTTONDOWN:
        print(x,y)
         
ss = np.zeros((512,512,3),np.uint8)

def createLineIterator(P1, P2, img):
    """
    Produces and array that consists of the coordinates and intensities of each pixel in a line between two points
    Parameters:
        -P1: a numpy array that consists of the coordinate of the first point (x,y)
        -P2: a numpy array that consists of the coordinate of the second point (x,y)
        -img: the image being processed
    Returns:
        -it: a numpy array that consists of the coordinates and intensities of each pixel in the radii (shape: [numPixels, 3], row = [x,y,intensity])     
    """
   #define local variables for readability
    imageH = img.shape[0]
    imageW = img.shape[1]
    P1X = P1[0]
    P1Y = P1[1]
    P2X = P2[0]
    P2Y = P2[1] 
   #difference and absolute difference between points
   #used to calculate slope and relative location between points
    dX = P2X - P1X
    dY = P2Y - P1Y
    dXa = np.abs(dX)
    dYa = np.abs(dY)
   #predefine numpy array for output based on distance between points
    itbuffer = np.empty(shape=(np.maximum(dYa,dXa),3),dtype=np.float32)
    itbuffer.fill(np.nan)
   #Obtain coordinates along the line using a form of Bresenham's algorithm
    negY = P1Y > P2Y
    negX = P1X > P2X
    if P1X == P2X: #vertical line segment
        itbuffer[:,0] = P1X
        if negY:
            itbuffer[:,1] = np.arange(P1Y - 1,P1Y - dYa - 1,-1)
        else:
            itbuffer[:,1] = np.arange(P1Y+1,P1Y+dYa+1)              
    elif P1Y == P2Y: #horizontal line segment
        itbuffer[:,1] = P1Y
        if negX:
            itbuffer[:,0] = np.arange(P1X-1,P1X-dXa-1,-1)
        else:
            itbuffer[:,0] = np.arange(P1X+1,P1X+dXa+1)
    else: #diagonal line segment
        steepSlope = dYa > dXa
        if steepSlope:
            slope = dX.astype(np.float32)/dY.astype(np.float32)
            if negY:
                itbuffer[:,1] = np.arange(P1Y-1,P1Y-dYa-1,-1)
            else:
                itbuffer[:,1] = np.arange(P1Y+1,P1Y+dYa+1)
            itbuffer[:,0] = (slope*(itbuffer[:,1]-P1Y)).astype(np.int) + P1X
        else:
            slope = dY.astype(np.float32)/dX.astype(np.float32)
            if negX:
                itbuffer[:,0] = np.arange(P1X-1,P1X-dXa-1,-1)
            else:
                itbuffer[:,0] = np.arange(P1X+1,P1X+dXa+1)
            itbuffer[:,1] = (slope*(itbuffer[:,0]-P1X)).astype(np.int) + P1Y
   #Remove points outside of image
    colX = itbuffer[:,0]
    colY = itbuffer[:,1]
    itbuffer = itbuffer[(colX >= 0) & (colY >=0) & (colX<imageW) & (colY<imageH)]
   #Get intensities from img ndarray
    itbuffer[:,2] = img[itbuffer[:,1].astype(np.uint),itbuffer[:,0].astype(np.uint)]
    return itbuffer
# ---
# 各層不同高度 ，目前取 [133, 152, 174, 196 ] (Y座標位置)
y= 152 
st_pt, end_pt = (266,133) , (266,230)
st_pt, end_pt = (220,y) , (320,y)
thickness = 2
data = createLineIterator(st_pt,end_pt,img.copy())

# --- Plot the diagram --- 
plt.plot(data[:,2])
plt.ylim((0,255))
plt.grid()
plt.xlabel('Position')
plt.ylabel('Grey Value')
plt.show()
```





## GUI + OpenCV&#x20;



```python
from PyQt5.QtCore import *
from PyQt5.QtGui import *
from PyQt5.QtWidgets import * 
from PyQt5 import uic 
import sys, cv2 
from datetime import *
from math import pi ,sqrt 
import numpy as np 

#-----
'''

todo 
-[x] slider bar value connect to thres value 
- remove the redundant parts and reorganize 
-[x] connect to webcam 
- [ ] adding the webcam configuration setup UI 
- [ ] Export the record data 


'''
#----
 
class MainWindow(QMainWindow):
	def __init__(self):
		super(MainWindow,self).__init__()
		uic.loadUi('Pool.ui',self)
		self.pwd = r'D:\personal\demo_proj\ChangProj\opencv_test' 
		self.CloseBtn.clicked.connect(self.CancelFeed)
		self.StartBtn.clicked.connect(self.OnClick)
		self.LoadBtn.clicked.connect(self.OpenFile)
		self.SaveBtn.clicked.connect(self.file_save)
		self.ThresSlider.sliderMoved.connect(self.slider_position)
		self.worker = Worker1()
		self.ThresVal.setText('90') # init threshold value 
	def OnClick(self):
		self.worker.start()	
		self.worker.ImageUpdate.connect(self.imageprocess)
	
	def ImageUpdateSlot(self,Image):
		self.OImgLabel.setPixmap(QPixmap.fromImage(Image))

	def ImageUpdateSlot2(self,Image):
		self.ImgLabel.setPixmap(QPixmap.fromImage(Image))
	
	def imageprocess(self,Img):
		self.area , cal_dia = [], []
		_, ThresImg = cv2.threshold(Img,int(self.ThresVal.text()),255,cv2.THRESH_BINARY)
		contour, _ = cv2.findContours(ThresImg,cv2.RETR_TREE,cv2.CHAIN_APPROX_SIMPLE)

		Convert2QtFormat = QImage(Img.data,Img.shape[1],Img.shape[0],QImage.Format_Indexed8)
		Thres2QtFormat = QImage(ThresImg.data,ThresImg.shape[1],ThresImg.shape[0],QImage.Format_Indexed8)
		# --- update the image ---# 
		self.ImageUpdateSlot(Convert2QtFormat)
		self.ImageUpdateSlot2(Thres2QtFormat)
		# --- calculate the thres image area and diameter ---# 
		for i in range(len(contour)):
			self.area.append(cv2.contourArea(contour[i]))
			M = cv2.moments(contour[i])
			if M['m00'] != 0 : 
				cent_x = int(M['m10']/M['m00'])
				cent_y = int(M['m01']/M['m00'])
				if M['m00'] > 100:
					cal_dia.append(round(sqrt(4*M['m00']/pi),3)/118)
					self.AreaNo.setText(str(int(self.area[-1])))

		

	def file_save(self):
		pass 
		# name = QFileDialog.getSaveFileName(self,'Save File(*.log)')
		'''
		cur_date = datetime.now().strftime('%Y-%m-%d') 
		cur_time = datetime.now().strftime('%H:%M:%S') 
		file_name = str(cur_date)+'.txt'
		with open( file_name,'w') as f:
			f.write(cur_time + '\n')
			f.write('---data start---')
			f.write(self.area)

		'''
	def slider_position(self,p):
		self.ThresVal.setText(str(p))

	def CancelFeed(self):
		self.worker.stop()

	def OpenFile(self):
		img, _ = QFileDialog.getOpenFileName(self,'select you file',self.pwd, 'all files (*); image files(*.jpg)')
		Qimage = QImage(img)
		Resize_QImage = QPixmap.scaled(self.ImgLabel.size(), Qt.KeepAspectRatio)
		self.ImageUpdateSlot(Resize_Qimage)

class Worker1(QThread):
	ImageUpdate = pyqtSignal(np.ndarray)
	def run(self):
		self.ThreadActive = True
		self.Cap = cv2.VideoCapture(0,cv2.CAP_DSHOW)
		while self.ThreadActive:
			ret, frame = self.Cap.read()
			if ret:
				GRAY =  cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
				self.ImageUpdate.emit(GRAY)

	def stop(self):
		self.ThreadActive = False 
		self.Cap.release()
		cv2.destroyAllWindows()
		self.quit()
	
if __name__ == '__main__':
	Ap = QApplication(sys.argv)
	Root =MainWindow()
	Root.show()
	sys.exit(Ap.exec_())
```





