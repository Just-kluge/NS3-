# NS3-
1.NS-3安装步骤（Windows）

1）.vmware安装教程：https://zhuanlan.zhihu.com/p/1920466932596969606

2）.Ubuntu安装教程（推荐使用国内镜像进行下载）：https://blog.csdn.net/MSDCP/article/details/143020714?ops_request_misc=&request_id=&biz_id=102&utm_term=ubuntu24.04%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-5-143020714.142^v102^pc_search_result_base5&spm=1018.2226.3001.4187

3）.安装docker：打开终端，先输入sudo apt-get update，再输入sudo apt-get install docker.io

4）.拉取ns3镜像：终端输入sudo docker pull docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized（ 其中docker-0.unsee.tech为镜像，不可用就进行更换，好用镜像地址参考：https://blog.csdn.net/qq_73162098/article/details/145014490?share_token=6e589c77-deea-47a7-b0c4-3d93e1a0cb8a）

5）.终端输入sudo docker run -it docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized(上面镜像更换后这个也要换)，再输入电脑用户密码即进入容器内。输入cd ~/ns3-ecn-sharp之后即可在当前界面使用./waf之类的命令进行操作，之后的实验编码就在docker内部（当前位置）进行。



2.使用过程中遇到的问题
1）.如何在终端实现编码，可行例子示例：
echo '
#include "ns3/core-module.h"
using namespace ns3;
int main() {
    std::cout << "NS-3 running dynamically!" << std::endl;
    Simulator::Run();
    Simulator::Destroy();
    return 0;
}
' > scratch/dynamic_test.cc && ./waf --run dynamic_test

2）.ECMP实现可借助已有代码，需修改部分：/src/internet/mode/global-route-manager-impl.cc中迪杰斯特拉算法，令其可以存储多条等价最短路径；然后修改/src/internet/mode/global-route-manager.cc中LookupGlobal（）函数，使其在ecmp实现部分由随机ecmp变为按照五元组哈希值来选择路径的ecmp.(待完成)

3).

