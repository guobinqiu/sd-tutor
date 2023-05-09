# AI绘图教程

要实现照片中的效果，光有prompt提示词是远远不够的，我们首先要去安装`stable diffusion webui`，它是`stable diffusion`（免费，没有生成次数限制）的一个用户操作界面，方便我们文生图，图生图，局部生图等。

教程分为`准备篇`、`安装篇`和`使用篇`三部分进行说明，该文档面向普通用户，所以我略过了很多细枝末节和优化项，只挑重点讲。

## 准备篇

先了解一下这几样东西：

- https://huggingface.co/ 一个ai模型分享社区

- https://civitai.com/ 一个ai作品分享社区

- https://github.com/AUTOMATIC1111/stable-diffusion-webui `stable diffusion webui`官方安装教程

最好有个vpn，没有也没关系，有的文件我已经为你下载好了。

## 安装篇

安装分为四个步骤：

1.安装python

2.下载大模型

3.下载小模型

4.运行

关于什么是python，什么是大模型，什么是小模型，概念自己上网去了解，这里不再赘述。

### 1.安装python

切记：python版本不能低于3.10.6！

#### 在Windows上安装

```
安装[Python 3.10.6](https://www.python.org/downloads/release/python-3106/)
```
`Files`下面，推荐选`Windows installer (64-bit)`

#### 在Linux上安装

```
# 基于 Debian：
sudo apt 安装 wget git python3 python3-venv
# 基于 Red Hat：
sudo dnf 安装 wget git python3
# 基于 Arch：
sudo pacman -S wget git python3
```

#### 在Apple Silicon上安装

```
brew install cmake protobuf rust python@3.10 git wget
```
如果未安装 Homebrew，请按照 https://brew.sh 上的说明进行安装。

### 2.下载大模型

大模型可以到 https://huggingface.co/ 上去下载，下载后放到`stable-diffusion-webui/models/Stable-diffusion`目录下。

由于大模型动辄好几个G，我没有办法让你高速获取，你需要自行下载。

这里推荐一个逼真人物模型：https://huggingface.co/naonovn/chilloutmix_NiPrunedFp32Fix/blob/main/chilloutmix_NiPrunedFp32Fix.safetensors

你也可以自己到 https://huggingface.co/models 上面搜索感兴趣的模型，下载`Files and verions`下面的`.safetensors`文件。

### 3.下载小模型

小模型可以到 https://huggingface.co/ 上去下载，下载后放到`stable-diffusion-webui/models/Lora`目录下。

小模型比较小，一般只有几百M，考虑到有些小伙伴没有vpn的，所以我事先下载了几个放进目录里了。

我这里下载了几个常用的Lora类型小模型：
- PiscesArmor.safetensors（双鱼座黄金圣衣）https://civitai-delivery-worker-prod-2023-03-30.5ac0637cfd0766c97916cefa3764fbdf.r2.cloudflarestorage.com/3497/model/piscesarmor.g7UW.safetensors?X-Amz-Expires=86400&response-content-disposition=attachment%3B%20filename%3D%22PiscesArmor.safetensors%22&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=2fea663d76bd24a496545da373d610fc/20230509/us-east-1/s3/aws4_request&X-Amz-Date=20230509T013341Z&X-Amz-SignedHeaders=host&X-Amz-Signature=f769bd7425b7f3bf6c3ab6759234139597284e248fab2c9901199e5919d7b6b2

- koreanDollLikeness_v15.safetensors (韩系真人女孩) https://huggingface.co/amornlnw7/koreanDollLikeness_v15/blob/main/koreanDollLikeness_v15.safetensors

- japaneseDollLikeness_v10.safetensors （日系真人女孩）https://huggingface.co/aimainia/japaneseDollLikeness_v10/blob/main/japaneseDollLikeness_v10.safetensors

- cuteGirlMix4_v10.safetensors （卡哇伊女孩）https://huggingface.co/luxluna/cuteGirlMix4_v10/blob/main/cuteGirlMix4_v10.safetensors

比如`PiscesArmor.safetensors`这个模型我们在 https://huggingface.co/ 上没有找到，可以去 https://civitai.com/ 搜搜看。打开civitai网站是需要vpn的，但是从它上面下载模型则不需要！

### 4.运行

运行`stable diffusion webui`之前，我们先要修改下一下它的一个配置文件`launch.py`，让它从国内镜像下载依赖包

1.从清华源下载
```
python = sys.executable
git = os.environ.get('GIT', "git")
index_url = os.environ.get('INDEX_URL', "") //这行改成：index_url = os.environ.get('INDEX_URL', "https://pypi.tuna.tsinghua.edu.cn/simple")
stored_commit_hash = None
dir_repos = "repositories"
```

2.package url前面附加一段`https://ghproxy.com`地址

由原来
```
gfpgan_package = os.environ.get('GFPGAN_PACKAGE', "git+https://github.com/TencentARC/GFPGAN.git@8d2447a2d918f8eba5a4a01463fd48e45126a379")
clip_package = os.environ.get('CLIP_PACKAGE', "git+https://github.com/openai/CLIP.git@d50d76daa670286dd6cacf3bcd80b5e4823fc8e1")
openclip_package = os.environ.get('OPENCLIP_PACKAGE', "git+https://github.com/mlfoundations/open_clip.git@bb6e834e9c70d9c27d0dc3ecedeebeaeb1ffad6b")
```
改成现在

```
gfpgan_package = os.environ.get('GFPGAN_PACKAGE', "git+https://ghproxy.com/https://github.com/TencentARC/GFPGAN.git@8d2447a2d918f8eba5a4a01463fd48e45126a379")
clip_package = os.environ.get('CLIP_PACKAGE', "git+https://ghproxy.com/https://github.com/openai/CLIP.git@d50d76daa670286dd6cacf3bcd80b5e4823fc8e1")
openclip_package = os.environ.get('OPENCLIP_PACKAGE', "git+https://ghproxy.com/https://github.com/mlfoundations/open_clip.git@bb6e834e9c70d9c27d0dc3ecedeebeaeb1ffad6b")
```

3.启动

- windows: `以普通非管理员用户身份从Windows资源管理器运行webui-user.bat`
- Linux: `./webui.sh`
- Mac: `./webui.sh`

启动成功后控制台会输出`Running on local URL:  http://127.0.0.1:7860`,把该地址粘贴到浏览器里就能享用啦！

![](screenshot.png)

## 使用篇

### 参数

名称|描述
---|---
Stable Diffusion checkpoint|大模型，底模型，`stable-diffusion-webui/models/Stable-diffusion`目录下的模型
Prompt|正向提示词，提示词中用`<类型:名称:权重>`包含起来的是小模型，对提示词的影响较大，可以是多个小模型的组合，权重之和尽量不超过1。`stable-diffusion-webui/models/Lora`目录下的模型
Negative prompt|负向提示词，修片用，写哪里不好，固定一个通用的
Sampling method|采样方法，很多，人像用DPM系列的比较好
Sampling steps|采样步长，28-35之间
Restore faces|建议安装，插值修片，脸部会更精细
Width|图像宽度，规范的小模型会标注所使用的训练图片的尺寸，取值建议和权重最高的小模型的宽接近
Height|图像高度，规范的小模型会标注所使用的训练图片的尺寸，取值建议和权重最高的小模型的高接近
CFG Scale|7-8.5，越大越靠近提示词，越小越灵活
Seed|先设置-1随机出图，等生成的图满意了点击右边`绿色的回收图标`，获取该图的Seed供下次稳定出图用

![](civitai.jpeg)

### 例子

![](00057-1728274835.png)

上面这张图的配置如下：

参数|值
---|---
Stable Diffusion checkpoint|chilloutmix_NiPrunedFp32Fix.safetensors
Prompt|`<lora:koreanDollLikeness_v10:0.3><lora:cuteGirlMix4_v10:0.1><lora:PiscesArmor:0.6>mix4, 1girl, solo, smile, excited, sunshine on the face, (short_hair:1.3),black_hair, greek template background, piscesarmor,gold armor, armor, greek temple background, looking_at_viewer, photorealistic, (8k, RAW photo, best quality, masterpiece:1.2), (realistic, photo-realistic:1.37), professional lighting, photon mapping, radiosity, physically-based rendering`
Negative prompt|`paintings, sketches, (worst quality:2), (low quality:2), (normal quality:2), lowres, normal quality, ((monochrome)), ((grayscale)), skin spots, acnes, skin blemishes, age spot, glans`
Sampling method|DPM++ SDE Karras
Sampling steps|30
Restore faces|勾选
Width|750
Height|900
CFG Scale|8
Seed|1728274835
