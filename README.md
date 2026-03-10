# Open-Vocabulary SAM [ECCV-2024]

[Haobo Yuan<sup>1</sup>](https://yuanhaobo.me), 
[Xiangtai Li<sup>1</sup>](https://lxtgh.github.io), 
[Chong Zhou<sup>1</sup>](https://chongzhou96.github.io), 
[Yining Li<sup>2</sup>](https://scholar.google.com/citations?user=y_cp1sUAAAAJ), 
[Kai Chen<sup>2</sup>](https://chenkai.site), 
[Chen Change Loy<sup>1</sup>](https://www.mmlab-ntu.com/person/ccloy/). 

[<sup>1</sup>S-Lab, Nanyang Technological University](https://www.mmlab-ntu.com/), 
[<sup>2</sup>Shanghai Artificial Intelligence Laboratory](https://www.shlab.org.cn/)

[![arXiv](https://img.shields.io/badge/arXiv-2401.02955-b31b1b.svg)](https://arxiv.org/abs/2401.02955)
[![Project Page](https://img.shields.io/badge/OVSAM-Project%20Page-green)](https://www.mmlab-ntu.com/project/ovsam)
[![HuggingFace Model](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-App-blue)](https://huggingface.co/spaces/HarborYuan/ovsam)
[![Open in OpenXLab](https://cdn-static.openxlab.org.cn/app-center/openxlab_app.svg)](https://openxlab.org.cn/apps/detail/houshaowei/Open-Vocabulary_SAM)


# RWKV-SAM [Arxiv](https://arxiv.org/abs/2406.19369)

[Haobo Yuan<sup>1</sup>](https://yuanhaobo.me), 
[Xiangtai Li<sup>2,1</sup>](https://lxtgh.github.io), 
[Tao Zhang<sup>2</sup>](https://zhang-tao-whu.github.io/), 
[Lu Qi<sup>3</sup>](http://luqi.info/), 
[Ming-Hsuan Yang<sup>3</sup>](http://faculty.ucmerced.edu/mhyang/), 
[Shuicheng Yan<sup>2</sup>](https://yanshuicheng.info/), 
[Chen Change Loy<sup>1</sup>](https://www.mmlab-ntu.com/person/ccloy/).

[<sup>1</sup>S-Lab, Nanyang Technological University](https://www.mmlab-ntu.com/),
[<sup>2</sup>SkyworkAI]()
[<sup>3</sup>UC Merced]()


## 📰 News
* **` Jul. 2, 2024`:** Open-Vocabulary SAM has been accepted by [ECCV 2024](https://eccv2024.ecva.net).
* **` Jun. 27, 2024`:** Release RWKV-SAM code and model [Paper](https://arxiv.org/abs/2406.19369). Please check out the [folder](https://github.com/HarborYuan/ovsam/tree/main/projects/rwkvsam).


## 👀 Overview
We introduce the Open-Vocabulary SAM, a SAM-inspired model designed for simultaneous interactive segmentation and recognition, leveraging two unique knowledge transfer modules: SAM2CLIP and CLIP2SAM. The former adapts SAM's knowledge into the CLIP via distillation and learnable transformer adapters, while the latter transfers CLIP knowledge into SAM, enhancing its recognition capabilities.

<p>
  <img src="https://www.mmlab-ntu.com/project/ovsam/img/ovsam_teaser.jpg" alt="OVSAM overview">
</p>

## 🔧Usage
To play with Open-Vocabulary SAM, you can:
1. Try the online demo on the [🤗Hugging Face Space](https://huggingface.co/spaces/HarborYuan/ovsam). Thanks for the generous support of the Hugging Face team.
2. Run the gradio demo locally by cloning and running the [repo](https://huggingface.co/spaces/HarborYuan/ovsam/tree/main) on 🤗Hugging Face:
    ```commandline
    git lfs install
    git clone https://huggingface.co/spaces/HarborYuan/ovsam ovsam_demo
    cd ovsam_demo
    conda create -n ovsam_demo python=3.10  && conda activate ovsam_demo
    python -m pip install gradio==4.7.1
    python -m pip install -r requirements.txt
    python main.py
    ```
3. Try to train or evaluate in this repo following the instructions below.

## ⚙️ Installation
We use conda to manage the environment.

Pytorch installation:
```commandline
conda install pytorch torchvision torchaudio pytorch-cuda=12.1 cuda -c pytorch  -c "nvidia/label/cuda-12.1.0" -c "nvidia/label/cuda-12.1.1"
```

mmengine installation:
```commandline
python -m pip install https://github.com/open-mmlab/mmengine/archive/refs/tags/v0.8.5.zip
```

mmcv installation (note that older version mmcv before this commit may cause bugs):
```commandline
TORCH_CUDA_ARCH_LIST="{COMCAP}" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" CUDA_HOME=$(dirname $(dirname $(which nvcc))) LD_LIBRARY_PATH=$(dirname $(dirname $(which nvcc)))/lib MMCV_WITH_OPS=1 FORCE_CUDA=1 python -m pip install git+https://github.com/open-mmlab/mmcv.git@4f65f91db6502d990ce2ee5de0337441fb69dd10
```
Please ask ChatGPT to get `COMCAP`:
```text
What is the `Compute Capability` of NVIDIA {YOUR GPU MODEL}? Please only output the number, without text.
```

Other OpenMMLab packages:
```commandline
python -m pip install \
https://github.com/open-mmlab/mmdetection/archive/refs/tags/v3.1.0.zip \
https://github.com/open-mmlab/mmsegmentation/archive/refs/tags/v1.1.1.zip \
https://github.com/open-mmlab/mmpretrain/archive/refs/tags/v1.0.1.zip
```

Extra packages:
```commandline
python -m pip install git+https://github.com/cocodataset/panopticapi.git \
git+https://github.com/HarborYuan/lvis-api.git \
tqdm terminaltables pycocotools scipy tqdm ftfy regex timm scikit-image kornia
```

## 📈 Datasets
Datasets should be put in the `data/` folder of this project similar to [mmdet](https://mmdetection.readthedocs.io/en/latest/user_guides/tracking_dataset_prepare.html). Please prepare dataset in the following format.
### COCO dataset
```text
├── coco
│   ├── annotations
│   │   ├── panoptic_{train,val}2017.json
│   │   ├── instance_{train,val}2017.json
│   ├── train2017
│   ├── val2017
│   ├── panoptic_{train,val}2017/  # png annotations
```
### SAM dataset
```text
├── sam
│   ├── train.txt
│   ├── val.txt
│   ├── sa_000020
│   │   ├── sa_223750.jpg
│   │   ├── sa_223750.json
│   │   ├── ...
│   ├── ...
```
`train.txt` and `val.txt` should contain all the folders you need:
```text
sa_000020
sa_000021
...
```

## 🚀 Training
Please extract the language embeddings first.
```commandline
bash tools/dist.sh gen_cls seg/configs/ovsam/ovsam_coco_rn50x16_point.py 8
```

### SAM2CLIP
SAM feature extraction:
```commandline
bash tools/dist.sh test seg/configs/sam2clip/sam_vith_dump.py 8
```
SAM2CLIP training:
```commandline
bash tools/dist.sh train seg/configs/sam2clip/sam2clip_vith_rn50x16.py 8
```

### CLIP2SAM
CLIP2SAM training:
```commandline
bash tools/dist.sh train seg/configs/clip2sam/clip2sam_coco_rn50x16.py 8
```

## 🏃‍♀️Inference
```commandline
bash tools/dist.sh test seg/configs/ovsam/ovsam_coco_rn50x16_point.py 8
```
Please refer to [🤗Hugging Face](https://huggingface.co/HarborYuan/ovsam_models) to get the pre-trained weights:
```commandline
git clone https://huggingface.co/HarborYuan/ovsam_models models
```

## RWKV-SAM

See [readme.md](./projects/rwkvsam/README.md) for the details.



## 🧭 项目文件逐项说明（中文）

> 说明：以下按仓库内**每个受版本管理文件**列出其作用，便于快速定位代码职责。

- `.gitignore`：项目文件。
- `LICENSE`：项目许可证文件，定义代码使用与分发约束。
- `README.md`：项目主说明文档，包含论文背景、安装、训练与推理流程。
- `ext/class_names/coco_4817_ids.py`：类别名称与 ID 对照表（COCO/LVIS 等）。
- `ext/class_names/lvis_ids.py`：类别名称与 ID 对照表（COCO/LVIS 等）。
- `ext/class_names/lvis_list.py`：类别名称与 ID 对照表（COCO/LVIS 等）。
- `ext/meta/sam_meta.py`：外部元信息定义（如 SAM 元数据）。
- `ext/open_clip/__init__.py`：OpenCLIP 扩展模块导出入口。
- `ext/open_clip/bpe_simple_vocab_16e6.txt.gz`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/coca_model.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/constants.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/factory.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/generation_utils.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/hf_configs.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/hf_model.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/loss.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/model.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/model_configs/EVA01-g-14-plus.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA01-g-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA02-B-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA02-E-14-plus.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA02-E-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA02-L-14-336.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/EVA02-L-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN101-quickgelu.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN101.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN50-quickgelu.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN50.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN50x16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN50x4.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/RN50x64.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-16-plus-240.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-16-plus.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-32-plus-256.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-32-quickgelu.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-H-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-H-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-L-14-280.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-L-14-336.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-L-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-L-16-320.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-L-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-M-16-alt.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-M-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-M-32-alt.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-M-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-S-16-alt.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-S-16.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-S-32-alt.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-S-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-bigG-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-e-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/ViT-g-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/coca_ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/coca_ViT-L-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/coca_base.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/coca_roberta-ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_base.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_base_w.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_base_w_320.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_large.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_large_d.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_large_d_320.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_small.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_tiny.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_xlarge.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_xxlarge.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/convnext_xxlarge_320.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/mt5-base-ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/mt5-xl-ViT-H-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/roberta-ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/swin_base_patch4_window7_224.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/vit_medium_patch16_gap_256.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/vit_relpos_medium_patch16_cls_224.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/xlm-roberta-base-ViT-B-32.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/model_configs/xlm-roberta-large-ViT-H-14.json`：OpenCLIP 具体模型结构参数 JSON（宽度、层数、patch 等）。
- `ext/open_clip/modified_resnet.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/openai.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/pretrained.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/push_to_hf_hub.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/timm_model.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/tokenizer.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/transform.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/transformer.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/utils.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/version.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/zero_shot_classifier.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/open_clip/zero_shot_metadata.py`：OpenCLIP 扩展实现（模型、tokenizer、预训练权重与零样本工具）。
- `ext/rwkv/cls_backbones/__init__.py`：RWKV 扩展模块导出入口。
- `ext/rwkv/cls_backbones/backbones/__init__.py`：RWKV 扩展模块导出入口。
- `ext/rwkv/cls_backbones/backbones/cuda/wkv_cuda.cu`：RWKV CUDA/C++ 自定义算子源码。
- `ext/rwkv/cls_backbones/backbones/cuda/wkv_op.cpp`：RWKV CUDA/C++ 自定义算子源码。
- `ext/rwkv/cls_backbones/backbones/vrwkv.py`：RWKV 分类骨干与工具实现。
- `ext/rwkv/cls_backbones/utils/__init__.py`：RWKV 扩展模块导出入口。
- `ext/rwkv/cls_backbones/utils/drop.py`：RWKV 分类骨干与工具实现。
- `ext/rwkv/cls_backbones/utils/resize_pos.py`：RWKV 分类骨干与工具实现。
- `ext/sam/__init__.py`：SAM 扩展模块导出入口。
- `ext/sam/common.py`：SAM 组件实现（图像编码器、提示编码器、mask decoder、transformer）。
- `ext/sam/image_encoder.py`：SAM 组件实现（图像编码器、提示编码器、mask decoder、transformer）。
- `ext/sam/mask_decoder.py`：SAM 组件实现（图像编码器、提示编码器、mask decoder、transformer）。
- `ext/sam/prompt_encoder.py`：SAM 组件实现（图像编码器、提示编码器、mask decoder、transformer）。
- `ext/sam/transformer.py`：SAM 组件实现（图像编码器、提示编码器、mask decoder、transformer）。
- `ext/templates/__init__.py`：模板与类别映射工具（如 ViLD prompt/template）。
- `ext/templates/vild.py`：模板与类别映射工具（如 ViLD prompt/template）。
- `poetry.lock`：Poetry 锁定依赖版本，保证可复现安装。
- `projects/rwkvsam/README.md`：子项目说明文档，介绍该子模块的目标、配置与使用方式。
- `projects/rwkvsam/configs/_base_/datasets/DIS/dis_5k_1024.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/ade/ade20k.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/coco/coco_detection.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/coco/coco_instance.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/coco/coco_instance_1024.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/coco/coco_instance_lsj.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/coconut/coconut_b_instance_lsj.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/entity/entity_lr_instance_lsj.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/hq_concat/concat_coconutbpan_entity_dis5k_sam.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/imagenet/imagenet_bs64_swin_224.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/sam/sam_001.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/sam/sam_distill.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/thin_obj_det/coift_1024.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/thin_obj_det/hrsod_1024.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/datasets/thin_obj_det/thin_obj_5k_1024.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/default_runtime.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/default_runtime_iterbased.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_120e_bs1024_for_imagenet.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_12e_distillation.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_160k_seg.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_160k_seg_adam.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_1x.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_1x_adam.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_24e_distillation.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/_base_/schedules/schedule_300e_bs1024_for_imagenet.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/backbone_dist/rwkvsam1001_000_vith_vitamin_rwkv_small_mlp2.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/configs/backbone_dist/sam_vith_dump.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `projects/rwkvsam/datasets/__init__.py`：数据集模块导出与注册入口。
- `projects/rwkvsam/datasets/coconut_panoptic.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/datasets/concat_dataset.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/datasets/dis5k.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/datasets/entity_seg.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/datasets/pipelines/__init__.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `projects/rwkvsam/datasets/pipelines/loading.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `projects/rwkvsam/datasets/pipelines/optimization.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `projects/rwkvsam/datasets/sam.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/datasets/thin_obj_det.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `projects/rwkvsam/evaluation/__init__.py`：评测模块导出入口。
- `projects/rwkvsam/evaluation/api_wrappers/coco_api.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `projects/rwkvsam/evaluation/biou_metric.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `projects/rwkvsam/evaluation/coco_boundary_metric.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `projects/rwkvsam/evaluation/iou_metric.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `projects/rwkvsam/evaluation/lvis_boundary_metric.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `projects/rwkvsam/models/__init__.py`：RWKV-SAM 子项目包初始化。
- `projects/rwkvsam/models/backbones/__init__.py`：骨干网络模块导出入口。
- `projects/rwkvsam/models/backbones/sam_backbone.py`：骨干网络实现（如 SAM/CLIP/ViT/RWKV 等特征提取器）。
- `projects/rwkvsam/models/backbones/vitamin.py`：骨干网络实现（如 SAM/CLIP/ViT/RWKV 等特征提取器）。
- `projects/rwkvsam/models/detectors/__init__.py`：Detector 模块导出入口。
- `projects/rwkvsam/models/detectors/det_and_seg.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/detectors/feature_extraction.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/detectors/json_loader.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/detectors/sam_clip_distill.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/detectors/sam_dump.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/detectors/sam_model.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `projects/rwkvsam/models/heads/__init__.py`：Head 模块导出入口。
- `projects/rwkvsam/models/heads/sam_mask_decoder.py`：任务头实现（掩码解码、分类或蒸馏相关头）。
- `projects/rwkvsam/models/heads/sam_mask_decoder_rwkv_mlpmerge.py`：任务头实现（掩码解码、分类或蒸馏相关头）。
- `projects/rwkvsam/models/necks/__init__.py`：Neck 模块导出入口。
- `projects/rwkvsam/models/necks/gap.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `projects/rwkvsam/models/necks/last_layer.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `projects/rwkvsam/models/necks/sam_pe.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `projects/rwkvsam/models/preprocessors/__init__.py`：数据预处理模块导出入口。
- `projects/rwkvsam/models/preprocessors/data_preprocessors.py`：数据预处理实现（归一化、打包、多模态输入整理）。
- `projects/rwkvsam/models/preprocessors/ovsam_preprocessor.py`：数据预处理实现（归一化、打包、多模态输入整理）。
- `projects/rwkvsam/models/preprocessors/sameval_preprocessor.py`：数据预处理实现（归一化、打包、多模态输入整理）。
- `projects/rwkvsam/utils/__init__.py`：工具函数导出入口。
- `projects/rwkvsam/utils/boundary_iou.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `projects/rwkvsam/utils/load_checkpoint.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `pyproject.toml`：Python 项目构建与依赖配置（Poetry/工具链入口）。
- `seg/configs/_base_/datasets/coco_ov_instance_lsj.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/datasets/lvis_norare.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/datasets/sam.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/datasets/sam_img.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/default_runtime.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/schedules/schedule_12e.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/schedules/schedule_24e.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/_base_/schedules/schedule_distillation.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/clip2sam/clip2sam_coco_rn50x16.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/clip2sam/clip2sam_lvis_rn50x16.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/ovsam/ovsam_coco_rn50x16_point.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/ovsam/ovsam_lvis_rn50x16_point.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/sam2clip/sam2clip_vith_rn50x16.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/configs/sam2clip/sam_vith_dump.py`：模型/数据/训练策略配置文件（OpenMMLab 风格可组合配置）。
- `seg/datasets/coco_ins_ov.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `seg/datasets/concat_dataset.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `seg/datasets/pipeliens/formatting.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `seg/datasets/pipeliens/frame_copy.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `seg/datasets/pipeliens/frame_sampling.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `seg/datasets/pipeliens/loading.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `seg/datasets/pipeliens/transforms.py`：数据流水线变换实现（加载、采样、增强、格式化等）。
- `seg/datasets/sam.py`：数据集定义文件（解析标注、组织样本、对接训练流程）。
- `seg/datasets/samplers/batch_sampler.py`：多数据集与批采样策略实现。
- `seg/datasets/samplers/multi_dataset_sampler.py`：多数据集与批采样策略实现。
- `seg/evaluation/ins_cls_iou_metric.py`：评测指标实现（IoU/Boundary IoU/实例分类等）。
- `seg/models/backbones/__init__.py`：骨干网络模块导出入口。
- `seg/models/backbones/openclip_backbone.py`：骨干网络实现（如 SAM/CLIP/ViT/RWKV 等特征提取器）。
- `seg/models/backbones/sam_backbone.py`：骨干网络实现（如 SAM/CLIP/ViT/RWKV 等特征提取器）。
- `seg/models/data_preprocessor/__init__.py`：数据预处理模块导出入口。
- `seg/models/data_preprocessor/ovsam_preprocessor.py`：数据预处理实现（归一化、打包、多模态输入整理）。
- `seg/models/detectors/__init__.py`：Detector 模块导出入口。
- `seg/models/detectors/clip2sam.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `seg/models/detectors/ovsam.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `seg/models/detectors/sam2clip_distill.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `seg/models/detectors/sam_dump.py`：整模型 detector 封装（训练、推理、损失与后处理）。
- `seg/models/heads/__init__.py`：Head 模块导出入口。
- `seg/models/heads/ovsam_head.py`：任务头实现（掩码解码、分类或蒸馏相关头）。
- `seg/models/necks/__init__.py`：Neck 模块导出入口。
- `seg/models/necks/last_layer.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `seg/models/necks/sam_pe.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `seg/models/necks/transformer_neck.py`：Neck 特征变换模块（对齐/融合/位置编码等）。
- `seg/models/utils/__init__.py`：工具函数导出入口。
- `seg/models/utils/class_overlapping.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/load_checkpoint.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/mask_pool.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/no_obj.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/offline_video_metrics.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/pan_seg_transform.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `seg/models/utils/video_gt_preprocess.py`：模型相关工具函数（checkpoint、mask 操作、指标辅助等）。
- `tests/__init__.py`：测试包初始化文件。
- `tests/conftest.py`：Pytest 全局夹具与测试环境初始化。
- `tests/integration/__init__.py`：测试包初始化文件。
- `tests/test_setup_validation.py`：自动化测试用例，用于验证工程配置或功能。
- `tests/unit/__init__.py`：测试包初始化文件。
- `tools/dist.sh`：分布式命令包装脚本（train/test/gen_cls 等）。
- `tools/gen_cls.py`：类别文本特征/分类器生成脚本。
- `tools/slurm.sh`：SLURM 集群提交与分布式启动脚本。
- `tools/test.py`：评测/推理入口脚本。
- `tools/train.py`：训练入口脚本（基于 OpenMMLab Runner）。

## 📚 Citation

If you think our codebases and works are useful for your research, please consider referring us:


```bibtex
@inproceedings{yuan2024ovsam,
    title={Open-Vocabulary SAM: Segment and Recognize Twenty-thousand Classes Interactively},
    author={Yuan, Haobo and Li, Xiangtai and Zhou, Chong and Li, Yining and Chen, Kai and Loy, Chen Change},
    booktitle={ECCV},
    year={2024}
}

@article{yuan2024mamba,
  title={Mamba or RWKV: Exploring High-Quality and High-Efficiency Segment Anything Model},
  author={Yuan, Haobo and Li, Xiangtai and Qi, Lu and Zhang, Tao and Yang, Ming-Hsuan and Yan, Shuicheng and Loy, Chen Change},
  journal={arXiv preprint},
  year={2024}
}
```
## License <a name="license"></a>

This project is licensed under <a rel="license" href="https://github.com/HarborYuan/ovsam/blob/master/LICENSE">NTU S-Lab License 1.0</a>. Redistribution and use should follow this license.
