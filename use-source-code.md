源码使用方法
环境要求
Node.js 16+
项目环境准备
安装Node.js环境（如已安装请跳过）: 下载Node.js安装结束后，打开命令行输入node -v将会输出Node.js的版本号即表示已安装完成
安装Android开发环境: 若你没有安装有安装Android开发环境，可以看环境准备的React Native CLI Quickstart说明
拉取代码: 克隆本仓库代码
安装依赖: 在项目根目录打开命令行，执行命令：npm install，若此命令执行的过程中报错可以尝试百度报错内容找解决方法，或在此处贴出报错日志一起讨论解决
使用Android Studio打开项目根目录下的android文件夹，同步一遍gradle
项目启动步骤
启动模拟器或连接真实设备
启动开发服务器: 在项目根目录打开命令行，执行命令：npm run dev，若开发服务器意外停止了，可以执行npm start重新启动
开发: 修改项目下的JS即可实时看到修改后的效果
Native开发
使用Android Studio打开项目根目录下的android文件夹，即可在Android Studio内进行安卓代码的开发与调试

构建安装包
首先生成安卓签名文件，然后将你的签名文件放在android/app/，然后在android/新建keystore.properties文件，填入你的签名信息：

storeFile=
storePassword=
keyAlias=
keyPassword=

最后在android/执行命令./gradlew assembleRelease，构建的安装包在android/app/build/outputs/apk/release/
