# 基于ROS架构的智能小车 （三）底盘的选取

## 1. 底盘选择

底盘的机械部分可以选择两轮驱动差速控制的，也有三个全向轮的，或者四驱的麦克纳姆轮等(Tinker就是四驱的麦克纳姆轮)，每一种底盘都有自己的底盘坐标系和轮速之间的对应的转换关系和运动学模型。需要了解清楚。

### 两轮差速驱动运动学模型

-  [https://blog.csdn.net/qq_16149777/article/details/73224070](https://blog.csdn.net/qq_16149777/article/details/73224070)

- [https://robotics.stackexchange.com/questions/106/what-is-a-suitable-model-for-two-wheeled-robots/134#134](https://robotics.stackexchange.com/questions/106/what-is-a-suitable-model-for-two-wheeled-robots/134#134)

### 全向轮驱动运动学模型

- [https://blog.csdn.net/jyaxp/article/details/55050393](https://blog.csdn.net/jyaxp/article/details/55050393)


### 四驱麦克纳姆轮运动学模型

- [https://www.bilibili.com/video/av17229132/](https://www.bilibili.com/video/av17229132/)

## 2.封装的运动学模型库参考

可以参考我封装的全向轮和四驱麦克纳姆轮的封装，

- 全向轮

> [https://os.mbed.com/users/himarsmty/code/omni/](https://os.mbed.com/users/himarsmty/code/omni/)

其中使用到的QEI的库是编码器的库，可以先不用管，直接看Motor_3和omni就好。

- 四驱麦克纳姆轮

> [https://os.mbed.com/users/himarsmty/code/mecanum_4/](https://os.mbed.com/users/himarsmty/code/mecanum_4/)

-------------------------

**我们这里以最简单的两轮差分模型来进行学习，在结合ros的时候可以降低理解的难度，但要先理解了两轮驱动的运动学模型。**