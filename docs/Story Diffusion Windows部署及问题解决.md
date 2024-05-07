# Story Diffusion Windows部署问题解决

## 环境

我当前的环境是:

- GPU: 4070Ti 12G显存
- 内存: 32G
- 系统: windows 11
- cuda: 1.12.1
- torch: 2.2.2
- python: 3.10

这个项目已经修改成了适配torch2.2.2版本. 3.11无法安装windows版本triton.

## 步骤

### clone项目

~~~shell
git clone https://github.com/quqibing/StoryDiffusion.git
~~~

### 新建环境

~~~shell
conda create -n story310 python=3.10
conda activate story310
~~~

### 安装依赖

如果安装CPU版本:

~~~shell
pip install -r requirements.txt
~~~

如果是安装GPU版本:

先去这两个网址下载对应GPU版本和系统的安装包, 放到项目目录下:

torch: <https://download.pytorch.org/whl/torch/>

torchvision: <https://download.pytorch.org/whl/torchvision/>

对应关系参考: <https://blog.csdn.net/shiwanghualuo/article/details/122860521>

安装命令参考:

~~~shell
pip install "torch-2.2.2+cu121-cp310-cp310-win_amd64.whl" "torchvision-0.17.2+cu121-cp310-cp310-win_amd64.whl" -r requirements.txt
~~~

gradio 4.23.0在windows下有bug, 所以必须用4.21.0版本.

安装Triton

下载: <https://github.com/PrashantSaikia/Triton-for-Windows>

安装:

~~~shell
pip install "triton-2.0.0-cp310-cp310-win_amd64.whl"      
~~~

同时会安装cmake.

## 运行

由于运行时需要下载模型, 默认下载到c盘, 如果c盘空间不够, 可以指定huggingface缓存目录到其他位置.

可以在win11中搜索"环境变量", 打开后添加环境变量如下:

~~~shell
TRANSFORMERS_CACHE=D:\workspaces\ai\models\huggingface\
HUGGINGFACE_HUB_CACHE=D:\workspaces\ai\models\huggingface\
~~~

添加代理环境变量:

~~~shell
HTTP_PROXY=http://xxx
HTTPS_PROXY=http://xxx
~~~

或者参考: <https://hf-mirror.com/>, 使用镜像网址下载, 可以不用代理. 添加环境变量:

~~~shell
HF_ENDPOINT=https://hf-mirror.com
~~~

重新打开终端, 运行项目:

~~~shell
python gradio_app_sdxl_specific_id.py
~~~

## 问题解决

此时运行会遇到一个错误:

~~~log
JITFunction.__init__() got an unexpected keyword argument 'debug' 
~~~

参考: <https://blog.csdn.net/jacke121/article/details/136019072> 的临时解决办法解决. 打开路径, 类似于:

~~~shell
D:\programs\miniconda3\envs\story310test\Lib\site-packages\triton\runtime
~~~

288行改为:

~~~python
    def __init__(self, fn, version=None, do_not_specialize=None,debug=False):
~~~

再次启动, 可以成功下载模型并进行生成.

## 访问

地址是: <http://127.0.0.1:7860/>

建议steps改为20, 分辨率改为640*640. 否则会显存溢出.
