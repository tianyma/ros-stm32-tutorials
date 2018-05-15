# 基于ROS架构的智能小车 （八）ros基础

接下来就正式进入到了ros的学习中，关于ros学习的资料网上有很多。官网的tutorial就是很好的资源，也有很多可以参考的博客和教科书。根据我的经验，我觉得先完成官网的beginner_tutorial系列教程是不错的选择，要充分理解ros的基本概念，完成tutorial后，要能够独立完成以下工作。

- 创建编译删除功能包。
- 会编写ros节点。
- 会修改CmakeList.txt和package.xml。
- 理解rosmsg，rostopic，rosservice这些基本概念，会编写相关节点进行实验。
- 理解roslaunch和最基本的用法。
- 理解单片机和主机之间通过ros节点的通讯。

其次要完成tf tutorial，这个在官网也有，这是理解navigation的第一步。这些工作都完成后，就可以开始navigation的学习，navigation的学习最主要的是要理解ros小车的分层控制思想，这里有两份很好的参考资料

- [ros_navigation_tutorial](http://www.cnblogs.com/aobosir/p/5928550.html)
- 《ros by example》教科书上载到了[github](https://github.com/himarsmty/ros-stm32-tutorials)