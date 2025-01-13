# NGS_commands


# Download the latest version from Genocode for the releavant reference fasta 

# To make the Gene bed file
wget -qO- gencode.v28.annotation.gff3.gz \
    | gunzip --stdout - \
    | awk '$3 == "gene"' - \
    | convert2bed -i gff --attribute-key="gene_name" - \
    > genes.bed
	
# Getting the Gene_list.txt and grepping the genes_of_interest.bed file  
awk 'FNR==NR {a[$1]; next} $4 in a' Gene_list.txt genes.bed > genes_of_interest.bed

# extracting the variants from the GI-merged or IndiGen vcf

tabix -h -R genes_of_interest.bed <GI/Indigen.vcf.gz> > <Gene_extracted.vcf>


# Optional commands
# Sorting and spliting into chromosome-wise from genes_of_interest_sort.bed (optional)
bedtools sort -i genes_of_interest.bed > genes_of_interest_sort.bed
awk '{print> $1}' genes_of_interest_sort.bed

# extracting the variants from the chromosome wise (optional)
for i in $(ls /mnt/faruq1/GenomeIndia/FullData/20241230-GI_full_data/chr_*.gz); do \
	n=${i##*/}; \
	chr_temp=${n%%_QCd_*}; \
	chr=${chr_temp/_/}; \
	echo "tabix -h -R $chr $i > ${chr}_pharm_genes.vcf"; \
done 
