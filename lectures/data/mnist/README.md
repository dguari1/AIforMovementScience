# MNIST and Fashion-MNIST subsets

Small, class-balanced subsets used in lecture 06.02 (so the notebook does not need to download the data).

| File | Train | Test | Classes |
| --- | --- | --- | --- |
| `mnist_subset.npz` | 5,000 images (500 per digit) | 2,000 images (200 per digit) | digits 0–9 |
| `fashion_mnist_subset.npz` | 5,000 images (500 per class) | 2,000 images (200 per class) | 0 T-shirt/top, 1 Trouser, 2 Pullover, 3 Dress, 4 Coat, 5 Sandal, 6 Shirt, 7 Sneaker, 8 Bag, 9 Ankle boot |

Each file has `X_train`, `y_train`, `X_test`, `y_test`. Images are 28 × 28 grayscale, `uint8` (0–255). The train images come from the original training sets and the test images from the original test sets (random seed 0).

```python
import numpy as np
d = np.load('data/mnist/mnist_subset.npz')
X_train, y_train = d['X_train'], d['y_train']
```

## Sources and licenses
- **MNIST**: Y. LeCun, C. Cortes and C. J. C. Burges. Files from https://github.com/fgnt/mnist (copy of the original files). License: CC BY-SA 3.0.
- **Fashion-MNIST**: H. Xiao, K. Rasul and R. Vollgraf (2017), "Fashion-MNIST: a Novel Image Dataset for Benchmarking Machine Learning Algorithms", arXiv:1708.07747. Files from https://github.com/zalandoresearch/fashion-mnist. License: MIT.
