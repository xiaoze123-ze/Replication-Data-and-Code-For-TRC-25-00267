# Experiments for *Collaborative optimization of train timetabling with maintenance window setting and maintenance scheduling considering fluctuating daily train volumes and dynamic maintenance needs*

This repository contains a set of Jupyter notebooks used in our study.

All experiments are implemented in Python, with models solved using the [Gurobi Optimizer](https://www.gurobi.com/).

------

## Repository Structure

The repository includes four main notebooks:

1. **`generate_basic_data.ipynb`** - Purpose: Generate the basic data used in the experiments.
   - Notes: Although we provide the basic data used in our study, users can also run this notebook to generate their own data.
   - Output: A structured data for subsequent experiments.
2. **`experiment_with_different_daily_train_volumes_maintenance_volume_mutually_exclusion_max_compatible_num.ipynb`** - Related to **Sections 7.1 and 7.4** of our research.
   - Purpose: Test the impact of different daily train volumes and maintenance volume.
   - How to use: Modify model parameters in the "Input Basic Data" section at the beginning of the notebook.
3. **`experiment_for_fixed_maintenance_scheduling.ipynb`** - Related to **Section 7.2**.
   - Purpose: Generate a fixed maintenance scheduling plan.
   - Output: A fixed maintenance scheduling plan with visualization.
4. **`experiment_of_solving_model_by_directly_calling_gurobi_solver.ipynb`** - Related to **Section 7.3**.
   - Purpose: Construct the collaborative optimization model and solve it directly using the Gurobi solver.
   - Output: Optimal/near-optimal solutions to the collaborative optimization problem if the model can be solved successfully.

------

## Requirements

- Python 3.10 +
- Jupyter Notebook / JupyterLab
- [Gurobi Optimizer](https://www.gurobi.com/) with a valid license
- Required Python packages:
  - `numpy`      
  - `pandas`
  - `gurobipy`
  - `matplotlib`

------

## Data Generation

In this case study, we stipulate that the planning horizon lasts three days. Due to the protection of commercial data, we are unable to obtain actual train operation and maintenance data. However, through investigation, we generate data consistent with the operational conditions, which is introduced as follows.

The first and most important to state is that in China, railway maintenance is typically performed at night to avoid peak daytime train operation periods. Thus, within the planning horizon, our experiments are conducted only between 18:00 on each day (hereinafter referred to as 18:00) and 6:00 on the following day (hereinafter referred to as 6:00(+1)).

**Train Operation Data**

In terms of train operation data, we plan to generate three types of daily train volume, namely 30, 40 and 50 trains departing from their start station between 18:00 and 6:00(+1) on each day, that is, the daily train volumes are 30, 40 and 50 respectively.

For passenger train operations, by querying on the China Railway Ticketing APP 12306 and for convenience, we assume that there are 8 passenger trains running between 18:00 and 6:00(+1) on each day, which depart from the start station (WC) at 18:30, 19:30, 20:30, 21:30, 4:15(+1), 4:45(+1), 5:15(+1), and 5:45(+1) respectively, and stop at YY and CS stations for 8 and 15 min, and finally arrive at the end station (ZZ). It should be noted that all passenger trains are high-speed trains (with a speed of 160km/h).

Then, we randomly generate freight train operation information, and the departure time window distribution of freight trains is different under different daily train volumes:

- **Volume 30:** We assume that 5, 5, 7, and 5 trains depart from the start station (WC) between 18:00-21:00, 21:00-0:00(+1), 0:00(+1)-3:00(+1), and 3:00(+1)-6:00(+1), respectively. Of these, 1, 1, 2, and 2 trains are slow-speed trains (with a speed of 120 km/h), and the rest are high-speed trains.
- **Volume 40:** 8, 8, 10, and 6 trains depart from the start station (WC) between the same time intervals respectively. Of these, 1, 2, 2, and 2 trains are slow-speed trains, and the rest are high-speed trains.
- **Volume 50:** 11, 11, 12, and 8 trains depart from the start station (WC) between the same time intervals respectively. During the above time periods, 2, 2, 3, and 2 trains are slow trains, and the rest are fast trains.

Note that the stop plan of each train is randomly generated within a reasonable range, with each train stops at least one times. If a train is originally scheduled to stop at a certain station, the minimum dwell time is set to 3. The pure running time of each freight train in each section depends on its rated speed and the section length shown in Table 4 of the main paper.

**Maintenance Needs**

In terms of maintenance needs, according to China Railway maintenance requirements, there are 17 types of main maintenance projects involved, which and their duration are listed in Table 1.

**Table 1: Main railway maintenance projects with duration**

| **No.** | **Railway maintenance projects**                             | **Duration (min)** |
| ------- | ------------------------------------------------------------ | ------------------ |
| 1       | Gage correction                                              | 20                 |
| 2       | Bolt fasteners oiling                                        | 15                 |
| 3       | Tamping                                                      | 50                 |
| 4       | Grouped turnout replacement                                  | 60                 |
| 5       | Fence repairs                                                | 40                 |
| 6       | Precise track alignment                                      | 40                 |
| 7       | Replacement of rail components                               | 30                 |
| 8       | Rail and turnout grinding with track maintenance machines    | 50                 |
| 9       | Ballast adjustment                                           | 20                 |
| 10      | Replacement of faulty equipment in color light signal        | 15                 |
| 11      | Internal maintenance of electric turnout switch devices      | 30                 |
| 12      | Section track circuit insulation device maintenance          | 40                 |
| 13      | Internal operations of electro-hydraulic turnout switch devices | 30                 |
| 14      | Balise equipment maintenance                                 | 20                 |
| 15      | Antenna equipment maintenance                                | 30                 |
| 16      | Cleaning and maintenance of overhead contact system insulation components | 30                 |
| 17      | Replacement of overhead contact system components            | 50                 |

Among them, maintenance projects 3, 4, and 8 are major maintenance that the related major maintenance activities must be scheduled on each day within the planning horizon. Other maintenance projects are routine maintenance that the related routine maintenance activities can be scheduled on any day within the flexible scheduling range, or not be scheduled, but will face penalties.

The following describes how to generate random maintenance needs. First, we generate two types of total maintenance volume within the planning horizon. Specifically, we generate the total of 240 and 180 maintenance activities, respectively. Since a total of 6 sections are involved, each section has an average of 40 and 30 maintenance activities within the planning horizon, respectively. Note that we set up 3 major maintenance activities related to maintenance project 3, 4, and 8 in the sections WC-XN, ZLQ-YY, and CS-ZZ, respectively, and there are no major maintenance activities in other sections.

Then, we generate the distribution of maintenance volume within the planning horizon. Under the fixed maintenance scheduling mode, the maintenance contents are fixed on each day. That is, each routine maintenance activity has a fixed scheduling day. To simulate different maintenance volume distributions, we generate two maintenance volume distribution scenarios: one in which maintenance volume is relatively balanced across the days, and the other in which the opposite occurs, with a highly uneven distribution of maintenance volume across the days. Specifically, under the fixed maintenance scheduling mode, for the former, days 1, 2, and 3 have 65, 80, and 92 routine maintenance activities, respectively; for the latter, days 1, 2, and 3 have 43, 71, and 123 routine maintenance activities, respectively. Notably, both exhibit an increasing trend in the number of routine maintenance activities. This serves to better illustrate the superior performance of the dynamic maintenance scheduling mode in coordinating daily train volumes and maintenance volume under the unbalanced daily train volumes of “30-40-50”. However, the former shows more balanced compared to the latter.

Afterwards, we randomly expand the optimal scheduling range for each routine maintenance activity based on its fixed scheduling day. Before doing so, we randomly assign the importance of routine maintenance activities at a ratio of 50%, 30%, and 20%, divided into three levels: 1, 2, and 3, corresponding to “normal”, “important”, and “very important”, respectively, to simulate the different importance of railway asset components to operational safety. For routine maintenance activities with importance level 3, the optimal scheduling range is limited to the fixed scheduling day, as mentioned above. Similarly, for routine maintenance activities with importance level 2, the optimal scheduling range is defined as the fixed scheduling day and its adjacent days. For routine maintenance activities with importance level 1, the optimal scheduling range is the entire planning horizon. Finally, we randomly select some routine maintenance activities with an optimal scheduling range of day 1, days 1 to 2, or days 1 to 3 as being based on the maintenance threshold, and the remaining routine maintenance activities are based on the flexible maintenance range.

Thus, although our maintenance needs are randomly generated, it conforms to the dynamic maintenance need characteristics introduced in Section 3.1.1 of the main paper and can also be well mapped to the actual railway maintenance needs. In addition, maintenance needs are not only reflected in the maintenance volume, but also related to the number of mutually exclusive relationships between maintenance activities and the number of maintenance activities allowed to be scheduled simultaneously. Imagine that the more mutually exclusive maintenance activities there are and the fewer maintenance activities are allowed to be scheduled simultaneously, the more difficult it will be to shorten the maintenance window duration, thereby compressing track capacity and affecting train operations. Thus, in the following case studies, we define 29 and 39 mutually exclusive pairs of maintenance activities, respectively, and limit the number of maintenance activities allowed to be scheduled simultaneously to 3 or 4. This allows us to explore the impact of these two factors on the coordination of transportation and maintenance, as well as their implications for real-world railway maintenance.
