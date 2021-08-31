---
theme: smartblue
---
# 前言
近期遇到一个需求：给树形结构的筛选器增加连线样式，美化的同时增加可读性，如下图
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/418336c0703c4e43a2cf0b9c9ffbb082~tplv-k3u1fbpfcp-watermark.image)

# 方案与实现
看到连线这种功能，当时首先想到的思路就是通过canvas去画，但是想了下我们业务的代码早就完成了，使用canvas肯定需要对代码进行调整，工作量大而且有点多余；

第二种方案就是通过js去计算，页面渲染完成后获取标签的信息，计算出线条高度等信息，直接通过js在原有标签外面画线。这种方案相对于上面的代价小了很多，但是使用js的增删dom的开销并不小，而且我们是支持无限递归的属性结构，层级太深标签太多的时候很有可能造成页面阻塞；

在权衡了各种方案利弊后，采取了如下手段：
## 思路
1. 子元素自身画出左延的横线，父标签提供串起来的竖线，构成初步的结构

    ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cae28be0c6f843ae886e8264149ea3f1~tplv-k3u1fbpfcp-watermark.image)

2. 完成第一步后，可以发现存在的问题就是，第一与最后一个标签的对应竖线超出范围了，所以这里我们需要使用一个方法解决。
    
    这边采用的方案就是在对应元素上/下角分别在生成一个元素块，遮住超出的线条
    
    ![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0c6763c35a974713a41ba03e9d136761~tplv-k3u1fbpfcp-watermark.image)
    
    然后把遮罩块设置成对应的颜色即可实现隐藏了
    
    ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/02b16167dc454a1a9de5deded7d15aaa~tplv-k3u1fbpfcp-watermark.image)


# 代码实现
## 子元素自身的横线与父元素的竖线
先借助伪元素新增一个元素，然后使用绝对定位与css3的位移属性
```
.father {
    position: relative;
    &::after {
      content: "";
      display: inline-block;
      height: 0.5px;
      width: 28px;
      background-color: #dddddd;
      position: absolute;
      left: 0;
      top: 50%;
      transform: translate(-100%, -50%);
    }
}
```
##  遮罩块的实现
与上面类似，不同的是需要考虑更多情况，比如背景色、偏移量等等，这里我们可以借助less的  [Mixins](https://less.bootcss.com/features/#mixins-feature)，具体代码可以参考底部的源码

# 结语
这种方法的好处就是不需要重构代码，css写样式要比js计算更节省资源；

不过缺点可能就是精度不是非常准确，而且如果需要画出的线带圆角，可能就不太好做了

这里主要是提供一种思路

> [github地址](https://github.com/CHU295/tree-connection) 
