install invadeGO and Go, then compile invadeGO into an executable with:
```
go build main.go
```
I renamed executable into invadego_build

Testrun to check differences between using --cluster kb:76.5 and --cluster 76500
```
folder="/Users/ssaadain/invasions/"
tool="/Users/ssaadain/invadego/invadego_build"

$tool --seed 1713797481534670000 --N 1000 --gen 100 --genome mb:1 --cluster 76500 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 > $folder/Dvir_bases.txt
```
invadeGo script:
```
folder="/Users/ssaadain/invasions/"
tool="/Users/ssaadain/invadego/invadego_build"
$tool --N 1000 --gen 100 --genome mb:1 --cluster 107000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dyak.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 102000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dfic.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 101000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dtak.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 76500 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dvir.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 68600 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dbia.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 61800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dere.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 54300 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dmoj.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 44900 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dper.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 30100 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dmel.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 29800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dpse.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 21800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dsim.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 18400 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dsub.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 15600 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dsuz.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 13000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dana.txt &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 10200 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $folder/Dazt.txt
```