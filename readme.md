# Undergraduate Research
These are the results of my involvement in an undergraduate research project with Dr. Thomas Humphries at UW Bothell. We were researching improvements to the iterative algorithm SART for reconstructing CT images. Our experiments revolved around utilizing a non-differentiable function ([BM3D](https://webpages.tuni.fi/foi/GCF-BM3D/BM3D_TIP_2007.pdf)) to generate perturbations for the superiorization of SART. More thorough background can be found in my paper, [BM3D as a Superiorization Function Within Sart for Denoising of Computed Tomography Images](https://github.com/j-henshaw/SART-ASTRA_pnp_BM3D/raw/main/Research_Paper_Final%20v%202.0.pdf). For comparison, please see the [original branch](https://github.com/TDHumphries/SART-ASTRA) of this project.

I have also included a .yml file which should be able to set up a conda environment suitable for running the software. While I have left the original Readme contents below, a few changes have been made to the code that this may not be up-to-date. However, the entire suite of updated command line arguments used to generate the data set for our experiments is included in [the paper](https://github.com/j-henshaw/SART-ASTRA_pnp_BM3D/raw/main/Research_Paper_Final%20v%202.0.pdf), for your reference.

# Original Readme
Note: This code requires the ASTRA library: https://www.astra-toolbox.com/

This directory contains two .py files:

make_sino.py: Generates a sinogram from a CT image in floating point format.

SART.py:      Reconstruct image from sinogram using SART.

SART is a well known method to iteratively solve the system Ax = b. My implementation is based on the description in the included paper (SART_CE03.pdf), pp. 54-55. One of the key features is that you can split the sinogram into subsets (e.g. the 1st, 13th, 25th, etc. view) and process only one subset of data at a time, in a cyclical fashion. This tends to make the algorithm converge much more quickly than if you work with the entire sinogram at once. 

The code also includes the option to superiorize the method (see included paper, HGDC12.pdf), using total variation (TV). The option --sup_params allow you to pass in a triplet of values corresponding to gamma and N (described on p.5537 of the paper) as well as an initial perturbation size, alpha_init. See example 4 below. TV superiorization can produce smoother images for the low-dose, sparse-view, and limited angle case, though the images may be over-smoothed in some cases, depending on the choice of parameter.

Here are some sample command-line calls to generate different types of data from one of the included .flt images (using make_sino.py) and run the reconstruction code. The samples directory includes pictures of what each of these four images should look like. Note that the SART.py method saves the reconstructed image in float32 format. To view as an image you can read in using Matlab or Python.


1) NDCT data

python make_sino.py --in imgs/00000001_img.flt --out sinos --dx 0.0568 --range 0 360 --counts 1e6

python SART.py --sino sinos/00000001_sino.flt --out recons --dx 0.0568 --range 0 360 --nsubs 12 --xtrue imgs/00000001_img.flt

2) LDCT data

python make_sino.py --in imgs/00000001_img.flt --out sinos --dx 0.0568 --range 0 360 --counts 5e4

python SART.py --sino sinos/00000001_sino.flt --out recons --dx 0.0568 --range 0 360 --nsubs 12 --xtrue imgs/00000001_img.flt

3) sparse view data

python make_sino.py --in imgs/00000001_img.flt --out sinos --dx 0.0568 --range 0 360 --ntheta 60 --counts 1e6

python SART.py --sino sinos/00000001_sino.flt --out recons --dx 0.0568 --range 0 360 --nsubs 12 --ntheta 60 --xtrue imgs/00000001_img.flt

4) sparse view data with superiorization

python make_sino.py --in imgs/00000001_img.flt --out sinos --dx 0.0568 --range 0 360 --ntheta 60 --counts 1e6

python SART.py --sino sinos/00000001_sino.flt --out recons --dx 0.0568 --range 0 360 --nsubs 12 --ntheta 60 --xtrue imgs/00000001_img.flt --sup_params 0.9995 20 0.5


5) limited angle data

python make_sino.py --in imgs/00000001_img.flt --out sinos --dx 0.0568 --range 0 150 --ntheta 375 --counts 1e6

python SART.py --sino sinos/00000001_sino.flt --out recons --dx 0.0568 --range 0 150 --nsubs 15 --ntheta 375 --xtrue imgs/00000001_img.flt
