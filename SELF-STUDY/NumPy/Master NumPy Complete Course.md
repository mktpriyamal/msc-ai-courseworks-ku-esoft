# Master NumPy: Complete Course

Nov 21, 2026 · @Thisara Priyamal

This 8-week course takes you from creating your first `ndarray` to writing fast, vectorised numerical code, covering every major area of NumPy with a use case for each.

## Course overview

**Who it is for:** students, analysts, engineers and ML practitioners who know basic Python. NumPy is the foundation under pandas, SciPy, scikit-learn, Matplotlib and most deep-learning libraries, so it pays off everywhere.

**What you will be able to do:** build and reshape n-dimensional arrays, index them precisely, compute without loops using ufuncs and broadcasting, do linear algebra and statistics, generate random data, read and write files, and optimise memory and speed.

**How to use it:** each module has topics with descriptions, key API, use cases, a code sample and exercises. Type every example and finish the exercises before moving on.

| Week | Modules | Milestone |
| --- | --- | --- |
| 1 | 1 to 3 | Create, index and slice arrays; know views vs copies |
| 2 | 4 to 5 | Control dtypes; compute with ufuncs and aggregations |
| 3 | 6 to 7 | Use broadcasting; reshape, stack and split |
| 4 | 8 to 9 | Sort, search, set operations; random sampling |
| 5 | 10 | Solve linear systems, decompositions |
| 6 | 11 to 12 | Statistics, masked data, FFT; file I/O, structured arrays |
| 7 | 13 | Optimise speed and memory; einsum, strides |
| 8 | 14 | Finish two capstone projects |

```python
import numpy as np

print(np.__version__)
```

## Module 1: Setup, ndarray basics and NumPy 2.x notes

Understand what an `ndarray` is and why it is far faster than a Python list.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Why NumPy | Homogeneous, contiguous, typed memory with compiled loops, so vector math runs 10x to 100x faster than Python loops | `np.array`, `%timeit` | Numerical computing |
| Installation | `pip install numpy`, virtual environments, conda | `pip`, `venv` | Reproducible setup |
| The ndarray | N-dimensional array with a fixed dtype and shape | `ndim shape size dtype itemsize nbytes` | Everything else builds on this |
| Lists vs arrays | Arrays apply operations element-wise; lists concatenate or repeat | `arr * 2` vs `lst * 2` | Avoiding loops |
| Axes | Axis 0 runs down rows, axis 1 across columns; reductions collapse an axis | `axis=` | Column or row statistics |
| NumPy 2.x notes | Several old aliases were removed (for example `np.float_`, `np.NaN`; use `np.float64`, `np.nan`); `np.trapz` became `np.trapezoid`; promotion rules for scalars changed (NEP 50). Check the migration guide for your version | `np.__version__` | Upgrading old code |
| Printing and display | Control output | `np.set_printoptions(precision=, suppress=True, threshold=)` | Readable output |

```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
print(a.shape, a.ndim, a.dtype, a.nbytes)   # (2, 3) 2 6 48
print(a.sum(axis=0))                        # [5 7 9]  (down columns)
print(a.sum(axis=1))                        # [ 6 15]  (across rows)

x = np.arange(1_000_000)
# %timeit x * 2                 -> typically ~1 ms
# %timeit [i * 2 for i in range(1_000_000)] -> ~50 ms or more
```

**Exercises**

1. Time an element-wise square on 1 million numbers using a list comprehension and using NumPy.
2. Create a 3x4 array and compute sums along each axis; explain which axis collapses.
3. Find the removed or renamed names your old code uses and update them for NumPy 2.

## Module 2: Creating arrays

Every way to build an array, from literal data to patterned and empty arrays.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| From Python data | Lists, tuples, nested lists | `np.array`, `np.asarray`, `np.asanyarray` | Converting inputs |
| Filled arrays | Zeros, ones, constants, uninitialised | `zeros`, `ones`, `full`, `empty`, `*_like` | Buffers, masks |
| Ranges | Evenly spaced values | `arange(start, stop, step)`, `linspace(a, b, n)`, `logspace`, `geomspace` | Axes for plots, grids |
| Identity and diagonals | Matrix helpers | `eye`, `identity`, `diag`, `tri`, `tril`, `triu` | Linear algebra |
| Grids | Coordinate matrices | `meshgrid`, `mgrid`, `ogrid`, `indices` | Evaluating f(x, y) |
| From functions | Build from index logic | `fromfunction`, `fromiter`, `vectorize` | Custom patterns |
| Copying | Duplicate data | `copy`, `array(a, copy=True)` | Safe edits |
| From buffers and files | Raw bytes and text | `frombuffer`, `fromfile`, `loadtxt`, `genfromtxt` | Binary or CSV data |
| Random arrays | Sampled data | `np.random.default_rng().random(...)` | Simulation |
| Repeating and tiling | Build from blocks | `repeat`, `tile`, `kron` | Patterns, upsampling |

```python
np.zeros((2, 3))                 # 2x3 of 0.0
np.full((2, 2), 7)               # filled with 7
np.arange(0, 10, 2)              # [0 2 4 6 8]
np.linspace(0, 1, 5)             # [0. .25 .5 .75 1.]
np.eye(3)                        # 3x3 identity

x, y = np.meshgrid(np.linspace(-2, 2, 5), np.linspace(-2, 2, 5))
z = np.exp(-(x**2 + y**2))       # 2D Gaussian surface
```

**Exercises**

1. Build a checkerboard 8x8 array of 0s and 1s using only slicing.
2. Create 100 evenly spaced x values from 0 to 2π and compute `sin(x)`.
3. Generate a multiplication table with `np.arange` and broadcasting.

## Module 3: Indexing, slicing, views vs copies

The single most important skill, and the biggest source of bugs: knowing when you hold a view of the same memory and when you hold a copy.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Basic indexing | Integers and slices, including negative indexes | `a[0]`, `a[1:4]`, `a[::-1]`, `a[1, 2]` | Rows, columns, reversal |
| Multi-dimensional slicing | One slice per axis | `a[:, 0]`, `a[1:, ::2]`, `a[..., 0]` | Subgrids, channels |
| `newaxis` | Add a dimension | `a[:, np.newaxis]`, `a[None, :]` | Prepare for broadcasting |
| Boolean masks | Select by condition | `a[a > 5]`, `a[(a > 2) & (a < 8)]` | Filtering |
| Fancy indexing | Select with integer arrays | `a[[0, 2, 4]]`, `a[rows, cols]` | Arbitrary picks, reordering |
| Assignment | Set slices, masks or fancy picks | `a[a < 0] = 0`, `a[:, 1] = 9` | Clipping, replacing |
| Conditional selection | Choose between arrays | `np.where(cond, x, y)`, `np.select`, `np.choose`, `np.piecewise` | Vectorised if/else |
| Index helpers | Find positions | `np.nonzero`, `np.argwhere`, `np.flatnonzero`, `np.ix_`, `np.take`, `np.put`, `np.take_along_axis` | Locating values |
| Views vs copies | Basic slices are views (writes change the original); masks and fancy indexing return copies | `a.base`, `np.shares_memory`, `a.copy()` | Avoiding silent edits |
| Diagonals and triangles | Matrix parts | `np.diag`, `np.diag_indices`, `np.tril_indices` | Linear algebra |
| Iteration | Visit elements | `np.nditer`, `np.ndenumerate`, `flat` | Rare element loops |

```python
a = np.arange(12).reshape(3, 4)

a[1, 2]                 # 6
a[:, 1]                 # column 1 -> [1 5 9]
a[a % 2 == 0]           # even elements (a copy)
a[[0, 2], [1, 3]]       # elements (0,1) and (2,3) -> [1 11]

v = a[0, :2]            # slice -> VIEW
v[0] = 99               # modifies a as well
c = a[a > 50].copy()    # independent copy
np.shares_memory(a, v)  # True

np.where(a > 6, 1, 0)   # 0/1 mask array
```

**Exercises**

1. From a 5x5 array, extract the border elements and the inner 3x3 block.
2. Replace all negative values in a random array with zero without a loop.
3. Prove with `np.shares_memory` which of slicing, boolean masking and fancy indexing make copies.
4. Select the maximum of each row using `argmax` and fancy indexing.

## Module 4: Data types and array attributes

Choosing the right dtype controls correctness, memory and speed.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Numeric dtypes | Signed and unsigned integers, floats, complex | `int8 int16 int32 int64 uint8 float16 float32 float64 complex128` | Memory vs precision |
| Other dtypes | Boolean, string, datetime, object | `bool_`, `str_`, `datetime64`, `timedelta64`, `object` | Mixed data |
| Specifying dtype | At creation or conversion | `dtype=`, `astype`, `np.dtype('<i4')` | Casting |
| Type limits | Range and precision | `np.iinfo`, `np.finfo` | Overflow checks |
| Integer overflow | Fixed-size integers wrap around silently | `np.uint8(255) + 1` | Image arithmetic bugs |
| Type promotion | Result dtype of mixed operations | `np.result_type`, `np.promote_types`, `np.can_cast` | Predicting output types |
| Floating-point care | Rounding, NaN, inf | `np.isclose`, `np.allclose`, `np.isnan`, `np.isinf`, `np.nan_to_num` | Comparing floats |
| Attributes | Describe an array | `shape ndim size dtype itemsize nbytes strides flags T real imag` | Inspecting memory |
| Byte order and layout | C vs Fortran order, endianness | `order='C'/'F'`, `np.ascontiguousarray`, `byteswap` | Interoperability, speed |
| Viewing memory differently | Reinterpret bytes | `a.view(dtype)` | Low-level work |

```python
img = np.array([250, 5], dtype=np.uint8)
print(img + 10)                    # [4 15]  <- wraps around!
print(img.astype(np.int16) + 10)   # [260 15]

np.iinfo(np.int32).max             # 2147483647
np.finfo(np.float32).eps           # ~1.19e-07

np.isclose(0.1 + 0.2, 0.3)         # True (0.1+0.2 == 0.3 is False)

big = np.random.default_rng(0).random(10_000_000)
big.nbytes / 1e6, big.astype(np.float32).nbytes / 1e6   # 80.0, 40.0 MB
```

**Exercises**

1. Demonstrate uint8 overflow, then fix it with a wider dtype.
2. Compare memory of 10 million values as float64, float32 and float16, and measure the precision loss.
3. Use `np.allclose` to test a floating-point identity that fails with `==`.

## Module 5: Math, ufuncs and aggregation

Universal functions (ufuncs) apply compiled element-wise operations; aggregations reduce arrays along axes.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Arithmetic | Element-wise operators | `+ - * / // % **`, `add subtract multiply divide floor_divide mod power` | Unit conversion |
| Math functions | Exponential, log, roots, rounding | `exp log log10 log1p sqrt cbrt square abs sign round floor ceil trunc` | Transformations |
| Trigonometry | Angles and waves | `sin cos tan arcsin arccos arctan arctan2 hypot deg2rad rad2deg sinh cosh tanh` | Signals, geometry |
| Comparison and logic | Boolean results | `== != < > <= >=`, `logical_and logical_or logical_not logical_xor`, `isin`, `isfinite` | Masks |
| Bitwise | Integer bit operations | `bitwise_and`, `left_shift`, `invert`, `packbits` | Flags, hashing |
| Aggregations | Reduce to scalars or along an axis | `sum prod mean min max ptp argmin argmax any all count_nonzero` | Column and row summaries |
| Axis and keepdims | Control reduction shape | `sum(axis=1, keepdims=True)` | Normalising rows |
| Cumulative operations | Running results | `cumsum cumprod diff gradient ediff1d` | Running totals, derivatives |
| NaN-aware versions | Ignore missing values | `nansum nanmean nanmax nanstd nanargmax` | Data with gaps |
| Clipping and rounding | Bound values | `clip(a, lo, hi)`, `around`, `rint` | Limiting outliers |
| ufunc methods | Generalised reductions | `np.add.reduce`, `np.add.accumulate`, `np.multiply.outer`, `np.add.at`, `out=`, `where=` | Efficient custom reductions |
| Integration | Numerical area | `np.trapezoid`, `np.cumsum` | Areas under curves |
| Sums and products of pairs | Combine arrays | `np.dot`, `np.outer`, `np.inner`, `np.cross` | Vector math |

```python
x = np.array([[1., 2., 3.], [4., 5., 6.]])

x.sum(), x.mean(axis=0), x.max(axis=1)   # 21.0, [2.5 3.5 4.5], [3. 6.]
x / x.sum(axis=1, keepdims=True)         # each row sums to 1
np.cumsum(x, axis=1)                     # running totals per row
np.diff([1, 4, 9, 16])                   # [3 5 7]

d = np.array([1., np.nan, 3.])
np.nanmean(d)                            # 2.0
np.clip(x, 2, 5)                         # limit to [2, 5]

np.add.at(counts := np.zeros(3), [0, 0, 2], 1)   # counts -> [2. 0. 1.]
```

**Exercises**

1. Normalise each column of a matrix to zero mean and unit variance.
2. Compute the Euclidean distance between every row and a given vector without loops.
3. Use `np.add.at` to build a histogram of category IDs and compare with `np.bincount`.
4. Estimate the area under `sin(x)` on \[0, π\] with `np.trapezoid` and check it is near 2.

## Module 6: Broadcasting

Broadcasting lets arrays of different shapes combine without copying data. It is the key to loop-free NumPy.

**The rule:** compare shapes from the last axis backwards. Two dimensions are compatible if they are equal or one of them is 1; a missing leading dimension counts as 1. The result takes the larger size on each axis.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Scalar with array | Scalar stretches to any shape | `a + 5` | Offsets, scaling |
| Row and column vectors | (n,) with (n,1) gives an (n,n) result | `a[:, None] + b[None, :]` | Pairwise tables |
| Matrix with vector | Add a row or column to every row | `M - M.mean(axis=0)` | Centering data |
| Adding axes | Make shapes compatible | `np.newaxis`, `reshape`, `expand_dims` | Aligning dimensions |
| Explicit broadcasting | Materialise or inspect | `np.broadcast_to`, `np.broadcast_arrays`, `np.broadcast_shapes` | Debugging shape errors |
| Pairwise distances | All-pairs differences | `X[:, None, :] - X[None, :, :]` | Clustering, kNN |
| Common errors | `operands could not be broadcast together` | Print `.shape` of both inputs | Fixing mismatches |
| Memory awareness | Large intermediate arrays can exhaust RAM | Chunk the computation | Big pairwise computations |

```python
X = np.random.default_rng(1).normal(size=(5, 3))

centered = X - X.mean(axis=0)                  # (5,3) - (3,) works
scaled = centered / X.std(axis=0)

row_sums = X.sum(axis=1)                       # shape (5,)
normalised = X / row_sums[:, None]             # need (5,1), not (5,)

diff = X[:, None, :] - X[None, :, :]           # (5,5,3)
dist = np.sqrt((diff ** 2).sum(axis=-1))       # (5,5) distance matrix

table = np.arange(1, 6)[:, None] * np.arange(1, 6)   # multiplication table
```

**Exercises**

1. Without running it, predict the result shape of adding arrays of shape (3,1,4) and (5,1).
2. Subtract the column mean from a 1000x20 matrix in one line.
3. Compute a full pairwise distance matrix for 2000 points in 2D, then reduce memory by processing 200 rows at a time.

## Module 7: Shape manipulation, stacking and splitting

Change how data is arranged without changing the data itself, and combine or divide arrays.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Reshaping | New shape, same elements; `-1` infers a size | `reshape`, `ravel`, `flatten`, `resize` | Images to vectors |
| Transposing and axes | Reorder dimensions | `T`, `transpose`, `swapaxes`, `moveaxis`, `rollaxis` | Channel-first vs channel-last |
| Adding and removing axes | Change dimensionality | `expand_dims`, `squeeze`, `atleast_1d/2d/3d` | Broadcasting prep |
| Concatenation | Join along an existing axis | `concatenate`, `hstack`, `vstack`, `dstack`, `column_stack`, `append` | Combining batches |
| Stacking | Join along a new axis | `np.stack`, `np.block` | Building batches, block matrices |
| Splitting | Divide into pieces | `split`, `array_split`, `hsplit`, `vsplit`, `dsplit` | Train/test folds |
| Repeating | Duplicate elements | `repeat`, `tile` | Upsampling |
| Flipping and rotating | Reverse or rotate | `flip`, `fliplr`, `flipud`, `rot90`, `roll` | Image augmentation, shifts |
| Padding | Add borders | `np.pad(a, width, mode='constant'/'edge'/'reflect')` | Convolutions, images |
| Inserting and deleting | Modify elements by position | `insert`, `delete` | Editing arrays |
| Memory order | C or Fortran layout | `order=`, `np.ascontiguousarray` | Interop and speed |
| Sliding windows | Overlapping views | `np.lib.stride_tricks.sliding_window_view` | Moving averages, patches |

```python
a = np.arange(12)

m = a.reshape(3, 4)             # a view when possible
m.T.shape                       # (4, 3)
m.reshape(-1)                   # flatten back

np.concatenate([m, m], axis=0).shape   # (6, 4)
np.stack([m, m]).shape                 # (2, 3, 4) new axis
np.split(a, 3)                         # three arrays of 4

np.pad(m, 1, mode='constant')          # zero border -> (5, 6)

from numpy.lib.stride_tricks import sliding_window_view as swv
swv(np.arange(6), 3).mean(axis=1)      # moving average, window 3
```

**Exercises**

1. Convert a (100, 28, 28) image batch to (100, 784) and back.
2. Move the channel axis of a (H, W, 3) image to the front.
3. Compute a 5-point moving average using `sliding_window_view`.
4. Split a dataset into 5 folds and build the train and validation sets for each fold.

## Module 8: Sorting, searching and set operations

Order data, find values and positions, and compare collections.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Sorting | Sorted copy or in place | `np.sort`, `a.sort()`, `axis=`, `kind='stable'` | Ordering |
| Argsort | Indexes that would sort | `np.argsort`, `np.lexsort` | Sort one array by another, multi-key sort |
| Partial sorts | Top-k without a full sort | `np.partition`, `np.argpartition` | Nearest neighbours |
| Extremes | Positions of min and max | `argmin`, `argmax`, `np.unravel_index` | Peak finding |
| Binary search | Position in sorted data | `np.searchsorted(a, v, side=)` | Binning, lookups |
| Binning | Count into intervals | `np.histogram`, `np.histogram2d`, `np.digitize`, `np.bincount` | Distributions |
| Unique values | Distinct items and counts | `np.unique(a, return_counts=True, return_inverse=True, axis=)` | Category counts |
| Set operations | Compare arrays | `intersect1d`, `union1d`, `setdiff1d`, `setxor1d`, `in1d`/`isin` | Matching IDs |
| Finding conditions | Locate matches | `np.where`, `np.nonzero`, `np.argwhere`, `np.extract` | Event detection |
| Counting | Tally matches | `count_nonzero`, `bincount` | Fast frequency counts |
| Interpolation | Estimate between points | `np.interp`, `np.polyfit` | Resampling signals |
| Run detection | Find change points | `np.diff`, `np.flatnonzero` | Streaks, segments |

```python
scores = np.array([72, 95, 88, 61, 95, 70])
names = np.array(['a', 'b', 'c', 'd', 'e', 'f'])

order = np.argsort(-scores, kind='stable')
names[order][:3]                         # top 3 by score

vals, counts = np.unique(scores, return_counts=True)
np.searchsorted([10, 20, 30], 25)        # 2
np.digitize(scores, [60, 80, 90])        # bin numbers 0..3
hist, edges = np.histogram(scores, bins=4)

k = 3
top_k = np.argpartition(scores, -k)[-k:]   # unsorted top-3 indexes
np.isin([1, 5, 9], [5, 9, 11])             # [False True True]
```

**Exercises**

1. Return the 5 largest values in a 1-million-element array without fully sorting it.
2. Sort rows of a matrix by the second column, breaking ties with the first (use `lexsort`).
3. Assign letter grades to scores using `digitize`.
4. Find IDs present in array A but missing from array B.

## Module 9: Random numbers

Use the modern `Generator` API rather than the legacy `np.random.seed` global functions.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Generator and seeds | Independent, reproducible random streams | `rng = np.random.default_rng(seed)` | Reproducible experiments |
| Uniform numbers | Floats and integers | `rng.random`, `rng.uniform`, `rng.integers(low, high, size)` | Simulation inputs |
| Normal and friends | Common continuous distributions | `rng.normal`, `lognormal`, `exponential`, `gamma`, `beta`, `chisquare`, `standard_t` | Noise, modelling |
| Discrete distributions | Counts and trials | `binomial`, `poisson`, `geometric`, `multinomial`, `hypergeometric` | Event counts |
| Sampling | Pick from data | `rng.choice(a, size, replace=, p=)` | Bootstrap, weighted draws |
| Shuffling | Reorder randomly | `rng.shuffle`, `rng.permutation`, `rng.permuted` | Train/test splits |
| Multivariate | Correlated variables | `rng.multivariate_normal(mean, cov, size)` | Correlated simulations |
| Spawning streams | Parallel-safe generators | `rng.spawn(n)`, `SeedSequence` | Parallel simulations |
| Legacy API | Older global state functions | `np.random.seed`, `np.random.rand`, `RandomState` | Reading old code |
| Monte Carlo patterns | Estimate by repeated sampling | Vectorised draws then mean | Probability and integrals |

```python
rng = np.random.default_rng(42)

rng.random((2, 3))                       # uniform [0, 1)
rng.integers(1, 7, size=10)              # ten dice rolls
rng.normal(loc=100, scale=15, size=5)
rng.choice(['A', 'B', 'C'], size=6, p=[.5, .3, .2])

# Monte Carlo estimate of pi
n = 1_000_000
pts = rng.random((n, 2))
pi_est = 4 * np.mean((pts ** 2).sum(axis=1) <= 1)   # ~3.14

# bootstrap 95% interval for a mean
data = rng.normal(50, 10, 200)
boot = rng.choice(data, (5000, data.size)).mean(axis=1)
np.percentile(boot, [2.5, 97.5])
```

**Exercises**

1. Simulate 10,000 rolls of two dice and compare the distribution of sums with theory.
2. Estimate the probability that two people in a group of 23 share a birthday.
3. Create a reproducible 80/20 train-test split using `permutation`.
4. Bootstrap a 95% confidence interval for the median of a sample.

## Module 10: Linear algebra

Matrix operations underpin regression, PCA, graphics, physics and neural networks. Most live in `np.linalg`.

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Products | Dot, matrix, outer, inner, Kronecker | `a @ b`, `np.dot`, `np.matmul`, `np.outer`, `np.inner`, `np.kron`, `np.tensordot` | Transformations, layers |
| Transpose and trace | Basic matrix properties | `a.T`, `np.trace`, `np.diag` | Matrix summaries |
| Determinant and rank | Invertibility checks | `np.linalg.det`, `matrix_rank`, `cond` | Detect singular matrices |
| Inverse | Matrix inverse, pseudo-inverse | `np.linalg.inv`, `pinv` | Small systems |
| Solving systems | Solve Ax = b (preferred over inverting) | `np.linalg.solve` | Equations |
| Least squares | Best fit when no exact solution | `np.linalg.lstsq` | Linear regression |
| Eigen decomposition | Eigenvalues and eigenvectors | `eig`, `eigh` (symmetric), `eigvals` | PCA, vibrations |
| SVD | Singular value decomposition | `np.linalg.svd` | Compression, PCA, recommenders |
| QR and Cholesky | Matrix factorisations | `qr`, `cholesky` | Stable solvers, sampling |
| Norms and distances | Size of vectors and matrices | `np.linalg.norm(x, ord=, axis=)` | Distances, regularisation |
| Matrix powers and exponentials | Repeated multiplication | `np.linalg.matrix_power` | Markov chains |
| Batched operations | Operate on stacks of matrices | Arrays of shape (..., n, n) | Many small problems at once |
| Einstein summation | Concise tensor contractions | `np.einsum('ij,jk->ik', a, b)` | Custom tensor math |

```python
A = np.array([[3., 1.], [1., 2.]])
b = np.array([9., 8.])

x = np.linalg.solve(A, b)          # [2. 3.]
np.allclose(A @ x, b)              # True

vals, vecs = np.linalg.eigh(A)     # symmetric matrix
U, s, Vt = np.linalg.svd(A)

# linear regression y = a*x + b via least squares
rng = np.random.default_rng(0)
xs = rng.uniform(0, 10, 50)
ys = 2.5 * xs + 4 + rng.normal(0, 1, 50)
X = np.column_stack([xs, np.ones_like(xs)])
coef, *_ = np.linalg.lstsq(X, ys, rcond=None)   # ~[2.5, 4.0]

np.linalg.norm([3, 4])             # 5.0
```

**Exercises**

1. Solve a 3-equation linear system and verify the answer by substitution.
2. Fit a quadratic curve to noisy data using `lstsq` with a design matrix.
3. Implement PCA on a 200x5 dataset using `np.linalg.eigh` of the covariance matrix and check against SVD.
4. Compute the steady state of a 3-state Markov chain with `matrix_power`.

## Module 11: Statistics, missing data, masked arrays, polynomials and FFT

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Descriptive statistics | Centre and spread | `mean median std var average(weights=) percentile quantile ptp` | Data summaries |
| Degrees of freedom | Sample vs population spread | `std(ddof=1)` | Correct sample statistics |
| Correlation and covariance | Relationships | `np.corrcoef`, `np.cov`, `np.correlate` | Feature relationships |
| Histograms | Distribution counts | `np.histogram`, `histogram_bin_edges` | Shape of data |
| NaN handling | Ignore or detect | `nanmean`, `nanmedian`, `nanpercentile`, `isnan`, `nan_to_num` | Gaps in sensors |
| Masked arrays | Arrays with invalid-value masks | `np.ma.masked_array`, `masked_invalid`, `masked_where`, `.filled()` | Excluding bad readings |
| Polynomials | Fit and evaluate | `np.polynomial.Polynomial.fit`, `polyfit`, `polyval`, `roots` | Trend curves |
| Interpolation | Fill between points | `np.interp` | Resampling |
| Convolution | Smoothing and filtering | `np.convolve`, `np.correlate` | Moving averages |
| Fourier transforms | Frequency analysis | `np.fft.fft`, `ifft`, `rfft`, `fftfreq`, `fft2` | Signals, periodicity |
| Numerical calculus | Derivatives, integrals | `np.gradient`, `np.diff`, `np.trapezoid`, `np.cumsum` | Physics, finance |
| Window functions | Signal windows | `hanning`, `hamming`, `blackman` | Spectral analysis |

```python
rng = np.random.default_rng(0)
x = rng.normal(50, 10, 1000)
x.mean(), x.std(ddof=1), np.percentile(x, [25, 50, 75])

data = np.array([1.0, 2.0, np.nan, 4.0])
np.nanmean(data)                                 # 2.333...
m = np.ma.masked_invalid(data); m.mean()         # same, using a mask

# detect the dominant frequency of a noisy 5 Hz signal
fs = 200
t = np.arange(0, 2, 1 / fs)
sig = np.sin(2 * np.pi * 5 * t) + 0.3 * rng.normal(size=t.size)
spec = np.abs(np.fft.rfft(sig))
freqs = np.fft.rfftfreq(t.size, 1 / fs)
freqs[spec.argmax()]                             # ~5.0

np.convolve(sig, np.ones(10) / 10, mode='same')  # 10-point smoothing
```

**Exercises**

1. Compute the correlation matrix of a 500x4 dataset and identify the strongest pair.
2. Detect and mask readings beyond 3 standard deviations, then recompute the mean.
3. Find the dominant frequency in a recorded signal using `rfft`.
4. Fit polynomials of degree 1, 3 and 9 to noisy data and compare errors.

## Module 12: File I/O, structured arrays and datetimes

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Binary `.npy` / `.npz` | Fast, exact, typed storage | `np.save`, `np.load`, `np.savez`, `np.savez_compressed` | Caching arrays and datasets |
| Memory mapping | Work with files larger than RAM | `np.load(mmap_mode='r')`, `np.memmap` | Huge arrays |
| Text files | CSV and delimited text | `np.loadtxt`, `np.genfromtxt(missing_values=, filling_values=)`, `np.savetxt` | Simple tables |
| Raw binary | Headerless data | `np.fromfile`, `tofile`, `frombuffer` | Sensor dumps, images |
| Structured arrays | Named, typed fields per record | `np.dtype([('name','U10'),('age','i4')])`, `a['age']` | Mixed-type records |
| Record arrays | Attribute access to fields | `np.rec.array`, `a.view(np.recarray)` | Convenient field access |
| Field utilities | Merge and convert | `numpy.lib.recfunctions` | Joining record sets |
| datetime64 | Dates and times as numbers | `np.datetime64('2026-11-21')`, `np.arange(start, stop, dtype='datetime64[D]')` | Time axes |
| timedelta64 | Durations | `np.timedelta64(5, 'D')`, `astype('timedelta64[h]')` | Date differences |
| Business days | Working-day calendars | `np.busday_count`, `np.is_busday`, `np.busday_offset` | Deadlines |
| String arrays | Vectorised text operations | `np.strings` (NumPy 2) / `np.char`: `upper`, `replace`, `find` | Simple text processing |
| Printing and exporting | Format output | `np.array2string`, `np.set_printoptions`, `tolist` | Reports |

Note: `np.load` of pickled object arrays is disabled by default (`allow_pickle=False`); only enable it for files you trust.

```python
np.save('weights.npy', weights)
w = np.load('weights.npy')
np.savez_compressed('bundle.npz', X=X, y=y)
bundle = np.load('bundle.npz'); bundle['X'].shape

big = np.load('huge.npy', mmap_mode='r')     # no full load
big[:1000].mean()

people = np.array([('Asha', 31, 52000.0), ('Ben', 27, 61000.0)],
                  dtype=[('name', 'U10'), ('age', 'i4'), ('pay', 'f8')])
people['pay'].mean()
people[people['age'] > 28]['name']

days = np.arange('2026-11-01', '2026-11-08', dtype='datetime64[D]')
np.busday_count('2026-11-01', '2026-11-30')
```

**Exercises**

1. Save three arrays to one compressed `.npz` file and reload only one of them.
2. Memory-map a large array and compute its column means in blocks.
3. Build a structured array of products with name, price and stock; list the items with low stock sorted by price.
4. Count working days between two dates, excluding a list of holidays (`holidays=` argument).

## Module 13: Performance and advanced topics

| Topic | Description | Key API | Use case |
| --- | --- | --- | --- |
| Vectorise everything | Replace Python loops with array expressions | ufuncs, `where`, broadcasting | 10x to 100x speed-ups |
| Profiling | Measure first | `%timeit`, `timeit`, `cProfile`, `tracemalloc` | Finding bottlenecks |
| Avoiding temporaries | Reuse output buffers | `out=`, in-place `+=`, `np.add(a, b, out=a)` | Lower memory, faster loops |
| Memory layout | Contiguity and strides affect speed | `flags`, `strides`, `ascontiguousarray`, `order=` | Cache-friendly code |
| Strides tricks | Zero-copy views | `as_strided` (use with care), `sliding_window_view`, `broadcast_to` | Windows, repeats |
| `einsum` and `tensordot` | Efficient tensor contraction | `np.einsum(..., optimize=True)`, `np.tensordot` | Batched matrix math |
| `np.vectorize` | Convenience wrapper, not a speed tool | `np.vectorize(f)` | Apply scalar function simply |
| Compiled acceleration | Speed up unavoidable loops | Numba `@njit`, Cython, `numexpr` | Custom algorithms |
| Dtype choice | Smaller types use less memory and bandwidth | `float32`, `int32`, `uint8` | Large datasets, ML |
| Chunking and memmap | Process data bigger than RAM | `np.memmap`, block loops | Large files |
| Custom ufuncs | Build your own broadcasting functions | `np.frompyfunc`, Numba `@vectorize` | Reusable element-wise ops |
| Subclassing and protocols | Extend arrays | `__array_function__`, `__array_ufunc__`, `np.ndarray` subclasses | Library authors |
| Typing | Static types for arrays | `numpy.typing.NDArray`, `ArrayLike` | Maintainable code |
| Interoperability | Share memory with other tools | `np.asarray(torch_tensor)`, `pandas.to_numpy`, DLPack, buffer protocol, `__array_interface__` | pandas, PyTorch, JAX, SciPy |
| Threading | BLAS uses multiple cores; many ufuncs release the GIL | `threadpoolctl`, env var `OMP_NUM_THREADS` | Controlling CPU use |
| Testing | Compare arrays properly | `np.testing.assert_allclose`, `assert_array_equal` | Unit tests |

```python
rng = np.random.default_rng(0)
x = rng.random(5_000_000)

# fewer temporaries: in-place, with out=
y = np.empty_like(x)
np.multiply(x, 2.0, out=y)
y += 1.0

# einsum: batched matrix product and trace in one call
A = rng.random((100, 4, 4)); B = rng.random((100, 4, 4))
C = np.einsum('bij,bjk->bik', A, B)
traces = np.einsum('bii->b', C)

# numba for an unavoidable loop
from numba import njit

@njit
def running_max_drawdown(p):
    peak, worst = p[0], 0.0
    for v in p:
        peak = max(peak, v)
        worst = min(worst, v / peak - 1)
    return worst

np.testing.assert_allclose(C[0], A[0] @ B[0])
```

**Exercises**

1. Benchmark `np.vectorize` against a true vectorised expression and against a Numba version.
2. Reduce peak memory of a chained expression using `out=` and in-place operators; confirm with `tracemalloc`.
3. Write an `einsum` expression for batched dot products between two (N, D) arrays.
4. Compare the time to sum a (10000, 10000) array along axis 0 for C-order and Fortran-order arrays and explain the difference.

## Module 14: Use cases and capstone projects

**Where NumPy is used**

| Domain | Typical NumPy work | Modules used |
| --- | --- | --- |
| Data science | Feature matrices, normalisation, correlation, bootstrapping | 5, 6, 9, 11 |
| Machine learning | Implement gradient descent, k-means, neural network layers from scratch | 5, 6, 10 |
| Image processing | Images as (H, W, C) arrays: crop, flip, threshold, convolve, grayscale | 3, 7, 11 |
| Signal and audio | Filtering, FFT spectra, resampling | 11 |
| Scientific computing | Simulations, numerical integration, ODE steps, grids | 2, 5, 10 |
| Finance | Returns, covariance, portfolio variance, Monte Carlo pricing | 5, 9, 10, 11 |
| Geospatial and graphics | Rotations, projections, distance matrices | 6, 10 |
| Games and simulation | Game of Life, random walks, cellular automata | 3, 6, 7 |

**Capstone projects**

| Project | Skills tested | Deliverable |
| --- | --- | --- |
| 1. Linear regression from scratch | Matrix algebra, `lstsq`, gradient descent | Notebook comparing closed-form and iterative solutions |
| 2. k-means clustering | Broadcasting, `argmin`, boolean masks | Working clustering function with no Python loops over points |
| 3. Image toolkit | Slicing, `pad`, convolution via `sliding_window_view` | Grayscale, blur, edge detection and flip functions on arrays |
| 4. Conway's Game of Life | `roll`, neighbour counting with slicing | Animated or stepwise simulation on a 100x100 grid |
| 5. Monte Carlo portfolio risk | Random multivariate normal, covariance, percentiles | Value-at-risk estimate for a 3-asset portfolio |
| 6. Audio spectrum analyser | `rfft`, windows, `fftfreq` | Dominant-frequency detector for a recorded tone |

```python
# Game of Life step in a few lines
def step(grid):
    n = sum(np.roll(np.roll(grid, i, 0), j, 1)
            for i in (-1, 0, 1) for j in (-1, 0, 1) if (i, j) != (0, 0))
    return ((n == 3) | ((grid == 1) & (n == 2))).astype(int)

grid = np.random.default_rng(3).integers(0, 2, (50, 50))
for _ in range(100):
    grid = step(grid)

# k-means assignment with broadcasting
def assign(X, centers):
    d = ((X[:, None, :] - centers[None, :, :]) ** 2).sum(axis=2)
    return d.argmin(axis=1)
```

**Practice sources:** the NumPy tutorials and "100 NumPy exercises", Kaggle datasets, and image or audio files you record yourself.

## Appendix: cheat sheet, pitfalls, checklist and resources

**Quick reference by task**

| I want to... | Use |
| --- | --- |
| Make an array | `array`, `zeros`, `ones`, `full`, `arange`, `linspace`, `eye`, `meshgrid` |
| Change shape | `reshape`, `ravel`, `T`, `transpose`, `expand_dims`, `squeeze` |
| Join or split | `concatenate`, `stack`, `vstack`, `hstack`, `split` |
| Select | slices, boolean masks, fancy indexing, `where`, `take` |
| Compute | ufuncs, `sum`, `mean`, `std`, `cumsum`, `diff`, `clip` |
| Combine shapes | broadcasting, `newaxis`, `broadcast_to` |
| Order and find | `sort`, `argsort`, `argpartition`, `searchsorted`, `unique`, `isin` |
| Random data | `default_rng`, `random`, `integers`, `normal`, `choice`, `permutation` |
| Linear algebra | `@`, `linalg.solve`, `lstsq`, `eigh`, `svd`, `norm`, `einsum` |
| Handle missing values | `isnan`, `nanmean`, `nan_to_num`, `ma.masked_invalid` |
| Save and load | `save`, `load`, `savez_compressed`, `loadtxt`, `memmap` |

**Common pitfalls**

1. Assuming a slice is a copy. Basic slices are views; use `.copy()` when you need independence.
2. Using `and` / `or` on arrays instead of `&` / `|` (with parentheses around each comparison).
3. Integer overflow and silent wrap-around in small integer dtypes such as `uint8`.
4. Comparing floats with `==` instead of `np.isclose` or `np.allclose`.
5. Shape `(n,)` vs `(n, 1)` confusion that produces unintended broadcasting.
6. Building arrays by repeated `np.append` in a loop. Preallocate or collect in a list first.
7. Inverting a matrix to solve equations. Use `np.linalg.solve` instead.
8. Using the legacy global `np.random.seed` in new code. Use `default_rng`.
9. Expecting `np.vectorize` to be fast. It is only a convenience wrapper around a Python loop.
10. Forgetting `ddof=1` when computing the standard deviation of a sample.
11. Relying on removed aliases after upgrading to NumPy 2.

**8-week practice plan**

| Weeks | Daily habit (45 to 60 minutes) |
| --- | --- |
| 1 to 3 | Read a module, type every example, finish the exercises |
| 4 to 6 | Solve 10 problems per week from "100 NumPy exercises" without loops |
| 7 | Profile and optimise one earlier solution for time and memory |
| 8 | Complete two capstones and publish them |

**Mastery checklist**

- [ ] I can predict an operation's result shape using the broadcasting rule.
- [ ] I know which operations return views and which return copies.
- [ ] I can replace any simple loop with vectorised code.
- [ ] I can choose dtypes for correctness and memory.
- [ ] I can solve linear systems and fit models with `np.linalg`.
- [ ] I can run reproducible random simulations with `Generator`.
- [ ] I can save, load and memory-map large arrays.
- [ ] I can profile slow code and speed it up.

**Resources**

- Official NumPy documentation: user guide, absolute beginners guide and API reference (numpy.org)
- *Guide to NumPy* by Travis Oliphant, NumPy's original author
- *From Python to NumPy* by Nicolas Rougier (free online)
- "100 NumPy exercises" on GitHub and Kaggle Learn
- Next steps: this course's companion, the pandas course, then SciPy and scikit-learn
