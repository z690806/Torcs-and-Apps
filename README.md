# Torcs-and-Apps
驾驶振动预警项目
1.场景：跟车行驶，前车平均速度40M（单向笔直车道） 
2.每次运行16分钟，前1分钟为车辆出发启动和被试熟悉时间，正式实验部分15分钟
3.正式实验开始后，前车刹车时，就向手机发送预警信息
4.正式实验的15分钟内，前车随机刹车次数15次，出现时间不固定

关键字段：
1、Time 时间
2、Distance 距离
3、Lead Brake （0，1）前车是否刹车
4、Brake Time 被试刹车的时间戳/ Lead Brake Time 前车刹车的时间戳
5、Brake Command （0，1）控制手机是否发送振动指令，和Lead brake关系如下

#include"some torcsfile"
int main ()
{
   //
   int Lead_Brake;
   // 是否发送刹车指令，1为刹车，2为不刹车
   int Brake_Command;
   // 检查安全距离
   if(Lead_Brake==0)
   {
       // 安全
       Brake_Command == 0;
   }
   else if(Lead_Brake==1 )
   {
       //不安全，预警
       Brake_Command == 1;
   }
}


iOS APP需求：
a.从pc通过wifi协议发送信息给手机；torcs会在一个IP端口（电脑的IP）每秒60次把时间给广播出来；
b.接受信息为：Time，Distance，BrakeCommand刹车信号（0和1表示）和时间戳； 手机接受刹车信号后，通过蓝牙发送通知给智能设备，让智能设备振动。
c.智能设备包括：小木智能戒指、项链，智能手表，谷歌glass（待选），罗技G29方向盘（待选）

当前问题
1.无关变量-振动强度：不同设备振动强度不一样，需要排除振动强度这个无关变量（主要是手腕部分，因为戒指和项链是一样的设备）。方案有两个，一是在两个试次中用小木项链分别用作手环和项链（本身可以作为手环）；二是定义好强度和振动频率，找到相同参数的手表设备。
2.手表如果通过通知的形式可能振动时间会很短暂
3.频率是60Hz还是50Hz，我们今天看了result里的文件，数据间隔时间是0.2秒一次，即50Hz。为了尽可能增加准确度，最高能达多少Hz？



二、开车发短信对行车安全影响项目
1.程序总目标：用户戴着智能手表的同时使用驾驶模拟器玩torcs，torcs和智能手表会以每秒60次的频率生成一系列数据，并将其中的时间戳广播出来，手机程序通过WIFI（或蓝牙）将智能手表及torcs中的时间数据同步至手机中并保存。
手机控制开启接收数据，在点击停止或放弃之前一直都在接收数据，将接收到的数据保存在手机中，每次操作保存的文件独立。

2.程序的用途（不用开发的）：手表的陀螺仪数据可以用于判断方向盘角度，torcs会每秒60次记录方向盘角度。目的是将同一时间的手表判断的方向盘角度和torcs实际的方向盘角度进行对比来判断是否驾驶分心。因此我们需要建立一个时间轴，将在手机上同时收到的来自智能手表和torcs的时间同步（同步依据手机的时间）。

3.目前已完成部分：①torcs会在一个IP端口（电脑的IP）每秒60次把时间给广播出来；②手机程序接收智能手表的数据；③torcs记录的数据文件在results文件夹中

4.剩余需要完成部分：将torcs中广播出的时间和手机的时间存储在手机中。只需要在原代码基础上增加这个功能，即在现有的数据（来自Android Wear）中加入一列或几列就可以了。
安卓得到数据是通过socket的，socket的server是torcs，client就是手机，这个IP地址就是电脑的IP。

三、搭建socket介绍：
https://github.com/codepath/android_guides/wiki/Sending-and-Receiving-Data-with-Sockets
https://www.cnblogs.com/wangcq/p/3520400.html
