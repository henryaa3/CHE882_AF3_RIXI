# CHE882_AF3_RIXI
AlphaFold3 for RIXI Protein
# AlphaFold3 on RIXI: Attempted HPCC Workflow

This repository documents the attempted workflow used to run **AlphaFold3** on the **RIXI protein** on the MSU HPCC system. It is written as a reproducible project summary so the setup, scripts, and failure points are all in one place.

The purpose of this workflow was to:
1. Take the RIXI protein sequence or generated RIXI mutant structures
2. Run AlphaFold3 on HPCC
3. Obtain structure confidence outputs such as **pTM/ipTM-style scores**
4. Use those scores to compare wild type and designed mutants

This repo is also useful as a handoff document because AlphaFold3 setup on shared clusters can fail for reasons unrelated to the actual protein input, especially around model weights, containers, paths, and cluster environment requirements.

---

## Project goal

The main goal was to run AlphaFold3 on **RIXI** and related mutant outputs so the resulting predictions could be scored and ranked. In practice, the user wanted AlphaFold3 mainly for **confidence metrics**, especially pTM or ipTM-type outputs, rather than only for raw structure prediction.

The intended workflow was:

- prepare RIXI input
- point AlphaFold3 to the correct model weights
- run AlphaFold3 inside a supported environment or container on HPCC
- collect JSON outputs containing ranking and confidence values
- compare RIXI wild type and mutant outputs

---

## What this repository contains

```text
alphafold3-rixi-attempt/
├── README.md
├── .gitignore
├── scripts/
│   ├── submit_alphafold3_rixi.slurm
│   ├── run_alphafold3_rixi.sh
│   ├── prepare_rixi_fasta.py
│   └── summarize_alphafold3_outputs.py
└── examples/
    ├── rixi.fasta
    └── expected_output_notes.txtImportant note

This repository is a documented reconstruction of the AlphaFold3 workflow attempt. It is not presented as a guaranteed plug-and-play AlphaFold3 install because the exact container build, weight placement, and cluster-specific runtime details were not fully stabilized during the original attempt.

So this repo is best understood as:

a clean summary of the attempted setup
a reusable starting point
a record of the key problems encountered
a bridge to future reruns or replacement with Boltz2 or another easier scoring model
Main issues encountered

Two main issues came up repeatedly:

AlphaFold3 setup on HPCC was difficult to stabilize, especially around container creation, file paths, model weight locations, and environment configuration.
The goal was mainly to extract pTM/ipTM-like confidence scores from designed RIXI outputs, but AlphaFold3 was too difficult to get running reliably enough on the cluster, which motivated looking at easier alternatives like Boltz2.

These two points summarize the practical outcome of the AlphaFold3 effort.

Intended input types

This workflow was meant to support one or both of the following:

1. Sequence-based RIXI prediction

A FASTA file containing the wild type or mutated RIXI sequence.

2. Designed RIXI variants

RIXI variants generated from external tools such as RFdiffusion, with the hope of rescoring or comparing them through AlphaFold3 confidence outputs.

Expected environment on HPCC

The scripts assume a Linux cluster environment with:

Slurm scheduler
GPU access
Python available through a module or virtual environment
AlphaFold3 source checked out locally
AlphaFold3 model weights already downloaded
Optionally a Singularity or Apptainer image if the local build path required containerization

Because AlphaFold3 setup varies by cluster, you may need to edit:

paths to the AlphaFold3 repository
paths to the model weights
paths to databases
module load lines
GPU resource requests
Basic usage
Step 1. Prepare the FASTA

Example:

python scripts/prepare_rixi_fasta.py

This writes a simple FASTA file for the RIXI sequence.

Step 2. Edit the Slurm script

Open:

scripts/submit_alphafold3_rixi.slurm

Update the following variables:

AF3_REPO
AF3_WEIGHTS
AF3_DB_DIR
INPUT_FASTA
OUTPUT_DIR

These paths must match your actual HPCC directories.

Step 3. Submit the job
sbatch scripts/submit_alphafold3_rixi.slurm
Step 4. Summarize outputs

If AlphaFold3 completes and writes output JSON files, summarize them with:

python scripts/summarize_alphafold3_outputs.py /path/to/output_dir

This script searches for JSON output files and extracts confidence-related fields if present.

Why AlphaFold3 was hard to run here

The main bottlenecks were not really about RIXI itself. The major barriers were infrastructure-related:

1. Model weights and paths

AlphaFold3 requires the correct weight files in the correct locations, and a mismatch in expected paths can stop the run before inference even begins.

2. Container or environment setup

Running AlphaFold3 on a shared cluster often depends on:

a compatible CUDA environment
a valid container build
correct mount points
correct Python package versions

If any one of those pieces is off, the job fails before useful outputs are generated.

3. Large dependency stack

AlphaFold3 is not a light tool. It depends on substantial infrastructure compared with smaller or more cluster-friendly alternatives.

4. User goal was scoring, not only structure prediction

In this project, the main value was confidence metrics for RIXI and mutants. Because AlphaFold3 setup took so much effort, it became more practical to consider alternative models that could return comparable ranking information with less overhead.

Why this mattered for RIXI

The RIXI project involved exploring mutations and ranking candidate designs. AlphaFold3 was attractive because the confidence metrics could have helped compare:

wild type RIXI
manually designed mutants
RFdiffusion-derived variants
possible complexes involving RIXI and target proteins

The problem was that the setup burden was too high relative to the immediate need for scoring.

Recommended next step

If the current goal is to rank RIXI variants rather than fully reproduce AlphaFold3, a more practical workflow is:

use a lighter prediction/scoring model such as Boltz2
collect confidence outputs across all mutants
rank variants by score
visualize the results with summary tables or heat maps

That path is usually much easier to maintain on HPCC.

Summary

This repository documents the AlphaFold3 attempt for RIXI as a reproducible GitHub-style record. The technical goal was sound, but the cluster setup required to run AlphaFold3 reliably was the main limiting factor. The attempt showed that the real challenge was environment deployment, not the biological input. For that reason, easier tools became the more practical option for comparing RIXI variants.
