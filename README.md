# NS3-
1.NS-3安装步骤（Windows）

1）.vmware安装教程：https://zhuanlan.zhihu.com/p/1920466932596969606

2）.Ubuntu安装教程（推荐使用国内镜像进行下载）：https://blog.csdn.net/MSDCP/article/details/143020714?ops_request_misc=&request_id=&biz_id=102&utm_term=ubuntu24.04%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-5-143020714.142^v102^pc_search_result_base5&spm=1018.2226.3001.4187

3）.安装docker：打开终端，先输入sudo apt-get update，再输入sudo apt-get install docker.io

4）.拉取ns3镜像：终端输入sudo docker pull docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized（ 其中docker-0.unsee.tech为镜像，不可用就进行更换，好用镜像地址参考：https://blog.csdn.net/qq_73162098/article/details/145014490?share_token=6e589c77-deea-47a7-b0c4-3d93e1a0cb8a）

5）.终端输入sudo docker run -it docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized(上面镜像更换后这个也要换)，再输入电脑用户密码即进入容器内。输入cd ~/ns3-ecn-sharp之后即可在当前界面使用./waf之类的命令进行操作，之后的实验编码就在docker内部（当前位置）进行。或者使用sudo docker run -it docker-0.unsee.tech/snowzjx/ns3-ecn-sharp:optimized bash -c "cd ~/ns3-ecn-sharp && bash"也可以。



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

2）.ECMP实现可借助已有代码，需修改部分：/src/internet/mode/global-route-manager-impl.cc中迪杰斯特拉算法，令其可以存储多条等价最短路径；然后修改/src/internet/mode/global-route-manager.cc中LookupGlobal（）函数，使其在ecmp实现部分由随机ecmp变为按照五元组哈希值来选择路径的ecmp。(待完成)以下是ai生成的需要修改的关键部分：
  在NS-3中实现ECMP（等价多路径路由）负载均衡，需修改以下关键函数和模块，结合搜索结果中的技术细节分析如下：
  ​1. 路由计算模块的扩展​
  ​修改 SPFNext 函数​
  在检测到等价路径时（cw->GetDistanceFromRoot() == distance），需将多条路径的下一跳信息存储到路由表中，而非仅合并。例如：
  扩展 SPFVertex 类，将 m_rootExitDirection 从单一入口改为列表（如 std::vector<NodeExit_t>），以保存多下一跳信息。
  在 SPFNext 中，通过 MergeRootExitDirections 合并路径时，需保留所有等价路径的下一跳和接口信息，而非覆盖。
​  修改 SPFNexthopCalculation 函数​
  需支持多下一跳的继承逻辑。例如，当非根节点继承父节点的下一跳时，需复制所有可能的路径选项，而非仅默认路径。
  ​2. 路由表结构的调整​
​  扩展 Ipv4GlobalRouting 类​
  将路由表从单一路由条目改为支持多下一跳的存储结构。例如：
  使用 std::map<Ipv4Address, std::vector<RouteEntry>> 存储目标地址对应的多路径。
  修改 AddNetworkRouteTo 方法，允许为同一目标添加多个下一跳条目。
​  实现路径选择策略​
  在 LookupGlobal 函数中，需实现负载均衡算法（如哈希、轮询或权重分配）：
​  哈希算法​：基于五元组（源/目标IP、端口、协议）哈希选择路径，避免流内乱序。
​  轮询​：依次分配流量到不同路径，适合短连接场景。
​  3. 负载均衡策略的集成​
​  新增负载均衡配置接口​
  在 GlobalRouteManagerImpl 中添加方法（如 SetECMPAlgorithm），允许用户选择哈希、轮询等策略。
  ​动态路径权重调整​
  若需支持动态负载（如基于链路利用率），需在 SPFCalculate 中引入链路状态监控，动态更新路径权重。
  ​4. 数据流处理的优化​
​  流一致性保障​
  在哈希算法中，需确保同一数据流的包始终选择相同路径，避免TCP乱序。可通过固定哈希种子或流标识符实现。
  ​大象流处理​
  对于大流量（如视频流），可结合显式拥塞通知（ECN）动态切换路径，参考论文《Expeditus》的拥塞感知机制。
  ​5. 测试与验证​
  ​NS-3仿真脚本扩展​
  在测试拓扑中构造多条等价路径，验证流量分布是否符合预期。


3).参考代码: 下载文件并解压到本地https://github.com/wany16/ns3-ecn-sharp      
            (1)ecmp:ns3-ecn-sharp中搜索ipv4-global-routing.cc，里面的lookupglobal函数就是实现ecmp关键实现部分。  
            (2)letflow:代码实现在src\letflow-routing\model\ipv4-letflow-routing.cc.   
            (3)conga:代码实现在src\conga-routing\model\ipv4-conga-routing.cc。
            (4)ecmp测试例子：https://github.com/mkheirkhah/ecmp/blob/development/scratch/ecmp.cc

4).显示容器内文件内容到控制台：cat +文件路径（如cat scratch/scratch-simulator.cc）

5）.修改容器内代码（临时，退出容器则恢复原状）：
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
