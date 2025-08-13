# NS3-
1.NS-3安装步骤（Windows）

1）.vmware安装教程：https://zhuanlan.zhihu.com/p/1920466932596969606

2）.Ubuntu安装教程（推荐使用国内镜像进行下载）：https://blog.csdn.net/MSDCP/article/details/143020714?ops_request_misc=&request_id=&biz_id=102&utm_term=ubuntu24.04%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-5-143020714.142^v102^pc_search_result_base5&spm=1018.2226.3001.4187

3）.安装docker：打开终端，先输入sudo apt-get update，再输入sudo apt-get install docker.io

4）.拉取ns3镜像：终端输入sudo docker pull docker.1ms.run/snowzjx/ns3-ecn-sharp:optimized（ 其中docker.1ms.run为镜像，不可用就进行更换，好用镜像地址参考：https://blog.csdn.net/qq_73162098/article/details/145014490?share_token=6e589c77-deea-47a7-b0c4-3d93e1a0cb8a）

5）.终端输入sudo docker run -itd --name ns3-simulator docker.1ms.run/snowzjx/ns3-ecn-sharp:optimized bash -c "cd ~/ns3-ecn-sharp && tail -f /dev/null"(上面镜像更换后这个也要换)，再输入电脑用户密码即可创建容器。之后键入sudo docker update --restart=unless-stopped ns3-simulator
设置容器开机自启动。后面加入该容器则用sudo docker exec -it ns3-simulator bash -c "cd ~/ns3-ecn-sharp && exec bash"

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

2）.ECMP实现可借助已有代码，需修改部分：/src/internet/mode/global-route-manager-impl.cc中迪杰斯特拉算法，令其可以存储多条等价最短路径；然后修改/src/internet/mode/global-route-manager.cc中LookupGlobal（）函数，使其在ecmp实现部分由随机ecmp变为按照五元组哈希值来选择路径的ecmp。

3).参考代码: 下载文件并解压到本地https://github.com/wany16/ns3-ecn-sharp      
            (1)ecmp:ns3-ecn-sharp中搜索ipv4-global-routing.cc，里面的lookupglobal函数就是实现ecmp关键实现部分。  
            (2)letflow:代码实现在src\letflow-routing\model\ipv4-letflow-routing.cc.   
            (3)conga:代码实现在src\conga-routing\model\ipv4-conga-routing.cc。
            (4)ecmp测试例子：https://github.com/mkheirkhah/ecmp/blob/development/scratch/ecmp.cc

4).显示容器内文件内容到控制台：cat +文件路径（如cat scratch/scratch-simulator.cc）

5）.修改容器内代码：
cat > scratch/first.cc <<EOF
 // 在此粘贴新代码
EOF
     
例子：cat > scratch/scratch-simulator.cc <<EOF

#include "ns3/core-module.h"
#include "ns3/log.h"
using namespace ns3;

NS_LOG_COMPONENT_DEFINE ("ScratchSimulator");

int 
main (int argc, char *argv[])
{LogComponentEnable("ScratchSimulator", LOG_LEVEL_ALL);
  std::cout<<"Scratch Simulator"<<std::endl; 

 std::cout<<"1"<<std::endl; NS_LOG_UNCOND ("Scratch Simulator Finish");

  Simulator::Run ();
  Simulator::Destroy ();
} 

EOF

6).日志功能失效：./waf configure --enable-examples --enable-tests --build-profile=debug(重新从源码编译NS-3，
确保启用日志).

7).拷贝容器内ns3-ecn-sharp目录下内容至宿主机目录/home/xiale/ns3：docker cp ns3-simulator:/root/ns3-ecn-sharp /home/xiale/ns3

8).下载netanim:终端输入wget https://www.nsnam.org/release/ns-allinone-3.29.tar.bz2，解压然后请教deepseek
9).把名称为ns3-simulator的容器目录/root/ns3-ecn-sharp下的ecmp.xml 拷贝到宿主机/home/xiale/ns3目录下  :docker cp ns3-simulator:/root/ns3-ecn-sharp/ecmp.xml /home/xiale/ns3
