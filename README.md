# Intelligent Traffic Signal Control Using Reinforcement Learning

This project explores adaptive traffic signal control with reinforcement learning on SUMO traffic simulations. It includes experiments for a single-intersection network and a larger IISER map using tabular Q-learning, SARSA, and Deep Q-Network (DQN) agents.

The repository is intended for research, experimentation, and comparison of traffic-control strategies such as fixed-time, actuated, random, and learned signal policies.

## Project Goals

- Model traffic intersections in SUMO using network, route, and configuration XML files.
- Train reinforcement-learning agents to choose traffic-light phases.
- Compare learned policies against fixed-time, actuated, and random baselines.
- Track reward curves, trip statistics, and TensorBoard logs.
- Save trained Q-tables and PyTorch model checkpoints for later evaluation.

## Techniques Used

- **Q-learning**: off-policy tabular RL agent for single-agent traffic signal control.
- **SARSA**: on-policy tabular RL agent for single-agent traffic signal control.
- **DQN**: neural-network Q-learning using PyTorch for larger or multi-agent traffic scenarios.
- **SUMO-RL**: Gymnasium/PettingZoo-compatible wrapper around SUMO traffic environments.
- **TensorBoard**: reward and exploration tracking during tabular-agent training.

## Repository Layout

```text
.
|-- maps/
|   |-- maps1/                  # Simple two-way single-intersection map
|   |-- maps2/                  # Single-intersection map with generated traffic outputs
|   `-- maps3/
|       `-- env_iiser/          # IISER traffic network and route files
|-- rl-agents/
|   |-- ql/                     # Q-learning notebooks, Q-table, logs, benchmark outputs
|   |-- SARSA/                  # SARSA notebook/script, Q-table, logs, benchmark outputs
|   `-- dqn/                    # DQN notebooks, IISER environment copy, models, results
|-- pyproject.toml              # Python project metadata and dependencies
|-- uv.lock                     # Locked dependency versions for uv
`-- README.md
```

## Main Components

### `maps/`

Contains SUMO environment assets used by the agents.

| Directory | Purpose |
| --- | --- |
| `maps/maps1/` | Basic two-way single-intersection scenario. |
| `maps/maps2/` | Single-intersection scenario with baseline, actuated, random, and evaluation trip outputs. |
| `maps/maps3/env_iiser/` | Larger IISER traffic environment with network, trip, route, and simulation config files. |
| `maps/maps3/env_iiser/new/` | IISER route split by vehicle type: cars, buses, bikes, and ambulance. |

Common SUMO file types:

- `.net.xml`: road network and traffic-light definitions.
- `.rou.xml`: routes and traffic demand.
- `.sumocfg`: SUMO simulation configuration.
- `.add.xml`: additional SUMO definitions, such as detectors or vehicle types.
- `tripinfo*.xml`: simulation output containing per-vehicle travel statistics.

### `rl-agents/ql/`

Contains the tabular Q-learning experiment.

Important files:

- `ql.ipynb`: Q-learning training and evaluation workflow.
- `benchmark.ipynb`: baseline and learned-policy comparison workflow.
- `qlearning_qtable.pkl`: saved Q-table.
- `qlearning_tensorboard/`: TensorBoard event logs.
- `reward.png`, `benchmark.png`: result visualizations.
- `tripinfo_eval.xml`, `baseline_tripinfo.xml`, `actuated_tripinfo.xml`, `random_tripinfo.xml`: evaluation outputs.

The Q-learning agent discretizes observations by rounding state values and stores action values in a Python dictionary-backed Q-table.

### `rl-agents/SARSA/`

Contains the SARSA experiment.

Important files:

- `main.ipynb`: SARSA training and evaluation workflow.
- `train.py`: script version of the SARSA training loop.
- `benchmark.ipynb`: baseline and learned-policy comparison workflow.
- `sarsa_qtable.pkl`: saved SARSA Q-table.
- `sarsa_tensorboard/`: TensorBoard event logs.
- `reward.png`, `benchmark.png`: result visualizations.
- `tripinfo_eval.xml`, `baseline_tripinfo.xml`, `actuated_tripinfo.xml`, `random_tripinfo.xml`: evaluation outputs.

SARSA differs from Q-learning by updating values with the next action actually selected by the policy, making it an on-policy method.

### `rl-agents/dqn/`

Contains DQN experiments for the IISER environment and 3x3/single-map benchmarks.

Important files and directories:

- `iiser.ipynb`, `iiser-v2.ipynb`, `iiser-v3.ipynb`: DQN training notebooks for the IISER environment.
- `benchmark.ipynb`, `benchmark-3x3.ipynb`: evaluation and comparison notebooks.
- `env_iiser/`: local copy of the IISER SUMO environment used by the notebooks.
- `models/`: saved PyTorch `.pth` checkpoints.
- `models/iiser/`: standard IISER DQN checkpoints.
- `models/iiser_priority/`: priority-reward DQN checkpoints.
- `results/`: generated tripinfo XML files for DQN and baselines.
- `runs/dqn_training/`: TensorBoard-compatible training logs.
- `training_curve.png`, `benchmark.png`: result visualizations.

The DQN notebooks define:

- a PyTorch `QNetwork`,
- independent Q-networks for traffic-signal agents,
- replay-buffer based training,
- epsilon-greedy action selection,
- priority-aware reward and observation functions for emergency or high-priority vehicles,
- periodic and final model checkpoint saving.

## Requirements

### Software

- Python **3.12+**
- [SUMO](https://www.eclipse.dev/sumo/)
- `uv` or `pip`
- Jupyter Notebook or JupyterLab

### Python Dependencies

The declared dependencies are in `pyproject.toml`:

- `matplotlib`
- `numpy`
- `stable-baselines3[extra]`
- `sumo-rl`
- `torch`

Notebook workflows also use packages commonly installed with the listed dependencies, including Gymnasium, PettingZoo-style environments, TensorBoard, and tqdm.

## SUMO Setup

SUMO must be installed and available through the `SUMO_HOME` environment variable.

### Windows PowerShell

```powershell
$env:SUMO_HOME = "C:\Program Files (x86)\Eclipse\Sumo"
$env:Path += ";$env:SUMO_HOME\bin"
```

To make this permanent, add `SUMO_HOME` and the SUMO `bin` directory to your Windows environment variables.

### Linux/macOS

```bash
export SUMO_HOME=/path/to/sumo
export PATH="$SUMO_HOME/bin:$PATH"
```

Verify SUMO is available:

```bash
sumo --version
```

## Installation

### Option 1: Using uv

```bash
uv sync
```

Then activate the virtual environment if needed:

```bash
.venv\Scripts\activate
```

On Linux/macOS:

```bash
source .venv/bin/activate
```

### Option 2: Using pip

```bash
python -m venv .venv
.venv\Scripts\activate
python -m pip install --upgrade pip
python -m pip install matplotlib numpy "stable-baselines3[extra]" sumo-rl torch
```

On Linux/macOS, replace the activation command with:

```bash
source .venv/bin/activate
```

## Running Experiments

Most workflows are notebook-based. Start Jupyter from the repository root:

```bash
jupyter lab
```

or:

```bash
jupyter notebook
```

### Train Q-learning

Open:

```text
rl-agents/ql/ql.ipynb
```

Run the notebook cells to:

1. Create the SUMO-RL environment.
2. Train the Q-learning agent.
3. Save `qlearning_qtable.pkl`.
4. Generate `tripinfo_eval.xml`.
5. Plot reward progress.

### Train SARSA

Open:

```text
rl-agents/SARSA/main.ipynb
```

or run the script from inside the SARSA directory:

```bash
cd rl-agents/SARSA
python train.py
```

The SARSA workflow trains the agent, writes TensorBoard logs to `sarsa_tensorboard/`, saves `sarsa_qtable.pkl`, and generates evaluation trip information.

### Train DQN

Open one of the DQN notebooks:

```text
rl-agents/dqn/iiser-v2.ipynb
rl-agents/dqn/iiser-v3.ipynb
```

These notebooks use:

```text
rl-agents/dqn/env_iiser/new/
```

as the IISER simulation environment. Models are saved under:

```text
rl-agents/dqn/models/iiser_priority/
```

## Benchmarking

Benchmark notebooks compare learned agents against baseline controllers.

| Agent | Benchmark notebook |
| --- | --- |
| Q-learning | `rl-agents/ql/benchmark.ipynb` |
| SARSA | `rl-agents/SARSA/benchmark.ipynb` |
| DQN | `rl-agents/dqn/benchmark.ipynb` |
| DQN 3x3 | `rl-agents/dqn/benchmark-3x3.ipynb` |

Typical benchmark outputs include:

- `baseline_tripinfo.xml`
- `actuated_tripinfo.xml`
- `random_tripinfo.xml`
- `rl_tripinfo.xml` or `tripinfo_eval.xml`
- `benchmark.png`

The notebooks parse tripinfo XML files to compare metrics such as waiting time, time loss, duration, and speed.

## TensorBoard

For Q-learning:

```bash
tensorboard --logdir rl-agents/ql/qlearning_tensorboard
```

For SARSA:

```bash
tensorboard --logdir rl-agents/SARSA/sarsa_tensorboard
```

For DQN:

```bash
tensorboard --logdir rl-agents/dqn/runs
```

Then open the URL printed by TensorBoard, usually:

```text
http://localhost:6006
```

## Outputs and Artifacts

The repository includes generated outputs from previous runs:

- Q-tables: `*.pkl`
- DQN models: `*.pth`
- SUMO trip statistics: `tripinfo*.xml`
- TensorBoard event files: `events.out.tfevents.*`
- Plots: `reward.png`, `benchmark.png`, `training_curve.png`

These artifacts are useful for reviewing previous experiments, but new runs may overwrite some of them depending on the notebook or script.

## Reproducibility Notes

- Run notebooks from their own agent directory unless paths are adjusted.
- Some notebooks use relative paths such as `env_iiser/new`, `models/iiser_priority`, `cross.net.xml`, and `cross.rou.xml`.
- SUMO simulations can produce different results if traffic demand, random seeds, or route generation settings change.
- Saved model and Q-table files represent specific training runs and may not match newly generated metrics after retraining.
- The SARSA script uses local relative network files from `rl-agents/SARSA/`.

## Troubleshooting

### `SUMO_HOME not found`

Set the `SUMO_HOME` environment variable and make sure SUMO's `bin` directory is on your `PATH`.

### `sumo` or `sumo-gui` is not recognized

Install SUMO and add the SUMO `bin` directory to your system path.

### Route or network file not found

Run the notebook or script from the directory expected by its relative paths. For example, run `train.py` from `rl-agents/SARSA/`.

### TensorBoard shows no data

Check that the training notebook or script completed at least one episode and wrote event files to the selected log directory.

### PyTorch model loading fails

Verify that the requested `.pth` file exists under `rl-agents/dqn/models/` and that the model architecture in the notebook matches the saved checkpoint.

## Suggested Workflow

1. Install SUMO and Python dependencies.
2. Verify a SUMO simulation can run using the map files.
3. Train the Q-learning or SARSA agent on the simple intersection.
4. Compare against fixed-time, actuated, and random baselines.
5. Move to DQN experiments on the IISER environment.
6. Use TensorBoard and benchmark plots to compare reward and traffic metrics.

## Future Improvements

- Convert notebook training workflows into reusable Python modules.
- Add command-line configuration for map paths, episode counts, and output directories.
- Add fixed random seeds for reproducible experiments.
- Add automated tests for tripinfo parsing and reward functions.
- Store large generated artifacts outside Git or with Git LFS.
- Document exact benchmark metric formulas beside each result plot.

## License

No license file is currently included. Add a license before distributing or reusing this project publicly.
