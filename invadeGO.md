install invadeGO and Go, then compile invadeGO into an executable with:
```
go build main.go
```
I renamed executable into invadego_build

Testrun to check differences between using --cluster kb:76.5 and 76500 (bases)
```
folder="/Users/ssaadain/invasions/"
tool="/Users/ssaadain/invadego/invadego_build"

$tool --seed 1713797481534670000 --N 1000 --gen 100 --genome mb:1 --cluster 76500 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 > $folder/Dvir_bases.txt
```
invadeGo script:
```
folder="/Users/ssaadain/invasions/"
tool="/Users/ssaadain/invadego/invadego_build"
$tool --N 1000 --gen 100 --genome mb:1 --cluster 107000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dyak &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 102000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dfic &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 101000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dtak &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 76500 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dvir &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 68600 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dbia &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 61800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dere &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 54300 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dmoj &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 44900 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dper &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 30100 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dmel &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 29800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dpse &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 21800 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dsim &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 18400 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dsub &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 15600 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dsuz &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 13000 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dana &
$tool --N 1000 --gen 100 --genome mb:1 --cluster 10200 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 -x 0 > $test/Dazt
```
