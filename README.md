# nerdl

> Named Entity Recognition, Disambiguation and Linking  

SoNAR (IDH) workflow: [zefys](https://github.com/sonar-idh/nerdl/blob/main/README.md#zefys) → [ocrd](https://github.com/sonar-idh/nerdl/blob/main/README.md#ocrd) → [page2tsv](https://github.com/sonar-idh/nerdl/blob/main/README.md#page2tsv) → [sbb_ner](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ner) → [sbb_ned](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ned) → [trs](https://github.com/sonar-idh/nerdl/blob/main/README.md#trs) → [neat](https://github.com/sonar-idh/nerdl/blob/main/README.md#neat)

#### required
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
You need either local or remote access to the digitised newspaper images from [ZEFYS](https://zefys.staatsbibliothek-berlin.de/)

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
to OCR digitised newspapers from [ZEFYS](http://zefys.staatsbibliothek-berlin.de/index.php?id=start&L=1) based 
on `zdb-id` and date of issue `yyyymmdd`
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

If images are served via [`iiif`](https://iiif.io/api/image/2.1/), the OCR coordinates can be used to 
generate according image urls by also providing the `--image-url`
```bash
page2tsv SNP27974534-19010712-0-1-0-0.xml SNP27974534-19010712-0-1-0-0.tsv \
--image-url=https://content.staatsbibliothek-berlin.de/zefys/SNP27974534-19010712-0-1-0-0/full/full/0/default.jpg
```

:warning: 
The following steps assume you have access to or setup local instances of
* [sbb_ner](https://github.com/qurator-spk/sbb_ner)
* [sbb_ned](https://github.com/qurator-spk/sbb_ned)

#### sbb_ner
Apply named entity recognition with [sbb_ner](https://github.com/qurator-spk/sbb_ner)
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ner-rest-endpoint
```

#### sbb_ned
Apply named entity disambiguation and linking with [sbb_ned](https://github.com/qurator-spk/sbb_ned)
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ned-rest-endpoint
```

#### trs
**TODO**  
Coupling `tsv` to [trs](https://github.com/sonar-idh/Transformer)

Information provided by `tsv` filename:  

SNP{`zdb-id`}-{`yyyymmdd`}-{`issue`}-{`page`}-{`article`}-{`version`}.tsv
  * `zdb-id` (any `-` removed)
  * date of issue (`yyyymmdd`)
  * issue number (`0` = morning issue, `1` = evening issue etc., default `0`)
  * page/image number
  * article id (not used, default `0`)
  * version number (not used, default `0`)

Example: `SNP27974534-19010712-0-1-0-0.tsv`

Information provided in `tsv` file:
  * `iiif_url` baseurl for the document as comment at the top
  * sentence position
  * token text (`utf-8`) 
  * surface entity label 
  * embedded entity label
  * surface entity wikidata ID (ranked candidates separated by `|`)
  * `url_id` is a placeholder
  * token OCR coordinates (`left`,`top`,`width`,`height`)
  
Example:
```tsv
SENTPOS TOKEN           NE-TAG  NE-EMB  WIKIDATA        url_id  left    top     width   height 
# https://iiif.url
36 	bekannter 	O 	O 	-           	- 	157 	181 	643 	660
37 	Comédie 	B-ORG 	B-LOC 	Q61460498 	- 	197 	262 	643 	661
38 	françaiſe 	I-ORG 	I-LOC 	Q61460498 	- 	277 	345 	642 	661
39 	anvertraut 	O 	O 	-          	- 	359 	440 	644 	659
```

#### neat
Use the browser-based [neat](https://github.com/qurator-spk/neat) to inspect, correct or annotate `tsv` files
```bash
git clone https://github.com/qurator-spk/neat
cd neat
firefox index.html
```

