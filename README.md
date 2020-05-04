## 使用coppliasim的notes

- [x] vrep仿真可限制最大速度已达到缓速运行的目的，vrep可使用与机械臂配套的伺服驱动软件实现实时仿真，real-time模式(SmartServoLIN,LBR_iiwa_7_R800,udp通讯)。
- [x] vrep仿真显示末端轨迹曲线，ABB IRB 4600-40-255模型中将graph挂到末端执行器上，可随动检测graph的位置，也可以直接定义末端连杆的位置，edit 3d curves，添加要显示轨迹的x-y-z的坐标。
- [x] vrep导出数据：export-->Selected graphs as CSV...]输出CSV格式的文件
- [x] 关节的模式passive mode便于编程实现对机械臂的控制
- [x] 雅各比矩阵不可逆时，可取广义逆(moore-penrose),pseudo inverse方法会寻找解向量中长度最小的一个（无穷多个解中2范数最小的解，即||Δθ<sub>0</sub>||<sub>2</sub>=min||Δθ||<sub>2</sub>，称为极小范数解）通常使用J<sup>+</sup>=J<sup>T</sup>(JJ<sup>T</sup>)<sup>-1</sup>(最小二乘法)
- [x] 当轴在某一个位置时，奇异型会使速度趋于$\infty$
- [x] vrep中服务端与客户端存在同步/异步,阻塞、非阻塞的通讯模式如：软件建立与外部接口（python，MATLAB）连接时，需采用阻塞进行判断；单纯发送命令时，可选用非阻塞式通讯，但需注意提前暂停通讯；信息流模式适用于一段时间内连续不断地采集数据，如获得轴的信息，且设有缓存区。
- [x] solidworks中分别导出stl文件<--->vrep中的自动分割网络
- [x] common中：默认图层1~8内的物体是可见的，图层9~16内的物体是隐藏的，使用右侧的layers可显示后8图层
- [x] 使用内置模型可以提高仿真效率与稳定性，如使用外界导入的模型stl并使能respondable会影响物理引擎计算性能；常用内置模型与凸壳（真实表皮）组合来建模。
- [x] 将隐藏的模型取消碰撞、可测量等特殊参数以提高运算速度。
- [x] Body is respondable选项下：从基座开始交替勾选Local respondable mask的前四位，后四位，以防止自身连杆之间的碰撞效应；local对有共同父节点起作用，global则对不相连物体起作用
- [x] 基座的body is dynamic 可以取消，作为小车需勾选Set to dynamic if gets parent
- [x] 模型根节点选择common下的Object is model base（出现包围体）以及Object/model can transfer or accept DNA（对副本的更改可应用到原模型）；对于每个连杆点选common中的Select base of model instead可避免偶然修改。
- [x] 力矩仿真模式下，body动态化，电机使能，使用pid位置控制，会默认采用最大力矩达到给定的最大目标速度。
- [x] 仿真时点选工具栏的动态显示按钮（弹球）可通过颜色查看各部分所处的运动状态。
- [x] 动态体的刚性地连接有两种方式：
	- [x] 工具栏中编辑下的Grouping/Merging --> Group selected shapes
	- [x] 在末端连杆中心添加力矩传感器（使用对齐操作），同时选择手抓和力矩传感器，点选工具栏下的Assemble / Disassemble即可。
- [x] 可在软件的models文件夹下新建usr文件，将组合的model导出为ttm格式，作为后续的基础模型使用。
- [x] non-thread 运行时序为叶子节点->根节点,运行后返回main script，类似于函数调用
- [x] thread 会在一个新的线程中运行,脚本属性对话框在左侧scripts，可设置thread的运行顺序，运行次数等。
- [x] main函数默认50ms运行一次，可以更改；精确同步时间需要使用toggle real-time mode，正常模式下仿真会以极快的速度运行，与真实时间并不同步。
- [x] 力矩控制可以设置一个极大的速度，设置并返回关节最大力矩或通过simSetJointForce函数设置关节最大力矩，这样关节就可以一直按照设置的最大力矩运转，从而达到调节关节力矩的目的。
	- [x] 位置模式：力矩为恒值，速度为PID的输出值
	- [x] 弹簧阻尼器模式：速度为恒值，力矩为PD的输出
- [x] graph中没有加速度的选项，但是注意下面有个Data transformation选项框可以对原始数据进行微分、积分、缩放、平移等变换。
- [x] 四元数有单独的一套定义方法,参考(https://www.cnblogs.com/21207-iHome/p/6952004.html)
- [x] 操纵仿真中的末端执行球可按住ctrl切换到z轴移动，左侧工具usr tool可以设置移动旋转步距
- [x] 为了实现两个模型之间的碰撞检测，需要先分别定义两个模型为collection形式，后添加碰撞目标检测。
- [x] xxx_tip和xxx_target为通常建立ik求解的dummy类，在执行轴末端添加一个球形工具，透明/Backface culling/以及特殊属性减少计算，且同在一个机器人项目下。
- [x] 做ik仿真时需要设置轴模式为inverse kinematics mode(hybrid)
- [x] ik求解分三种情况：
	- [x] 单链使用一个IK group(IK element)即可
	- [x] 多个互不干扰的单链，使用两个IK group两个element，求解顺序对结果无影响
	- [x] 若一个单链的target在另一个单链上，需要先对后者进行求解
	- [x] 两个运动链共享同一个关节，需要将两个IK element 放到同一个IK group中同时进行逆解计算
	- [x] 伪逆矩阵法收敛快，但是在奇异位置处存在问题；DLS法在奇异位置处计算结果更稳定，但是运算较慢。因此可以将两者结合起来使用，可以在IK Group中添加undamped的伪逆矩阵法和damped的DLS法。
- [x] VREP中直接设置物体姿态的函数有3个：
	1. simSetObjectOrientation：通过欧拉角设置姿态
	2. simSetObjectQuaternion：通过四元数设置姿态
	3. simSetObjectMatrix：通过旋转矩阵设置姿态（同时也可以设置位置）
- [x] 路径规划算法调用成熟软件，[Reflexxes运动库类型II或IV](http://www.reflexxes.com/)
- [x] C++客户端与VREP通信：
	1. 在客户端文件中包含exApi.h，exApi.c，extApiPlatform.h，extApiPlatform.c  
	2. 项目属性-->C/C++-->预处理器-->预处理器定义中定义：
	~~~
	NON_MATLAB_PARSING 
	MAX_EXT_API_CONNECTIONS=255
	~~~ 
	3. 在项目属性-->C/C++-->常规-->附加包含目录中包含：
	~~~
	C:\Program Files\V-REP3\VREP_PRO_EDU\programming\include
	C:\Program Files\V-REP3\VREP_PRO_EDU\programming\remoteApi
	~~~
## some practice
1. mygrasper.ttt具体代码参考在txt文件中保存，参考了软件自带的scenes中的ur5grasper例子，难点是需要获取抓取过程中的各轴的角度，解决方法是提前采用`ik逆解混合模式`，不需要添加脚本，运行条件下拖动末端执行器的`球体`到参考点处，记录此时各轴角度。
2. wait ..


 
