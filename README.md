# SHIM Latency Measurement and Insertion

- [SHIMの遅延値計測、挿入](#shimの遅延値計測挿入)
  - [はじめに](#はじめに)
  - [遅延値計測](#遅延値計測)
  - [遅延値挿入](#遅延値挿入)

## Description

This directory provides functions to handle SHIM latency.
SHIM is a hardware abstraction.

Follow the steps below to complete the process.

 1. [Latency Measurement](#Latency Measurement)
 2. [Latency Insertion](#Latency Insertion)

## Latency Measurement

Execute the measurement programs on the actual machine and output the execution cycles and memory access count.
This feature is available in the [*measure*](./mesure) directory.

## Latency Insertion

Calculate the latency using the execution cycles obtained in the previous step.
This feature is available in the [*latency*](./latency) directory.