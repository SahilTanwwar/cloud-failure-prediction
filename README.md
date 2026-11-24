# Cloud Failure Prediction

This project explores cloud workload failure prediction using the Google
Cluster Trace 2011 dataset. It currently contains dataset documentation and an
exploratory analysis notebook; model training has not been implemented yet.

## Dataset

The project uses the **Google Cluster Trace 2011**, published as
`clusterdata-2011-2` (v2.1), from a single Google Borg cell. The full trace
covers 29 days in May 2011 and approximately 12,500 machines. The complete
compressed dataset is about 41 GB, so this project uses only one representative
shard from each of the job-event and task-event tables.

Dataset links:

- [Official Google Cluster Data repository](https://github.com/google/cluster-data)
- [2011 trace documentation](https://github.com/google/cluster-data/blob/master/ClusterData2011_2.md)
- [Google Cloud Storage bucket](https://console.cloud.google.com/storage/browser/clusterdata-2011-2)
- [Job-event sample shard](https://storage.googleapis.com/clusterdata-2011-2/job_events/part-00000-of-00500.csv.gz)
- [Task-event sample shard](https://storage.googleapis.com/clusterdata-2011-2/task_events/part-00000-of-00500.csv.gz)
- [Dataset schema](https://storage.googleapis.com/clusterdata-2011-2/schema.csv)

The inspected sample shards contain **10,704 job-event records** and
**450,146 task-event records**. The raw files are not included in this
repository; download and provenance details are in
[the dataset documentation](docs/dataset-description.md).

## Work completed

- Confirmed the source, version, table names, shard naming, and schema for the
  2011 trace.
- Downloaded and inspected one job-event shard, one task-event shard, and the
  official schema.
- Created an exploratory notebook that examines the schema and data types,
  missing values and duplicates, identifier uniqueness, event-type counts,
  scheduling-class and task-priority distributions, task resource requests,
  and numeric correlations.
- Documented dataset provenance, sample checksums, limitations, and
  considerations for future job- and task-level failure labels.
- Set up the Python dependency list and repository documentation.

## Current status

The exploratory notebook is
[`ml/notebooks/01_data_exploration.ipynb`](ml/notebooks/01_data_exploration.ipynb).
It expects the downloaded data in the local `ml/dataset/` folders. Raw data and
generated analysis outputs are excluded from this repository.

The files in `ml/src/` (`train.py`, `preprocessing.py`, and `evaluate.py`) are
currently empty placeholders. No model has been trained, and label
construction, leakage-safe feature preparation, and model evaluation remain
future work.

## Setup

Use Python 3 and install the dependencies:

```bash
python -m pip install -r ml/requirements.txt
```

See [the dataset documentation](docs/dataset-description.md) for sample
download instructions and additional data details.

## Repository contents

- `docs/`: dataset provenance, schema, and methodology notes
- `ml/notebooks/`: exploratory data analysis
- `ml/src/`: planned preprocessing, training, and evaluation modules
- `ml/requirements.txt`: Python dependencies
