##extract files from NCBI / SRA:

Go to the Project's SRA page and mark all files you want, download the metadata and you will get a file calles SraRunTable.txt
then use this code to extract all the accession names:
```
awk -F',' 'NR>1 {print $1}' SraRunTable.txt > SRR_numbers.txt
````
confirm the number of accessions is the same as on the homepage:
```
wc -l SRR_numbers.txt
```

go to ENA Browser (not NCBI, its unnecessary compliacted) and download all samples 

go to ENA Browser and download all assemblies

