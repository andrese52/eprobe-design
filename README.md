# Downloading Genome Assemblies from NCBI Using Micromamba and NCBI Tools

This guide explains how to create a micromamba environment, install required tools, and download genome assemblies from NCBI using a list of assembly accession numbers derived from a taxonomic query.

---

## 📦 Step 1: Set Up Micromamba Environment

Open a terminal and create a new environment:

```bash
micromamba create -n ncbi-genomes -c conda-forge -c bioconda python=3.10 ncbi-datasets-cli entrez-direct unzip
micromamba activate ncbi-genomes
```

This installs:
- Python 3.10
- `ncbi-datasets-cli` – for downloading genome data
- `entrez-direct` – for querying NCBI databases using `esearch`, `esummary`, `efetch`, `xtract`
- `unzip` – to unpack downloaded genomes

---

## 📂 Step 2: Prepare Assembly Accession List from NCBI

To fetch genomes for a specific taxonomic group (e.g., *Candidatus Phytoplasma*) while excluding a subgroup (e.g., `txid85624`), use:

```bash
esearch -db assembly -query "txid33926[Organism:exp] NOT txid85624[Organism:exp]" \
  | esummary \
  | xtract -pattern DocumentSummary -element AssemblyAccession \
  > assembly_accessions.txt
```

You should now have a file `assembly_accessions.txt` with one GCF/GCA accession per line.

---

## ⬇️ Step 3: Download Genome Assemblies with Datasets CLI

```bash
datasets download genome accession --inputfile assembly_accessions.txt --filename phytoplasma_genomes.zip --include genome
```

Then extract:

```bash
unzip phytoplasma_genomes.zip -d phytoplasma_genomes
```

Each genome will be downloaded into a folder with its FASTA file and metadata.

---

## 🔁 Reactivating the Environment Later

Each time you start a new terminal session:

```bash
micromamba activate ncbi-genomes
```

## Installing sra tools for downloading SRA files

After reactivating your environment, you can install the sra tools by running:

```bash
micromamba install -c bioconda sra-tools
```

---

## 📘 Notes

- You may modify the `--include` flag to also download `annotation`, `genomic-cds`, or `protein` files.
- For downloading only complete genomes:  
  Add `AND "complete genome"[Assembly Level]` to your query.

Example:
```bash
esearch -db assembly -query "txid33926[Organism:exp] NOT txid85624[Organism:exp] AND \"complete genome\"[Assembly Level]" ...
```

---

## 📚 References

- [Entrez Direct Documentation](https://www.ncbi.nlm.nih.gov/books/NBK179288/)
- [NCBI Datasets CLI Documentation](https://www.ncbi.nlm.nih.gov/datasets/docs/command-line-start/)
- [Micromamba](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html)

---
