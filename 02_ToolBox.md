run the ToolBox to remove redundant sequences  
run the ToolBox again to remove low-complexity reads

````
#!/bin/bash

# Assigning arguments to variables
FASTQ="/home/vetlinux04/Sarah/trapmodel/ovaries_without_adapter"

# Iterate through the fastq directory
for fastq_file in "${FASTQ}"/*.fq; do
    # Execute TBr2 scripts
    perl TBr2_collapse.pl -i "${fastq_file}" -o "${fastq_file}-collapsed.fastq"
    perl TBr2_duster.pl -I "${fastq_file}-collapsed.fastq"
    
    # Separator line
    echo "------------------------"
done
```

