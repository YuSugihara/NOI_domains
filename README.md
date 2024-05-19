# NOI domains

Datasets and scripts used in Fujisaki et al. (2023)

### Table of contents

1. [hmmsearch against Poaceae proteomes](https://github.com/YuSugihara/NOI_domains/tree/main#1-hmmsearch-against-poaceae-proteomes)
2. [NLRtracker to annotate NLR proteins](https://github.com/YuSugihara/NOI_domains/tree/main#2-nlrtracker-to-annotate-nlr-proteins)
3. [Phylogenetic analysis using IQ-TREE](https://github.com/YuSugihara/NOI_domains/tree/main#3-phylogenetic-analysis-using-iq-tree)

## 1. hmmsearch against Poaceae proteomes

We used [hmmsearch](https://www.ebi.ac.uk/Tools/hmmer/search/hmmsearch) to search for NOI domain sequences against the "Reference Proteomes" for Poaceae (taxid: 4479). The profile HMM [PF05627](https://www.ebi.ac.uk/interpro/entry/pfam/PF05627) was used as the query profile.

- Input profile HMM ([HMM](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/PF05627.hmm))
-	619 significant hit full-length sequences ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/A1583BAC-4B06-11EE-ABCE-5F13FAE0C6C4.1-fullseq.fasta))
-	873 significant hit regions ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/A1583BAC-4B06-11EE-ABCE-5F13FAE0C6C4.1.fasta))
-	Hit information table ([TXT](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/A1583BAC-4B06-11EE-ABCE-5F13FAE0C6C4.1.tsv))

We filtered out sequences having any missing residues or more than two NOI domains. The [six validated rice NOI proteins](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/rice_NOI_proteins.fasta) were then added to the 607 retained sequences.

- 613 NOI proteins for futher analysis ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/Poaceae_NOIs.fasta))

## 2. NLRtracker to annotate NLR proteins

NLR proteins were annotated using [NLRtracker](https://github.com/slt666666/NLRtracker) ([Kourelis et al., 2021](https://doi.org/10.1371/journal.pbio.3001124)).

```bash
NLRtracker.sh -s Poaceae_NOIs.fasta \
              -o nlrtracker_result \
              -c 30
```

The resulting files are [here](https://github.com/YuSugihara/NOI_domains/blob/main/20_nlrtracker).

## 3. Phylogenetic analysis using IQ-TREE

The command-line ```hmmsearch``` was executed again with the same cut-offs to generate a multiple sequence alignment.

```bash
hmmsearch -T 3 \
          --domT 3 \
          --incT 5 \
          --incdomT 5 \
          -A Poaceae_NOIs.fasta.hmmsearch_alin.stockholm \
          --domtblout Poaceae_NOIs.fasta.hmmsearch_summary.txt \
          PF05627.hmm \
          Poaceae_NOIs.fasta \
        > Poaceae_NOIs.hmmsearch_output.txt
```

- Input profile HMM ([HMM](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/PF05627.hmm))
- Input NOI proteins ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/10_hmmsearch_RefProt_Poaceae/Poaceae_NOIs.fasta))
- Output result ([TXT](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.hmmsearch_output.txt))
- Output summary ([TXT](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_summary.txt))
- Output alignment ([STOCKHOLM](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_alin.stockholm))

The alignment file was converted to FASTA format using ```esl-reformat```.

```bash
esl-reformat afa Poaceae_NOIs.fasta.hmmsearch_alin.stockholm \
               > Poaceae_NOIs.fasta.hmmsearch_alin.fasta
```

- Input Stockholm ([STOCKHOLM](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_alin.stockholm))
- Output FASTA ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_alin.fasta))

Sequences containing NOI motifs without any gaps were retained for phylogenetic analysis. Then, [IQ-TREE](http://www.iqtree.org) ([Minh et al., 2020](https://doi.org/10.1093/molbev/msaa015)) was used to reconstruct phylogenetic tree.

```bash
iqtree -s Poaceae_NOIs.fasta.hmmsearch_alin.with_motif_regions.fasta \
       -m MFP \
       -st AA \
       -bb 1000 \
       -nt 2
```

The resulting files are [here](https://github.com/YuSugihara/NOI_domains/tree/main/30_iqtree).

- Input FASTA ([FASTA](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_alin.with_motif_regions.fasta))
- Output tree ([NEWICK](https://github.com/YuSugihara/NOI_domains/blob/main/30_iqtree/Poaceae_NOIs.fasta.hmmsearch_alin.with_motif_regions.fasta.treefile))

