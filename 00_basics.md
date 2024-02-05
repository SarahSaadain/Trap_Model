copy files from Roco to my computer:

```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at sarah@INSERTIPADRESS:/mnt/data2/different_species/ovaries/Dana_ovaries_SRR23593056_fastqc.html Desktop/
````

copy files from my computer to Roco / Vetlinux:
```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at Desktop/Dana_ovaries_SRR23593056_fastqc.html vetlinux04@pgnsrv042.vu-wien.ac.at:/home/vetlinux04/Sarah/softwares
```

Trimgalore
```
trim_galore --small_rna --stringency 6 -e 0.1 --output_dir /mnt/data2/different_species/ovaries/trimgalore_other_settings/ /mnt/data2/different_species/ovaries/Dana_ovaries_SRR23593056.fastq
```

Riccardo:
```
#!/bin/bash

# Check for correct number of arguments
if [ "$#" -ne 3 ]; then
    echo "Usage: $0 <fastq> <genome> <scripts>"
    exit 1
fi

# Assigning arguments to variables
FASTQ="$1"
GENOME="$2"
SCRIPTS_FOLDER="$3"

# Execute commands
perl "${SCRIPTS_FOLDER}/TBr2/TBr2_collapse.pl" -i "${FASTQ}" -o "${FASTQ}-collapsed.fastq"
perl "${SCRIPTS_FOLDER}/TBr2/TBr2_duster.pl" -I "${FASTQ}-collapsed.fastq"
perl "${SCRIPTS_FOLDER}/sRNAmapper.pl" -input "${FASTQ}-collapsed.fastq.no-dust" -genome "${GENOME}" -alignments best
perl "${SCRIPTS_FOLDER}/proTRAC_2.4.3.pl" -map "${FASTQ}-collapsed.fastq.no-dust.map" -genome "${GENOME}"
```
