# 基于ROS架构的智能小车 （一）介绍

#### 		引：这个例程是我在准备robot-IDC时的学习笔记，结合之前学习ros的一些体会，和参加RoboCup时Tinker的设计思路做的一些自己的总结，希望对对ROS和机器人感兴趣的童鞋一些帮助。

**
What is ROS?
The Robot Operating System (ROS) is a set of software libraries and tools that help you build robot applications. From drivers to state-of-the-art algorithms, and with powerful developer tools, ROS has what you need for your next robotics project. And it's all open source.（引自ROS官网）
**

ROS是一个基于节点设计的开源机器人操作系统架构，关于ROS的介绍和Tutorial等等可以在官网[http://www.ros.org](http://www.ros.org "www.ros.org")上找到，推荐大家在学习tutorial的时候能够手边有个实体的小车进行练习可以加深理解。

-----------------------------------
如果要对ROS达到一个比较好的理解，除了在软件方面要懂**C/C++或者python编程**,了解ROS库函数,在硬件方面也要有比较充足的了解，比如知道底层驱动控制的方法，理解当前主流的单片机**（stm32/arduino）**的工作方式，理解底层的编程环境，并有一些调试底层bug的经验。这样当我们逐步架构到上位机进行软件开发的时候就可以游刃有余了，到那个时候，我们就可以把底层只当作一个节点，和其他诸如机械臂，雷达的部分一样都是平行的节点，他们统一通过主节点（树莓派上的Master）进行通讯。
> 但是，对于ros的学习，我认为如果对于底层的硬件部分的开发没有经验的话，直接上手去学ROS Tutorial可能有点云里雾里，学过之后也不会有多少印象，反而可能更加失去学习的动力，相反呢，知道了硬件开发，就自然会理解ROS的思想，从而提高我们的学习效率和理解。

####  正如梁老师讲的，机器人的开发最重要的是分层设计的思想，一步步进行学习和封装，就会使我们的机器人功能逐渐增多而不显得杂乱无章。在这个例程里面，我首先会讲解如何一步一步做一个纯stm32底层单片机控制的遥控小车，然后再讲解ros的架构以及如何用ros的节点机制来进行键盘控制和自动导航，最后可能会加上各种各样的传感器，甚至是机械臂，组成一个类似于Tinker的家庭服务机器人。
 
那就让我们开始底层硬件部分的学习吧。


