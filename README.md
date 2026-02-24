# Clinical Variant Pathogenicity Analysis (GRCh38)

## Assignment Overview

This assignment investigates the pathogenicity of three clinically significant genetic variants using publicly available genomic databases and standardized variant interpretation guidelines. The objective is to integrate clinical, computational, and genomic evidence into a structured and reproducible analysis workflow.

All genomic coordinates are based on the GRCh38 reference genome.

---

## Selected Variants

| Disease | Gene | Variant (HGVS) | Genomic Position (GRCh38) | Clinical Significance |
|----------|--------|----------------|----------------------------|------------------------|
| Cystic Fibrosis | CFTR | c.1521_1523delCTT (p.Phe508del) | chr7:117559593 | Pathogenic |
| Sickle Cell Disease | HBB | c.20A>T (p.Glu6Val) | chr11:5227002 | Pathogenic |
| Phenylketonuria | PAH | c.1222C>T (p.Arg408Trp) | chr12:102840493 | Pathogenic |

Variants were selected from ClinVar based on:
- Pathogenic or Likely Pathogenic classification  
- Strong review status (≥2 stars when available)  
- Multiple independent submissions  

---

## Methodology

### 1. Variant Selection and Clinical Evidence (ClinVar)

For each disorder:
- The disease was searched in ClinVar.
- Variants classified as Pathogenic or Likely Pathogenic were filtered.
- Review status and supporting studies were examined.
- Gene name, HGVS nomenclature, genomic coordinates, and clinical assertions were extracted.
- Evidence summaries were written in original language based on submission data.

---

### 2. Phenotype and Inheritance Analysis (OMIM)

For each gene:
- The OMIM entry was reviewed.
- Disease phenotype descriptions were extracted.
- Major clinical features were summarized.
- Mode of inheritance (Autosomal Dominant, Autosomal Recessive, etc.) was documented.

---

### 3. Pathogenicity Scoring (UCSC Genome Browser)

Each variant position was analyzed using the UCSC Genome Browser with the following tracks enabled:

- AlphaMissense  
- RAVEL  

The following were recorded:
- AlphaMissense classification  
- AlphaMissense score  
- RAVEL score  

Screenshots were captured and stored in the repository.

---

### 4. ACMG/AMP Variant Interpretation

Each variant was evaluated according to ACMG/AMP guidelines.

Criteria assessed included:
- PVS1 (null variant in gene with established loss-of-function mechanism)  
- PS1 (same amino acid change as known pathogenic variant)  
- PM2 (absence or rarity in population databases)  
- PP3 (computational evidence supporting deleterious effect)  

A final ACMG classification was assigned with justification.

---

### 5. VCF File Creation
A multi-variant VCF file was manually constructed using GRCh38 coordinates.

The VCF file includes:

- VCF v4.2 header
- Chromosome (CHROM)
- Position (POS)
- Reference allele (REF)
- Alternate allele (ALT)
- Quality (QUAL)
- Filter status (FILTER)
- INFO annotations

The file was:

- Converted to Unix format
- Compressed using `bgzip` (via **bcftools** installed in WSL)
- Indexed using `tabix` (via **bcftools** installed in WSL)

## ClinVar Annotation
The VCF file was annotated using a ClinVar GRCh38 dataset.

**Workflow: (done in WSL)**

```bash
# Install required tools in WSL
sudo apt update
sudo apt install bcftools tabix wget

# Download vcfanno from GitHub
wget -O vcfanno https://github.com/brentp/vcfanno/releases/latest/download/vcfanno_linux_amd64
chmod +x vcfanno
# Add vcfanno to PATH or run directly

# Compress and index VCF
bgzip variants.vcf
tabix -p vcf variants.vcf.gz

# Annotate using ClinVar
./vcfanno clinvar.conf variants.vcf.gz > annotated_variants.vcf
```
The annotated and original VCF files are available in the repository.
