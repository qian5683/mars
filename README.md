<div align="center"><h2>MARS: An Instance-aware, Modular and Realistic Simulator for Autonomous Driving</h1></div>

<p align="center">
    <!-- community badges -->
    <a href="https://open-air-sun.github.io/mars/"><img src="https://img.shields.io/badge/Project-Page-ffa"/></a>
    <!-- doc badges -->
    <a href='https://github.com/OPEN-AIR-SUN/mars'>
        <img src='https://img.shields.io/badge/arXiv-Page-aff'>
    </a>
    <a href='https://open-air-sun.github.io/mars/static/data/MARS-poster.pdf'>
        <img src='https://img.shields.io/badge/Poster-PDF-pink' />
    </a>
    <!-- license badge -->
    <a href="https://github.com/xBeho1der/mars-release/blob/main/LICENSE">
        <img alt="License" src="https://img.shields.io/badge/License-Apache_2.0-blue.svg">
    </a>
</p>
<div align="center"><h4>CICAI 2023 Best Paper Runner-up Award</h4></div>
<div align="center">
  <img alt="" src="https://github.com/xBeho1der/mars-release/assets/7344146/8c2b525d-84b4-4e53-8a33-ad6f5e77b149">
</div>

<div align="center"><h4>For business inquiries, please contact us at <a href="mailto:zhaohao@air.tsinghua.edu.cn">zhaohao@air.tsinghua.edu.cn</a>.</h4></div>

## 1. Installation: Setup the environment

### Prerequisites

You must have an NVIDIA video card with CUDA installed on the system. This library has been tested with version 11.3/11.7 of CUDA. You can find more information about installing CUDA [here](https://docs.nvidia.com/cuda/cuda-quick-start-guide/index.html).

#### Create environment

Nerfstudio requires `python >= 3.7`. We recommend using conda to manage dependencies. Make sure to install [Conda](https://docs.conda.io/en/latest/miniconda.html) before proceeding.

```bash
conda create --name nerfstudio -y python=3.8
conda activate nerfstudio
python -m pip install --upgrade pip
pip install --upgrade pip setuptools
```

#### Installation

This section will walk you through the installation process. Our system is dependent on the <a href="https://github.com/nerfstudio-project/nerfstudio">nerfstudio</a> project.

1. Install [tiny-cuda-nn](https://github.com/NVlabs/tiny-cuda-nn) firstly.
2. Install MARS locally with:

```bash
git clone git@github.com:OPEN-AIR-SUN/mars.git
cd mars/nerfstudio
pip install -e .[dev]         # install nerfstudio and its dependencies
cd ..
pip install -e .
ns-install-cli                # optional, only for tab completion
```

## 2. Training from Scratch

The following will train a MARS model.

Our repository provides dataparser for KITTI and vKITTI2 datasets, for your own data, you can write your own dataparser or convert your own dataset to the format of the provided datasets.

### From Datasets

#### Data Preparation

The data used in our experiments should contain both the pose parameters of cameras and object tracklets. The camera parameters include the intrinsics and the extrinsics. The object tracklets include the bounding box poses, types, ids, etc. For more information, you can refer to KITTI-MOT or vKITTI2 datasets below.

#### KITTI

The [KITTI-MOT](https://www.cvlibs.net/datasets/kitti/eval_tracking.php) dataset should look like this:

```
.(KITTI_MOT_ROOT)
├── panoptic_maps                    # (Optional) panoptic segmentation from KITTI-STEP dataset.
│   ├── colors
│   │   └── sequence_id.txt
│   ├── train
│   │   └── sequence_id
│   │       └── frame_id.png
└── training
    ├── calib
    │   └── sequence_id.txt
    ├── completion_02                # (Optional) depth completion
    │   └── sequence_id
    │       └── frame_id.png
    ├── completion_03
    │   └── sequence_id
    │       └── frame_id.png
    ├── image_02
    │   └── sequence_id
    │       └── frame_id.png
    ├── image_03
    │   └── sequence_id
    │       └── frame_id.png
    ├── label_02
    │   └── sequence_id.txt
    └── oxts
        └── sequence_id.txt
```

> We download the KITTI-STEP annotations and generate the panoptic segmentation maps for KITTI-MOT dataset. You can download the demo panoptic maps [here](https://drive.google.com/drive/folders/1obAyq1jlHbyA9CS9Rg66N3YyI_sjpfGB?usp=drive_link) and put them in the `KITTI-MOT` directory, or you can visit the official website of [KITTI-STEP](https://www.cvlibs.net/datasets/kitti/eval_step.php) for more information.

To train a reconstruction model, you can use the following command:

```bash
ns-train nsg-kitti-car-depth-recon --data /data/kitti-MOT/training/image_02/0006
```

or if you want to use the Python script (please refer to the `launch.json` file in the `.vscode` directory):

```bash
python nerfstudio/nerfstudio/scripts/train.py nsg-kitti-car-depth-recon --data /data/kitti-MOT/training/image_02/0006
```

#### vKITTI2

Your data structure should look like this:

```
.(vKITTI2_ROOT)
└── sequence_id
    └── scene_name
        ├── bbox.txt
        ├── colors.txt
        ├── extrinsic.txt
        ├── info.txt
        ├── instrinsic.txt
        ├── pose.txt
        └── frames
            ├── depth
            │   ├── Camera_0
            │   │   └── frame_id.png
            │   └── Camera_1
            │   │   └── frame_id.png
            ├── instanceSegmentation
            │   ├── Camera_0
            │   │   └── frame_id.png
            │   └── Camera_1
            │   │   └── frame_id.png
            ├── classSegmentation
            │   ├── Camera_0
            │   │   └── frame_id.png
            │   └── Camera_1
            │   │   └── frame_id.png
            └── rgb
                ├── Camera_0
                │   └── frame_id.png
                └── Camera_1
                    └── frame_id.png
```

To train a reconstruction model, you can use the following command:

```bash
ns-train nsg-vkitti-car-depth-recon --data /data/vkitti/Scene06/clone
```

or if you want to use the python script:

```bash
python nerfstudio/nerfstudio/scripts/train.py nsg-vkitti-car-depth-recon --data /data/vkitti/Scene06/clone
```

#### Your Own Data

For your own data, you can refer to the above data structure and write your own dataparser, or you can convert your own dataset to the format of the dataset above.

### From Pre-Trained Model

Our model uses nerfstudio as the training framework, we provide the reconstruction and novel view synthesis tasks checkpoints.

Our pre-trained model is uploaded to Google Drive, you can refer to the below table to download the model.


<center>
<table class="tg">
<thead>
  <tr>
    <th class="tg-9wq8">Dataset</th>
    <th class="tg-9wq8">Setting</th>
    <th class="tg-0lax">PSNR</th>
    <th class="tg-0lax">SSIM</th>
    <th class="tg-9wq8">Download</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-9wq8" rowspan="4">KITTI-MOT</td>
    <td class="tg-9wq8">Reconstruction</td>
    <td class="tg-0lax">29.06</td>
    <td class="tg-0lax">0.885</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1g0eoq4QerA4kq21Nmoq9osl2RsZk-cYM/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8">Novel View Synthesis 75%</td>
    <td class="tg-0lax">24.23</td>
    <td class="tg-0lax">0.845</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1NVBlmugj8W3Hdz-YaxLQlwr0R9N-FoC1/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8">Novel View Synthesis 50%</td>
    <td class="tg-0lax">24.00</td>
    <td class="tg-0lax">0.801</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1xc7aLzU76gAgoOTuJlOYJ3LgoeLZOxSC/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8">Novel View Synthesis 25%</td>
    <td class="tg-0lax">23.23</td>
    <td class="tg-0lax">0.756</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1jf8hT5603u1wO1JqE3gCJSVqHuK8oi99/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8" rowspan="3">Vitural KITTI-2</td>
    <td class="tg-9wq8">Novel View Synthesis 75%</td>
    <td class="tg-0lax">29.79</td>
    <td class="tg-0lax">0.917</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/14uZ0Y-SyzBohUfKby72gibrhGo5i2FIZ/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8">Novel View Synthesis 50%</td>
    <td class="tg-0lax">29.63</td>
    <td class="tg-0lax">0.916</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1IzfYP9bpqKr94Q93JtpOHb6YPo0WrRyq/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
  <tr>
    <td class="tg-9wq8">Novel View Synthesis 25%</td>
    <td class="tg-0lax">27.01</td>
    <td class="tg-0lax">0.887</td>
    <td class="tg-9wq8"><a href="https://drive.google.com/file/d/1W8JrjJ9izg3r3sgbK0jmTC9uur-pcUx2/view?usp=drive_link" target="_blank" rel="noopener noreferrer">model</a></td>
  </tr>
</tbody>
</table>
</center>


You can use the following command to train a model from a pre-trained model:

```bash
ns-train nsg-kitti-car-depth-recon --data /data/kitti-MOT/training/image_02/0006 --load-dir outputs/experiment_name/method_name/timestamp/nerfstudio
```

### Model Configs

Our modular framework supports combining different architectures for each node by modifying model configurations. Here's an example of using Nerfacto for background and our category-level object model:

```python
model=SceneGraphModelConfig(
    background_model=NerfactoModelConfig(),
    object_model_template=CarNeRFModelConfig(_target=CarNeRF),
    object_representation="class-wise",
    object_ray_sample_strategy="remove-bg",
)
```

For more information, please refer to our provided configurations at `nsg/cicai_configs.py`. We use wandb for logging by default, you can also specify other viewers (tensorboard/nerfstudio-viewer supported) with the `--vis` config. Please refer to the nerfstudio documentation for details.

## Citation

You can find our paper [here](https://open-air-sun.github.io/mars/static/data/CICAI_MARS_FullPaper.pdf). If you use this library or find the repo useful for your research, please consider citing:

```
@article{wu2023mars,
  author    = {Wu, Zirui and Liu, Tianyu and Luo, Liyi and Zhong, Zhide and Chen, Jianteng and Xiao, Hongmin and Hou, Chao and Lou, Haozhe and Chen, Yuantao and Yang, Runyi and Huang, Yuxin and Ye, Xiaoyu and Yan, Zike and Shi, Yongliang and Liao, Yiyi and Zhao, Hao},
  title     = {MARS: An Instance-aware, Modular and Realistic Simulator for Autonomous Driving},
  journal   = {CICAI},
  year      = {2023},
}
```

## Acknoledgement

Part of our code is borrowed from [Nerfstudio](https://nerf.studio). This project is sponsored by Tsinghua-Toyota Joint Research Fund (20223930097) and Baidu Inc. through Apolo-AIR Joint Research Center.

## Notice

This open-sourced version will be actively maintained and regularly updated. For more features, please contact us for a commercial version.