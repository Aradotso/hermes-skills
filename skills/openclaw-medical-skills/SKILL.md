```markdown
---
name: openclaw-medical-skills
description: Comprehensive medical AI skills library for OpenClaw/NanoClaw covering clinical workflows, bioinformatics, drug discovery, genomics, and healthcare AI
triggers:
  - how do I use OpenClaw medical skills
  - install medical AI skills for OpenClaw
  - what medical capabilities can I add to my AI agent
  - setup bioinformatics skills in NanoClaw
  - configure clinical research skills for OpenClaw
  - add genomics and drug discovery tools to my agent
  - use medical device regulatory skills in OpenClaw
  - integrate healthcare databases with my AI assistant
---

# OpenClaw Medical Skills

> Skill by [ara.so](https://ara.so) — Hermes Skills collection.

OpenClaw Medical Skills is the largest open-source medical AI skill library for OpenClaw and NanoClaw frameworks. It provides 869 curated skills covering clinical workflows, bioinformatics pipelines, drug discovery, genomics, medical imaging, regulatory compliance, and healthcare AI. Each skill is a self-contained module that teaches Claude-based AI agents specialized domain knowledge, connects them to real databases/APIs, and enables structured medical/scientific outputs.

## What It Does

- **Clinical Skills**: SOAP notes, discharge summaries, clinical decision support, oncology workflows, medical imaging analysis
- **Bioinformatics**: RNA-seq, scRNA-seq, GWAS, variant calling, pathway analysis, epigenomics, metagenomics
- **Drug Discovery**: ChEMBL, DrugBank, DDI prediction, pharmacovigilance, ADME analysis, molecular docking
- **Genomics**: VCF annotation, ACMG classification, polygenic risk scores, ancestry analysis, pharmacogenomics
- **Databases**: PubMed, ClinicalTrials.gov, FDA, UniProt, TCGA, gnomAD, COSMIC
- **Regulatory**: FDA compliance, CE mark, IEC 62304, ISO 14971, medical device documentation
- **Research Tools**: Literature search, reference management, scientific writing, data visualization

## Installation

### Requirements

- [OpenClaw](https://github.com/openclaw/openclaw) or [NanoClaw](https://github.com/qwibitai/nanoclaw) installed
- Git with sparse-checkout support (for large repo optimization)
- Git LFS (optional, only if you need bundled datasets)

### For OpenClaw

OpenClaw loads skills from:
- **Per-workspace**: `<workspace>/skills/` (higher priority)
- **Global**: `~/.openclaw/skills/` (shared across agents)

#### Method 1: Clone and Copy (Recommended)

```bash
# Sparse checkout to avoid large data files
git clone --depth=1 --no-checkout https://github.com/FreedomIntelligence/OpenClaw-Medical-Skills.git
cd OpenClaw-Medical-Skills
git sparse-checkout init --cone
git sparse-checkout set skills
git checkout main

# Install to workspace (recommended)
cp -r skills/* /path/to/your-workspace/skills/

# OR install globally
cp -r skills/* ~/.openclaw/skills/
```

#### Method 2: Install Selected Skills

```bash
# Example: clinical + genomics + drug discovery
SKILLS=(
  "clinical-reports"
  "tooluniverse-drug-research"
  "biomedical-search"
  "clinicaltrials-database"
  "bio-vcf-annotation"
  "bio-gwas-analysis"
  "tooluniverse-drug-drug-interaction"
)

for skill in "${SKILLS[@]}"; do
  cp -r OpenClaw-Medical-Skills/skills/$skill ~/.openclaw/skills/
done
```

#### Method 3: Mount Directory Permanently

Add to `~/.openclaw/openclaw.json`:

```json
{
  "plugins": {
    "local": ["/path/to/OpenClaw-Medical-Skills"]
  }
}
```

### For NanoClaw

```bash
# Clone repository
git clone https://github.com/FreedomIntelligence/OpenClaw-Medical-Skills.git

# Copy to NanoClaw container skills
cp -r OpenClaw-Medical-Skills/skills/* /path/to/nanoclaw/container/skills/

# Rebuild container
cd /path/to/nanoclaw
./container/build.sh
```

### Verification

After installation, test with your agent:

```
What medical and clinical skills do you have available?
```

The agent should list installed skills with capabilities.

## Skill Structure

Each skill is a `SKILL.md` file with:

```markdown
---
name: skill-name
description: One-line description
triggers:
  - natural language phrase 1
  - natural language phrase 2
---

# Skill Name

[Documentation with examples, APIs, workflows]
```

Skills are auto-loaded when placed in the skills directory. No restart required.

## Key Categories

### 1. Clinical & Medical (119 skills)

**Clinical Documentation:**
```
skills/clinical-reports/          # SOAP notes, discharge summaries
skills/clinical-decision-support/ # CDS hooks, diagnosis assistance
skills/medical-imaging/           # DICOM, radiology reports
```

**Example Use:**
```
Generate a SOAP note for a patient with hypertension and diabetes
Create a discharge summary for post-surgical care
Analyze this chest X-ray report and suggest differential diagnoses
```

### 2. Bioinformatics (239 skills from gptomics)

**Variant Analysis:**
```
skills/bio-vcf-annotation/        # Annotate VCF files with ANNOVAR
skills/bio-variant-filtering/     # Filter variants by AF, impact
skills/bio-acmg-classification/   # ACMG/AMP pathogenicity rules
```

**Sequencing QC:**
```
skills/bio-fastqc/               # Read quality assessment
skills/bio-multiqc/              # Aggregate QC reports
skills/bio-trimmomatic/          # Adapter trimming
```

**Differential Expression:**
```
skills/bio-deseq2/               # RNA-seq DE analysis
skills/bio-edger/                # Count-based DE
skills/bio-limma/                # Microarray/RNA-seq
```

**Example Python Integration:**
```python
# VCF annotation workflow
from pathlib import Path
import subprocess

vcf_file = Path("sample.vcf")
output_dir = Path("annotated_output")
output_dir.mkdir(exist_ok=True)

# Annotate with ANNOVAR (bio-vcf-annotation skill)
cmd = [
    "table_annovar.pl",
    str(vcf_file),
    "/data/humandb/",
    "-buildver", "hg38",
    "-out", str(output_dir / "annotated"),
    "-protocol", "refGene,clinvar_20220320,gnomad312_genome",
    "-operation", "g,f,f",
    "-nastring", ".",
    "-vcfinput"
]
subprocess.run(cmd, check=True)

# Filter pathogenic variants
import pandas as pd
anno = pd.read_csv(output_dir / "annotated.hg38_multianno.txt", sep="\t")
pathogenic = anno[
    (anno['CLNSIG'].str.contains('Pathogenic', na=False)) &
    (anno['gnomAD_AF'] < 0.01)
]
pathogenic.to_csv(output_dir / "pathogenic_variants.csv", index=False)
```

### 3. Drug Discovery (43+ skills)

**Chemical Databases:**
```
skills/tooluniverse-drug-research/        # ChEMBL queries
skills/tooluniverse-pharmacovigilance/    # Adverse event monitoring
skills/tooluniverse-drug-drug-interaction/ # DDI prediction
```

**Example R Integration:**
```r
# Drug-drug interaction analysis
library(httr)
library(jsonlite)

# Query DrugBank API (use env var for key)
drugbank_key <- Sys.getenv("DRUGBANK_API_KEY")

check_ddi <- function(drug1_name, drug2_name) {
  url <- "https://api.drugbank.com/v1/ddi"
  response <- GET(
    url,
    add_headers(Authorization = drugbank_key),
    query = list(drug1 = drug1_name, drug2 = drug2_name)
  )
  
  if (status_code(response) == 200) {
    content(response, "parsed")
  } else {
    stop("API request failed: ", status_code(response))
  }
}

# Check interaction
ddi <- check_ddi("warfarin", "aspirin")
cat("Severity:", ddi$severity, "\n")
cat("Description:", ddi$description, "\n")
```

### 4. Genomics & Databases (64 skills)

**Database Access:**
```
skills/biomedical-search/         # PubMed, PMC queries
skills/clinicaltrials-database/   # ClinicalTrials.gov API
skills/scientific-databases/      # UniProt, Ensembl, NCBI
```

**Example Database Query:**
```python
# PubMed literature search
from Bio import Entrez
import os

Entrez.email = os.getenv("ENTREZ_EMAIL")  # Required by NCBI

def search_pubmed(query, max_results=10):
    handle = Entrez.esearch(
        db="pubmed",
        term=query,
        retmax=max_results,
        sort="relevance"
    )
    record = Entrez.read(handle)
    handle.close()
    return record["IdList"]

def fetch_abstracts(id_list):
    ids = ",".join(id_list)
    handle = Entrez.efetch(db="pubmed", id=ids, rettype="abstract", retmode="xml")
    records = Entrez.read(handle)
    handle.close()
    return records

# Search and retrieve
pmids = search_pubmed("CRISPR cancer therapy", max_results=5)
abstracts = fetch_abstracts(pmids)

for article in abstracts['PubmedArticle']:
    title = article['MedlineCitation']['Article']['ArticleTitle']
    print(f"Title: {title}\n")
```

### 5. Medical Device & Regulatory (20+ skills)

```
skills/meddev-agent-skills/      # IEC 62304, ISO 14971, FDA compliance
```

**Example Documentation:**
```
Generate an IEC 62304 Software Development Plan for a Class II medical device
Create a risk analysis following ISO 14971 for an infusion pump
Draft FDA 510(k) premarket notification for a diagnostic imaging system
```

### 6. Single-Cell & Spatial Omics (80+ skills)

**ClawBio Pipelines:**
```
skills/clawbio-scrna-pipeline/    # End-to-end scRNA-seq
skills/clawbio-spatial-pipeline/  # Spatial transcriptomics
```

**BioOS Extended:**
```
skills/bioos-scrna-*             # 30+ single-cell analysis tools
skills/bioos-spatial-*           # Spatial omics agents
```

**Example Workflow:**
```python
# Single-cell RNA-seq analysis with Scanpy
import scanpy as sc
import os

# Load 10X data
adata = sc.read_10x_mtx(
    'data/filtered_gene_bc_matrices/hg38/',
    var_names='gene_symbols',
    cache=True
)

# QC and filtering
sc.pp.filter_cells(adata, min_genes=200)
sc.pp.filter_genes(adata, min_cells=3)
adata.var['mt'] = adata.var_names.str.startswith('MT-')
sc.pp.calculate_qc_metrics(adata, qc_vars=['mt'], inplace=True)
adata = adata[adata.obs.pct_counts_mt < 5, :]

# Normalization and scaling
sc.pp.normalize_total(adata, target_sum=1e4)
sc.pp.log1p(adata)
sc.pp.highly_variable_genes(adata, n_top_genes=2000)
sc.pp.scale(adata, max_value=10)

# Dimensionality reduction
sc.tl.pca(adata, svd_solver='arpack')
sc.pp.neighbors(adata, n_neighbors=10, n_pcs=40)
sc.tl.umap(adata)

# Clustering
sc.tl.leiden(adata, resolution=0.5)

# Visualization
sc.pl.umap(adata, color=['leiden', 'CD3E', 'CD79A'], save='_clusters.png')

# Differential expression
sc.tl.rank_genes_groups(adata, 'leiden', method='wilcoxon')
sc.pl.rank_genes_groups(adata, n_genes=20, sharey=False, save='_markers.png')
```

## Configuration

### Environment Variables

Skills often require API keys or credentials. Set these as environment variables:

```bash
# NCBI/PubMed
export ENTREZ_EMAIL="your-email@example.com"
export NCBI_API_KEY="your_ncbi_key"

# Drug databases
export DRUGBANK_API_KEY="your_drugbank_key"
export CHEMBL_API_KEY="your_chembl_key"

# Clinical APIs
export CLINICALTRIALS_API_KEY="your_ct_gov_key"

# Cloud storage (for large datasets)
export AWS_ACCESS_KEY_ID="your_aws_key"
export AWS_SECRET_ACCESS_KEY="your_aws_secret"
export AWS_DEFAULT_REGION="us-east-1"
```

### Skill-Specific Config

Some skills use config files in their directories:

```yaml
# skills/bio-gwas-analysis/config.yaml
reference_genome: /data/reference/hg38.fa
plink_path: /usr/local/bin/plink
min_maf: 0.01
max_missing: 0.05
```

## Common Patterns

### 1. Clinical Report Generation

```
User: Generate a discharge summary for John Doe, 65yo male, admitted 3 days ago for acute MI

Agent: [Uses clinical-reports skill]
- Extracts structured data
- Generates SOAP format
- Includes medication reconciliation
- Provides follow-up instructions
```

### 2. Variant Interpretation Pipeline

```python
# Multi-skill pipeline
# 1. bio-vcf-annotation: Annotate variants
# 2. bio-variant-filtering: Filter by frequency/impact
# 3. bio-acmg-classification: Pathogenicity assessment
# 4. clinical-reports: Generate genetic counseling report

from pathlib import Path
import subprocess
import pandas as pd

def interpret_variants(vcf_path, output_dir):
    # Step 1: Annotate
    subprocess.run([
        "annotate_vcf.sh",
        str(vcf_path),
        str(output_dir / "annotated.vcf")
    ])
    
    # Step 2: Filter
    anno_df = pd.read_csv(output_dir / "annotated.txt", sep="\t")
    filtered = anno_df[
        (anno_df['ExonicFunc.refGene'].isin(['frameshift', 'stopgain', 'stoploss'])) |
        ((anno_df['gnomAD_AF'] < 0.01) & (anno_df['CADD_phred'] > 20))
    ]
    
    # Step 3: ACMG classification
    for _, variant in filtered.iterrows():
        acmg_score = calculate_acmg(variant)  # Uses bio-acmg-classification
        variant['ACMG_class'] = acmg_score
    
    # Step 4: Report generation
    generate_genetics_report(filtered, output_dir / "report.pdf")

interpret_variants(Path("patient.vcf"), Path("results"))
```

### 3. Drug Discovery Workflow

```r
# Multi-database drug screening
library(httr)
library(dplyr)

# 1. Target identification (UniProt)
target_gene <- "EGFR"
uniprot_id <- get_uniprot_id(target_gene)

# 2. Find compounds (ChEMBL)
compounds <- query_chembl(uniprot_id, activity_threshold = 100)  # nM

# 3. Check drug interactions
approved_drugs <- compounds %>% filter(max_phase == 4)
for (drug in approved_drugs$molecule_chembl_id) {
  ddis <- check_ddi_chembl(drug)
  print(paste("Drug:", drug, "Interactions:", nrow(ddis)))
}

# 4. Pharmacovigilance check
for (drug in approved_drugs$pref_name) {
  adverse_events <- query_faers(drug)  # FDA FAERS database
  print(paste("Drug:", drug, "Serious AEs:", sum(adverse_events$serious)))
}
```

### 4. Bioinformatics Pipeline Orchestration

```python
# ClawBio scRNA-seq pipeline
import clawbio

pipeline = clawbio.Pipeline("scrna-analysis")

# Configure pipeline stages
pipeline.add_stage("qc", {
    "min_genes": 200,
    "max_mt_percent": 5
})

pipeline.add_stage("normalization", {
    "method": "scran",
    "target_sum": 1e4
})

pipeline.add_stage("clustering", {
    "algorithm": "leiden",
    "resolution": 0.5
})

pipeline.add_stage("annotation", {
    "reference": "Human Primary Cell Atlas",
    "method": "SingleR"
})

# Run pipeline
results = pipeline.run(
    input_data="data/10x_pbmc/",
    output_dir="results/pbmc_analysis/"
)

# Generate report
clawbio.generate_report(results, "results/pbmc_report.html")
```

## Troubleshooting

### Skills Not Loading

**Issue**: Agent doesn't recognize installed skills

**Solution**:
```bash
# Check skill directory
ls -la ~/.openclaw/skills/

# Verify SKILL.md files exist
find ~/.openclaw/skills/ -name "SKILL.md" | head -5

# Check OpenClaw config
cat ~/.openclaw/openclaw.json

# Restart agent or reload workspace
```

### Large Repository Clone Timeout

**Issue**: Full clone times out due to bundled datasets

**Solution**: Use sparse checkout (see Installation Method 1)
```bash
# Only clone skills, skip data
git sparse-checkout set skills
```

### Missing Dependencies

**Issue**: Skill references unavailable tools (ANNOVAR, PLINK, etc.)

**Solution**: Install bioinformatics dependencies
```bash
# Conda environment for bioinformatics
conda create -n bioinformatics python=3.9
conda activate bioinformatics

# Common tools
conda install -c bioconda annovar plink bcftools samtools vcftools
conda install -c bioconda gatk4 bwa star salmon
conda install scanpy scvi-tools scrublet

# R packages
conda install -c conda-forge r-base r-essentials
R -e 'install.packages(c("Seurat", "DESeq2", "edgeR", "limma"))'
```

### API Rate Limits

**Issue**: PubMed/NCBI API throttling

**Solution**: Use API key for higher limits
```bash
# Register at https://www.ncbi.nlm.nih.gov/account/
export NCBI_API_KEY="your_key_here"

# With key: 10 requests/sec vs 3/sec without
```

### Memory Issues with Large Datasets

**Issue**: Single-cell analysis crashes with OOM

**Solution**: Use backed mode or chunking
```python
# Backed mode (on-disk storage)
adata = sc.read_10x_mtx('data/', cache=True)
adata.write('data.h5ad', compression='gzip')
adata = sc.read('data.h5ad', backed='r+')

# Process in chunks
for chunk in adata.chunked_X(chunk_size=1000):
    # Process chunk
    pass
```

### Skill Conflicts

**Issue**: Multiple skills provide same trigger phrase

**Solution**: Use explicit skill naming
```
# Instead of: "analyze this VCF file"
# Use: "use bio-vcf-annotation skill to annotate sample.vcf"
```

## Advanced Usage

### Creating Custom Skills

```markdown
---
name: my-custom-medical-skill
description: Custom workflow for my lab
triggers:
  - run my custom analysis
  - process samples with my pipeline
---

# My Custom Medical Skill

## Overview
Combines variant calling + expression analysis for cancer samples.

## Requirements
- bio-vcf-annotation
- bio-deseq2
- clinical-reports

## Workflow
1. Somatic variant calling from tumor/normal pairs
2. RNA-seq differential expression
3. Integrated report with mutations + DE genes

## Code
[Your Python/R code here]
```

### Combining Skills for Complex Workflows

```python
# Precision oncology workflow combining 10+ skills
def precision_oncology_pipeline(patient_id, tumor_vcf, rna_fastq):
    # 1. Variant calling (bio-somatic-variants)
    variants = call_somatic_variants(tumor_vcf, "normal.vcf")
    
    # 2. Annotation (bio-vcf-annotation)
    annotated = annotate_variants(variants)
    
    # 3. Oncogenic classification (bio-oncokb)
    oncogenic = classify_oncogenic(annotated)
    
    # 4. RNA-seq (bio-star-alignment, bio-deseq2)
    expression = analyze_expression(rna_fastq)
    
    # 5. Pathway analysis (bio-gsea)
    pathways = pathway_enrichment(expression)
    
    # 6. Drug matching (tooluniverse-drug-research)
    therapies = match_targeted_therapies(oncogenic)
    
    # 7. Clinical trials (clinicaltrials-database)
    trials = find_matching_trials(oncogenic, patient_id)
    
    # 8. Generate report (clinical-reports)
    generate_oncology_report({
        "patient_id": patient_id,
        "variants": oncogenic,
        "expression": expression,
        "pathways": pathways,
        "therapies": therapies,
        "trials": trials
    })
```

## Resources

- **Repository**: https://github.com/FreedomIntelligence/OpenClaw-Medical-Skills
- **OpenClaw**: https://github.com/openclaw/openclaw
- **NanoClaw**: https://github.com/qwibitai/nanoclaw
- **ClawHub**: https://clawhub.com
- **Skills Collection**: [Awesome LLM Resources](https://github.com/WangRongsheng/awesome-LLM-resources)

## License

Individual skills may have different licenses. Check each skill's directory for specific licensing information.
```
