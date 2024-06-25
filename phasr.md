got a curated TE list from Bergman: D_mel_transposon_sequence_set.fa
concatinate this to the file dmel-all-chromosome-r6.57.fasta from https://ftp.flybase.net/genomes/Drosophila_melanogaster/current/fasta by doing  
```cat D_mel_transposon_sequence_set.fa >> dmel-all-chromosome-r6.57.fasta```
upload dmel-all-chromosome-r6.57.fasta (containing the flybase Dmel ref and the curated TEs from Bergman) to vetlinux
there index dmel-all-chromosome-r6.57.fasta by doing ```bwa index dmel-all-chromosome-r6.57.fasta```
then map by ```bwa mem -t 4 dmel-all-chromosome-r6.57.fasta Dmel_ovaries_SRR23593024_trimmed.fq > Dmel_ovaries_SRR23593024.sam```
convert sam to bam file with
```samtools view -S -b Dmel_ovaries_SRR23593024.sam > Dmel_ovaries_SRR23593024.bam```
sort it with
```samtools sort Dmel_ovaries_SRR23593024.bam -o Dmel_ovaries_SRR23593024.sorted.bam```
everything is in pingpong/mapped_to_genome

I redo the same with the transciptome and TE list from Bergman: dmel-all-transcript-r6.52_consensus_TEs.fasta (already concatinated by Matt)
and then index and map like before
looks better (mapping worked) its picking up the TE reads but the reads are too long

I redo it with the ones with the trimming steps from Lopik et al where I did 3 trimming steps: Dmel_ovaries_SRR23593024_trimmed_final.fq


