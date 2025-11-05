# FragPT2

Fragment-embedding CASCI/PT2 library built on PySCF. FragPT2 assumes a partition of a molecular active space into two fragments and computes an embedding energy with optional second-order perturbative corrections: dispersion, single charge transfer (1CT), double charge transfer (2CT), and triplet–triplet (TT) terms.

Based on the paper: [J. Chem. Theory Comput. (2025)](https://pubs.acs.org/doi/10.1021/acs.jctc.4c01221) (DOI: 10.1021/acs.jctc.4c01221)

<!-- FragPT2 is research code designed for clarity and experiment velocity. It exposes a small, explicit API over PySCF and keeps heavy intermediates (RDMs) cached to avoid recomputation. -->

## Install

Requirements

- Python >= 3.8
- numpy, scipy, pyscf (declared in `pyproject.toml`)

Editable install from repo root:

```bash
python -m pip install -e .
```

<!-- If PySCF wheels are not available for your platform, follow the PySCF installation guide for your environment before installing this package. -->

## Quickstart

Run the included water–ammonia example from the repository root:

```bash
python examples/water_ammonia/water_ammonia_calculation.py
```

- `fragci.FragCI`: sets up active fragments, extracts active-space integrals, and runs the self-consistent CASCI loop. Pluggable `fcisolver` defaults to PySCF FCI.
- `fragpt2.FragPT2`: inherits from `FragCI` and adds PT2 drivers for dispersion (`run_disp`), charge-transfer (`run_1ct`, `run_2ct`), and TT (`run_tt`).
- `utils.pyscf_util.unpack_pyscf`: loads MO energies and coefficients from a simple text format used by the examples.

## Conventions you should know

- All MO indices in the code are 0-based. Examples convert human-friendly 1-based inputs via `np.array(idx) - 1`.
- The integrals tuple is `(c1, c2)` with chemists' two-electron convention; the active-space projection returns `(c0, c1, c2)` where `c0` is the core energy contribution.
- Fragment-local indices `l1_idx`, `l2_idx` enumerate positions within the active space; `act_idx_l1`/`act_idx_l2` are the corresponding global MO indices.

## Performance notes

- 4-RDMs and especially 5-RDMs are expensive. `FragPT2` caches intermediates on the instance (`self.rdms`, `self.rdms5`, `ct_rdms`, etc.). Reuse the same object to avoid recomputation.
<!-- - Memory usage grows quickly with active-space size; ensure sufficient RAM for FCI/RDM steps. The example sets `mol.max_memory = 8000` (MB). -->
<!-- 
## Developing and extending

- New PT2 term: implement a module under `src/fragpt2/perturbations/` exposing `get_h0(indices, rdms, ...)`, `get_ovlp(indices, rdms, ...)`, and `get_h_prime(indices, rdms, integrals)`. See `fragpt2_1ct.py` and `fragpt2_disp.py` for concrete patterns.
- New RDM mapping: add under `src/fragpt2/rdm/`, then wire it into the appropriate `FragPT2.get_*_rdms` method with caching.
- Swapping FCI backend: set `FragCI.fcisolver` to a drop-in taking `(c1, c2, norb, nelec, ...)` and returning `(energy, civec)`.

## Troubleshooting

- Import errors: ensure you installed with `pip install -e .` from the repo root so the `src/` layout is discoverable.
- PySCF install: if wheels are missing for your platform, consult PySCF docs for build/conda instructions, then reinstall this package.
- Shape mismatches: most einsum expressions assume specific axis orders; print array shapes near the failing call and verify active-space indices.

## License and citation

This is academic research software. If you plan to publish results produced with this code, please contact the authors for citation guidance. -->
