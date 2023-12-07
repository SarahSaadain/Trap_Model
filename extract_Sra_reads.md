extract files from NCBI / SRA:

Go to the Project's SRA page and mark all files you want, download the metadata and you will get a file calles SraRunTable.txt
then use this code to extract all the accession names:
```
awk -F',' 'NR>1 {print $1}' SraRunTable.txt > SRR_numbers.txt
````

