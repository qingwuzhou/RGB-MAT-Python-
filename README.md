# RGB-MAT-Python-zhisensor
# python语言的图像转CV::mat，以zhisensor-RGB为例

##主要代码

```python
RawdataToRgb888CSharp(camera_obj1, rgb, rgbpixel, rgb_num);
        # img = cv2.imdecode(np.frombuffer(rgbpixel, np.uint8), cv2.IMREAD_COLOR) #bgr cv2.imdecode 从内存中的缓冲区读取图像   # np.frombuffer 将data以流的形式读入转化成ndarray对象
        ndarray = np.frombuffer(rgbpixel, np.uint8) #np.uint8
        print(ndarray.shape)
        print(type(ndarray))
        print(ndarray)
        rgb_image = ndarray.reshape(972,1296,3)
        img=cv2.cvtColor(rgb_image,cv2.COLOR_BGR2RGB)
```



## 全部代码

```python
import numpy
from DkamSDK import *
import cv2
import numpy as np
import math
import PIL
from PIL import Image
from io import BytesIO
from matplotlib import pyplot as plt

if __name__ == "__main__":
    camera_num = DiscoverCamera()
    print("camera_num=", camera_num)
    if camera_num > 0:
        camera_sort = CameraSort(0)
        print("camera_sort=", camera_sort)
    for i in range(camera_num):
        print("IP:", CameraIP(i))
        if CameraIP(i) == b'192.168.30.93':
            camera_ret = i
    camera_obj1 = CreateCamera(camera_ret)
    connect = CameraConnect(camera_obj1)
    print("connect=", connect)
    if connect == 0:  

        # get width and height
        width_register_addr_rgb = GetRegisterAddr(camera_obj1, b"Width") + 0x100
        height_register_addr_rgb = GetRegisterAddr(camera_obj1, b"Height") + 0x100
        width_rgb = new_intArray(0)
        height_rgb = new_intArray(0)
        ReadRegister(camera_obj1, width_register_addr_rgb, width_rgb)
        ReadRegister(camera_obj1, height_register_addr_rgb, height_rgb)
        width_RGB = intArray_getitem(width_rgb, 0)
        height_RGB = intArray_getitem(height_rgb, 0)
        print("Width_rgb=%d, height_rgb=%d" % (width_RGB, height_RGB))

        # SetRGBTriggerMode   
        tirggerMode = SetRGBTriggerMode(camera_obj1, 0)
        print("tirggerModeRGB=", tirggerMode)

        # StreamOn 
        stream_RGB = StreamOn(camera_obj1, 2)
        print("stream_RGB=", stream_RGB)

        acquistion = AcquisitionStart(camera_obj1)
        print("acquistion=", acquistion)

    cv2.namedWindow("ColorViewer",0)
    cv2.resizeWindow("ColorViewer", 1296,972)
    for i in range(500):

        rgb = PhotoInfoCSharp()   
        rgb_num = width_RGB * height_RGB * 3
        rgbpixel = bytes(rgb_num)

        FlushBuffer(camera_obj1,2)
        SetRGBTriggerCount
        triggerCountRGB = SetRGBTriggerCount(camera_obj1)
        print("triggerCountRGB:", triggerCountRGB)
        #
        captureRGB = CaptureCSharp(camera_obj1, 2, rgb, rgbpixel, rgb_num)
        #captureRGB = TimeoutCapture(camera_obj1, 2, rgb, 7000000);
        print("capture RGB=", captureRGB)

        RawdataToRgb888CSharp(camera_obj1, rgb, rgbpixel, rgb_num);
        # img = cv2.imdecode(np.frombuffer(rgbpixel, np.uint8), cv2.IMREAD_COLOR) #bgr cv2.imdecode 从内存中的缓冲区读取图像   # np.frombuffer 将data以流的形式读入转化成ndarray对象
        ndarray = np.frombuffer(rgbpixel, np.uint8) #np.uint8
        print(ndarray.shape)
        print(type(ndarray))
        print(ndarray)
        rgb_image = ndarray.reshape(972,1296,3)
        img=cv2.cvtColor(rgb_image,cv2.COLOR_BGR2RGB)
        # img = cv2.merge((rgb_image[:,:,2], rgb_image[:,:,1], rgb_image[:,:,0]))
        cv2.imshow("ColorViewer", img)  #图像窗口的名字，图片的像素值矩阵。

        # image = np.frombuffer(rgbpixel, np.uint8)
        # img = PIL.Image.fromarray(image.reshape((972,1296,3)))  #array转换成image
        # plt.imshow(img)


        key = cv2.waitKey( 1 )
        # 按 ESC 或 'q' 关闭窗口
        if key & 0xFF == ord( 'q' ) or key == 27:
            cv2.destroyAllWindows()
            break

        i = i+460
        rgb_name = b"ZhiCaputure/%05d.png" %i
        savergb = SaveToBMPCSharp(camera_obj1, rgb, rgbpixel, rgb_num, rgb_name)
        print("save rgb=", savergb)

    streamoff_rgb = StreamOff(camera_obj1, 2)
    print("streamoff_rgb=", streamoff_rgb)

    disconnect = CameraDisconnect(camera_obj1)
    print("disconnect=", disconnect)

    DestroyCamera(camera_obj1)

```

