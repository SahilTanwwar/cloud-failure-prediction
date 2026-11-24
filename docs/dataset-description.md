# Google Cluster Trace 2011 Dataset Description

## Source

This project uses the Google Cluster Trace 2011 dataset, specifically the published `clusterdata-2011-2` (v2.1) trace from a Borg cell. The official repository identifies it as a 29-day trace from May 2011 covering approximately 12,500 machines.

- Official repository: <https://github.com/google/cluster-data>
- 2011 trace description: <https://github.com/google/cluster-data/blob/master/ClusterData2011_2.md>
- Storage bucket: `gs://clusterdata-2011-2`
- License: Creative Commons CC-BY

The research reference supplied for this project is the 2022 paper **"Cloud Failure Prediction Based on Traditional Machine Learning and Deep Learning"**. Its exact bibliography, DOI, and all dataset-processing details remain **pending verification** before they are included as project methodology.

## Trace Scope

The trace contains workload and scheduling information for one Google Borg cell. It includes jobs, their component tasks, scheduling events, machine assignments, and task resource requests. It is not the 2009 Google cluster trace and is not the 2019 Borg trace.

The official documentation describes the complete compressed trace as approximately 41 GB. It should therefore not be downloaded or loaded in full for initial development.

## Tables Used by the Project

The research methodology uses two event tables:

### Job Event Table

A job event row records an event affecting a job. The confirmed v2.1 table structure has eight columns:

| Column | Meaning |
|---|---|
| `time` | Trace timestamp, in the trace's defined timestamp unit |
| `missing info` | Missing-information field associated with the event |
| `job ID` | Anonymized identifier for the job |
| `event type` | Type of job event |
| `user` | Anonymized submitting user |
| `scheduling class` | Job scheduling class/category |
| `job name` | Anonymized job name |
| `logical job name` | Anonymized logical job name |

The official schema document should be consulted for the exact timestamp unit and field encoding before implementation. Those details are currently **pending verification**.

### Task Event Table

A task event row records an event affecting an individual task belonging to a job. The confirmed v2.1 table structure has thirteen columns:

| Column | Meaning |
|---|---|
| `time` | Trace timestamp, in the trace's defined timestamp unit |
| `missing info` | Missing-information field associated with the event |
| `job ID` | Anonymized identifier for the job |
| `task index` | Index of the task within the job |
| `machine ID` | Identifier of the machine involved, when applicable |
| `event type` | Type of task event |
| `user` | Anonymized submitting user |
| `scheduling class` | Task scheduling class/category |
| `priority` | Task priority at the time of the event |
| `CPU request` | Requested CPU resources |
| `memory request` | Requested memory resources |
| `disk space request` | Requested local-disk resources |
| `different machines restriction` | Boolean placement restriction |

Resource requests and machine identifiers are trace-normalized or anonymized where documented by the schema. Exact units, normalization behavior, and constraint semantics should be confirmed from the v2.1 schema document before training.

## Event Types

The 2011 trace documentation and schema distinguish scheduling lifecycle events. The generally documented event values are:

| Value | Meaning |
|---:|---|
| 0 | SUBMIT |
| 1 | SCHEDULE |
| 2 | EVICT |
| 3 | FAIL |
| 4 | FINISH |
| 5 | KILL |
| 6 | LOST |
| 7 | UPDATE_PENDING |
| 8 | UPDATE_RUNNING |

The meaning of every value for the job and task tables should be validated against the downloaded schema and files. Applicability may differ by table; values 7 and 8 are update events and should not be assumed to occur in both tables without verification.

## Dataset Format and Download

The trace is distributed from Google Cloud Storage in the `clusterdata-2011-2` bucket. The repository instructs users to use `gcloud storage` for downloading. Files are published in compressed form; the complete trace is approximately 41 GB compressed.

The exact object names and shard layout are now confirmed. Both tables use 500 gzip-compressed CSV shards named `part-NNNNN-of-00500.csv.gz` under their respective prefixes. The following representative objects were downloaded for initial inspection:

- `gs://clusterdata-2011-2/job_events/part-00000-of-00500.csv.gz`
- `gs://clusterdata-2011-2/task_events/part-00000-of-00500.csv.gz`
- `gs://clusterdata-2011-2/schema.csv`
- `gs://clusterdata-2011-2/README`

The downloaded files are retained unchanged in `ml/dataset/raw/job_events/`, `ml/dataset/raw/task_events/`, and `ml/dataset/sample/`.

### Planned storage locations

Original, unmodified data:

- `ml/dataset/raw/job_events/`
- `ml/dataset/raw/task_events/`

Small inspection samples:

- `ml/dataset/sample/`

Reproducible processed datasets:

- `ml/dataset/processed/jobs.csv`
- `ml/dataset/processed/tasks.csv`

Raw data must remain unchanged. Processed files should be generated from raw files by versioned scripts rather than edited manually.

## Sampling and Download Approach

For the first implementation:

1. Install and authenticate Google Cloud CLI if not already available.
2. List the official bucket contents.
3. Identify the exact job-event and task-event prefixes and shard names.
4. Download only a small number of representative shards or a bounded file range.
5. Never download the complete 41 GB trace during initial development.
6. Record source URLs, object names, downloaded checksums, and sampling dates.
7. Keep unmodified downloads in the raw directories and derive all analysis datasets under `processed/`.

A useful first inspection command for a confirmed shard is:

```bash
cd ~/cloud-failure-prediction
mkdir -p ml/dataset/sample

gsutil cp 'gs://clusterdata-2011-2/job_events/part-00000-of-00500.csv.gz' ml/dataset/sample/
gzip -cd ml/dataset/sample/part-00000-of-00500.csv.gz | head -n 20
```

The exact object paths above were confirmed by listing the official bucket.

Bucket discovery commands do not download trace data:

```bash
gcloud storage ls gs://clusterdata-2011-2/ | head -n 100
gcloud storage ls gs://clusterdata-2011-2/job_events/ | head -n 20
gcloud storage ls gs://clusterdata-2011-2/task_events/ | head -n 20
```

The `job_events/` and `task_events/` prefix names are confirmed.

## Downloaded Sample Provenance

The two representative shards contain:

- Job events: 10,704 records.
- Task events: 450,146 records.

SHA-256 checksums:

- Job shard: `921fa8e019fb8ec5be9a2cb096912850d5959da33d9fb3b46807d9d0a9eae86f`
- Task shard: `eae977d521bc6ed5d8deef56f1e06009d41844d4eb9a7e308e610d68e1b10f18`
- Schema: `b3490ab5ad8806ef30eb5bdf432b662bc4e280abdec549bb3c3edb4b1ab6baeb`
- README: `34ed43fef528d8c972b0559790d9c149755c8acc683b764cc5e2fdf5e77d4008`

## Known Limitations

Verified official documentation limitations include:

- The trace covers one Borg cell and should not be assumed to represent all Google clusters or general cloud workloads.
- Some jobs are omitted or have no task-event records.
- Missing and lost event records exist.
- Disk-time-fraction data is incomplete after a monitoring-system change in the first 14 days.
- Some resource usage measurements are missing or anomalous.
- Identifiers and resource values are anonymized or normalized.

Additional dataset limitations, class imbalance, and label quality will be measured empirically during EDA.

## Job-Level and Task-Level Prediction

### Job prediction

Job-level observations represent a job as a whole. Labels must be derived from job-event outcomes and other job records. The training set must prevent future or outcome fields from leaking into features.

The target will be whether a job finishes successfully or fails. Exact label derivation is pending implementation and will be documented in preprocessing code.

### Task prediction

Task-level observations represent one task identified by `(job ID, task index)`. Labels must be derived from task-event outcomes. Features must describe information available before the target outcome to avoid leakage.

The target will be whether an individual task finishes successfully or fails. Exact label derivation is pending implementation.

## Pending Verification

Before preprocessing and modeling, verify:

- Exact v2.1 timestamp units and absolute trace-time conversion.
- Event-type applicability and meaning for each table.
- The 2022 paper's DOI, authors, data split, label construction, and baseline models.
- Whether the paper's terminology corresponds exactly to this v2.1 trace or to a derived dataset.
