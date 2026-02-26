# Clinical Variant Pathogenicity Analysis (GRCh38)

## Assignment Overview

This assignment investigates the pathogenicity of six clinically significant genetic variants using publicly available genomic databases and standardized variant interpretation guidelines. The objective is to integrate clinical, computational, and genomic evidence into a structured and reproducible analysis workflow.

All genomic coordinates are based on the GRCh38 reference genome.

---

## Selected Variants

### Common Genetic Diseases

| Disease | Gene | Variant (HGVS) | Genomic Position (GRCh38) | Clinical Significance |
|----------|--------|----------------|----------------------------|------------------------|
| Duchenne Muscular Dystrophy | DMD | c.8713C>T (p.Arg2905Ter) | chrX:32389644 | Pathogenic |
| Cystic Fibrosis | CFTR | c.1521_1523delCTT (p.Phe508del) | chr7:117548628 | Pathogenic |
| Hereditary Breast & Ovarian Cancer | BRCA1 | c.5266dupC (p.Gln1756Profs*74) | chr17:43093141 | Pathogenic |

### Rare Genetic Diseases

| Disease | Gene | Variant (HGVS) | Genomic Position (GRCh38) | Clinical Significance |
|----------|--------|----------------|----------------------------|------------------------|
| Wilson Disease | ATP7B | c.3207C>A (p.His1069Gln) | chr13:52533870 | Pathogenic |
| Gaucher Disease Type 1 | GBA | c.1226A>G (p.Asn409Ser) | chr1:155235252 | Pathogenic |
| Phenylketonuria | PAH | c.1222C>T (p.Arg408Trp) | chr12:102917337 | Pathogenic |

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
- Evidence summaries were written in the Excel file.

---

### 2. Phenotype and Inheritance Analysis (OMIM)

For each gene:
- The OMIM entry was reviewed.
- Disease phenotype descriptions were extracted.
- Major clinical features were summarized.
- Mode of inheritance (Autosomal Dominant, Autosomal Recessive, etc.) was documented in the Excel file.

---

### 3. Pathogenicity Scoring (UCSC Genome Browser)

Each variant position was analyzed using the UCSC Genome Browser with the following tracks enabled:

- AlphaMissense  
- REVEL  

The following were recorded:
- AlphaMissense classification  
- AlphaMissense score  
- REVEL score  

Screenshots were captured and stored in the /Screenshots directory in the repository.

---

### 4. ACMG/AMP Variant Interpretation

Each variant was evaluated according to ACMG/AMP guidelines.

Criteria assessed included:
- PVS1 (null variant in gene with established loss-of-function mechanism)  
- PS1 (same amino acid change as known pathogenic variant)  
- PM2 (absence or rarity in population databases)  
- PP3 (computational evidence supporting deleterious effect)  

A final ACMG classification was assigned with justification in the Excel file.

---

### 5. VCF File Creation

A multi-variant VCF file was manually constructed using GRCh38 coordinates for all 6 variants across both common and rare disease categories.

The VCF file includes:

- VCF v4.2 header with contig definitions
- Chromosome (CHROM)
- Position (POS)
- Reference allele (REF)
- Alternate allele (ALT)
- Quality (QUAL)
- Filter status (FILTER)
- INFO annotations including GENE, PROT, CONSEQUENCE, CLNSIG, CLNDN, CLNREVSTAT, ALPHAMISSENSE, REVEL, ACMG criteria, and TRANSCRIPT

The file was compressed and indexed using `bgzip` and `tabix` in WSL. The `tabix` indexing step was used as a workaround to resolve contig header parsing issues encountered during sorting:

```bash
# Compress and index VCF
bgzip patient_variants.vcf
tabix -p vcf patient_variants.vcf.gz

# Sort and index
bcftools sort patient_variants.vcf.gz -Oz -o patient_variants_sorted.vcf.gz
bcftools index -t patient_variants_sorted.vcf.gz
```

---

## ClinVar Annotation

Variants were annotated using the official GRCh38 ClinVar VCF dataset downloaded from NCBI ClinVar. A `vcfanno` configuration file (`clinvar_config.toml`) was created to extract the following ClinVar INFO fields and add them to the variant file: `CLNSIG`, `CLNDN`, `CLNREVSTAT`, `CLNDISDB`, and `CLNHGVS`.

The config file is available in the /Data directory in the repository.

**Workflow (done in WSL):**

```bash
# Install required tools
sudo apt update
sudo apt install bcftools tabix wget -y

# Download vcfanno
wget https://github.com/brentp/vcfanno/releases/download/v0.3.5/vcfanno_linux64
chmod +x vcfanno_linux64
sudo mv vcfanno_linux64 /usr/local/bin/vcfanno

# Download ClinVar GRCh38 dataset
wget https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh38/clinvar.vcf.gz
wget https://ftp.ncbi.nlm.nih.gov/pub/clinvar/vcf_GRCh38/clinvar.vcf.gz.tbi

# Compress and index VCF (tabix used to bypass contig header issue)
bgzip patient_variants.vcf
tabix -p vcf patient_variants.vcf.gz

# Sort and index
bcftools sort patient_variants.vcf.gz -Oz -o patient_variants_sorted.vcf.gz
bcftools index -t patient_variants_sorted.vcf.gz

# Annotate using vcfanno
vcfanno clinvar_config.toml patient_variants_sorted.vcf.gz > patient_variants_annotated.vcf

# Export summary as TSV
bcftools query -f '%CHROM\t%POS\t%REF\t%ALT\t%INFO/GENE\t%INFO/CLNSIG\t%INFO/CLNDN\t%INFO/CLNREVSTAT\n' \
  patient_variants_annotated.vcf -o annotated_summary.tsv
```

The annotated,original and sorted VCF files are available in the /Data directory in this repository.

---

## Assignment Submitted by:
- Washma Sajjad
- Ashna Abrar
- Nawal Babar
- Manaal Tufail
