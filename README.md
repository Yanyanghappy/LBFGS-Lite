# Yanyang
## 关键是xi是x，xi+1是y，gi是f对x的梯度，gi+1是f对y的梯度，所以通过在optimize中不断调整x来影响g然后根据g的变化来进行优化直到达到前面定义的最小化参数比如梯度阈值才停止迭代视为达到最优
成本函数是著名的 Rosenbrock 函数
这段代码使用了 L-BFGS (Limited-memory Broyden–Fletcher–Goldfarb–Shanno) 算法来最小化一个成本函数。具体来说，该成本函数是著名的 Rosenbrock 函数，也被称为 Rosenbrock 谷函数。以下是代码中的数学原理及其详细解释：

### Rosenbrock 函数
Rosenbrock 函数是一个经典的测试函数，常用于优化算法的性能测试。对于二维情况，Rosenbrock 函数定义如下：

\[ f(x, y) = (1 - x)^2 + 100(y - x^2)^2 \]

该函数在点 \((1, 1)\) 处有一个全局最小值，其值为 0。这个函数的梯度计算如下：

\[ \frac{\partial f}{\partial x} = -2(1 - x) - 400x(y - x^2) \]
\[ \frac{\partial f}{\partial y} = 200(y - x^2) \]

### L-BFGS 算法
L-BFGS 算法是一种用于大规模优化问题的算法，它是 BFGS 算法的改进版本，适用于内存受限的情况。BFGS 算法是一种拟牛顿法，利用梯度信息逐步逼近 Hessian 矩阵（目标函数的二阶导数矩阵），从而实现快速收敛。L-BFGS 通过只存储几个最近的梯度和步长信息来节省内存。

### 代码解释

1. **类定义与成员函数**
   - `MinimizationExample` 类包含一个成员函数 `run`，用于执行优化过程。
   - 成员函数 `costFunction` 和 `monitorProgress` 分别用于计算成本函数值和梯度，以及监控优化过程的进展。

2. **初始化优化参数**
   - 变量 `x` 是优化变量的初始值。在这里，每隔一个元素设置为 -1.2 和 1.0。
   - `params` 是 L-BFGS 算法的参数，包括梯度的收敛阈值 (`g_epsilon`)、历史步数 (`past`) 和误差阈值 (`delta`)。

3. **开始优化**
   - 调用 `lbfgs::lbfgs_optimize` 函数进行优化，该函数接受初始变量、最终成本值、成本函数、梯度监控函数和 L-BFGS 参数。

4. **成本函数与梯度计算**
   - `costFunction` 函数计算 Rosenbrock 函数的值和梯度。对于每个偶数索引 `i`：
     - 计算 `t1 = 1.0 - x(i)` 和 `t2 = 10.0 * (x(i + 1) - x(i) * x(i))`。
     - 计算梯度 `g(i + 1) = 20.0 * t2` 和 `g(i) = -2.0 * (x(i) * g(i + 1) + t1)`。
     - 累加成本值 `fx += t1 * t1 + t2 * t2`。

5. **进展监控**
   - `monitorProgress` 函数在每次迭代时打印当前迭代次数、函数值、梯度的无穷范数和当前变量值。

6. **主函数**
   - 创建 `MinimizationExample` 对象并调用 `run` 函数，运行 L-BFGS 优化。

### 数学细节

1. **Rosenbrock 函数的梯度计算**
   - 对于 `f(x, y) = (1 - x)^2 + 100(y - x^2)^2`：
     - \(\frac{\partial f}{\partial x} = -2(1 - x) - 400x(y - x^2)\)
     - \(\frac{\partial f}{\partial y} = 200(y - x^2)\)

2. **梯度计算在代码中的实现**
   - `t1 = 1.0 - x(i)` 对应 \((1 - x)\)
   - `t2 = 10.0 * (x(i + 1) - x(i) * x(i))` 对应 \(10(y - x^2)\)
   - `g(i + 1) = 20.0 * t2` 对应 \(\frac{\partial f}{\partial y}\)
   - `g(i) = -2.0 * (x(i) * g(i + 1) + t1)` 对应 \(\frac{\partial f}{\partial x}\)

3. **优化过程**
   - L-BFGS 利用梯度信息更新变量 `x`，逐步逼近最优解。
   - 在每次迭代中，通过 `monitorProgress` 打印当前状态，帮助理解收敛过程。

### 结论
这段代码展示了如何使用 L-BFGS 算法优化 Rosenbrock 函数，并通过计算梯度和跟踪进展来实现最小化过程。Rosenbrock 函数是一个具有挑战性的测试函数，常用于评估优化算法的性能。


# LBFGS-Lite
A header-only L-BFGS unconstrained optimizer.

## 0. About

__LBFGS-Lite__ is a __C++__ [__header-only__](https://en.wikipedia.org/wiki/Header-only) library for __unconstrained optimization__. Many engineering considerations are added for improved robustness compared to the original version by Nocedal.

## 1. How to use

All explanations are detailed by the comments in "lbfgs.hpp". See "lbfgs_example.cpp" for the calling procedure. You may need to install Eigen via "sudo apt install libeigen3-dev" because we use Eigen for better performance since [ver. 2.1](https://github.com/ZJU-FAST-Lab/LBFGS-Lite/tags). If you need a pure C-style lib without any external dependence, please refer to [ver. 0.9](https://github.com/ZJU-FAST-Lab/LBFGS-Lite/tags).

## 2. Features

- Only one header file "lbfgs.hpp" is all you need.

- The library implements [Limited-Memory Broyden-Fletcher-Goldfarb-Shanno Method](https://doi.org/10.1007/BF01589116) (L-BFGS).

- A __highly robust line search__ proposed by [Lewis and Overton](https://link.springer.com/article/10.1007/s10107-012-0514-2) has been employed since [ver. 2.1](https://github.com/ZJU-FAST-Lab/LBFGS-Lite/tags).

- Both __smooth__ ([C2](https://en.wikipedia.org/wiki/Smoothness)) and __nonsmooth__ ([C0 but piecewise C2](https://en.wikipedia.org/wiki/Smoothness)) functions are supported.

- __Cautious update__ by [Li and Fukushima](https://epubs.siam.org/doi/pdf/10.1137/S1052623499354242) is employed for __global convergence__ in nonconvex cases.

- __Externally provided maximum step size__ is convenient to functions defined on bounded sets.

## 3. Why this lib

- Our lib is well-organized and concise (about 800 lines).

- Many other libs use [Nocedal's zoom line search](https://link.springer.com/book/10.1007%2F978-0-387-40065-5), [More-Thuente line search](https://dl.acm.org/doi/abs/10.1145/192115.192132), or [Hager-Zhang line search](https://doi.org/10.1137/030601880). The truth is, interpolation-based schemes bring many tunable parameters and make more or less assumptions on the function smoothness. Engineering practice tells us that these assumptions can fail due to bad numerical conditions and ill-shaped functions. Admittedly, they slightly reduce the iteration number in some cases, but also easily-crashed.

- Some other libs provide user-specified options for [Armijo and weak/strong Wolfe condition](https://en.wikipedia.org/wiki/Wolfe_conditions) without considering positive definiteness (PD) of the approximated Hessian. This is misleading because if only Armijo condition is satisfied, the PD property is not guaranteed and the solver is unstable or easily-crashed. We make the weak Wolfe condition mandatory here, which suffices for PD property.

- We use [Lewis-Overton line search](https://link.springer.com/article/10.1007/s10107-012-0514-2) as the only scheme since ver. 2.1 from which nonsmooth functions are supported. Other schemes either assume high orders of continuity, or enforce the strong Wolfe condition can never be fulfilled by nonsmooth functions. Moreover, Lewis-Overton line search are widely adopted in many graphics applications involving optimization on [scene](https://dl.acm.org/doi/abs/10.1145/2766929), [shape](https://dl.acm.org/doi/abs/10.1145/2897824.2925918), or [mesh](https://dl.acm.org/doi/abs/10.1145/3197517.3201303), showing its practical robustness.

- According to our practice, the function/gradient evaluation numbers are comparable with interpolation-based schemes. Sometimes it even requires fewer function calls. If you insist an interpolation-one for smooth well-shaped cost function, we also propose our [ver. 0.9](https://github.com/ZJU-FAST-Lab/LBFGS-Lite/tags) where a More-Thuente line search is kept.

- Other schemes' global convergence on non-convex functions are not guaranteed theoretically. We avoid the potential problems by employing the [cautious update](https://epubs.siam.org/doi/pdf/10.1137/S1052623499354242) scheme in our lib without additional computation overhead.

## 6. Licence

LBFGS-Lite is modified from [the C version](https://github.com/chokkan/liblbfgs) by Okazaki, which is further based on [the original Fortran version](https://doi.org/10.1007/BF01589116) by Nocedal. Thus it is distributed under the term of the MIT license according to previous ones by Okazaki and by Nocedal. Please refer to LICENSE file in the distribution.

## 7. Maintaince

If any bug, please contact [Zhepei Wang](https://zhepeiwang.github.io/) (<wangzhepei@live.com>).
