# 基于ROS架构的智能小车 （五）PID控制

获取了编码器的速度后，就可以通过PID进行闭环控制了，从而让我们的小车可以以精确的速度进行移动了。这尤其在三轮全向轮的控制这种轮速对底盘运动方向影响很明显的情况有帮助。

### PID算法(比例-积分-微分)

可以参考以下博客：
- [https://www.cnblogs.com/cv-pr/p/4785195.html](https://www.cnblogs.com/cv-pr/p/4785195.html)
- [http://blog.gkong.com/liaochangchu_117560.ashx](http://blog.gkong.com/liaochangchu_117560.ashx)

算法代码可以参考我写的一个简单的pid，可以自己进行一些模拟输入加深理解。

```c++
#ifndef PID_MTY_H_
#define PID_MTY_H_
typedef struct PID_tag{
    double setPoint;//desired value
    double P;//proportion
    double I;//Integral
    double D;//Dervative
    double LastError;
    double PrevError;
    double SumError;
}PID;


double compute(PID *pid_contorler,double NextPoint)
{
    double dError,Error;
    Error=pid_contorler->setPoint-NextPoint;//设定值-采样值
    pid_contorler->SumError+=Error;//积分
    dError=pid_contorler->LastError-pid_contorler->PrevError;//微分，偏差相减
    pid_contorler->PrevError=pid_contorler->LastError;
    pid_contorler->LastError=Error;
    double speed=pid_contorler->P*Error+pid_contorler->I*pid_contorler->SumError+pid_contorler->D*dError;//delta speed
    return speed;//比例项+积分项+微分项
}
//double  sensor()
//{
//    return 100.0;
//}
#endif
//int main()
//{
//    PID my_pid;
//    double rout;
//    double rin;
//    my_pid.P=0.5;
//    my_pid.I=0,5;
//    my_pid.D=0.0;
//    my_pid.setPoint=(100.0);
//    while(1)
//    {
//        rin=sensor();
//        rout=compute(&my_pid,rin);
//        cout<<rout<<endl;
//    }
//}
```