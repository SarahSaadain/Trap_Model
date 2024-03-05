code to trace back the 5 most recent LTRs, non-LTRs and TIRs

use the TE libraries for each species from Lopik, then our filtered files for that (Dana.fa, Dazt.fa etc)
in there it says already the class and the TE family (for some reason it is cut of in our RM output, maybe during roberts cleaning script)

so they look like this:
```
>TE_00000001#LINE/R1
ACCGTTGGAGAACGGTTGCGCTTCGCACCACACCTTGCCAATCTGAAGGACCGATTGGTCGGTACGGTTGGACGATTGCGCCGAGTTTTGAGAAGTGAATGGGGCCTCAGCAGAAAAGCTGCTCGCACCATATATGGTGGTCTTTTTGTTGCTTGCGCAGCAAGCGGATCACCTGCGTGGTACGATGCAGTCTTGGACGTTAGGGGCAGAATGAAAATTTTAAGTGTGCAAAGGTTGATTTTGTTGGGGTGTATGCCTGTGTGTCGCACTGTCTCTACGGAGGCATTGCAAGT
```

use this one liner to extract the infor and add it in a new column:
```
grep '^>' Dyak.fa | awk -F'[>#/]' '{print $2"\t"$3"\t"$4}' > Dyak_TE.txt
```

