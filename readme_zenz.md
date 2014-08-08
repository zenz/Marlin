# 使用说明

本固件是根据RichCattell的固件作了非常小的更改而成。主要是以下几点：

1、步进参数要修改对应的配置值，然后在固件编译时自动生成。

2、更正了一个可能是笔误的代码错误。

3、把最大检测尝试次数改成常量，可以在编译前设定。

下面是Configuration.h中需要修改的配置参数的说明：

`#define DEFAULT_DELTA_DIAGONAL_ROD 213.5 // mm`  
吊臂两端轴孔中心距离

`#define DELTA_SMOOTH_ROD_OFFSET 137.5 // mm`  
挤出嘴到立柱的距离

`#define DELTA_EFFECTOR_OFFSET 19.0 // mm`  
挤出嘴到吊块与吊臂连接处螺丝中轴的垂直距离

`#define DELTA_CARRIAGE_OFFSET 11.0 // mm`  
滑块上吊臂螺丝中轴到立柱的距离

`#define AUTOCALIBRATION_PRECISION 0.03 // mm`  
自动检测调平的控制精度

`#define BED_DIAMETER 160`  
打印床的可测试面积直径

`#define Z_PROBE_OFFSET {1, 13.58, -7.2, 0}`
探针放下之后与挤出嘴之间的偏移量，顺序是 y,x,z,e（E永远为0）

`#define Z_PROBE_DEPLOY_START_LOCATION {25, 90, 100, 0}`  
`#define Z_PROBE_DEPLOY_END_LOCATION {0, 90, 100, 0}`  
上一句是把探针放下之前的准备位置  
下一句是把探针放下之后的位置

`#define Z_PROBE_RETRACT_START_LOCATION {-60, 68.9, 20, 0}`  
`#define Z_PROBE_RETRACT_END_LOCATION {-60, 68.9, 0.6, 0}`  
上一句是把探针收起之前的准备位置
下一句是把探针收起之后的位置

`#define XYZ_FULL_STEPS_PER_ROTATION 200`  
步进电机转360度需要的步数

`#define XYZ_MICROSTEPS 16`  
A4988设置的驱动精度

`#define XYZ_BELT_PITCH 2`  
传送带的齿距

`#define XYZ_PULLEY_TEETH 16`  
传动齿轮的齿数

其他参数，例如温感探头类型等等，请根据自己的机器进行设置。

调平测试步骤如下（已经编译上传此固件）：

1、执行M502，把固件中各设置参数读取。  
2、执行M500，把这些参数写入到EEPROM。  
3、执行G30 A，开始自动检测调平。  
4、调平结束收针之后，执行M500把当前调平测试的结果保存到EEPROM，以后会自动调用。

调平检测是中间取1点，打印床圈内均匀选取6点，共7个点进行检测。每个点可能会敲击多次，只要有一次Z限位触发的结果与计算结果一致，就进入下一个检测点，但是如果检测不一致，就会一直敲击同一位置，直到21次检测，不管Z限位触发结果是否与计算结果一致，都跳到下一检测位置。

检测的顺序是先判断ROD是否正确，根据检测结果自动放大或缩小ROD。然后是检测XYZ的最大限位开关位置是否正确，然后修正偏移，然后是3根立柱的距离检测，最后是整机的各个参数匹配测试。

检测完成后，可以用M666 L来查看检测的结果参数。也可以用M666 H238.5等方式直接修改参数（会影响调平结果，例子是改打印的高度行程）。

嗯，就写这么多，大家自己多研究吧。