### Read first
__This is a forked 3DGRUT Tutorial and the main codes will not be updated__
This forked GitHub project is intented for folks who have little to know command-line knowledge and want to install, train, and view results from 3DGRUT. 

The section below is from the original GitHub page. Jump down to [Go to Dependencies and Installation](#-1-dependencies-and-installation) to get started. <br>
<br>
<br>

<p align="center">
  <img height="100" src="assets/3dgrut_logo.png">
</p>

---
<p align="center">
  <img width="100%" src="assets/nvidia-hq-playground.gif">
</p>

This repository provides the official implementations of **3D Gaussian Ray Tracing (3DGRT)** and **3D Gaussian Unscented Transform (3DGUT)**. Unlike traditional methods that rely on splatting, 3DGRT performs ray tracing of volumetric Gaussian particles instead. This enables support for distorted cameras with complex, time-dependent effects such as rolling shutters, while also efficiently simulating secondary rays required for rendering phenomena like reflection, refraction, and shadows. However, 3DGRT requires dedicated ray-tracing hardware and remains slower than 3DGS.

To mitigate this limitation, we also propose 3DGUT, which enables support for distorted cameras with complex, time-dependent effects within a rasterization framework, maintaining the efficiency of rasterization methods. By aligning the rendering formulations of 3DGRT and 3DGUT, we introduce a hybrid approach called **3DGRUT**. This technique allows for rendering primary rays via rasterization and secondary rays via ray tracing, combining the strengths of both methods for improved performance and flexibility.


> __3D Gaussian Ray Tracing: Fast Tracing of Particle Scenes__  
> [Nicolas Moenne-Loccoz*](https://www.linkedin.com/in/nicolas-moënne-loccoz-71040512/?original_referer=https%3A%2F%2Fwww%2Egoogle%2Ecom%2F&originalSubdomain=ca), [Ashkan Mirzaei*](https://ashmrz.github.io), [Or Perel](https://orperel.github.io/), [Riccardo De Lutio](https://riccardodelutio.github.io/), [Janick Martinez Esturo](https://jme.pub/),   
> [Gavriel State](https://www.linkedin.com/in/gavstate/?originalSubdomain=ca), [Sanja Fidler](https://www.cs.utoronto.ca/~fidler/), [Nicholas Sharp^](https://nmwsharp.com/), [Zan Gojcic^](https://zgojcic.github.io/) _(*,^ indicates equal contribution)_  
> _SIGGRAPH Asia 2024 (Journal Track)_  
> __[Project page](https://research.nvidia.com/labs/toronto-ai/3DGRT)&nbsp;/ [Paper](https://research.nvidia.com/labs/toronto-ai/3DGRT/res/3dgrt_compressed.pdf)&nbsp;/ [Video](https://research.nvidia.com/labs/toronto-ai/3DGRT/res/3dgrt_supplementary_video.mp4)&nbsp;/ [BibTeX](assets/3dgrt2024.bib)__

> __3DGUT: Enabling Distorted Cameras and Secondary Rays in Gaussian Splatting__  
> [Qi Wu*](https://wilsoncernwq.github.io/), [Janick Martinez Esturo*](https://jme.pub/), [Ashkan Mirzaei](https://ashmrz.github.io),   
> [Nicolas Moenne-Loccoz](https://www.linkedin.com/in/nicolas-moënne-loccoz-71040512/?original_referer=https%3A%2F%2Fwww%2Egoogle%2Ecom%2F&originalSubdomain=ca), [Zan Gojcic](https://zgojcic.github.io/)  _(* indicates equal contribution)_  
> _CVPR 2025 (Oral)_  
> __[Project page](https://research.nvidia.com/labs/toronto-ai/3DGUT)&nbsp;/ [Paper](https://research.nvidia.com/labs/toronto-ai/3DGUT/res/3DGUT_ready_main.pdf)&nbsp;/ [Video](https://research.nvidia.com/labs/toronto-ai/3DGUT/#supp_video)&nbsp;/ [BibTeX](assets/3dgut2025.bib)__


## 🔥 News
- ✅[2025/04] Support for image masks.
- ✅[2025/04] SparseAdam support.
- ✅[2025/04] MCMC densification strategy support.
- ✅[2025/04] Stable release [v1.0.0](https://github.com/nv-tlabs/3dgrut/releases/tag/v1.0.0) tagged.
- ✅[2025/03] Initial code release!
- ✅[2025/02] [3DGUT](https://research.nvidia.com/labs/toronto-ai/3DGUT/res/3DGUT_ready_main.pdf) was accepted to CVPR 2025!
- ✅[2024/08] [3DGRT](https://research.nvidia.com/labs/toronto-ai/3DGRT/res/3dgrt_compressed.pdf) was accepted to SIGGRAPH Asia 2024!

## Contents

- [🔥 News](#-news)
- [Contents](#contents)
- [🔧 1 Dependencies and Installation](#-1-dependencies-and-installation)
  - [Running with Docker](#running-with-docker)
- [2. Prepare your data](#2-prepare-your-data)
- [💻 3. Train 3DGRT or 3DGUT scenes](#-3-train-3dgrt-or-3dgut-scenes)
  - [Using image masks](#using-image-masks)
- [🎥 4. Rendering from Checkpoints](#-4-rendering-from-checkpoints)
  - [To visualize training progress interactively](#to-visualize-training-progress-interactively)
  - [To visualize a pre-trained checkpoint](#to-visualize-a-pre-trained-checkpoint)
- [📋 5. Evaluations](#-5-evaluations)
- [🛝 6. Interactive Playground GUI](#-6-interactive-playground-gui)
- [🎓 7. Citations](#-7-citations)
- [🙏 8. Acknowledgements](#-8-acknowledgements)

## 🔧 1 Dependencies and Installation
- CUDA 11.8+ Compatible System
- For good performance with 3DGRT, we recommend using an NVIDIA GPU with Ray Tracing (RT) cores.
- Currently, only Linux environments are supported by the included install script (Windows support coming soon!)

<details> 
<summary> NOTE: gcc versions >11 (expand for details)</summary>
</br>
Currently the codebase requires gcc <= 11.  If your machine uses the compiler gcc-12 or newer (i.e., in Ubuntu 24.04), you may need to install and use gcc-11. 

First, install gcc 11:
```sh
sudo apt-get install gcc-11 g++-11
```

Then run the install script with the optional `WITH_GCC11` flag, which additionally configures the conda environment to use gcc-11:
```sh
./install_env.sh 3dgrut WITH_GCC11
```
</details>

<details> 
<summary> NOTE: Blackwell GPU support</summary>
</br>
The current codebase uses CUDA 11.8, which is not compatible with the new Blackwell GPUs (e.g., RTX 5090) or GPUs with compute capability 10.0+.
An experimental build script has been kindly implemented by <a href="https://www.github.com/johnnynunez">@johnnynunez</a> to support Blackwell GPUs. To enable it:

Run the install script with both the `WITH_GCC11` flag and a CUDA version. Currently, only CUDA 12.8.1 is supported:
```sh
CUDA_VERSION=12.8.1 ./install_env.sh 3dgrut_cuda12 WITH_GCC11
```

To build the docker image, you can use the following command:
```sh
docker build --build-arg CUDA_VERSION=12.8.1 -t 3dgrut:cuda128 .
```
</details>

**NOTE: This seems to be a promising route to get it working on WSL2: [https://github.com/nv-tlabs/3dgrut/issues/40#issuecomment-2781067451](https://github.com/nv-tlabs/3dgrut/issues/40#issuecomment-2781067451)**

</br>

### Running with Conda (CUDA Toolkit 11.8/12.8)
If you have CUDA Toolkit 11.8 or 12.8, follow the original instructions

To set up the environment using conda, first clone the repository and run `./install_env.sh` script as:

```bash
git clone --recursive https://github.com/nv-tlabs/3dgrut.git
cd 3dgrut

# You can install each component step by step following install_env.sh
chmod +x install_env.sh
./install_env.sh 3dgrut
conda activate 3dgrut
```

### Running with Conda (CUDA Toolkit 12.0-12.6)
If you are not using CUDA Toolkit 11.8 or 12.8, you need to specify the install script to use 11.8 build tools.

To set up the environment using conda, first clone the repository and run `./install_env.sh` script as:
```
# Clone to a new folder
git clone --recursive https://github.com/nv-tlabs/3dgrut.git 3dgrut
cd 3dgrut

# Set the CUDA version that matches PyTorch
export CUDA_VERSION=11.8.0

# Create the environment manually first so CONDA_PREFIX exists
conda create -n 3dgrut_test python=3.11 -y
conda activate 3dgrut_test

# Update PATH so conda’s CUDA 11.8 takes priority
export PATH="$CONDA_PREFIX/bin:$PATH"

# Now install everything using the script
chmod +x install_env.sh
./install_env.sh 3dgrut_test
```

### Running with Docker
Ensure you have [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) intalled. 

Build the docker image:
```bash
git clone --recursive https://github.com/nv-tlabs/3dgrut.git
cd 3dgrut
docker build . -t 3dgrut
````

Run it:
```bash
xhost +local:root
docker run -v --rm -it --gpus=all --net=host --ipc=host -v $PWD:/workspace --runtime=nvidia -e DISPLAY 3dgrut
```
> [!NOTE]
> Remember to set DISPLAY environment variable if you are running on a remote server from command line.

## 2. Prepare your data

This section covers how to prepare your data for the colmap_3dgrt, colmap_3dgut, colmap_3dgrt_mcmc, and colmap_3dgut_mcmc config files. As more image types are supported, I will expand on this section.

Required software:
- COLMAP
- Imagemagick (for image downsampling)
<br>

The data format is expected to be in the same format as the original 3DGS project:
```
<location>
|---images
|   |---<image 0>
|   |---<image 1>
|   |---...
|---sparse
    |---0
        |---cameras.bin
        |---images.bin
        |---points3D.bin
```

However, I have not tested other COLMAP export formats but the Python code suggests camera and images txt files with a ply can be used as well.

### Downsampling Images
You may run out of memory if you have low VRAM and/or are using too many high resolution images. Downsampling will reduce the amount of VRAM needed and speed up training. I suggest keeping images around 2k-4k pixels on the longest dimension.

Step 1: Install Imagemagick if not already installed:
```
sudo apt update
sudo apt install imagemagick
```

Step 2: Make a downsample directory and downsample your images. This example is for half scale. For quarter, eighth scale, etc. call your folder images_4, images_8, etc. and change the resize value to 25%, 12.5%, etc.:
```
mkdir -p ./images_2
mogrify -path ./images_2 -resize 50% ./images/*.jpg
```

Your resulting folder should look like this:
```
|---images
|   |---<image 0>
|   |---<image 1>
|---images_2
|   |---<image 0>
|   |---<image 1>
```

### Running COLMAP
COLMAP is a specialized software for running structure from motion. This software will determine where each photo was taken in 3D space as well as build a 3D point cloud for you.

If you alread have it installed and are comfortable using it in terminal, follow these commands:
```
mkdir sparse

# Use images_2 if you want downsampled images
# Change to OPENCV_FISHEYE for fisheye lenses
# Remove --ImageReader.single_camera if using more than one camera
colmap feature_extractor \
    --database_path database.db \
    --image_path images \
    --ImageReader.camera_model SIMPLE_PINHOLE \
    --ImageReader.single_camera 1

colmap exhaustive_matcher \
    --database_path database.db

# Use images_2 if you want downsampled images
colmap mapper \
    --database_path database.db \
    --image_path images \
    --output_path sparse
```

For Newbies, the GUI path is much easier to follow.

Step 1: Install COLMAP - this will install COLMAP from Ubuntu's package manager. If you want the latest updates, consult COLMAP's install page on its website.
```
sudo apt update
sudo apt install colmap
```

Step 2: Launch the GUI and set up a new project
- Launch COLMAP using `colmap gui` in terminal. A GUI will launch. From there, select `File > New Project`
- Select `New` for the Database and create a new database file called `database.db` in your project's root folder (not the images folder)
- For images, select the `images` folder or `images_2` folder if it exist. Using the downsampled images will run faster and often yields better results!

Step 3: Run Feature Extraction
- Select `Processing > Feature Extraction`
- For Camera model select `PINHOLE`, `SIMPLE_PINHOLE`, or `OPENCV_FISHEYE` depending on your camera. I suggest unless you are using a fisheye camera, you should choose `SIMPLE_PINHOLE`
- Checkmark `Shared for all images` if you used one camera for the entire scene.
- Click Extract - this should only take a minute or two to run.

Step 4: Feature Matching
- Select `Processing > Feature Matching`
- For randomly captured images, use the `Exhaustive` tab, for images taken in sequence, use the `Sequential` tab.
- Leave parameters at default and click `Run` for large datasets this could take a while...and yes, it runs primarily on CPU.

Step 5: Reconstruction
- Select `Reconstruction > Start Reconstruction` - this can take a while...and again, it runs on CPU.
- You will see the scene incrementally build before your eyes. Grab popcorn and enjoy!

Step 6: Export
- Select `File > Export Model`
- In the export folder dialog box, create a new folder in your project folder called `sparse`. Within the newly created sparse folder, create another folder called `0`. Save your files into the 0 folder. Refer to my file structure reference above.

BOOM! You are ready to create your very own 3DGRUT scene!!!

<br>

## 💻 3. Train 3DGRT or 3DGUT scenes
There is a lot of confusing instructions in this section. I added a simplfied explainer at the top for you!

### How to train with 3DGRT (Ray-Traced Results)
Run this command for the base 3DGRT training:
```
python train.py --config-name apps/colmap_3dgrt.yaml \
    path=data/my_scene \
    out_dir=runs \
    experiment_name=my_scene_3dgrt \
    with_gui=True \
    dataset.downsample_factor=2 \
    export_ply.enabled=true
```

Run this command for the 3DGRT training with the MCMC densification strategy:
```
python train.py --config-name apps/apps/colmap_3dgrt_mcmc.yaml \
    path=data/my_scene \
    out_dir=runs \
    experiment_name=my_scene_3dgrt \
    with_gui=True \
    dataset.downsample_factor=2 \
    export_ply.enabled=true
```
**NOTES:** 
- replace "my_scene" withe the actual path to your project.
- Experiment name, you can call it whatever you want as long as you don't have space.
- `with_gui=True` will launch the GUI during training. You cannot close the GUI once training starts!
- Remove `dataset.downsample_factor=2` if you are not downsampling the data

<br>

### How to train with 3DGUT (Ray-Traced > Tranformed to GS Results - FASTER RENDERING!!!)
Run this command for the base 3DGRT training:
```
python train.py --config-name apps/colmap_3dgut.yaml \
    path=data/my_scene \
    out_dir=runs \
    experiment_name=my_scene_3dgrt \
    with_gui=True \
    dataset.downsample_factor=2 \
    export_ply.enabled=true
```

Run this command for the 3DGRT training with the MCMC densification strategy:
```
python train.py --config-name apps/apps/colmap_3dgut_mcmc.yaml \
    path=data/my_scene \
    out_dir=runs \
    experiment_name=my_scene_3dgrt \
    with_gui=True \
    dataset.downsample_factor=2 \
    export_ply.enabled=true
```

**NOTES:** 
- replace "my_scene" withe the actual path to your project.
- Experiment name, you can call it whatever you want as long as you don't have space.
- `with_gui=True` will launch the GUI during training. You cannot close the GUI once training starts!
- Remove `dataset.downsample_factor=2` if you are not downsampling the data

<br>

### The original instructions from the project

We provide different configurations for training using 3DGRT and 3DGUT models on common benchmark datasets. 
For example you can download [NeRF Synthetic dataset](https://www.kaggle.com/datasets/nguyenhung1903/nerf-synthetic-dataset), 
[MipNeRF360 dataset](https://jonbarron.info/mipnerf360/) or [ScanNet++](https://kaldir.vc.in.tum.de/scannetpp/), 
and then run one of the following commands:

```bash
# Train Lego with 3DGRT & 3DGUT
python train.py --config-name apps/nerf_synthetic_3dgrt.yaml path=data/nerf_synthetic/lego out_dir=runs experiment_name=lego_3dgrt
python train.py --config-name apps/nerf_synthetic_3dgut.yaml path=data/nerf_synthetic/lego out_dir=runs experiment_name=lego_3dgut

# Train Bonsai
python train.py --config-name apps/colmap_3dgrt.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgrt dataset.downsample_factor=2 
python train.py --config-name apps/colmap_3dgut.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgut dataset.downsample_factor=2 

# Train Scannet++
python train.py --config-name apps/scannetpp_3dgrt.yaml path=data/scannetpp/0a5c013435/dslr out_dir=runs experiment_name=0a5c013435_3dgrt
python train.py --config-name apps/scannetpp_3dgut.yaml path=data/scannetpp/0a5c013435/dslr out_dir=runs experiment_name=0a5c013435_3dgut
```

We also support MCMC densification strategy and selective Adam optimizer for 3DGRT and 3DGUT. 

To enable MCMC, use:
```bash
python train.py --config-name apps/colmap_3dgrt_mcmc.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgrt dataset.downsample_factor=2 
python train.py --config-name apps/colmap_3dgut_mcmc.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgut dataset.downsample_factor=2 
```

To enable selective Adam, use:
```bash
python train.py --config-name apps/colmap_3dgrt.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgrt dataset.downsample_factor=2 optimizer.type=selective_adam
python train.py --config-name apps/colmap_3dgut.yaml path=data/mipnerf360/bonsai out_dir=runs experiment_name=bonsai_3dgut dataset.downsample_factor=2 optimizer.type=selective_adam
```

If you use MCMC and Selective Adam in your research, please cite [3dgs-mcmck](https://github.com/ubc-vision/3dgs-mcmc), [taming-3dgs](https://github.com/humansensinglab/taming-3dgs),
and [gSplat](https://github.com/nerfstudio-project/gsplat/tree/main) library from which the code was adopted (links to the code are provided in the source files).

> [!Note] 
> For ScanNet++, we expect the dataset to be preprocessed following [FisheyeGS](https://github.com/zmliao/Fisheye-GS?tab=readme-ov-file#prepare-training-data-on-scannet-dataset)'s method.

> [!Note]  
> If you're running from PyCharm IDE, enable rich console through:
> Run Configuration > Modify Options > Emulate terminal in output console*

### Using image masks
In order to use image masks, you need to provide a mask for each image in the dataset. The mask is a grayscale image (0s and 1s) that masks out the parts of the image that should not be used during training, i.e. all the pixels with value 0 will be ignored in the loss computation. 

The provided masks should have the same resolution as their corresponding images and be stored in the same folder with the same name but with `_mask.png` extension. For example, to mask out the parts of the image `path-to-image/image.jpeg`, the mask should be stored at `path-to-image/image_mask.png`.

**NOTE**: The masks are only used for loss computation and not for computing the metrics.

## 🎥 4. Rendering from Checkpoints
Evaluate Checkpoint with Splatting / OptiX Tracer / Torch
```bash
python render.py --checkpoint runs/lego/ckpt_last.pt --out-dir outputs/eval
```


### To visualize training progress interactively
```bash
python train.py --config-name apps/nerf_synthetic_3dgut.yaml path=data/nerf_synthetic/lego with_gui=True 
```

### To visualize a pre-trained checkpoint
```bash
python train.py --config-name apps/nerf_synthetic_3dgut.yaml path=data/nerf_synthetic/lego with_gui=True test_last=False export_ingp.enabled=False resume=runs/lego/ckpt_last.pt 
```
> [!NOTE]
> Remember to set DISPLAY environment variable if you are running on a remote server from command line.

On start up, you might see a black screen, but you can use the GUI to navigate to correct camera views:
<img src="assets/train_gui_initial.jpg" height="400"/> 
<img src="assets/render_lego.jpg" height="400"/>

## 📋 5. Evaluations

We provide scripts to reproduce results reported in publications.

```bash
# Training
bash ./benchmark/mipnerf360_3dgut.sh <config-yaml>
# Rendering
bash ./benchmark/mipnerf360_3dgut_render.sh <results-folder>
```

<details>
<summary><strong><a name="grt-benchmark">3DGRT Results Produced on RTX 5090</a></strong></summary>
<br/>


**NeRF Synthetic Dataset**

```bash
bash ./benchmark/nerf_synthetic.sh apps/nerf_synthetic_3dgrt.yaml
bash ./benchmark/nerf_synthetic_render.sh results/nerf_synthetic
```

|            | PSNR	  | SSIM	| Train (s) |	FPS |
|------------|--------|-------|-------|------|
| Chair      | 35.85	| 0.988	| 556.4	| 299 |
| Drums      | 25.87	| 0.953	| 462.8	| 389 |
| Ficus      | 36.57	| 0.989	| 331.0	| 465 |
| Hotdog     | 37.88	| 0.986	| 597.0	| 270 |
| Lego       | 36.70	| 0.985	| 469.8	| 360 |
| Materials  | 30.42	| 0.962	| 463.3	| 347 |
| Mic        | 35.90	| 0.992	| 443.4	| 291 |
| Ship       | 31.73	| 0.909	| 510.7	| 360 |
| *Average*  | 33.87	| 0.971	| 479.3	| 347 |


**MipNeRF360 Dataset**

```bash
bash ./benchmark/mipnerf360.sh apps/colmap_3dgrt.yaml
bash ./benchmark/mipnerf360_render.sh results/mipnerf360
```
|           | PSNR  | SSIM	| Train (s) |	FPS |
|-----------|-------|-------|-------|------|
| Bicycle   | 24.85	| 0.748	| 2335	| 66 |
| Bonsai    | 31.95	| 0.942	| 3383	| 72 |
| Counter   | 28.47	| 0.905	| 3247	| 62 |
| Flowers   | 21.42	| 0.615	| 2090	| 86 |
| Garden    | 26.97	| 0.852	| 2253	| 70 |
| Kitchen   | 30.13	| 0.921	| 4837	| 39 |
| Room      | 30.35	| 0.911	| 2734	| 73 |
| Stump     | 26.37	| 0.770	| 1995	| 73 |
| Treehill  | 22.08	| 0.622	| 2413	| 68 |
| *Average* | 27.22	| 0.817	| 2869	| 68 |

</details>


<details>
<summary><strong><a name="gut-benchmark">3DGUT Results Produced on RTX 5090</a></strong></summary>
<br/>

**NeRF Synthetic Dataset**

```bash
bash ./benchmark/nerf_synthetic.sh paper/3dgut/unsorted_nerf_synthetic.yaml
bash ./benchmark/nerf_synthetic_render.sh results/nerf_synthetic
```

|            | PSNR	  | SSIM	| Train (s) |	FPS |
|------------|--------|-------|-------|------|
| Chair      | 35.61	| 0.988	| 265.6	| 599  |
| Drums      | 25.99	| 0.953	| 254.1	| 694  |
| Ficus      | 36.43	| 0.988	| 183.5	| 1053 |
| Hotdog     | 38.11	| 0.986	| 184.8	| 952  |
| Lego       | 36.47	| 0.984	| 221.7	| 826  |
| Materials  | 30.39	| 0.960	| 194.3	| 1000 |
| Mic        | 36.32	| 0.992	| 204.7	| 775  |
| Ship       | 31.72	| 0.908	| 208.5	| 870  |
| *Average*  | 33.88	| 0.970	| 214.6	| 846  |


**MipNeRF360 Dataset**

GS Strategy, Unsorted

```bash
bash ./benchmark/mipnerf360.sh paper/3dgut/unsorted_colmap.yaml
bash ./benchmark/mipnerf360_render.sh results/mipnerf360
```

|           | PSNR  | SSIM	| Train (s) |	FPS |
|-----------|-------|-------|-------|------|
| Bicycle   | 25.01	| 0.759	| 949.8	| 275 |
| Bonsai    | 32.46	| 0.945	| 485.3	| 362 |
| Counter   | 29.14	| 0.911	| 484.5	| 380 |
| Flowers   | 21.45	| 0.612	| 782.0	| 253 |
| Garden    | 27.18	| 0.856	| 810.2	| 316 |
| Kitchen   | 31.16	| 0.928	| 664.8	| 275 |
| Room      | 31.63	| 0.920	| 448.8	| 370 |
| Stump     | 26.50	| 0.773	| 742.6	| 319 |
| Treehill  | 22.35	| 0.627	| 809.6	| 299 |
| *Average* | 27.43	| 0.815	| 686.4	| 317 |


MCMC Strategy, Unsorted

```bash
bash ./benchmark/mipnerf360.sh paper/3dgut/unsorted_colmap_mcmc.yaml
bash ./benchmark/mipnerf360_render.sh results/mipnerf360
```
|           | PSNR  | SSIM	| Train (s) |	FPS |
|-----------|-------|-------|-------|------|
| Bicycle   | 25.31	| 0.765	| 502.3	| 361 |
| Bonsai    | 32.51	| 0.947	| 670.6	| 274 |
| Counter   | 29.40	| 0.916	| 752.7	| 254 |
| Flowers   | 21.86	| 0.616	| 553.3	| 298 |
| Garden    | 27.06	| 0.852	| 512.7	| 360 |
| Kitchen   | 31.71	| 0.930	| 739.6	| 258 |
| Room      | 32.04	| 0.928	| 643.7	| 313 |
| Stump     | 27.06	| 0.795	| 487.0	| 339 |
| Treehill  | 23.11	| 0.650	| 508.6	| 365 |
| *Average* | 27.78	| 0.822	| 596.7	| 308 |



GS Strategy, Unsorted, Sparse Adam

|           | PSNR   | SSIM  | Train (s) | FPS |
|-----------|--------|-------|-----------|-----|
| Bicycle   | 25.04  | 0.759 | 835.2     | -   |
| Bonsai    | 32.63  | 0.945 | 457.1     | -   |
| Counter   | 29.12  | 0.911 | 468.8     | -   |
| Flowers   | 21.55  | 0.614 | 741.7     | -   |
| Garden    | 27.12  | 0.855 | 757.4     | -   |
| Kitchen   | 31.37  | 0.929 | 639.3     | -   |
| Room      | 31.72  | 0.921 | 415.2     | -   |
| Stump     | 26.58  | 0.774 | 695.7     | -   |
| Treehill  | 22.30  | 0.625 | 749.8     | -   |
| *Average* | 27.49  | 0.815 | 640.0     | -   |


**Scannet++ Dataset**

```bash
bash ./benchmark/scannetpp.sh paper/3dgut/unsorted_scannetpp.yaml
bash ./benchmark/scannetpp_render.sh results/scannetpp
```
> [!Note]
> We followed [FisheyeGS](https://github.com/zmliao/Fisheye-GS?tab=readme-ov-file#prepare-training-data-on-scannet-dataset)'s convention to prepare the dataset for fair comparisons

|           | PSNR  | SSIM	| Train (s) |	FPS |
|-----------|-------|-------|-------|------|
| 0a5c013435 | 29.67	| 0.930	| 292.3	| 389 | 
| 8d563fc2cc | 26.88	| 0.912	| 286.1	| 439 | 
| bb87c292ad | 31.58	| 0.941	| 316.9	| 448 | 
| d415cc449b | 28.12	| 0.871	| 394.6	| 483 | 
| e8ea9b4da8 | 33.47	| 0.954	| 280.8	| 394 | 
| fe1733741f | 25.60	| 0.858	| 355.8	| 450 | 
| *Average*  | 29.22	| 0.911	| 321.1	| 434 |

</details>

## 🛝 6. Interactive Playground GUI

The playground allows interactive exploration of pretrained scenes, with raytracing effects such as inserted objects, 
reflections, refractions, depth of field, and more.

Run the playground UI to visualize a pretrained scene with:
```
python playground.py --gs_object <ckpt_path>
```

See [Playground README](threedgrut_playground/README.md) for details.

*Update (2025/04): The playground engine is now exposed and remote rendering is supported,
see README for details.*


## 🎓 7. Citations

```
@article{loccoz20243dgrt,
    author = {Nicolas Moenne-Loccoz and Ashkan Mirzaei and Or Perel and Riccardo de Lutio and Janick Martinez Esturo and Gavriel State and Sanja Fidler and Nicholas Sharp and Zan Gojcic},
    title = {3D Gaussian Ray Tracing: Fast Tracing of Particle Scenes},
    journal = {ACM Transactions on Graphics and SIGGRAPH Asia},
    year = {2024},
}
```

```
@article{wu20253dgut,
    title={3DGUT: Enabling Distorted Cameras and Secondary Rays in Gaussian Splatting},
    author={Wu, Qi and Martinez Esturo, Janick and Mirzaei, Ashkan and Moenne-Loccoz, Nicolas and Gojcic, Zan},
    journal = {Conference on Computer Vision and Pattern Recognition (CVPR)},
    year={2025}
}
```

## 🙏 8. Acknowledgements

We sincerely thank our colleagues for their valuable contributions to this project.

Hassan Abu Alhaija, Ronnie Sharif, Beau Perschall and Lars Fabiunke for assistance with assets.
Greg Muthler, Magnus Andersson, Maksim Eisenstein, Tanki Zhang, Nathan Morrical, Dietger van Antwerpen and John Burgess for performance feedback.
Thomas Müller, Merlin Nimier-David, and Carsten Kolve for inspiration and pointers. 
Ziyu Chen, Clement Fuji-Tsang, Masha Shugrina, and George Kopanas for technical & experiment assistance,
and to Ramana Kiran and Shailesh Mishra for typo fixes.
