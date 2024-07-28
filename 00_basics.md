copy stuff from roco to vetlinux04
```
scp -r sarah@10.120.3.47:/mnt/data2/sarah/different_species/somatic_follicle_with_adapter vetlinux04@pgnsrv042.vu-wien.ac.at:/home/vetlinux04/Sarah/trapmodel
```

copy stuff from my computer to roco
```
scp /Users/ssaadain/Downloads/reader-rm.py sarah@10.120.3.47:/home/sarah
```

copy stuff from roco to my computer
```
scp -r sarah@10.120.3.47:/home/sarah/RM /Users/ssaadain/Downloads
```

copy files from Roco to my computer via jumphost:

```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at sarah@INSERTIPADRESS:/mnt/data2/different_species/ovaries/Dana_ovaries_SRR23593056_fastqc.html Desktop/
````

copy files from my computer to Roco / Vetlinux via jumphost:
```
scp -o ProxyJump=NAME@pgnport.vu-wien.ac.at Desktop/Dana_ovaries_SRR23593056_fastqc.html vetlinux04@pgnsrv042.vu-wien.ac.at:/home/vetlinux04/Sarah/softwares
```

```
ssh -J <jump-server> <remote-target>
```



unzip the SRR files
```
gzip -d /home/vetlinux04/Sarah/trapmodel/somatic_follicle_with_adapter/*.gz
```

Trimgalore

Trimgalore multiple species (I used for the ovary run in february that worked)
```
find /mnt/data2/different_species/ovaries/ -type f -name "*.fastq" -exec trim_galore --small_rna --stringency 6 -e 0.1 --output_dir /mnt/data2/different_species/ovaries/trimmed/ {} \;
```

same for the somatic_follicle cells
```
find /home/vetlinux04/Sarah/trapmodel/somatic_follicle_with_adapter/ -type f -name "*.fastq" -exec ./trim_galore --small_rna --stringency 6 -e 0.1 --output_dir /home/vetlinux04/Sarah/trapmodel/somatic_follicle_with_adapter/ {} \;
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
perl "${SCRIPTS_FOLDER}/proTRAC_2.4.4.pl" -map "${FASTQ}-collapsed.fastq.no-dust.map" -genome "${GENOME}"
```
