# FDMTools

FDMTools constructs three-dimensional fuzzy dark matter (FDM) halos from
spherical eigenmodes and evolves them by advancing the eigenmode phases. The
repository contains a Python implementation and a notebook that demonstrates
the complete construction and evolution workflow.

## Features

- Build an NFW target profile with an optional soliton core.
- Solve the radial Schrödinger–Poisson eigenmodes.
- Fit mode amplitudes to the target density profile.
- Reconstruct a complex three-dimensional halo wavefunction.
- Evolve the halo to any requested time in Gyr.
- Plot the radial density profile and the projected initial density.

The workflow does not require Agama and does not generate Agama density or
potential files.

## Requirements

- Python 3.11
- NumPy
- SciPy
- h5py
- scikit-learn
- Matplotlib
- pyshtools
- JupyterLab and ipykernel for the notebook

Install the environment from the project directory:

```bash
python3.11 -m venv --system-site-packages .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -m ipykernel install --user \
  --name fdmtools --display-name "Python (FDMTools)"
```

## Notebook workflow

Start JupyterLab and open `halobuilder.ipynb`:

```bash
.venv/bin/python -m jupyter lab
```

Select the `Python (FDMTools)` kernel and run the cells from top to bottom. The
notebook has two stages:

1. Construct the target density, potential, eigenmodes, amplitudes, random
   phases, and the initial halo.
2. Advance the mode phases and reconstruct the evolved halo at
   `evolution_time`.

The main parameters are in the first code cell:

- `m_as`: FDM particle masses in eV/c².
- `M`: halo mass in solar masses.
- `a`: NFW scale radius in kpc.
- `R_max` and `R_fit`: radial solution and fitting limits in kpc.
- `seeds`: random phase realizations.
- `t_step` and `t_stop`: the time grid in Gyr.
- `projection_rmax`, `projection_resolution`, and `projection_rshells`:
  reconstruction settings.

By default, the evolution cell reconstructs the first halo at the last entry
of `t_array`. It leaves the complex wavefunction in `evolved_psi` and the
density in `evolved_density`; no external potential format is written.

The construction stage saves diagnostic PNG files under a directory named
from the selected particle mass, halo mass, time step, and radial resolution.

## Python API

```python
import numpy as np
from fdmtools import FDMTools

fdm = FDMTools(m_a=1.0e-22)
rshells = np.linspace(1.0e-4, 30.0, 128)

# E_nl, R_nl, a_nlm, and phase_nlm are produced by solve_amps_itr().
psi, radii, theta, phi = fdm.evolve(
    rshells,
    E_nl,
    R_nl,
    a_nlm,
    phase_nlm,
    t=1.0,
)
density = np.abs(psi) ** 2
```

`FDMTools.evolve` expects time in Gyr. The default physical convention uses
kpc, km/s, Gyr, and solar masses.

## Batch environments

`2.sh` is an example SLURM launcher for an interactive CPU allocation. Its
account, queue, constraint, and port values are site-specific and should be
adjusted before use.
