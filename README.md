# SpoofCloudFinder

Live demo available here: [https://digitalfreelancing.co.uk](https://digitalfreelancing.co.uk) 

## Files

| Path | Description |
| --- | --- |
| `Datasets/April-May 2026.parquet` | Primary dataset file. |
| `dataset_profile.ipynb` | Profiling notebook used to inspect schema, coverage, labels, and summary statistics. |

## Data Collection

The upstream AIS corpus used for SpoofCloudFinder was assembled from four freely accessible sources during April and May 2026, then standardised into a unified Parquet pipeline before filtering and labelling. The repository dataset is a labelled subset of that larger collection.

| Source | How data was collected | Geographic coverage |
| --- | --- | --- |
| Danish Maritime Authority (DMA) | Daily `aisdk-YYYY-MM-DD.zip` archives were downloaded from the public DMA AIS portal and extracted from CSV files. | Danish waters and nearby surrounding seas. |
| Fintraffic / Digitraffic | Live AIS messages were collected from the Fintraffic MQTT-over-WebSocket feed on `meri.digitraffic.fi`, subscribing to vessel location and metadata topics. | Eastern Baltic and Gulf of Finland coverage. |
| AISStream.io | Live AIS messages were collected from the AISStream WebSocket API using a world bounding box subscription. | Worldwide community-operated coverage. |
| AISHub | Polled snapshots were fetched from the AISHub `ws.php` endpoint, typically returned as zipped JSON (`compress=1`). | Worldwide community-operated coverage. |

In the full study, these sources were chosen because they provided public or research-permitted access to largely unfiltered AIS messages at a scale suitable for coordinated spoofing analysis. Coverage was strongest across Northern Europe and major European coastal regions, with additional wider-area visibility from the two global community feeds.

## Dataset Summary
aiss
| Field | Value |
| --- | --- |
| Format | Parquet |
| Rows | 2,909,832 |
| Raw columns | 9 |
| Time span | 2026-04-01 00:00:00.127201 to 2026-05-31 23:59:57 |
| Unique MMSI | 14,373 |
| Labeled rows | 2,909,832 |
| Positive labels | 261,627 |
| Negative labels | 2,648,205 |
| Positive label rate | 8.9911% |

## Schema

| Column | Type | Description |
| --- | --- | --- |
| `timestamp` | `datetime64[ns]` | Message timestamp. |
| `mmsi` | `str` | Vessel MMSI identifier. |
| `lat` | `float64` | Latitude in decimal degrees. |
| `lon` | `float64` | Longitude in decimal degrees. |
| `sog` | `float64` | Speed over ground. |
| `cog` | `float64` | Course over ground. |
| `y_true` | `bool` | Binary spoofing label. |
| `original_file` | `str` | Source slice filename carried into the combined dataset for provenance. |

The profiling notebook uses a reduced analysis view based on `timestamp`, `mmsi`, `lat`, `lon`, `sog`, `cog`, and `y_true`.

## Data Quality

| Column | Nulls | Null rate |
| --- | ---: | ---: |
| `cog` | 44,054 | 1.5140% |
| `sog` | 32,757 | 1.1257% |
| `timestamp` | 0 | 0.0000% |
| `mmsi` | 0 | 0.0000% |
| `lat` | 0 | 0.0000% |
| `lon` | 0 | 0.0000% |
| `y_true` | 0 | 0.0000% |
| `original_file` | 0 | 0.0000% |

`timestamp`, `mmsi`, `lat`, `lon`, `y_true`, and `original_file` are complete. Missing values are concentrated in `sog` and `cog`.

## Selected Numeric Summary

| Column | Non-null count | Mean | Median | Min | Max |
| --- | ---: | ---: | ---: | ---: | ---: |
| `lat` | 2,909,832 | 53.4746 | 55.0331 | -90.0000 | 91.0000 |
| `lon` | 2,909,832 | 10.3308 | 11.1255 | -180.0000 | 179.9996 |
| `sog` | 2,877,075 | 13.1782 | 9.8000 | 0.0000 | 333.0000 |
| `cog` | 2,865,778 | 191.1387 | 208.3000 | 0.0000 | 409.5000 |

## Relation To The Paper

The repository dataset covers the same April-May 2026 period discussed in the SpoofCloudFinder paper, but it is a repository subset rather than the full upstream collection. The paper reports the following full-scale monthly totals:

| Month | Messages | Unique vessels |
| --- | ---: | ---: |
| April 2026 | 156 million | 107 thousand |
| May 2026 | 200 million | 180 thousand |

