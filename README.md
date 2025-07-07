# NS3-
1.NS-3安装步骤（Windows）
1）.vmware安装教程：https://zhuanlan.zhihu.com/p/1920466932596969606
2）.Ubuntu安装教程（推荐使用国内镜像进行下载）：https://blog.csdn.net/MSDCP/article/details/143020714?ops_request_misc=&request_id=&biz_id=102&utm_term=ubuntu24.04%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-5-143020714.142^v102^pc_search_result_base5&spm=1018.2226.3001.4187
3）.打开终端，先输入sudo apt-get update，再输入sudo apt-get install docker.io（安装docker）
4）.拉取ns3镜像：终端输入sudo docker pull docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized（ 其中docker-0.unsee.tech为镜像，不可用就进行更换，好用镜像地址参考：https://blog.csdn.net/qq_73162098/article/details/145014490?share_token=6e589c77-deea-47a7-b0c4-3d93e1a0cb8a）
5）.终端输入sudo docker run -it docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized(上面镜像更换后这个也要换)，再输入电脑用户密码即进入容器内。输入cd ~/ns3-ecn-sharp之后即可在当前界面使用./waf之类的命令进行操作，之后的实验编码就在docker内部（当前位置）进行。
