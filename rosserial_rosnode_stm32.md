# ros 和 stm32 之间的节点通讯

### stm32底层

- stm32底层代码采用mbed示例代码

```c++
#include "mbed.h"
#include <ros.h>
#include <std_msgs/String.h>

ros::NodeHandle nh;
DigitalOut myled(PC_13);

void messageCb(const std_msgs::String& toggle_msg){
    myled = !myled;   // blink the led
}
// 建立接收节点
ros::Subscriber<std_msgs::String> sub("toggle_led", &messageCb);

int main() {
    nh.initNode();
//订阅
    nh.subscribe(sub);

    while (1) {
        nh.spinOnce();
        wait_ms(1);
    }
}
```


因为采用节点通讯，所以在stm32端和ros端都要有相应的节点建立同样的topic，不能用ASIO串口通讯库

- ros

发布节点代码：
```c++
/*发送和接收节点的msg类型必须相同，这里都std_msg/String*/
#include "ros/ros.h"
#include <string>
#include <std_msgs/String.h>
#include <sstream>
int main(int argc,char **argv)
{
    ros::init(argc,argv,"talker");
    ros::NodeHandle n;
    ros::Publisher chatter_pub=n.advertise<std_msgs::String>("toggle_led",1000);//发布节点
    ros::Rate loop_rate(0.5);//0.5Hz发送消息

    while(ros::ok())
    {
        std_msgs::String msg;
        std::stringstream ss;
        ss<<"toogle_led";
        msg.data=ss.str();
        ROS_INFO("%s",msg.data.c_str());//打印发送消息
        chatter_pub.publish(msg);
        ros::spinOnce();
        loop_rate.sleep();
    }
    return 0;
}
```
不要忘记改节点下的CMakeList.txt文件

----------------
##advanced： 在stm32端解析收到的message
- 有几个必须要注意的坑
1.正确理解rostopic，在任意**一对**publisher和subscriber中，必须要有**相同的topic和message类型**才能进行二者的通讯，例如
```c++
  ros::Publisher chatter_pub=n.advertise<std_msgs::String>("talker",1000);
  ros::Subscriber<std_msgs::String> sub("talker", &messageCb);
```
在pub函数中，第一个参数是topic，第二个参数是数据长度（以字节记）
在sub函数中，第一个参数是topic，第二个参数是回调函数，回调函数就是，接收到数据后stm32将要执行的工作，例如
```c++
void messageCb(const std_msgs::String &toggle_msg){
    myled = !myled;   // blink the led
    pc.printf("message:%s\n",toggle_msg.data);
}
```
回调函数的参数是收到的message，如果要读取message的data，则使用msg.data读取，示例中是在串口中打印message。
**要特别注意在stm32和ros上写subscriber节点的区别，两者使用的库虽然都是kinetic，但函数还是有一定区别，所以要区别开**
####最后，看一下在ros端的publisher节点和stm32端的subscirber节点的完整代码，这里模拟发送限速度和角速度,然后在stm32解析出来并在串口打印
publisher：
```c++
#include <stdio.h>
#include "ros/ros.h"
#include <string>
#include <std_msgs/String.h>
#include <sstream>

int main(int argc,char **argv)
{
    ros::init(argc,argv,"talker");
    ros::NodeHandle n;
	int a,b,c,d,e,f,sum;
	a=b=c=d=e=f=2;
	sum=a+b+c+d+e+f;
  ros::Publisher chatter_pub=n.advertise<std_msgs::String>("talker",1000);
    ros::Rate loop_rate(0.5);

    while(ros::ok())
    {
        std_msgs::String msg;
        std::stringstream ss;
		ss<<"S W "<<a<<" "<<b<<" "<<c<<" "<<d<<" "<<e<<" "<<f<<" "<<sum<<" E";
        msg.data=ss.str();
        ROS_INFO("%s",msg.data.c_str());
        chatter_pub.publish(msg);
        ros::spinOnce();
        loop_rate.sleep();
    }
    return 0;
}
```
subscriber:
```c++
/*
 * rosserial Subscriber Example
 * Blinks an LED on callback
 */
#include "mbed.h"
#include <ros.h>
#include <std_msgs/String.h>

std_msgs::String str_msg;
ros::Publisher chatter("chatter", &str_msg);

DigitalOut myled(PC_13);
Serial pc(PA_9,PA_10,9600);

char a,b,c,d,e,f,sum;
void messageCb(const std_msgs::String &toggle_msg){
    myled = !myled;   // blink the led
    pc.printf("message:%s\n",toggle_msg.data);
    
}
std_msgs::String receivemessage;
ros::Subscriber<std_msgs::String> sub("talker", &messageCb);
int main() {
    ros::NodeHandle nh;
    nh.initNode();
    nh.subscribe(sub);
    while (1) {
        nh.spinOnce();
        wait_ms(1000);
    }
}
```	
注意这两种打印方式的区别
```c++
//串口打印，在stm32端读取使用message的内容
    pc.printf("message:%s\n",toggle_msg.data);
 //ROS打印   
    ROS_INFO("%s",msg.data.c_str());
```
----------------------------
另外，要注意rosserial_python默认的发送波特率是57600,stm32接收的波特率也是57600。如果要从串口测验读取message，则需要在新的引脚（PA_9,PA_10）读取，因为ros和stm32通讯占用了（PA_2,PA_3）,并把波特率设置成其他，这里是选择了9600。串口调试工具我在ubuntu用cutecom，windos不太清楚请自查。
参考网址：
http://www.360doc.com/content/16/0820/12/7821691_584529013.shtml
https://www.cnblogs.com/xuanxiaochen/p/6013886.html

