# ww-star
[![Project Status: Experimental – Useable, some support, not open to feedback, unstable API.](https://getwilds.org/badges/badges/experimental.svg)](https://getwilds.org/badges/#experimental)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This WILDS WDL workflow performs RNA-seq alignment using STAR's two-pass methodology. It is designed to be a modular component within the WILDS ecosystem that can be used independently or integrated with other WILDS workflows.

## Overview

The workflow performs the following key steps:
1. STAR index building from provided reference genome files
2. STAR two-pass alignment for each sample
3. Generation of gene counts and alignment metrics

## Features

- Two-pass STAR alignment for improved splice junction detection
- Produces sorted BAM files with indexes
- Generates gene count matrices for downstream analysis
- Outputs detailed alignment logs for QC assessment
- Compatible with the WILDS workflow ecosystem

## Usage

### Requirements

- [Cromwell](https://cromwell.readthedocs.io/), [MiniWDL](https://github.com/chanzuckerberg/miniwdl), [Sprocket](https://sprocket.bio/), or another WDL-compatible workflow executor
- Docker/Apptainer (the workflow uses WILDS Docker containers)

### Basic Usage

1. Create an inputs JSON file with your sample information:

```json
{
  "star_example.samples": [
    {
      "name": "sample1",
      "r1": "/path/to/sample1_1.fastq.gz",
      "r2": "/path/to/sample1_2.fastq.gz"
    },
    {
      "name": "sample2",
      "r1": "/path/to/sample2_1.fastq.gz",
      "r2": "/path/to/sample2_2.fastq.gz"
    }
  ],
  "star_example.reference_genome": {
    "name": "hg38",
    "fasta": "/path/to/reference/genome.fasta",
    "gtf": "/path/to/reference/annotation.gtf"
  }
}
```

2. Run the workflow using your preferred WDL executor:

```bash
# Cromwell
java -jar cromwell.jar run ww-star.wdl --inputs ww-star-inputs.json --options ww-star-options.json

# miniWDL
miniwdl run ww-star.wdl -i ww-star-inputs.json

# Sprocket
sprocket run ww-star.wdl ww-star-inputs.json
```

### Integration with Other WILDS Workflows

This workflow pairs well with other WILDS workflows:
- [ww-sra](https://github.com/getwilds/ww-sra) for downloading data from NCBI's Sequence Read Archive
- [ww-star-deseq2](https://github.com/getwilds/ww-star-deseq2) for differential expression analysis

### Detailed Options

The workflow accepts the following inputs:

| Parameter | Description | Type | Required? | Default |
|-----------|-------------|------|-----------|---------|
| `samples` | Array of sample information objects | Array[SampleInfo] | Yes | - |
| `reference_genome` | Reference genome information | RefGenome | Yes | - |

#### SampleInfo Structure

Each entry in the `samples` array should contain:
- `name`: Sample identifier
- `r1`: Path to R1 FASTQ file
- `r2`: Path to R2 FASTQ file

#### RefGenome Structure

The `reference_genome` should contain:
- `name`: Reference genome name
- `fasta`: Path to reference FASTA file
- `gtf`: Path to reference GTF file

### Output Files

The workflow produces the following outputs:

| Output | Description |
|--------|-------------|
| `star_bam` | Aligned BAM files for each sample |
| `star_bai` | BAM indexes for each sample |
| `star_gene_counts` | Raw gene counts for each sample |
| `star_log_final` | STAR final logs |
| `star_log_progress` | STAR progress logs |
| `star_log` | STAR main logs |
| `star_sj` | STAR splice junction files |

## For Fred Hutch Users

For Fred Hutch users, we recommend using [PROOF](https://sciwiki.fredhutch.org/dasldemos/proof-how-to/) to submit this workflow directly to the on-premise HPC cluster. To do this:

1. Clone or download this repository
2. Update `ww-star-inputs.json` with your sample names and FASTQ file paths
3. Update `ww-star-options.json` with your preferred output location (`final_workflow_outputs_dir`)
4. Submit the WDL file along with your custom JSONs to the Fred Hutch cluster via PROOF

Additional Notes:
- All file paths in the JSONs must be visible to the Fred Hutch cluster, e.g. `/fh/fast/`, AWS S3 bucket, etc.
- To avoid duplication of reference genome data, we highly recommend executing this workflow with call caching enabled in the options json (`write_to_cache`, `read_from_cache`).

## Docker Containers

This workflow uses the following Docker container from the [WILDS Docker Library](https://github.com/getwilds/wilds-docker-library):

- `getwilds/star:2.7.6a` - For STAR alignment and index building

The container is available on both DockerHub and GitHub Container Registry.

## Support

For questions, bugs, and/or feature requests, reach out to the Fred Hutch Data Science Lab (DaSL) at wilds@fredhutch.org, or open an issue on our [issue tracker](https://github.com/getwilds/ww-star/issues).

## Contributing

If you would like to contribute to this WILDS WDL workflow, please see our [WILDS Contributor Guide](https://getwilds.org/guide/) for more details.

## License

Distributed under the MIT License. See `LICENSE` for details.
