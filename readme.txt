
��������
ubuntu Ubuntu 20.04.5
gcc 9.4.0
kernel:5.15.0-67
supported debian,fedora,centos,xubuntu

��ѹ������������6���ļ���
Makefile :makefile �ļ�
readme.txt ����˵���ļ�
sio_gpio.c ��gpio�����ļ�
sio_gpio.h : gpio����ͷ�ļ�
testgpio.c : gpio������֤����


�������������ʹ�ã�

��Ҫ��
����Ӧ�õ������漰��������Ҫ����Ϣ����ע����FAE��ȡ������õ�ֵ���Ը������ǲ����õ�Ӧ���������ϣ�������ܻ�����쳣��
#define IO_INDEX                   0x2E
#define DEFAULT_DUALIO             0
#define SIO_DID                    0x8728
u8 gpio_pin[8] = {64,66,63,67,23,16,37,14};
����������ϲ����󣬰������²��������������֤����


1.��������
make

2.���޴��󣬻���Ŀ¼������sio_gpio.ko�ļ���������������ҪrootȨ�ޣ�
insmod sio_gpio.ko

3.�鿴�����豸��Ϣ"xc_gpio"(����dmesg���ӡ������ϵͳ����˿ڵ�ַ��Ϣ)
ls /dev/

4.���ķ���Ȩ�ޣ��Ա���ͨ�û����Է����豸
chmod a+rw /dev/xc_gpio

5.������Գ���
gcc -o testgpio testgpio.c

���ڲ��Գ���Ĳ���˵��:
(1).-a �趨ȫ���˿ڣ�������������� -s ����
(2).-p �趨����Ķ˿ڣ�������������� -s ����
(3).-s ���ö˿ڵ�ֵ������ߵ͵�ƽ��ֵ����Ϊ0��1
(4).-g ��ѯ�˿�״̬����ע����ʱ�޷���������˿�״̬��
(5).-e �趨Led�˿ڣ�������������� -s ����

����
�趨ȫ��������˿�����͵�ƽ��
./testgpio -a -s 0

�趨ȫ��������˿�����ߵ�ƽ��
./testgpio -a -s 1

�趨��1������ߵ�ƽ��
./testgpio -p 1 -s 1

�趨��3������ߵ�ƽ��
./testgpio -p 3 -s 1

�趨��1������͵�ƽ��
./testgpio -p 1 -s 0

�趨��3������͵�ƽ��
./testgpio -p 3 -s 0

��ѯ���˿����������ƽ״̬(��ʱ���Գ����޷����ܸ�������ŵ������ƽ���������޸Ĳ��Գ���ʵ�����й���)��
./testgpio -g

//�趨PCH_GPIO12����ߵ�ƽ 
./testgpio -e 1 -s 1

//�趨PCH_GPIO24����ߵ�ƽ 
./testgpio -e 2 -s 1

//�趨PCH_GP_LED1����ߵ�ƽ 
./testgpio -e 4 -s 1

//�趨PCH_GP_LED2����ߵ�ƽ 
./testgpio -e 5 -s 1

��������˵��:
Ŀǰ�����������ʵ������ͨ��ioctl�Ĳ�����û��ʵ��write ,read������lseekҲδʵ�֣���Ҫʹ��write,read ,lseek�������ͻ������и���ʵ��ʹ�����ǡ�


sio_gpio.hͷ�ļ�����˵��
#define SET_GPIO_LOWLEVEL                  //��������˿�����͵�ƽ
#define SET_GPIO_HIGHLEVEL                 //��������˿�����ߵ�ƽ
#define GET_GPIO_LEVEL                         //��ȡ�˿����������ƽ�ߵ�״̬��1Ϊ�ߵ�ƽ��0Ϊ�͵�ƽ
#define SET_LED_LOWLEVEL                   //����LED�˿�����͵�ƽ
#define SET_LED_HIGHLEVEL                  //����LED�˿�����ߵ�ƽ

�����ʹ�÷�ʽ��ο�testgpio.c��set_port,set_ledport��get_port����
���Գ������ձ�׼�ļ���дʵ�֣�open->ioctl->close    ע�⣺����δʵ��read,write,lseek������ʹ����Щ��׼C�⺯�����ʣ�ioctl����
