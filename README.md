# TetraTSDF

Volumetric TSDF regression with tetrahedral representation for 3D human body prediction from single color image.


## Requirements

In this code, we used 2 versions of the python.

Dataset and mesh generation

- python 2.7

- binascii

- pymesh

- pyflann

Network training and testing

- python 3.6

- CUDA 10.0  (Our code maybe works on other CUDA versions)

- keras with TensorFlow background

Evaluation

- python 3.6 and 2.7

- shutil

- Open3D


## Dataset generation

- Coarse human outer shell

We need to prepare coarse human outer shell for dataset generation.

In our paper, we generated it based on male SMPL model by using Blender.

(the coarse human is already contained in the code)

if you want to use another coarse human (generated by yourself), see the README in ./coarsehuman folder

### Articulated dataset
1. Download Articulated dataset from http://people.csail.mit.edu/drdaniel/mesh_animation/ to somewhere in your computer  
We only used crane, bouncing, jumping, march 2 sequence that we could get correct SMPL ground truth pose parameters and weights for re-pose.

2. Unzip images and meshes folder  

```
[Articulated dataset root]  
      ├─D_bouncing
      ├─D_march
      ├─I_crane
      └─I_jumping
              ├─images
              └─meshes
```

3. Generate TSDF

```
cd [TetraTSDF root]/coarsehuman/gendata_Articulated
python genTSDF.py
```

4. Crop images into 256x256  
Execute cropimg.py on "images" directory in each sequence
```
python cropimg.py --imgdir [path to images directory] --skeletondir ./smplfitresults_articulated/[sequence]/openpose_joints
```
We used OpenPose joints to centering & scaling the images (The person in the image covers about 65% of the image height).
Articulated dataset contains images from 8 views for each sequence. We selected one of them for training.

## Network training
Run main.py with python3.6 environment    
```
cd [TetraTSDF root]/network
python main.py --mode 0 --datasetroot [Path to Articulated dataset root]
```

## Testing
```
cd [TetraTSDF root]/network
python main.py --mode 1 (--imgpath_pred [Path to test images(file or dir)])
```
TSDF data (.bin) is saved to [TetraTSDF root]/network/result  
Then execute TSDF2mesh.py to visualize the tsdf
```
(python2.7 environment)
cd [TetraTSDF root]/visualize
python TSDF2mesh.py (--tsdfpath [path to tsdf] --parampath [path to smplparam])
```

## Evaluation
```
cd [TetraTSDF root]/network
python main.py --mode 2 --datasetroot [Path to Articulated dataset root]
```
Then execute TSDF2mesh.py to visualize the tsdf
```
(python2.7 environment)
cd [TetraTSDF root]/visualize
python TSDF2chamfer.py --TSDFdir_pred ../network/for_evaluation/TSDF_pred --TSDFdir_GT ../network/for_evaluation/TSDF_GT --paramdir_pred ../network/for_evaluation/params --paramdir_GT ../network/for_evaluation/params
```

## Reference
This work is to be used for educational and research purpose only. If you use part of this work, you are required to refer our paper:

```
@inproceedings{Onizuka2020,
  title={TetraTSDF: 3D human reconstruction from a single image with a tetrahedral outer shell},
  author={Onizuka, Hayato and Haiyrci, Zehra and Thomas, Diego and Sugimoto, Akihiro and Uchiyama, Hideaki and Taniguchi, Rin-Ichiro},
  booktitle={Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition},
  pages={--},
  year={2020}
}
```

## Issues
Since our system has been trained on small dataset, the generalizability is very low. To have more generalizability, our system needs more dataset.

