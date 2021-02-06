# nerdl

> Named Entity Recognition, Disambiguation and Linking

### Pipeline

[zefys](http://zefys.staatsbibliothek-berlin.de/index.php?id=start&L=1) → [ocrd](https://github.com/qurator-spk/ocrd-galley) → [page2tsv](https://github.com/qurator-spk/page2tsv) → [sbb_ner](https://github.com/qurator-spk/sbb_ner) → [sbb_ned](https://github.com/qurator-spk/sbb_ned) → [trs](https://github.com/sonar-idh/Transformer) [→  [neat](https://github.com/qurator-spk/neat)]

---

#### requirements
- [Python3](https://www.python.org/) 

recommended
- `virtualenv`

Setup a Python3 virtual environment and activate it
```
python3 -m venv /path_to_venv
source /path_to_venv/bin/activate
```

#### zefys

local
```bash
mkdir zefys
mount -o ro,noload /zefys/archive /zeyfs
```

remote  
> Download images via `iiif`  
(see [sbb-lab](https://lab.sbb.berlin/5393/?lang=en) for further information)


#### ocrd
Install [OCR-D](https://ocr-d.de/) workflow via [ocrd-galley](https://github.com/qurator-spk/ocrd-galley)
```bash
git clone https://github.com/qurator-spk/ocrd-galley
cd ocrd-galley
./build
```

You can now use [zdb2ocr](https://github.com/qurator-spk/ocrd-galley/blob/master/zdb2ocr) 
to process digitised newspapers from ZEFYS with OCR based on their `zdb-id` and date of issue `yyyymmdd`
```bash
./zdb2ocr 27974534 19010712
```


#### page2tsv


#### sbb_ner


#### sbb_ned


#### trs


#### neat
