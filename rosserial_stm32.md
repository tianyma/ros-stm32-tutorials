#ros与stm32串口通讯操作方法

---------------------

- 利用mbed在stm32上烧写好串口通讯的程序

```c++
#include "mbed.h"

//------------------------------------
// Hyperterminal configuration
// 9600 bauds, 8-bit data, no parity
//------------------------------------

Serial pc(PA_2, PA_3);

int main()
{
    while(1)
    {
        pc.printf("rikirobot");
        wait(1);
        }
}


```

- 连接到ros上，注意可能需要修改设备的权限
```
sudo chmod 777 /dev/ttyUSB0
```
或者永久修改权限
```
sudo usermod -aG dialout ${当前用户名}
```
- 在已有的ros package的src目录下写一个message收发的节点，以boost_node.cpp为例

```
$ cd ~/catkin_ws/src/rikirobot_project/rikirobot/src
$ sudo vi boost_node.cpp
```
    
```c++
#include <iostream>
#include <string>
#include "std_msgs/String.h"
#include <boost/asio.hpp>
#include <boost/bind.hpp>
#include <math.h>
#include <ros/ros.h>


using namespace std;
using namespace boost::asio;

int main(int argc, char* argv[])
{
ros::init(argc,argv,"boost");
ros::NodeHandle n;
ros::Rate loop_rate(0.5);
io_service iosev;
ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter",1000);

//节点文件
serial_port sp(iosev, "/dev/ttyUSB0");
// 设置参数
sp.set_option(serial_port::baud_rate(9600));
sp.set_option(serial_port::flow_control(serial_port::flow_control::none));
sp.set_option(serial_port::parity(serial_port::parity::none));
sp.set_option(serial_port::stop_bits(serial_port::stop_bits::one));
sp.set_option(serial_port::character_size(8));
// 向串口读数据
char bufstart[13];
read(sp,buffer(bufstart));
printf("%s\n",bufstart);
while(ros::ok())
{

  	read(sp, buffer(buf));
    printf("buf= %s\n",buf);
    ros::spinOnce();
    loop_rate.sleep();
}
iosev.run();
return 0;
}

```



- 编写完成后，需要修改CMakelist

```
$ cd ~/catkin_ws/src/rikirobot_project/rikirobot/
$ sudo vi CMakelist.txt
```

在最后添加如下代码

```
add_executable(boost_node src/boost_node.cpp)
target_link_libraries(boost_node ${catkin_LIBRARIES})
```

保存

```
$ cd ~/catkin_ws
$ catkin_make
```

-编译如果失败可能没有获取设备最高权限
- 编译成功后

```
$ rosrun rikirobot boost_node.cpp
```


就可以看到串口传输过来的数据，可以读写，如果数据格式有问题，可以去源码修正
#### 参考网址

- https://blog.csdn.net/u010925447/article/details/58042702
- https://www.cnblogs.com/li-yao7758258/p/5794005.html
