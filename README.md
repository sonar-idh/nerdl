# nerdl

> Named Entity Recognition, Disambiguation and Linking

### Pipeline

[zefys](https://github.com/sonar-idh/nerdl/blob/main/README.md#zefys) → [ocrd](https://github.com/sonar-idh/nerdl/blob/main/README.md#ocrd) → [page2tsv](https://github.com/sonar-idh/nerdl/blob/main/README.md#page2tsv) → [sbb_ner](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ner) → [sbb_ned](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ned) → [trs](https://github.com/sonar-idh/nerdl/blob/main/README.md#trs) → [neat](https://github.com/sonar-idh/nerdl/blob/main/README.md#neat)

---

#### requirements
- [Python3](https://www.python.org/) 

#### recommended
- `virtualenv`

---

Setup a Python3 virtual environment and activate it
```
python3 -m venv /path_to_venv
source /path_to_venv/bin/activate
```

#### zefys
You need either local or remote access to the digitised newspapers images from [ZEFYS](https://github.com/sonar-idh/nerdl/blob/main/README.md#zefys)

local
```bash
mkdir zefys
mount -o ro,noload /zefys/archive /zeyfs
```
remote  
> Download images via [`iiif`](https://iiif.io/)  
(see [sbb-lab](https://lab.sbb.berlin/5393/?lang=en) for further information)

#### ocrd
Install [OCR-D](https://ocr-d.de/) workflow via [ocrd-galley](https://github.com/qurator-spk/ocrd-galley)
```bash
git clone https://github.com/qurator-spk/ocrd-galley
cd ocrd-galley
./build
```

You can now use [zdb2ocr](https://github.com/qurator-spk/ocrd-galley/blob/master/zdb2ocr) 
to OCR digitised newspapers from ZEFYS based on `zdb-id` and date of issue `yyyymmdd`
```bash
zdb2ocr 27974534 19010712
```

#### page2tsv
Install [page2tsv](https://github.com/qurator-spk/page2tsv)
```bash
git clone https://github.com/qurator-spk/page2tsv
cd page2tsv
pip install .
```

You can now use [page2tsv](https://github.com/qurator-spk/page2tsv) to transform the 
[PAGE-XML](https://github.com/PRImA-Research-Lab/PAGE-XML) output of the OCR into a tab-separated-values (`tsv`) format
```bash
page2tsv SNP27974534-19010712-0-1-0-0.xml SNP27974534-19010712-0-1-0-0.tsv
```

If images are served via [iiif-image-api](https://iiif.io/api/image/2.1/), the OCR 
coordinates can be used to generate according image urls by also providing the iiif-server-baseurl as `--image-url`
```bash
page2tsv SNP27974534-19010712-0-1-0-0.xml SNP27974534-19010712-0-1-0-0.tsv \
--image-url=https://content.staatsbibliothek-berlin.de/zefys/SNP27974534-19010712-0-1-0-0/full/full/0/default.jpg
```

**Warning**  
The following steps assume you have access to or setup local instances of both
* [sbb_ner](https://github.com/qurator-spk/sbb_ner)
* [sbb_ned](https://github.com/qurator-spk/sbb_ned)

#### sbb_ner
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ner-rest-endpoint
```

#### sbb_ned
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ned-rest-endpoint
```

#### trs
**TODO**  
Coupling `tsv` to [trs](https://github.com/sonar-idh/Transformer)

#### neat
Use the browser-based [neat](https://github.com/qurator-spk/neat) to inspect, correct or annotate `tsv` files
```bash
git clone https://github.com/qurator-spk/neat
cd neat
firefox index.html
```

