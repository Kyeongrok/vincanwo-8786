
开发环境
ubuntu Ubuntu 20.04.5
gcc 9.4.0
kernel:5.15.0-67
supported debian,fedora,centos,xubuntu

本压缩包包含以下6个文件：
Makefile :makefile 文件
readme.txt ：本说明文件
sio_gpio.c ：gpio驱动文件
sio_gpio.h : gpio驱动头文件
testgpio.c : gpio测试验证程序


驱动及测试软件使用：

重要：
你所应用的主板涉及到以下重要的信息，请注意向FAE索取相关配置的值，以更改它们并良好的应用在主板上，否则可能会出现异常：
#define IO_INDEX                   0x2E
#define DEFAULT_DUALIO             0
#define SIO_DID                    0x8728
u8 gpio_pin[8] = {64,66,63,67,23,16,37,14};
更改完成以上参数后，按照以下步骤编译程序进行验证功能


1.编译驱动
make

2.如无错误，会在目录下生成sio_gpio.ko文件，加载驱动（需要root权限）
insmod sio_gpio.ko

3.查看生成设备信息"xc_gpio"(或者dmesg会打印驱动向系统申请端口地址信息)
ls /dev/

4.更改访问权限，以便普通用户可以访问设备
chmod a+rw /dev/xc_gpio

5.编译测试程序
gcc -o testgpio testgpio.c

关于测试程序的参数说明:
(1).-a 设定全部端口，这个参数依赖于 -s 参数
(2).-p 设定具体的端口，这个参数依赖于 -s 参数
(3).-s 配置端口的值，输出高低电平，值必须为0与1
(4).-g 轮询端口状态，（注：此时无法更改输出端口状态）
(5).-e 设定Led端口，这个参数依赖于 -s 参数

比如
设定全部的输出端口输出低电平：
./testgpio -a -s 0

设定全部的输出端口输出高电平：
./testgpio -a -s 1

设定第1脚输出高电平：
./testgpio -p 1 -s 1

设定第3脚输出高电平：
./testgpio -p 3 -s 1

设定第1脚输出低电平：
./testgpio -p 1 -s 0

设定第3脚输出低电平：
./testgpio -p 3 -s 0

轮询各端口输入输出电平状态(此时测试程序无法接受更改输出脚的输出电平，请自行修改测试程序实现自有功能)：
./testgpio -g

//设定PCH_GPIO12输出高电平 
./testgpio -e 1 -s 1

//设定PCH_GPIO24输出高电平 
./testgpio -e 2 -s 1

//设定PCH_GP_LED1输出高电平 
./testgpio -e 4 -s 1

//设定PCH_GP_LED2输出高电平 
./testgpio -e 5 -s 1

驱动程序说明:
目前驱动程序仅仅实现了普通的ioctl的操作，没有实现write ,read操作，lseek也未实现，不要使用write,read ,lseek操作。客户可自行更改实现使用它们。


sio_gpio.h头文件定义说明
#define SET_GPIO_LOWLEVEL                  //设置输出端口输出低电平
#define SET_GPIO_HIGHLEVEL                 //设置输出端口输出高电平
#define GET_GPIO_LEVEL                         //获取端口输入输出电平高低状态，1为高电平，0为低电平
#define SET_LED_LOWLEVEL                   //设置LED端口输出低电平
#define SET_LED_HIGHLEVEL                  //设置LED端口输出高电平

具体的使用方式请参考testgpio.c的set_port,set_ledport与get_port样例
测试程序依照标准文件读写实现：open->ioctl->close    注意：驱动未实现read,write,lseek，请勿使用这些标准C库函数访问，ioctl除外
