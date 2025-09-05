# Kuramoto oscillators on directed networks with negative binomial degree distributions

This repository contains C implementations of Kuramoto oscillators on networks generated using a negative binomial configuration model. The code supports parallel execution using OpenMP and flexible parameter input through command-line options.

We consider $N$ phases $\theta_1,\dots,\theta_N$  that evolve in time according to the system of equations[^1]:
$$
\begin{equation}
  \frac{d \theta_i}{d t} = \omega_i +  K\sum_{j=1}^N A_{ij} \sin{\left[\theta_j (t)  -  \theta_i (t)   \right]}
  \label{kuramoto},
\end{equation}
$$
where $K$ is the coupling strength, and $\{\omega_i\}_{i=1}^N$ are the natural frequencies, which are sampled from a normal distribution. The terms $\{A_{ij}\}_{i,j=1}^N$ are the elements of the adjacency matrix, where $A_{ii}=0 \; \forall i$. We also consider that the networks are directed, i.e., $A_{ij} \neq A_{ji}$. The in-degrees $k_i^{(in)} = \sum_{j=1}^N A_{ij}$ and the out-degrees $$k_i^{(out)} = \sum_{j=1}^N A_{ji}$$ are sampled from a negative binomial distribution[^2]: 
$$
P_k = \frac{\Gamma(\alpha + k)}{k! \, \Gamma(\alpha)} \left( \frac{c}{\alpha} \right)^k \left( \frac{\alpha}{ \alpha + c} \right)^{\alpha + k},
$$
where $c$ is the average degree and $\alpha > 0$ is the network heterogeneity parameter. For  $\alpha = 1$, $P_k$ becomes the exponential distribution, while in the limit $\alpha \rightarrow \infty$ it converges to the Poisson distribution. For $\alpha \rightarrow 0$, the variance  diverges, and nodes with unusually high number neighbors appear in the network.

---

## Features

- Generates directed networks with tunable heterogeneity using the negative binomial configuration model.
- Simulates Kuramoto oscillators with transient and total time steps.
- Supports varying either the coupling strength (`K`) or the average degree (`avg_k`) over a range of values.
- Parallelized using OpenMP for faster computation of multiple independent realizations.
- Command-line interface with default parameters and optional overrides.
- Outputs matrices of order parameters for each experiment.

---

## Files

| File                              | Description                                                  |
| --------------------------------- | :----------------------------------------------------------- |
| `KuramotoDirectedNB.c`            | Main simulation code with parameter parsing and parallel execution. |
| `nb_kuramoto.c` / `nb_kuramoto.h` | Functions to generate the negative binomial configuration model and integrate Kuramoto's equations. |
| `README.md`                       | This documentation.                                          |

---

## Compilation

### Serial version
```bash
gcc -o kur_dir KuramotoDirectedNB.c nb_kuramoto.c -ligraph -lgsl -lgslcblas -lm
```

### Parallel version

```bash
gcc -fopenmp -o kur_dir KuramotoDirectedNB_with_function.c nb_kuramoto.c -ligraph -lgsl -lgslcblas -lm
```



## Usage

```bash
./kur_dir [options]
```

### Available options

| Option                    | Description                     | Default |
| ------------------------- | ------------------------------- | ------- |
| `--dt <val>`              | Time step                       | 0.01    |
| `--ttot <val>`            | Total simulation time           | 100     |
| `--ttrans <val>`          | Transient time                  | 50      |
| `--N <val>`               | Number of nodes                 | 100     |
| `--alpha <val>`           | Network heterogeneity           | 1.0     |
| `--avg_k <val>`           | Average degree                  | 4.0     |
| `--K <val>`               | Coupling strength               | 3.5     |
| `--start_param <val>`     | Start of parameter range        | 2.0     |
| `--end_param <val>`       | End of parameter range          | 5.0     |
| `--num_params <val>`      | Number of parameter values      | 10      |
| `--num_experiments <val>` | Number of experiments per param | 30      |
| `--vary_coupling <0/1>`   | 1 = vary `K`, 0 = vary `avg_k`  | 1       |
| `--help`                  | Show help message               | -       |



## Output

* The program outputs a `.dat` file containing the results of all experiments for each parameter value. The first column corresponds to the varied parameter (`avg_k` or `K`), and the second column contains the corresponding order parameter. The data is organized in row-major order, with each row representing a single experiment for a specific parameter value.
* File naming depends on which parameter is varied (`K` or `avg_k`).

## Example 

```bash
# Vary coupling strength
./kur_dir --vary_coupling 1 --num_params 5 --num_experiments 10

# Vary average degree
./kur_dir --vary_coupling 0 --start_param 2 --end_param 8 --num_params 7

```



## References

[^1]: Francisco A. Rodrigues, Thomas K. DM. Peron, Peng Ji, and Jürgen Kurths, The Kuramoto model in complex networks, *Physics Reports*, vol. 610, 1-98, 2016. [DOI](https://doi.org/10.1016/j.physrep.2015.10.008)[^1].
[^2]: Fernando L Metz and Thomas Peron, Mean-field theory of vector spin models on networks with arbitrary degree distributions, *Journal of Physics: Complexity*, vol. 3, 015008, 2022. [DOI](https://doi.org/10.1088/2632-072X/ac4bed)

