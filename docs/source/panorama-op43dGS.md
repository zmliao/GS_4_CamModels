# panorama-op43dgs

## Overview

This tutorial is based on op43dgs, which minimizes the projection error in 3DGS.

## Installation

```sh
git clone https://github.com/LetianHuang/op43dgs
cd op43dgs
conda env create --file environment.yml
conda activate op43dgs
pip install submodules/diff-gaussian-rasterization-panorama
```

## Train

```sh
python train.py \
    -s <path to your dataset> \
    -m <path to model to be trained> \
    --iterations <integer, number of iterations> \
    --save_iterations <array of integers> \
    --test_iterations <array of integers> \
    --sh_degree 3
```

Or simply use:

```sh
sh scripts/train.sh
```

## Render

```sh
python render.py \
    -s <path to your dataset> \
    -m <path to model to be trained> \
    --iterations <integer, number of iterations> \
    --skip_train \
    --eval \
    --fov_ratio 1
```

Or simply use:

```sh
sh scripts/render.sh
```

## Evaluate

```
python -m <path to pre-trained model>
```

Or simply use:

```sh
sh scripts/eval.sh
```

## Results (pinhole vs panorama)

Mip-NeRF 360 Dataset - bicycle scene:

![vs](C:\Users\13040\Desktop\vs.png)

## Formula derivation

There are two main differences in code between the pinhole camera model and the panorama camera model: the calculation of Jacobi matrix in `computeCov2D` kernel and the coordinate transformation in the `preprocessCUDA` kernel.

### Jacobi matrix:

$$
\left[\begin{matrix}\frac{W z}{2 \pi \left(x^{2} + z^{2}\right)} & 0 & - \frac{W x}{2 \pi \left(x^{2} + z^{2}\right)}\\- \frac{H x y}{\pi \sqrt{x^{2} + z^{2}} \left(x^{2} + y^{2} + z^{2}\right)} & \frac{H \sqrt{x^{2} + z^{2}}}{\pi \left(x^{2} + y^{2} + z^{2}\right)} & - \frac{H y z}{\pi \sqrt{x^{2} + z^{2}} \left(x^{2} + y^{2} + z^{2}\right)}\\0 & 0 & 0\end{matrix}\right]
$$

### Coordinate Transformation:

$$
X= \frac{W \left(\operatorname{atan}_{2}{\left(x,z \right)} + \pi\right)}{2 \pi} \\
Y= \frac{H \operatorname{atan}_{2}{\left(y,\sqrt{x^{2} + z^{2}} \right)}}{\pi} + \frac{H}{2}
$$

