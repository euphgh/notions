# riscv-privileged reading notes

## mstatus CSR

### MIE, SIE(WARL)

表示该特权级别的中断使能，低特权的中断使能恒关闭，高特权的中断使能恒开启

### SPIE, MPIE, SPP, MPP(WARL)

xPIE记录该特权级之前的中断使能，xPP记录自陷入该特权级的特权。**如果未实现特权模式x，则xPP必须仅读取0**。

当从特权y自陷到特权x时，xPIE设置为xIE的值。xIE设置为0，xPP设置为y。
在执行xRET指令时，xIE设置为xPIE，特权模式更改为xPP的值，xPIE设置为1，xPP设置为最不特权的支持模式（如果实现U模式，否则M）。
**如果xPP̸=M，xRET也设置MPRV=0。**

### SXL, UXL(WARL)

指定该特权级的字长，可以实现与MXLEN相同。在RV64中，恒为2

### MPRV, MXR, SUM(WARL)

MPRV为1时data的地址翻译和保护按照MPP位标识的特权级做，否则正常。**如果未实现U特权，仅读取0**。

MXR为1时data可以从exec的页加载，**如果未实现页表翻译，仅读取0**。

SUM为1时允许在S级别进行U页面的data访存，如果MPRV为1且MPP为S特权级，该字段也有效。**如果未实现S特权，仅读取0**。

### MBE,SBE,UBE(WARL)

指定非取指的数据访存的大小端，可以仅仅实现均为MBE，小端为0

### TVM, TM, TSR

TVM = 1时需要将S特权下的写satp或者执行SFENCE.VMA,SINVAL.VMA报非法指令异常，**如果未实现S特权，仅读取0**。

TM = 1时需要将非M特权下的TW指令报非法指令异常**如果未实现其他特权，仅读取0**。

TSR = 1时需要将S特权下的SRET指令报非法指令异常**如果未实现特权S，仅读取0**。

### FS, VS, XS(WARL), SD(READ_ONLY)

FS,VS,XS分别代表浮点，向量和用户扩展的寄存器状态，用于进行特权代码的上下文保存，
SD用于表示存在一个需要保存的上下文。
`SD = FS==Dirty || VS==Dirty || XS==Dirty`
