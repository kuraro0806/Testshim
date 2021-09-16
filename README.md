# SHIM Latency Measurement and Insertion

- [SHIM Latency Measurement and Insertion](#shim-latency-measurement-and-insertion)
  - [Description](#description)
  - [Latency Measurement](#latency-measurement)
  - [Latency Insertion](#latency-insertion)

## Description

This directory provides functions to handle SHIM latency.
SHIM is a hardware abstraction.

Follow the steps below to complete the process.

 1. [Latency Measurement](#Latency-Measurement)
 2. [Latency Insertion](#Latency-Insertion)

## Latency Measurement

Execute the measurement programs on the actual machine and output the execution cycles and memory access count.
This feature is available in the [*measure*](./mesure) directory.

## Latency Insertion

Calculate the latency using the execution cycles obtained in the previous step.
This feature is available in the [*latency*](./latency) directory.