# Cloud Failure Prediction

Exploration of the Google Cluster Trace 2011 (`clusterdata-2011-2`) dataset for
cloud workload failure prediction.

## Dataset

The project analyzes job-event and task-event records from the Google Borg
cluster trace. Raw dataset shards are not included in this repository. See
[the dataset documentation](docs/dataset-description.md) for provenance,
schema, and instructions for obtaining the sample shards.

## Setup

Use Python 3 and install the project dependencies:

```bash
python -m pip install -r ml/requirements.txt
```

The exploratory notebook is
[`ml/notebooks/01_data_exploration.ipynb`](ml/notebooks/01_data_exploration.ipynb).
It expects the downloaded job and task event shards under `ml/dataset/raw/`.

The `ml/src/` training, preprocessing, and evaluation files are currently
placeholders; model training is not implemented yet.

## Repository contents

- `docs/`: dataset provenance and schema notes
- `ml/notebooks/`: exploratory analysis
- `ml/src/`: planned preprocessing, training, and evaluation modules
- `ml/requirements.txt`: Python dependencies