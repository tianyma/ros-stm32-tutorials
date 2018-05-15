 # 基于ROS架构的智能小车 （七）遥控

小车的遥控涉及到stm32的中断处理，王若溪学长写了相关的代码直接用就可以了。

我们用的是futaba 6-channel 2.4GHz Computer Radio System遥控器

![](https://i.imgur.com/yuiyTCA.jpg)

![](https://i.imgur.com/SJbg0tp.png)

资料貌似只有英文的，所以还是看说明书吧。简单介绍一下，一共有六个通道可以进行控制，小车的运动只涉及两个通道（前后，左右）。

直接在main中的中断中可以获得tim3IC1Width, tim3IC2Width, tim3IC3Width, tim3IC4Width, tim1IC2Width, tim1IC3Width六个通道的值，通过和电机的转速进行换算就可以进行控制了。

例如
```c++

auto2wheel my_auto;
Ticker toggle_time_ticker;

void time_ticker(){//中断
//    Tim3_ser1.printf(" %d  %d\n",tim3IC3Width, tim3IC4Width);//使用tim3的通道3和通道4，值的范围是1100-1950.
    if((1200<tim3IC3Width<1800)||(1200<tim3IC4Width<1800))// controler without pid
    {
        my_auto.mv_x(0,0);//my_auto 是封装的二轮驱动小车的类的对象
    }
    if(tim3IC3Width<1200||tim3IC3Width>1800)
    {
        my_auto.spin(-tim3IC3Width+1450);   
    }
    if(tim3IC4Width<1200||tim3IC4Width>1800)
    {
        my_auto.mv_x(-tim3IC4Width+1450,-tim3IC4Width+1450);
    }
}
int main()
{
    //controler init
    HAL_Init();
    TIM1_3_Config();
    toggle_time_ticker.attach(&time_ticker, 0.2);
}

```

到此阶段，底层硬件的学习就告一段落了。接下来就开始激动人心的ROS学习了，我们需要充分理解节点这一概念进行小车控制的整体设计，开始学习吧。