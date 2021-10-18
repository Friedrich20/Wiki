1. 下载并安装[Beyond Compare](https://www.scootersoftware.com/download.php)
2. 进入Beyond Compare应用程序目录下(/Applications/Beyond Compare.app/Contents/MacOS)
3. 将主启动程序BCompare重命名为BCompare.real
4. 在同级目录下新建一个脚本文件命名为BCompare，编辑内容如下:  
```
#!/bin/bash
rm "/Users/$(whoami)/Library/Application Support/Beyond Compare/registry.dat"
"`dirname "$0"`"/BCompare.real $@
```
说明：第一行是注明解释器，第二行是删除注册信息，第三行是启动真正的主程序  

5. 授予文件执行权限  
```
chmod a+x /Applications/Beyond\ Compare.app/Contents/MacOS/BCompare
```

这样我们每次打开软件的时候，都会先自动删掉注册信息，也就是永久免费试用了。