# 基于ROS架构的智能小车 （四）编码器

通过motor_2库可以进行单个电机的转速和转向的控制，第一层封装就完成了，接下来我们可以同时进行两个电机的控制，开始第二层的设计了，这一层里，我们为了准确的控制小车的运动，要引入编码器。

关于编码器的理解可以参考：[http://yenstation.synology.me/wordpress/2016/12/22/mbed-encoder/](http://yenstation.synology.me/wordpress/2016/12/22/mbed-encoder/)

在mbed中使用编码器可以直接调用QEI库，**要养成看库文档的习惯来了解其用法**。

-  连接方式

我们用到的编码器和电机一起一共有六根连线，左右最外面的两根是电机的输入，往里是编码器的电源，注意区分正负极，编码盘上有标明，最中间是编码器的两个通道输出。同时使用两个通道，不仅可以测速更加准确，还可以通过正负判断电机转向。

- 程序编写

首先进行对象的声名，注意前两个参数是编码器两个通道的引脚声名。

`
QEI wheel1(PB_3, PB_4, NC, 11, QEI::X4_ENCODING);
`

![](https://i.imgur.com/D3tGWek.png)

其次主要是通过`get_v1 = (double)wheel1.getPulses();`来获取速度。
和 ` wheel1.reset()`来对编码器进行复位。

我们用到的库函数就大概就这三个。其中获取速度需要声名中断在中断的执行函数中来执行。声名中断需要用到Tiker类，使用方法如下。

```c++
Tiker tiker;
void callback()
{
	get_v1 = (double)wheel1.getPulses();
	...
}
int main()
{
	tiker.attach(&callback,0.2);//0.2s中断
	...
}
```

这就是编码器的使用方法。