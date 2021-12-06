# 安装Anaconda

安装好anaconda后先增加源

```
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/msys2/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/bioconda/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/menpo/ 
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/pytorch/ 
conda config --set show_channel_urls yes 
conda config --show
```

写错了可以remove源

```
conda config --remove channels 'https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/'
```

# CPU版

使用华为笔记本电脑部署安装CPU版本，电脑的配置是：

```
设备名称	LAPTOP-G3IANL5K
处理器	Intel(R) Core(TM) i5-8265U CPU @ 1.60GHz   1.80 GHz
机带 RAM	8.00 GB (7.85 GB 可用)
设备 ID	BE811514-76B7-415B-A8A4-39EB7EF898A3
产品 ID	00342-35439-96452-AAOEM
系统类型	64 位操作系统, 基于 x64 的处理器
笔和触控	为 20 触摸点提供笔和触控支持
```

```
conda create -n pytorch python=3.7
conda activate pytorch
# 利用torch官方源下载，速度还挺快
pip install torch==1.8.1+cpu torchvision==0.9.1+cpu torchaudio==0.8.1 -f https://download.pytorch.org/whl/torch_stable.html
```

接着在(pytorch)环境下安装内核到Jupyter

```
pip install ipykernel -i https://pypi.douban.com/simple
python -m ipykernel install --name pytorch
jupyter kernelspec list
```

然后切换到有Jupyter的(base)环境下启动

```
jupyter lab
```

在浏览器界面中的确认内核是不是(pytorch)：

```
import sys
sys.executable  # 输出是'D:\\Anaconda3\\envs\\pytorch\\python.exe'，证明生效了
```

# 配置markdown目录

https://zhuanlan.zhihu.com/p/357524726

结合我的评论：

感谢作者！我补充一点：windows电脑安装的话，在3.3到3.4步骤间，可能还需要把nvm下载好的node的目录文件夹添加到系统环境变量，并且我nvm安装好的node的文件名称是node64.exe，需要重命名为node.exe，不然npm找不到node。
此外针对ValueError: "@jupyterlab/toc" is not a valid npm package的问题，可能是包版本升级了。。执行这样的指令试试：jupyter labextension install @jupyterlab/toc@2.0.0（详细见：[https://github.com/jupyterlab/jupyterlab-toc/issues/35](http://link.zhihu.com/?target=https%3A//github.com/jupyterlab/jupyterlab-toc/issues/35)）

# TF和Pytorch

https://blog.csdn.net/weixin_38739735/article/details/109673524

静态图意味着我们只需要定义一个计算图，然后对其进行编译，在后续过程中不断使用它。而动态图则相反，由于并没有预先定义，即在每次使用时建立，程序按照我们编写命令的顺序进行执行。

静态图以TensorFlow为例，我们在模型运行前定义一个静态图，至此，所有和外部的通信都是通过tf.Session()对象和tf.Placeholder占位符执行的，其中tf.Seesion用来开启会话，tf.Placeholder是在运行时外部数据传入的占位符张量。鉴于静态图的这种构建机制，从理论上讲，容易在图上对其进行优化，因此其运算效率会比较高。

动态图以Pytorch为例，事情就变得比较动态化：我们可以随时定义、更改和执行代码，而不需要特殊的会话窗口和占位符。并且该框架和Python语言紧密结合，省出了额度学习成本，用起来会感觉比TensorFlow更为原生和友好。动态图的构造原理使得调试过程变得更为容易，也能更直观地设计，因此比较方便我们将自己的想法快速进行复现，可以说是学术“利器”。

借用网络上一个小伙伴的有趣比喻，假如我们让TensorFlow和Pytorch分别造一座房子：

TensorFlow会先将造房子的流程预演一遍，设计好流程图，看下都需要什么资源，提前规划好什么时候需要做什么，最后找到一个可以建造这种房子的工程队。最后把上面那些全部交给工程队搞定。

Pytorch会先将造房子分成几个项目，每一个项目相互独立。等到开始建造房子的时候，写一个TODO list把需要做的事情安装所需顺序串起来，然后分别去执行每个项目。
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9tbWJpei5xcGljLmNuL21tYml6X3BuZy9vV1laTlFPZ013ejRYbkcxRTdlQU96cHZBTkFlbDQxcnFBSjJSYUxIcG5rTmNLaFVHTWx3bGlib1ZpYXl1WFQwYkJ3RVFlaWF5Q29XTnBjaGt2ajN2QzYzQS82NDA?x-oss-process=image/format,png)

