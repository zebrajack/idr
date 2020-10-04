# Multiview Neural Surface Reconstruction with Implicit Lighting and Material

### [Project Page](https://lioryariv.github.io/idr/) | [Paper](https://arxiv.org/abs/2003.09852) | [Data](https://www.dropbox.com/sh/5tam07ai8ch90pf/AADniBT3dmAexvm_J1oL__uoa)

<div align="center">
	<video width="100%" playsinline="" autoplay="" loop="" preload="" muted="">
		<source src="media/teaser.mp4" type="video/mp4">
	</video>
</div>

This repository contains an implementation to the NeurIPS 2020 paper <a href="https://arxiv.org/abs/2003.09852">Multiview Neural Surface Reconstruction with Implicit Lighting and Material</a>.

The paper introduce Implicit Differentiable Renderer (IDR): a neural network architecture that simultaneously learns the 3D geometry, appearance and cameras from a set of 2D images.
IDR able to produce high fidelity 3D surface reconstruction, by disentangling geometry and appearance, learned solely from masked 2D images and rough camera estimates.


## Installation Requirmenets
The code is compatible with python 3.7 and pytorch 1.2. In addition, the following packages are required:  
numpy, pyhocon, plotly, scikit-image, trimesh, imageio, opencv, torchvision.

You can create an anaconda environment called `idr` with the required dependencies by runnig:
```
conda env create -f environment.yml
conda activate idr
```

## Usage
### Multiview 3D reconstruction
#### Data
We apply our multiview surface reconstruction model to real 2D images from the <a href="http://roboimagedata.compute.dtu.dk/?page_id=36" target="_blank">DTU MVS repository</a>. 
The 15 scans data, including the manually annotated masks and the noisy initializations for the trainable cameras setup, can be download using:
```
bash data/download_data.sh 
```


We used our method to generate 3D reconstructions in two different setups:
#### Training with fixed ground truth cameras
For training IDR run:
```
cd ./code
python training/exp_runner.py --conf ./confs/dtu_fixed_cameras.conf --scan_id SCAN_ID
```
where SCAN_ID is the id of the DTU scene to reconstruct.

Then, to produce the meshed surface, run:
```
cd ./code
python evaluation/eval.py  --conf ./confs/dtu_fixed_cameras.conf --scan_id SCAN_ID --checkpoint CHECKPOINT [--eval_rendering]
```
where CHECKPOINT is the epoch you wish to evaluate or 'latest' if you wish to take the most recent epoch.
Turning on `--eval_rendering` will further produce and evaluate PSNR of train image reconstructions.


#### Training with trainable cameras with noisy initializations
For training IDR with cameras optimization run:
```
cd ./code
python training/exp_runner.py --train_cameras --conf ./confs/dtu_trained_cameras.conf --scan_id SCAN_ID
```

Then, to evaluate cameras accuracy and to produce the meshed surface, run:
```
cd ./code
python evaluation/eval.py  --eval_cameras --conf ./confs/dtu_trained_cameras.conf --scan_id SCAN_ID --checkpoint CHECKPOINT [--eval_rendering]
```


#### Evaluation on pretrained models

We have uploaded IDR trained models, and you can run the evaluation using:
```
cd ./code
python evaluation/eval.py --exps_folder trained_models --conf ./confs/dtu_fixed_cameras.conf --scan_id SCAN_ID  --checkpoint 2000 [--eval_rendering]
```
Or, for trained cameras:
```
python evaluation/eval.py --exps_folder trained_models --conf ./confs/dtu_trained_cameras.conf --scan_id SCAN_ID --checkpoint 2000 --eval_cameras [--eval_rendering]
```


### Disentanglement of geometry and appearance

<div align="center">
  <img width="100%" src="switch_wn_flatt.jpg"/>
</div>

Towards the goal of disentangling geometry and appearance, we can transfer the appearance learned from one scene to unseen geometry. 
Running:

For two models trained on two different DTU scenes, we show (left to right): the reconstructed geometry; novel views using the trained renderer; and novel views rendered using the renderer from the other (unseen) scene.

```
cd ./code
python evaluation/eval.py --geometry_id GEOMETRY_ID --appearance_id APPEARANCE _ID
```
Will produce novel views of the geometry of the `GEOMETRY_ID` scan trained model, and the rendering of the `APPEARANCE_ID` scan trained model.

## Citation
If you find our work useful in your research, please consider citing:

	@article{yariv2020multiview,
	title={Multiview Neural Surface Reconstruction with Implicit Lighting and Material},
	author={Lior Yariv and Yoni Kasten and Dror Moran 
	  	    and Meirav Galun and Matan Atzmon and Ronen Basri and Yaron Lipman},
	journal={NeurIPS},
	year={2020},
	}
	
	
## Related papers
Here are related works on implicit neural representation from our group:
* [Gropp et al. - Implicit Geometric Regularization for Learning Shapes (2020)](https://arxiv.org/abs/2002.10099)
* [Atzmon & Lipman. - SAL++: Sign Agnostic Learning with Derivatives (2020)](https://arxiv.org/abs/2006.05400)
* [Atzmon & Lipman. - SAL: Sign Agnostic Learning of Shapes From Raw Data (2020)](https://arxiv.org/abs/1911.10414)
* [Atzmon et al. - Controlling Neural Level Sets (2019)](https://arxiv.org/abs/1905.11911)
