# Chronocell

Chronocell is a Python package that implements the trajectory fitting procedures described in [Trajectory inference from single-cell genomics data with a process time model](https://doi.org/10.1371/journal.pcbi.1012752). The code to reproduce results in the paper is in another repository: https://github.com/pachterlab/FGP_2024

# Usage

## Installation

Clone this repository to your local machine using Git:

```
git clone https://github.com/pachterlab/Chronocell.git
```

After installation, you can use the package as follows:
```
import Chronocell
```
Or import specific objects directly:
```
from Chronocell.inference import Trajectory
from Chronocell.mixtures import PoissonMixtureSS, PoissonMixture, GammaPoissonMixture
from Chronocell.plotting import *
```

## Trajectory
The core component of this project is the Trajectory class, which allows you to create trajectory instances and fit them to data using the Expectation-Maximization (EM) algorithm. 

1. **Initialization**
Create a `Trajectory` instance by providing the topology and tau parameters which describe the trajectory structure, and model that specify the trasncription model, along with optional settings:

```python
trajectory = Trajectory(
    topo,                 # 2D numpy array representing the trajectory topology (integers)
    tau,                  # Array of tau parameters (floats)
    model="two_species_ss",   # Model type (default: "two_species_ss")
    restrictions={},          # Optional model restrictions (default: empty dict)
    verbose=0,                # Verbosity level (default: 0)
    store_info=True           # Whether to store additional info (default: True)
)

   Instantiate a `Trajectory` object with the relevant parameters:

   ```python
   trajectory = Trajectory(<parameters>)
   
The trajectory is primarliy defined by trajectory structure and sampling assumption. 3) scRNA-seq count matrix. Trajectory structure is provided to Chronocell as a 2D array, with each lineage (path) represented as a row. Along with the structure, an initial guess of switching time is also needed as a starting point in the fitting. The sampling assumption refers to the prior distribution of the latent variables (process time and lineages) for each cell. This is represented as a 3D array with shape (n, L, M), where n is the number of cells, L is the number of lineages, and M is the number of time grids.

Model.
Building upon the common transcription model, we have two classes of models based on the assumption of global switch time: (1) the synchronized model, which assumes a completely synchronized switch in transcription rates across all genes; and (2) the desynchronized model, where each gene has its own switching time. The desynchronized model is more challenging to fit from scratch, so we recommend using a warm start based on the results of the synchronized model.

Inference.
We use the expectation–maximization algorithm to fit the trajectory model on the scRNA-seq count matrix. See Section Maximum likelihood estimates of parameters by EM algorithm.

Output.
The primary output of Chronocell consists of the parameters and posterior distribution for each cell. Other relevant information such as the Akaike Information Criterion (AIC) and the Fisher information matrix can also be calculated.



inference.py: contains the Trajectory class and methods for fitting.
mixtures.py: contains the code and classes for Poisson mixture model.
models/: contains the model specific functions to calculate log likelihood and optimize parameters.
simulation.py: contains the code for generating simulations.
plotting.py: contains some convenient but not essential functions for plotting.
utils.py: contains some helper functions.
