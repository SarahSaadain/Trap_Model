got a curated TE list from Bergman: D_mel_transposon_sequence_set.fa
concatinate this to the file dmel-all-chromosome-r6.57.fasta from https://ftp.flybase.net/genomes/Drosophila_melanogaster/current/fasta by doing  
```cat D_mel_transposon_sequence_set.fa >> dmel-all-chromosome-r6.57.fasta```
upload dmel-all-chromosome-r6.57.fasta (containing the flybase Dmel ref and the curated TEs from Almo/Bergman) to vetlinux
there infex dmel-all-chromosome-r6.57.fasta by doing ```bwa index dmel-all-chromosome-r6.57.fasta```
then map by ```bwa mem -t 4 dmel-all-chromosome-r6.57.fasta Dmel_ovaries_SRR23593024_trimmed.fq > Dmel_ovaries_SRR23593024_flybase_aligned.sam```


