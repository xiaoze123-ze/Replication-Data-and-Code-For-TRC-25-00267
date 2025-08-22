# Experiments for *Collaborative optimization of train timetabling with maintenance window setting and maintenance scheduling considering fluctuating daily train volumes and dynamic maintenance needs*

This repository contains a set of Jupyter notebooks used in our study.  
All experiments are implemented in Python, with models solved using the [Gurobi Optimizer](https://www.gurobi.com/).

---

## Repository Structure

The repository includes four main notebooks:

1. **`generate_basic_data.ipynb`**  
   - Purpose: Generate the basic data used in the experiments.  
   - Notes: Although we provide the basic data used in our study, users can also run this notebook to generate their own data.  
   - Output: A structured data for subsequent experiments.

2. **`experiment_with_different_daily_train_volumes_maintenance_volume_mutually_exclusion_max_compatible_num.ipynb`**  
   - Related to **Sections 7.2 and 7.5** of our research.  
   - Purpose: Test the impact of different daily train volumes and maintenance volume.
   - How to use: Modify model parameters in the "Input Basic Data" section at the beginning of the notebook.

3. **`experiment_for_fixed_maintenance_scheduling.ipynb`**  
   - Related to **Section 7.3**.
   - Purpose: Generate a fixed maintenance scheduling plan.  
   - Output: A fixed maintenance scheduling plan with visualization.

4. **`experiment_of_solving_model_by_directly_calling_gurobi_solver.ipynb`**  
   - Related to **Section 7.4**.  
   - Purpose: Construct the collaborative optimization model and solve it directly using the Gurobi solver.  
   - Output: Optimal/near-optimal solutions to the collaborative optimization problem if the model can be solved successfully.

---

## Requirements

- Python 3.10 +
- Jupyter Notebook / JupyterLab  
- [Gurobi Optimizer](https://www.gurobi.com/) with a valid license
- Required Python packages:
  - `numpy`
  - `pandas`
  - `gurobipy`
  - `matplotlib`

