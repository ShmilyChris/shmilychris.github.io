## 记些用得到又会忘的命令

- adb线刷：`adb sideload *.zip`
- adb安装apk包：adb install -t -r -g *.apk
- adb devices #查看当前连接设备
- adb -s 设备号 指令 #发现多个设备
- adb uninstall com.xxx.app #卸载app
  - adb uninstall -k com.xxx.app #卸载app但保留数据
- adb push xx.jpg /sdcarc/ #传递文件
- adb shell pm list packages #查看手机端安装的所有app包名
- adb shell screencap /sdcard/xxx.png #手机截图
- adb shell screenrecord /sdcard/xxx.mp4 #手机录屏
- adb shell ps #查看进程



