got a curated TE list from Almo: D_mel_transposon_sequence_set.fa
concatinate this to the file dmel-all-chromosome-r6.57.fasta from https://ftp.flybase.net/genomes/Drosophila_melanogaster/current/fasta by doing
```cat D_mel_transposon_sequence_set.fa >> dmel-all-chromosome-r6.57.fasta```

and then map with bwa my sRNA Dmel ovary to the concatinated file: 
