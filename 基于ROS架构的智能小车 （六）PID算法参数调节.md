# 基于ROS架构的智能小车 （六）PID算法参数调节

PID虽然是闭环控制，但P,I,D三个参数调节不适当，依然会出问题，导致运动一抽一抽的，很搞笑，而且也不实用。

调节参数参考：

- [http://www.eadianqi.com/zc/guochengkongzhi/5319.html](http://www.eadianqi.com/zc/guochengkongzhi/5319.html)

有学长写的一个安卓app通过蓝牙动态调节PID参数，很有帮助，
在头文件bluetooth.h中

```c++
    typedef enum
{
    CheckS,CheckW,Checka,Checkb,Checkc,Checkd,Checke,Checkf,CheckSum
}STATE;

void btserialread(){
    char u1Rec; 
    static STATE State = CheckS;//初始状态待检查
    static char PositionTemp[7]={0,0,0,0,0,0,0};//装载数据值a(左右转) b(前后退) c(机械手高度) d(机械手旋转角度) Sum(求和校验)
    u1Rec = bt.getc(); 
    switch(State)
    {
        case CheckS:
            if(u1Rec==(char)'S')
                State=CheckW;//准备跳转至数据读取
            else
                State=CheckS;
            break;
        case CheckW:
            if(u1Rec==(char)'W')
            State=Checka;
            else if(u1Rec==(char)'S')
                State=CheckW;
            else 
                State=CheckS;
            break;
        case Checka:
            PositionTemp[0]=u1Rec;
            State=Checkb;
            break;
        case Checkb:
            PositionTemp[1]=u1Rec;
            State=Checkc;
            break;
        case Checkc:
            PositionTemp[2]=u1Rec;
            State=Checkd;
            break;
        case Checkd:
            PositionTemp[3]=u1Rec;
            State=Checke;
            break;
        case Checke:
            PositionTemp[4]=u1Rec;
            State=Checkf;
            break;
        case Checkf:
            PositionTemp[5]=u1Rec;
            State=CheckSum;
            break;
        case CheckSum:
            PositionTemp[6]=u1Rec;
            if(PositionTemp[6]==(char)(PositionTemp[0]+PositionTemp[1]+PositionTemp[2]+PositionTemp[3]+PositionTemp[4]+PositionTemp[5]))
            {
                pid1.P = PositionTemp[0];//your pid
                pid1.I = PositionTemp[1];
                pid1.D = PositionTemp[2];
                pid2.P = PositionTemp[3];
                pid2.I = PositionTemp[4];
                pid2.D = PositionTemp[5];
            }
            State=CheckS;
            break;
        default:
            State=CheckS;
            break;
    }
}

```
通过串口来调节


```c++
//typedef struct{
//
//}PID;
PID pid1,pid2;
//bluetooth serial
Serial bt(PA_2,PA_3);

void btserialread(){//蓝牙读取PID
    char u1Rec; 
    static STATE State = CheckS;//初始状态待检查
    static char PositionTemp[7]={0,0,0,0,0,0,0};//装载数据值a(左右转) b(前后退) c(机械手高度) d(机械手旋转角度) Sum(求和校验)
    u1Rec = bt.getc(); 
    switch(State)
    {
        case CheckS:
            if(u1Rec==(char)'S')
                State=CheckW;//准备跳转至数据读取
            else
                State=CheckS;
            break;
        case CheckW:
            if(u1Rec==(char)'W')
            State=Checka;
            else if(u1Rec==(char)'S')
                State=CheckW;
            else 
                State=CheckS;
            break;
        case Checka:
            PositionTemp[0]=u1Rec;
            State=Checkb;
            break;
        case Checkb:
            PositionTemp[1]=u1Rec;
            State=Checkc;
            break;
        case Checkc:
            PositionTemp[2]=u1Rec;
            State=Checkd;
            break;
        case Checkd:
            PositionTemp[3]=u1Rec;
            State=Checke;
            break;
        case Checke:
            PositionTemp[4]=u1Rec;
            State=Checkf;
            break;
        case Checkf:
            PositionTemp[5]=u1Rec;
            State=CheckSum;
            break;
        case CheckSum:
            PositionTemp[6]=u1Rec;
            if(PositionTemp[6]==(char)(PositionTemp[0]+PositionTemp[1]+PositionTemp[2]+PositionTemp[3]+PositionTemp[4]+PositionTemp[5]))
            {
                pid1.P = PositionTemp[0]/10.0;
                pid1.I = PositionTemp[1]/10.0;
                pid1.D = PositionTemp[2]/10.0;
                pid2.P = PositionTemp[3]/10.0;
                pid2.I = PositionTemp[4]/10.0;
                pid2.D = PositionTemp[5]/10.0;
            }
            State=CheckS;
            break;
        default:
            State=CheckS;
            break;
    }
}

int main()
{
    bt.attach(&btserialread); 
	...
}
```