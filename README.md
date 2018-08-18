# 【微信好友全家福】python拼接微信好友头像

## 目录
* [前言](#前言)
* [环境准备](#环境准备)
* [代码使用说明](#代码使用说明)
* [代码详解](#代码详解)
  * [导入库](#导入库)
  * [下载所有好友的头像图片](#下载所有好友的头像图片)
  * [计算拼接的排列方法](#计算拼接的排列方法)
  * [实现拼接](#实现拼接)
  * [通过文件传输助手发送到自己微信中](#通过文件传输助手发送到自己微信中)
* [全部的代码](#全部的代码)

## 前言
最近看到网上一些关于爬取微信好友头像的一些程序，今天就特地就去实验了一番，感觉还是挺好玩的。先放上效果图(因隐私问题，已做模糊处理)：
![](https://github.com/lubin007/wechat_familyphoto/blob/master/img/all.jpg)
## 环境准备
本程序的环境是：MacPro；python3.6；以及itchat包、PIL包、math包； （注：安装包的具体安装方法可以百度，此处不赘述）
## 代码使用说明
下载 `familyphot.py` 文件，使用编辑器打开该文件，需要**修改其中的某些代码**，之后运行全部代码，会弹出一个二维码，扫码登录即可。<br>
## 代码详解
### 导入库
```python
import itchat
import math
import os
import PIL.Image as Image
```
### 下载所有好友的头像图片
运行itchat.auto_login(hotReload=True)后会弹出微信扫码界面让你授权微信登录，以便接下来的好友数据获取；给auto_login方法传入值为真的hotReload，其目的是即使程序关闭，一定时间内重新开启也可以不用重新扫码。
```python
itchat.auto_login(hotReload=True)
friends = itchat.get_friends(update=True)
#下载所有好友的头像图片
num = 0
for i in friends:
    img = itchat.get_head_img(i["UserName"])
    with open('/Users/lubin/PycharmProjects/wechat_python/headImg/' + str(num) + ".jpg",'wb') as f:  #修改为用于存放图片的文件夹位置
        f.write(img)
        f.close()
        num += 1
```
### 计算拼接的排列方法
因为需要知道最终拼接图片的行列数，这里采用的方法是：先设定最终图片的像素大小（这里用的是1180\*900），对总面积开根号后再除以图片总数求的每一张头像图片的大小，最后用行的像素值除以它求的每一行放置的个数。
```python
#获取文件夹内的文件个数
length = len(os.listdir('/Users/lubin/PycharmProjects/wechat_python/headImg')) #修改路径为存放图片的文件夹位置
#根据总面积求每一个的大小
each_size = int(math.sqrt(float(1180*900)/length))
#每一行可以放多少个
lines = int(1180/each_size)
```
这只是我的排列方法，可以根据需要自行设定，此方法有个弊端是，无法铺满整个区域。<br>
后来看到另一种处理方法，觉得不错，也放上来供大家参考：[puke3615_GitHub源码](https://github.com/puke3615/wechat_avatar_python)，或者点击[puke3615_blog](https://puke3615.github.io/2018/07/31/Python-Wechat-Avatar/)也可以查看。

### 实现拼接
拼接图片调用的是图片库 `PIL` 。
```python
#生成白色背景新图片
image = Image.new('RGB', (1180, 900),'white')  #注意此处像素值要与上述的一致
x = 0
y = 0
for i in range(0,length):
    try:
        img = Image.open('/Users/lubin/PycharmProjects/wechat_python/headImg/' + str(i) + ".jpg") #修改路径为存放图片的文件夹位置
    except IOError:
        print(i)
        print("Error")
    else:
        img = img.resize((each_size, each_size), Image.ANTIALIAS) #resize image with high-quality
        image.paste(img, (x * each_size, y * each_size))
        x += 1
        if x == lines:
            x = 0
            y += 1
image.save('/Users/lubin/PycharmProjects/wechat_python/headImg/' + "all.jpg") #存放最终照片的文件夹位置
```
### 通过文件传输助手发送到自己微信中
这个方法比较炫酷且方便，不用再从电脑导入到手机了。
```python
#通过文件传输助手发送到自己微信中
itchat.send_image('/Users/lubin/PycharmProjects/wechat_python/headImg/' + "all.jpg",'filehelper') #修改路径为存放最终照片的文件夹位置
image.show()
```
OK，这样就大功告成了。

## 全部的代码
下面为全部代码及注解。
```python
import itchat
import math
import os
import PIL.Image as Image
 
#给auto_login方法传入值为真的hotReload.即使程序关闭，一定时间内重新开启也可以不用重新扫码
itchat.auto_login(hotReload=True)
friends = itchat.get_friends(update=True)
#下载所有好友的头像图片
num = 0
for i in friends:
    img = itchat.get_head_img(i["UserName"])
    with open('/Users/lubin/PycharmProjects/wechat_python/headImg/' + str(num) + ".jpg",'wb') as f:
        f.write(img)
        f.close()
        num += 1
#获取文件夹内的文件个数
length = len(os.listdir('/Users/lubin/PycharmProjects/wechat_python/headImg'))
#根据总面积求每一个的大小
each_size = int(math.sqrt(float(1180*900)/length))
#每一行可以放多少个
lines = int(1180/each_size)
#生成白色背景新图片
image = Image.new('RGB', (1180, 900),'white')
x = 0
y = 0
for i in range(0,length):
    try:
        img = Image.open('/Users/lubin/PycharmProjects/wechat_python/headImg/' + str(i) + ".jpg")
    except IOError:
        print(i)
        print("Error")
    else:
        img = img.resize((each_size, each_size), Image.ANTIALIAS) #resize image with high-quality
        image.paste(img, (x * each_size, y * each_size))
        x += 1
        if x == lines:
            x = 0
            y += 1
image.save('/Users/lubin/PycharmProjects/wechat_python/headImg/' + "all.jpg")

#通过文件传输助手发送到自己微信中
itchat.send_image('/Users/lubin/PycharmProjects/wechat_python/headImg/' + "all.jpg",'filehelper')
image.show()
```
