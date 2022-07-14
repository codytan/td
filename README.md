# 命令行辅助工具

## td工具介绍
td命令行工具为方便针对标注平台数据上传、下载和处理的本地命令行工具，在较大数据量时，浏览器上传下载受限时可用命令行工具操作更高效。
支持Mac（Arm构架和Intel构架）、Linux、Windows系统。

## 下载地址 
下载成功后可以加入到本地的环境变量中，可在命令行终端中直接使用td命令（放入到环境变量中可将程序重命名为td，windows为td.exe），或者通过完整路径执行td命令。  
<br>
[Linux版本](https://github.com/codytan/td/releases/download/0.3.0/td_linux)  
[Mac-Arm版本](https://github.com/codytan/td/releases/download/0.3.0/td_mac_arm)  
[Mac-Intel版本](https://github.com/codytan/td/releases/download/0.3.0/td_mac_intel)  
[Windows版本](https://github.com/codytan/td/releases/download/0.3.0/td_win.exe)     

## 配置账户及数据集
AK参数可从账户的秘钥管理菜单中获取，目前项目经理角色支持开发者权限。 
```
td config -a 开发者账户中的AccessKey -d 数据集ID
```
配置成功后将保存在当前目录（命令执行目录，每个数据集单独建立一个目录）和当前用户Home下目录（存放秘钥） `.tdconfig`文件中，配置成功后不应该对其修改，如果确认修改对数据完整性没有影响时可重新执行配置命令，将会更新配置文件。   
如有多个数据集，在已经配置了AccessKey的情况下，也可以只输入`-d`参数单独设置数据集（注意需要在一个独立的目录，否则会覆盖原数据集的配置）。  
```
td config -d 数据集ID
```

## 初始化批次目录
针对每次上传和处理的批次，需要先生成一个批次号，批次号的作用用于区分不同上传和需要处理的数据批次，以批次为单位进行管理。批次内如果有重复的文件（md5相同），在同一个数据集内将会过滤排除掉。执行batch create命令后，会在当前目录创建一个以批次号命名的子目录，用于存放要推送到平台的数据文件。
```
td batch create 
```

## 上传数据
不同类型数据集目录的结构和要求请参考 [支持的数据类型](/dataset)，将符合格式要求的数据放入到创建的批次目录中，包含自定义的目录名称，如图像序列数据集为：  
```
--通过create命令生成的批次号目录
  --自定义的目录名称 //如第一批数据
    ——segment1 //若为序列数据集，则含此目录否则可不含，目录名称可自定义
      ——img // 源文件目录，可自定义
        ——00.jpg // 可自定义
```
然后执行push命令，td将会将批次目录进行zip压缩，然后传输到平台中，将会打印进度信息。
```
//batch-dir为create命令自动生成的目录，也等同于批次号
td push -d batch-dir
```
如需要上传已经压缩好的zip文件，可将zip（对应和自定义目录一致）放入批次目录内，使用--zip命令进行上传
```
td push -d batch-dir --zip 
```


## 下载数据
通过pull命令下载数据集数据，支持按批次下载和数据集全部下载，支持只下载标注结果（默认）和只下载源文件，或全都下载，如示例：
```
#下载一个批次标注结果JSON数据
td pull -b batch-dir

#下载整个数据集全部批次标注JSON数据
td pull -b all

#下载一个批次源文件数据
td pull -b batch-dir -t label

#下载整个数据集全部批次标注和源文件
td pull -b batch-dir -t all
```
pull命令辅助参数有`--try`，用于单个文件下载失败时的重试次数，默认为10，`--worker`，用于指定并发下载的协程数量，默认为2。  

## 其他
#### 检查上传目录结构是否符合要求。  
```
td check -d batch-dir
```

#### 切换到不同的环境
如有需要切换到不同的部署环境中，可通过 cofnig 命令的`--host`参数设置命令行的使用服务器。
```
td config -a 开发者账户中的AccessKey -d 数据集ID --host 指定的环境的服务器地址，如http://label-std.testin.cn
```

#### 打印详细日志
如遇到问题，可以在命令中加上`-v`参数，可以打印详细的执行日志信息，包括上传过程中的临时压缩包文件也会保留。  

#### 查看客户端版本。  
```
td version
```

