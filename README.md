# MIXLY
一个关于工训赛的介绍和资源同步
##介绍MIXLY已实现并通过验证的代码（该区域是详细介绍逻辑，最好理解一下，第二部分会在这个基础上提出需求，其实就是在这上面修改，*如果与需求相关我会打斜体*）
**首先我们关于视觉的代码位于主代码的上方该区域，以函数作用分成了两个部分，分别是功能函数与任务函数**
<img width="1006" height="498" alt="image" src="https://github.com/user-attachments/assets/639b41c6-2691-4b53-9a4a-3fe8402d0c27" />
**那我们先从任务函数讲起,然后再逐个解释任务函数里面的功能函数**
1.二维码识别函数
<img width="1013" height="1159" alt="image" src="https://github.com/user-attachments/assets/7475bb38-7221-4013-92de-8aa34a29cb60" />
第一个函数块调用了失能陀螺仪函数，如果存在失能那必然存在使能，但是这个是使用I2C通信才需要使用的，如果你使用串口通信就不需要太注意。
第二个进行一个是否开启了二维码扫描的条件判断，初始化了全局变量last_state=-1与state=0,如果没有开启就会开启，相当于给相机端**发送**开启信息；反之就不会进入。
第三个进行一个二维码返回数据的处理，二维码需返回**字符串**，打印的那些函数你可以学习一下，调试验证的时候可以用到，过于简单直接我们就不细讲。但是我们要关注的是，*以下函数可以对字符串进行处理*，直接学习就行，运用蜂鸣器可以知道车的状态，可以学习。
<img width="852" height="160" alt="image" src="https://github.com/user-attachments/assets/351e1d3d-c05b-46fb-966c-3239a32be87d" />
我们有一个**else否则**的条件判断，如果二维码没有返回数据就会进入该区域，该区域我们定义了一个QR_err_cnt的变量，这个是用来让车辆在没有识别到的时候自行移动的变量，0就不用动，1就动。
**函数使用办法**
<img width="406" height="385" alt="image" src="https://github.com/user-attachments/assets/e3632d26-ed78-4333-b9d5-cc522e441a9d" />
该函数返回一个ret变量，ret初值是0，如果扫描到二维码并获取到数据，ret会被置1，那么我们可以通过这个标志位判断是否进入状态机循环进行车子的挪动。

2.三色识别函数
<img width="616" height="1131" alt="image" src="https://github.com/user-attachments/assets/aa2b366a-e8b6-4d24-9b13-fef3042c5fd5" />
跟二维码识别函数的逻辑是十分相似的，都是获取数据。相同我就不再赘述，都是判断是否开启过，否就开启三色识别并等待获取识别结果。
*这里我们就要插入一个“获取色块信息”功能函数*
<img width="618" height="217" alt="image" src="https://github.com/user-attachments/assets/fd78d1e2-eeb4-4f88-b102-ca77ff5e3a04" />
这个函数运用了obj_xy_arr数组的两个变量存入获取到的xy值，obj_size_arr的两个数组来存入获取到的宽度和高度（这个我们应该用不着）
因此三色识别函数，在数据处理上**就是对视觉获取到的颜色进行赋值和xy数据的存储**
<img width="418" height="96" alt="image" src="https://github.com/user-attachments/assets/0993501c-b8d1-454e-9a0a-a4f27f4ea0c1" />
如上图，我们如果识别到了绿色，就设置标志位为2，后期状态机就可以简单判断这个标志位来测试有没有正确识别颜色了。
同理，最后我们返回一个ret变量，在完全没有看到信息返回的时候移动一段距离直到探测到。

3.对准中心框函数（这个大致了解就好）
<img width="1376" height="924" alt="image" src="https://github.com/user-attachments/assets/df5d5669-b8cf-4cc6-9ed8-3e43b80e19a1" />
首先进入该函数，我们通过getoffect函数块处理我们的目标值，就是我们设置一个屏幕点（因为我们有时是需要机械臂到达某个位置，这个位置刚好是屏幕的某个点，比如（50，50），这时我们*中心坐标*就要设置该值，如下图所示）
<img width="424" height="65" alt="image" src="https://github.com/user-attachments/assets/f9e21a96-5579-4043-afdb-e87f160bc1eb" />
接着就得到一个对目标的偏置，我们需要对这个偏置进行简单的处理，也就是pid中的p，那就用到了Get_dis_xy函数块。
<img width="605" height="236" alt="image" src="https://github.com/user-attachments/assets/41bf63d4-d747-4471-a875-1d4df0cab6b0" />
最后就来到我们的核心运动函数，就根据坐标直接运动就好。
同样该函数返回的是ret，如果没有运动成功可以进入函数块输出信息进行调试。

##提出具体需求，以任务作为分点，会存在许多重复的需求，重复就会简单提及并跳过，**以下图片均为示例**
1.二维码识别
-需要有开启二维码识别函数；
<img width="225" height="32" alt="image" src="https://github.com/user-attachments/assets/05b37273-7975-4e7b-8304-5faf109a5030" />
-需要有是否返回数据的函数，判断是否扫描到二维码的状态；
<img width="674" height="239" alt="image" src="https://github.com/user-attachments/assets/f5e40883-9487-46f2-bd85-18b77303723e" />
-需要有二维码返回字符串处理函数；
<img width="672" height="121" alt="image" src="https://github.com/user-attachments/assets/55662581-95e6-4d7e-a57b-0248043991d9" />

2.第一个任务色块识别
-需要返回xy坐标信息函数；
<img width="490" height="78" alt="image" src="https://github.com/user-attachments/assets/17e85131-9557-45ab-a64e-681f39fd2fde" />
-需要有开启色块识别函数；
-需要有是否返回数据函数；
<img width="421" height="101" alt="image" src="https://github.com/user-attachments/assets/51d8b9ab-86f8-418a-af01-f8618551404b" />
-需要有颜色检测函数；
<img width="381" height="93" alt="image" src="https://github.com/user-attachments/assets/3d2fa9c1-da81-4f9c-8571-0de849970b06" />

3.第二个任务标靶识别同上且逻辑相同

4.第三个任务PLA物块识别同上且逻辑相同


##写在最后
后期调试最耗费时间就是二三任务，因为给出来的xy值带给车辆的pid调节运动校准需要大量的经验调试，如果越快完成任务，我们的运动就可以越精准❤




