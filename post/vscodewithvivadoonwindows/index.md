
<left><font face="STZhongsong">

# Windows下在VSCode中基于VivadoCLI进行开发

这是对于本人使用VivadodCLI(Command Line Interface)的一个简单总结.

# A.前提

- Windows11
- [Vivado2024.2](https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vivado-design-tools.html)或[这里](https://pan.baidu.com/s/1fZeZ0Mifc-EOIqoPnUapjw?pwd=ilad)
- VSCode
- VSCode插件[DigitalIDE](https://github.com/Digital-EDA/Digital-IDE.git).

# B.配置

讲在前面: ~~VivadoGUI疑似有些过于效率低下了,~~ 为了了解VivadoGUI中开发流程的每一步在干什么, 调用了哪些工具, 故转而探究其CLI下的开发方式.

## 1.添加Windows环境变量

将安装根目录下的`/Vivado/2024.2/bin`添加到Windows环境变量Path中, 保证xvlog, xelab, xsim等工具可以正常在命令行中调用, 可以在VSCode的命令行中使用`xvlog --version`测试环境变量是否正确配置, 一般需要先重启VSCode.

## 2.配置DigitalIDE

一般来说该插件会正确识别环境变量中的Vivado, 需要关注的点是: 将Verilog, SystemVerilog, Vhdl的linter都选用vivado, 这样可以在VSCode内快捷检测语法错误.

## 3.基本工具与语法

**Step1**
在简单开发中, 在VSCode中打开工程目录, 创建`src`, `sim`这两个文件夹, 在`src`中编写模块文件, 此外还要令写一份testbench, 用于后续测试.

**Step2**
`xvlog [list_of_src_files(include testbench)]`(根据SV,V,Vhdl分别使用`xvlog -sv [list_of_src_files]`, `xvlog [list_of_src_files]`或`xvhdl [list_of_src_files]`)进行编译(compilation).

**Step3**
`xelab -debug typical -top [testbench_src_file] -snapshot [name_of_snap_shot]`生成分析(elaboration).

**Step4**
在`sim`文件夹中创建`xsim_cfg.tcl`并写入
```tcl
log_wave -recursive *
run all
exit
```
然后, `xsim [name_of_snap_shot] -R`运行模拟(simulation), `xsim [name_of_snap_shot] --tclbatch xsim_cfg.tcl`生成波形(waveform database), 运行`xsim --gui [name_of_snap_shot].wdb`打开GUI查看波形.
`

# C.总结

以上是非常简略的CLI下工作流程, 更多关于tcl, Makfile等脚本的编写以及涉及真实器件的使用等内容有待补充.

</font></left>

Ref: [Long Live CLI](https://www.itsembedded.com/dhd/vivado_sim_1/)