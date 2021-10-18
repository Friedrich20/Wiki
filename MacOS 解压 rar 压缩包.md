1. 下载 [RAR archiver](https://www.rarlab.com/download.htm) e.g. RAR 5.80 for macOS (64 bit)
2. 双击下载文件解压得到rar文件夹
3. cd到rar文件夹下
4. 安装  
`install -c -o $USER rar /usr/local/bin/`  
`install -c -o $USER unrar /usr/local/bin/`
5. 完成
***
* 压缩命令  
`rar a <压缩后的文件名>.rar <要压缩的文件>`  
（a代表压缩）
* 解压命令  
`unrar  x  <压缩文件名>.rar`  
（x代表解压）