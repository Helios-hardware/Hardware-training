# 使用clion搭配stm32

在这个框架下开发环境需要：

* Clion ：IDE
* CubeMX ：代码生成器
* arm-gcc ：工具链
* openocd ：固件烧录软件

## 1 首先是官方手册

官方手册的地址 [STM32CubeMX projects | CLion Documentation (jetbrains.com)](https://www.jetbrains.com/help/clion/embedded-development.html)

## 2 环境准备

### 2.1 下载安装Clion

进入官网下载Clion

[Download CLion: A Smart Cross-Platform IDE for C and C++ (jetbrains.com)](https://www.jetbrains.com/clion/download/#section=windows)

选择安装路径的时候，安装路径不要有空格和中文

安装选项给他全勾上

### 2.2 下载arm-gcc

* 进入官网下载windows版的arm交叉编译器[Arm GNU Toolchain Downloads – Arm Developer](https://developer.arm.com/downloads/-/arm-gnu-toolchain-downloads)

<img src="assets/image-20240912154558436.png" alt="image-20240912154558436" style="zoom:67%;float:left" />

* 下载完后，解压缩，我是把解压缩完的文件放在D盘，看个人喜好了

* 然后进入解压的bin文件夹，把这个路径添加到系统环境变量PATH中

* 然后在命令行里面确认添加成功没有，打开cmd或者powershell都行

```
arm-none-eabi-gcc
```

![image-20240912155242792](assets/image-20240912155242792.png)

### 2.3 下载openocd

* 先进入第三方网站下载openocd[Download OpenOCD for Windows (gnutoolchains.com)](https://gnutoolchains.com/arm-eabi/openocd/)

<img src="assets/image-20240912155403874.png" alt="image-20240912155403874" style="zoom:67%;float:left" />

* 同样解压缩完，添加bin路径到PATH里面

<img src="assets/image-20240912155518742.png" alt="image-20240912155518742" style="zoom:100%;float:left" />

### 2.4 下载CubeMX

进入官网下载

[STM32CubeMX - STM32Cube初始化代码生成器 - 意法半导体STMicroelectronics](https://www.st.com.cn/zh/development-tools/stm32cubemx.html)

### 2.5 安装驱动

#### stlink驱动

目前个人用的还是stlink，后面应该会更换成jlink

#### jlink驱动

## 3 开始配置Clion

打开Clion，破解的话，打开clion.zip那个文件夹，解压缩完看文件夹里面的说明

### 3.1 路径设置

* 打开设置

<img src="assets/image-20240912164740340.png" alt="image-20240912164740340" style="zoom:67%;float:left" />

* 然后先检查工具链

<img src="assets/image-20240912164944971.png" alt="image-20240912164944971" style="zoom:67%;float:left" />

* 然后再在嵌入式开发里面，检查openocd和cubemx的位置，换成你自己下载这两个东西的地址

<img src="assets/image-20240912165220805.png" alt="image-20240912165220805" style="zoom:67%;float:left" />

### 3.2 创建新的cubemx工程

* 创建路径根据个人爱好习惯

<img src="assets/image-20240912170301879.png" alt="image-20240912170301879" style="zoom:67%;float:left" />

* 点击旁边的.ioc文件，再通过STM32CubeMX打开

<img src="assets/image-20240912170414326.png" alt="image-20240912170414326" style="zoom:67%;float:left" />

* 点击左上角的STM32F030F4Px，更换自己需要的芯片

![image-20240912170517916](assets/image-20240912170517916.png)

其余的就按照正常的cubemx去配置就好了

需要注意的只有这里，跟别的是不一样的

![image-20240912171455023](assets/image-20240912171455023.png)

### 3.3 烧录文件配置

* 在工程里面加入stlink.cfg文件

<img src="assets/image-20240912165438625.png" alt="image-20240912165438625" style="zoom:67%;float:left" />

* 然后在里面加入配置代码：

很明显这个代码是给stlink、f4的芯片用的

```
# choose st-link/j-link/dap-link etc.
# adapter driver cmsis-dap
# transport select swd
    source [find interface/stlink.cfg]
    transport select hla_swd
    source [find target/stm32f4x.cfg]
# download speed = 10MHz
    adapter speed 10000000
```

<img src="assets/image-20240912165557703.png" alt="image-20240912165557703" style="zoom:67%;float:left" />

如果后续要更改为daplink或者jlink，又或者要换芯片，就要对这个代码进行修改

解释下这个代码

```
#设置使用的调试烧录器为ST-Linkv
source [find interface/stlink.cfg]

#设置使用的调试烧录器为J-link
source [find interface/jlink.cfg]

#设置使用的调试烧录器为DAP-Link
source [find interface/cmsis-dap.cfg]

# 调试接口选择swd，此类型是ST-Link专用的
transport select hla_swd

# 调试接口选择swd，此类型是J-link，DAP-Link通用
transport select swd

#设置目标芯片，选择stm32f1x，如果是f4系列的换成f4就行了其他系列以此类推
source [find target/stm32f1x.cfg]
```

* 然后配置烧录

点击编辑配置

![image-20240912170002032](assets/image-20240912170002032.png)

然后在i面板配置文件里面，把路径改成自己的stlink.cfg文件所在路径

<img src="assets/image-20240912170047500.png" alt="image-20240912170047500" style="zoom:67%;float:left" />

### 3.4 构建工程，烧录

<img src="assets/image-20240912171530685.png" alt="image-20240912171530685" style="zoom:67%;float:left" />

左上角那个小锤子是构建，然后右边那个三角形是烧录

![image-20240912171634839](assets/image-20240912171634839.png)

只要出现旁边那个已下载固件，就算成功，左边的虽然是红色字体，但不是报错

## 4 使用clion管理git

### 4.1 在clion中打开整个仓库

* 直接打开那个仓库的整个文件夹

![image-20240923001100241](assets/image-20240923001100241.png)

* 然后可以看到那个仓库下面的所有文件，左下角有个Git分支类似的图标

<img src="assets/image-20240923001136853.png" alt="image-20240923001136853" style="zoom:80%;float:left" />

* 点击那个图标可以看到，整个仓库的git分支图

![image-20240923001431563](assets/image-20240923001431563.png)

### 4.2 在clion中给仓库添加.gitignore文件

* 如果忘了.gitignore文件的，回到“简单的github使用与git操作说明”那节

* 先在插件这边下载.ignore
  1. 文件——>设置

<img src="assets/image-20240923001912452.png" alt="image-20240923001912452" style="zoom:67%;float:left" />

	2. 点击插件，然后下载.ignore

<img src="assets/image-20240923002034420.png" alt="image-20240923002034420" style="zoom:80%;float:left" />

* 下载完.ignore，叉掉设置，回到左边的项目，新建文件（右键点击——>.ignore File ——>.gitignore File(Git)）

![image-20240923002149956](assets/image-20240923002149956.png)

* 点击Example user template，再点击Generate

<img src="assets/image-20240923002254115.png" alt="image-20240923002254115" style="zoom:67%;float:left" />

* 能看见生成了.gitignore文件，里面的代码如上图，对这里面的代码进行修改，修改成下面这样

```
### Example user template template
### Example user template

# IntelliJ project files

# 整个.idea文件夹都不会上传
.idea

# 后缀为.gitignore文件不会上传，后缀为.iml文件不会上传
*.gitignore
*.iml
out
gen
```

![image-20240923002435982](assets/image-20240923002435982.png)

这样在git管理的时候，里面的文件/文件夹就不会上传了

### 4.3 使用clion管理git仓库

#### 4.3.1 提交

可以看到现在是在Hardware-training仓库里面，并且是feature分支

<img src="assets/image-20240928031601278.png" alt="image-20240928031601278" style="zoom:67%;float:left" />

#### 4.3.2 切换分支

点击签出，就可以切换分支了

<img src="assets/image-20240928031635319.png" alt="image-20240928031635319" style="zoom:67%;float:left" />

#### 4.3.3 如何从dev分支合并到main分支（在有分支保护的情况下）

当你的dev分支有新的commit并且推送到远程仓库的时候，就可以在github上看到Compare & pull request，点击它，发起PR

<img src="assets/image-20240928031844044.png" alt="image-20240928031844044" style="zoom:67%;float:left" />

在写PR的时候，注意标题和描述都要清晰，避免被打回

<img src="assets/image-20240928032015308.png" alt="image-20240928032015308" style="zoom:67%;float:left" />

当你的PR被审核人通过的时候，可以看到下面界面

<img src="assets/image-20240928032056985.png" alt="image-20240928032056985" style="zoom:67%;float:left" />

当这个时候你再打开你的远程仓库，你会发现你的合并并没有同步到本地仓库，所以，点击左侧的提取所有远程

![image-20240928032202211](assets/image-20240928032202211.png)

然后你可以看到远程仓库的main分支被拉取了下来

![image-20240928032245692](assets/image-20240928032245692.png)

接着点击左侧的本地main分支

<img src="assets/image-20240928032309350.png" alt="image-20240928032309350" style="zoom:67%;float:left" />

然后再点击更新所选

<img src="assets/image-20240928032336008.png" alt="image-20240928032336008" style="zoom:67%;float:left" />

就可以看到本地仓库的main分支和远程仓库的同步了
