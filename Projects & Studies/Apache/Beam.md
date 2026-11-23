
## Introduction
The **Apache Beam SDK** is a set of open-source programming libraries used to build unified batch and streaming data processing pipelines that can run across multiple execution engines.

Instead of writing separate code for different backends, you define your data workflow once using a Beam SDK and deploy it anywhere—from local testing to distributed clusters. (Works for most conventional DB and cloud-based data warehouses).

![[Pasted image 20261123112301.png]]

---
## Core Concepts
- **Pipeline.** Encapsulates your entire data processing workflow from start to finish, including reading input data, transforming it, and writing output.

- **PCollection (Input/Output Dataset).** Represents a distributed, immutable dataset that moves through your pipeline—either bounded (fixed size for batch) or unbounded (continuous for streaming).

- **PTransform (Dataset Transformation).** Represents a data processing operation applied to one or more `PCollections`, such as filtering, grouping (`GroupByKey`), or custom element-wise logic (`ParDo`).

- **Runner.** The execution engine that translates your Beam code into a job compatible with a specific backend, such as [Google Cloud Dataflow](https://cloud.google.com/products/dataflow), [Apache Flink](https://flink.apache.org/), [Apache Spark](https://spark.apache.org/), or the local `DirectRunner`

![[Pasted image 20261123112149.png]]

---

## References & Architecture

![[Pasted image 20261123112330.png]]

![[Pasted image 20261123112158.png]]