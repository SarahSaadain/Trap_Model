install invadeGO and go, then:
```
go build main.go
```
so executable file appears

invadeGo script:
```
folder="/Users/ssaadain/invasions/"
tool="/Users/ssaadain/invadego/invadego_build"

$tool --seed 1713797481534670000 --N 1000 --gen 100 --genome mb:1 --cluster b:76500 --rr 4 --rep 100 --u 0.1 --basepop 100 --steps 25 > $folder/Dvir_bases.txt
```

