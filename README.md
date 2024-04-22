# CAPRI-Net
This is an updated and adapted version of the code for the CAPRI-Net(CAPRI-Net: Learning Compact CAD Shapes with Adaptive Primitive Assembly).
You can find more references and info in the official [project page](https://fenggenyu.github.io/capri.html).

### Environment Installation

The following code was tested on Ubuntu 22.04 with CUDA 12.1.
Create a new conda environment with python 3.10 and activate it:

    conda create -n capri_env python=3.10
    conda activate capri_env

First install the correct torch version, depending on your CUDA version. 

    pip install torch --index-url https://download.pytorch.org/whl/cu121

Next, you need to install the right version of the PyMesh library. The original repository is compatible only with python 3.7, so we will
modify the version released in [this fork](https://github.com/nuvolos-cloud/PyMesh). 
Find [here the latest release](https://github.com/nuvolos-cloud/PyMesh/releases/tag/v0.3.1). Copy the link to the wheel file and install it with pip.

    pip install https://github.com/nuvolos-cloud/PyMesh/releases/download/v0.3.1/pymesh2-0.3.1-cp310-cp310-linux_x86_64.whl

Finally, install all the other requirements:

    pip install -r requirements.txt


## Datasets and Pre-trained weights
Depending on which experiment you want to run, you need to download the corresponding dataset and pre-trained weights.

For the ABC processed data, you can find them [here](https://drive.google.com/file/d/1DqyZw8zpCiEJMSYp6J6IocMB_IYMwYL1/view?usp=share_link) and pre-trained weights from [here](https://drive.google.com/drive/folders/1Mh5ngnlhi1OqNh0DG1KpZhAQKn5dNa7M?usp=sharing).

For Shapenet [processed data](https://drive.google.com/file/d/1rANlqwGGBqY5Ih0GHhAd3VG_SZlhhOmi/view?usp=sharing) and [pre-trained weights](https://drive.google.com/file/d/1RU1IY_HOHDhc9APb9r4GcsxZkiwmJkCc/view?usp=sharing).

Once downloaded, extract the data files in the data folder of the repository.
The pre-trained weights need to be extracted at the root of the repository. They are of different types, depending on the experiment you want to run. For example, "shapenet_voxel" is the pre-trained weights for the voxel experiment on the Shapenet dataset. Once extracted, change the "DataSource" path in the specs.json file to point to the correct data folder. For example, if the repository is on the home of the user, the path should be "/home/user/CAPRI-Net/data/shapenet_release".


### Usage

Pre-training the network:
```
python train.py -e {experiment_name} -g {gpu_id} -p 0
```

Fine-tuning the network.
For voxel input
```
python fine-tuning.py -e {experiment_name} -g {gpu_id} --test --voxel --start {start_index} --end {end_index}
```
For point cloud input, please change --voxel to --surface, for example:
```
python fine-tuning.py -e {experiment_name} -g {gpu_id} --test --surface --start 0 --end 1
```

Testing fine-tuned model for each shape, example is below:
```
python test.py -e {experiment_name} -g {gpu_id} -p 2 -c best_stage2 --test --voxel --start 0 --end 1 
```
If you want to get the CSG output as Figure 3 of our paper, please add --csg to above command.

Testing for pre-trained model, example is below:
```
python test_pretrain.py -e {experiment_name} -g {gpu_id} -p 0 -c initial --test --voxel --start 0 --end 1 --mc_threshold 0.5
```


### Example

If you want the CSG output for shape 1000 in the Shapenet dataset, using the voxel model and the first available gpu, first run
```
python fine-tuning.py -e shapenet_voxel -g 0 --test --voxel --start 1000 --end 1001
```
and then run
```
python test.py -e shapenet_voxel -g 0 -p 2 -c best_stage2 --test --voxel --start 1000 --end 1001 --csg
```
The parameters of the primitives and the CSG tree will be saved in the "csg_parts" folder. 
Note that if the folder already exists, the program will return an error. You can either delete the folder or change the name of the folder in the code.

## Citation
If you use this code, please cite the following paper.
```
@InProceedings{Yu_2022_CVPR,
    author    = {Yu, Fenggen and Chen, Zhiqin and Li, Manyi and Sanghi, Aditya and Shayani, Hooman and Mahdavi-Amiri, Ali and Zhang, Hao},
    title     = {CAPRI-Net: Learning Compact CAD Shapes With Adaptive Primitive Assembly},
    booktitle = {Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    month     = {June},
    year      = {2022},
    pages     = {11768-11778}
}
```

## Code Reference

The framework of this repository is adopted from [DeepSDF](https://github.com/facebookresearch/DeepSDF)
