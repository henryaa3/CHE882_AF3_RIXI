#!/bin/bash
#SBATCH --job-name=af3_rixi
#SBATCH --partition=gpu
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=8
#SBATCH --mem=64G
#SBATCH --time=24:00:00
#SBATCH --output=slurm-%j.out

set -euo pipefail

# ==========================
# USER-EDITABLE PATHS
# ==========================
AF3_REPO="/path/to/alphafold3"
AF3_WEIGHTS="/path/to/alphafold3_weights"
AF3_DB_DIR="/path/to/alphafold3_databases"
INPUT_FASTA="/path/to/alphafold3-rixi-attempt/examples/rixi.fasta"
OUTPUT_DIR="/path/to/alphafold3_outputs/rixi_run"

# ==========================
# ENVIRONMENT SETUP
# ==========================
module purge
module load Python/3.10.4
# module load CUDA/appropriate-version
# module load Apptainer/appropriate-version

mkdir -p "${OUTPUT_DIR}"

echo "Starting AlphaFold3 RIXI job"
echo "AF3 repo: ${AF3_REPO}"
echo "Weights:  ${AF3_WEIGHTS}"
echo "DB dir:   ${AF3_DB_DIR}"
echo "Input:    ${INPUT_FASTA}"
echo "Output:   ${OUTPUT_DIR}"

bash "${AF3_REPO}/run_alphafold3_rixi.sh" \
    "${AF3_REPO}" \
    "${AF3_WEIGHTS}" \
    "${AF3_DB_DIR}" \
    "${INPUT_FASTA}" \
    "${OUTPUT_DIR}"

echo "Job completed"#!/bin/bash
set -euo pipefail

AF3_REPO="$1"
AF3_WEIGHTS="$2"
AF3_DB_DIR="$3"
INPUT_FASTA="$4"
OUTPUT_DIR="$5"

# This script is a generic wrapper for an AlphaFold3 launch.
# You must adapt the actual command below to the exact AlphaFold3
# installation method used on your cluster.

cd "${AF3_REPO}"

echo "Running AlphaFold3 wrapper..."
echo "Repository: ${AF3_REPO}"
echo "Weights: ${AF3_WEIGHTS}"
echo "Databases: ${AF3_DB_DIR}"
echo "Input FASTA: ${INPUT_FASTA}"
echo "Output dir: ${OUTPUT_DIR}"

# ------------------------------------------------------------------
# EXAMPLE PLACEHOLDER COMMAND
# ------------------------------------------------------------------
# Replace this block with the exact command required by your install.
#
# For example, this might be:
#   - a direct python invocation
#   - a launcher script provided by the AF3 repo
#   - an apptainer/singularity exec command
#
# Example pseudo-command:
#
# python run_alphafold.py \
#   --fasta_paths="${INPUT_FASTA}" \
#   --model_dir="${AF3_WEIGHTS}" \
#   --data_dir="${AF3_DB_DIR}" \
#   --output_dir="${OUTPUT_DIR}"
#
# Or, if containerized:
#
# apptainer exec --nv your_af3.sif python /app/run_alphafold.py \
#   --fasta_paths="${INPUT_FASTA}" \
#   --model_dir="${AF3_WEIGHTS}" \
#   --data_dir="${AF3_DB_DIR}" \
#   --output_dir="${OUTPUT_DIR}"
#
# ------------------------------------------------------------------

echo "No exact AlphaFold3 command was inserted because the original"
echo "attempt did not stabilize to one confirmed working cluster command."
echo "Edit this script with the final command for your environment."from pathlib import Path

rixi_sequence = (
    "AGKTGQMTVFWGRNKNEGTLKETCDTGLYTTVVISFYSVFGHGRYWGDLSGHDLRVIGADIKHCQSKNIF"
    "VFLSIGGAGKDYSLPTSKSAADVADNIWNAHMDGRRPGVFRPFGDAAVDGIDFFIDQGAPDHYDDLARNL"
    "YAYNKMYRARTPVRLTATVRCAFPDPRMKKALDTKLFERIHVRFYDDATCSYNHAGLAGVMAQWNKWTAR"
    "YPGSHV"
)

out_path = Path("examples/rixi.fasta")
out_path.parent.mkdir(parents=True, exist_ok=True)

with open(out_path, "w") as f:
    f.write(">RIXI_WT\n")
    f.write(rixi_sequence + "\n")

print(f"Wrote FASTA to {out_path.resolve()}")import json
import sys
from pathlib import Path

def extract_scores(obj):
    keys_of_interest = [
        "ptm",
        "iptm",
        "ranking_score",
        "ranking_confidence",
        "plddt",
        "mean_plddt",
        "confidence"
    ]
    found = {}
    if isinstance(obj, dict):
        for k, v in obj.items():
            if k.lower() in keys_of_interest:
                found[k] = v
            nested = extract_scores(v)
            found.update(nested)
    elif isinstance(obj, list):
        for item in obj:
            found.update(extract_scores(item))
    return found

def main(output_dir):
    output_dir = Path(output_dir)
    if not output_dir.exists():
        print(f"Directory not found: {output_dir}")
        sys.exit(1)

    json_files = list(output_dir.rglob("*.json"))
    if not json_files:
        print("No JSON files found.")
        return

    print("file\tavailable_scores")
    for jf in sorted(json_files):
        try:
            with open(jf, "r") as f:
                data = json.load(f)
            scores = extract_scores(data)
            print(f"{jf}\t{scores}")
        except Exception as e:
            print(f"{jf}\tERROR: {e}")

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python scripts/summarize_alphafold3_outputs.py /path/to/output_dir")
        sys.exit(1)
    main(sys.argv[1])>RIXI_WT
AGKTGQMTVFWGRNKNEGTLKETCDTGLYTTVVISFYSVFGHGRYWGDLSGHDLRVIGADIKHCQSKNIFVFLSIGGAGKDYSLPTSKSAADVADNIWNAHMDGRRPGVFRPFGDAAVDGIDFFIDQGAPDHYDDLARNLYAYNKMYRARTPVRLTATVRCAFPDPRMKKALDTKLFERIHVRFYDDATCSYNHAGLAGVMAQWNKWTARYPGSHVExpected outputs from a successful AlphaFold3 run may include:
- structure files
- ranking JSON files
- confidence summaries
- pLDDT / pTM / ipTM-style metrics depending on run mode and output schema

In this project, the main desired outputs were confidence metrics that could be used
to compare RIXI wild type and mutant designs.
