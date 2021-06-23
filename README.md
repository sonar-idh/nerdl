> Named Entity Recognition, Disambiguation and Linking of Digitized Newspapers for Historical Network Analysis 

This repository contains a proof of concept workflow for optical character recognition (OCR), named entity recognition (NER), named entity disambiguation and linking (NED) and transformation of digitized historical newspapers for historical network analysis (HNA). 

The workflow was developed by the [Berlin State Library (SBB)](https://staatsbibliothek-berlin.de/), the [Berlin School of Library and Information Science (IBI)](https://www.ibi.hu-berlin.de/) and the [German Research Center for Artificial Intelligence (DFKI)](https://www.dfki.de/) in the context of the [SoNAR (IDH)](http://sonar.fh-potsdam.de/) project.

The main aims were to explore the technical feasibility, quality and usability of the results for scholarly use cases in historical network analysis and [data visualization](https://github.com/sonar-idh/visualization-prototypes).

The individual components are based on state-of-the-art open source technologies from the [OCR-D](https://github.com/OCR-D) and [QURATOR](https://github.com/qurator-spk) projects.

The workflow was evaluated and the results are published in [BIPRA](https://www.degruyter.com/document/isbn/9783110691597/html).

### About

The workflow includes the following steps:

[1. Access images of digitized newspapers from Zefys](https://github.com/sonar-idh/nerdl/blob/main/README.md#zefys)  
[2. Apply OCR to the images using the OCR-D framework](https://github.com/sonar-idh/nerdl/blob/main/README.md#ocrd)  
[3. Transform the OCR output into TSV format](https://github.com/sonar-idh/nerdl/blob/main/README.md#page2tsv)  
[4. Recognize named entities in the OCRed text](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ner)  
[5. Disambiguate and link entities to Wikidata-IDs](https://github.com/sonar-idh/nerdl/blob/main/README.md#sbb_ned)  
[6. Manually inspect or edit the results in a browser](https://github.com/sonar-idh/nerdl/blob/main/README.md#neat)  
[7. Transform the results for use in a graph db](https://github.com/sonar-idh/nerdl/blob/main/README.md#trs)  

### Prerequisites

To install and test the workflow, the following prerequisites must be met.

##### required
- [Python3](https://www.python.org/) 

##### recommended
- [`virtualenv`](https://virtualenv.pypa.io/en/stable/)
- [`pip`](https://pip.pypa.io/en/stable/)

Setup a Python3 `virtualenv` and activate it
```bash
python3 -m venv /path_to_venv
source /path_to_venv/bin/activate
```

Update `pip`
```python
pip install -U pip
```

### zefys
You need either local or remote access to the digitised newspaper images from [Zefys](https://zefys.staatsbibliothek-berlin.de/)

##### local
```bash
mkdir zefys
mount -o ro,noload /zefys/archive /zeyfs
```

##### remote  
Download images using the [`API`](https://lab.sbb.berlin/5393/?lang=en)  

### ocrd
Install [OCR-D](https://ocr-d.de/) via [ocrd-galley](https://github.com/qurator-spk/ocrd-galley)
```bash
git clone https://github.com/qurator-spk/ocrd-galley
cd ocrd-galley
./build
```

You can now use [zdb2ocr](https://github.com/qurator-spk/ocrd-galley/blob/master/zdb2ocr) 
to OCR digitised newspapers from [Zefys](https://zefys.staatsbibliothek-berlin.de/) based 
on their `zdb-id` (with any `-` removed) and date of issue `yyyymmdd`
```bash
zdb2ocr 27974534 19010712
```

### page2tsv
Install [page2tsv](https://github.com/qurator-spk/page2tsv)
```bash
git clone https://github.com/qurator-spk/page2tsv
cd page2tsv
pip install .
```

You can now use [page2tsv](https://github.com/qurator-spk/page2tsv) to transform the 
[PAGE-XML](https://github.com/PRImA-Research-Lab/PAGE-XML) output of the OCR into a tab-separated-values ([`tsv`](https://github.com/sonar-idh/nerdl#tsv-documentation)) format
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

### sbb_ner
Apply named entity recognition with [sbb_ner](https://github.com/qurator-spk/sbb_ner)
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ner-rest-endpoint
```

### sbb_ned
Apply named entity disambiguation and linking with [sbb_ned](https://github.com/qurator-spk/sbb_ned)
```bash
page2tsv SNP27974534-19010712-0-1-0-0.tsv --ned-rest-endpoint
```

### neat
Use the browser-based [neat](https://github.com/qurator-spk/neat) to inspect, correct or annotate `tsv` files
```bash
git clone https://github.com/qurator-spk/neat
cd neat
firefox neat.html
```

### trs
**TODO**  
Coupling [`tsv`](https://github.com/sonar-idh/nerdl#tsv-documentation) to [trs](https://github.com/sonar-idh/Transformer)


### TSV documentation
Information provided by the `tsv` filename:  

SNP{`zdb-id`}-{`yyyymmdd`}-{`issue`}-{`page`}-{`article`}-{`version`}.tsv
  * `zdb-id` (any `-` removed)
  * date of issue (`yyyymmdd`)
  * issue number (`0` = morning issue, `1` = evening issue etc., default `0`)
  * page/image number
  * article id (not used, default `0`)
  * version number (not used, default `0`)

Example: `SNP27974534-19010712-0-1-0-0.tsv`

Information provided in the `tsv` file columns:
  * `iiif_url` placeholder injected as a comment under the column headers
  * `No.` indicates the sentence position (`≥1`, `0` marks sentence boundaries)
  * `TOKEN` contains the token text (`utf-8` encoded) 
  * `NE-TAG` contains the surface entity label (`BIO` chunking)
  * `NE-EMB` contains the embedded entity label (`BIO` chunking)
  * `ID` contains the surface entity wikidata ID (ranked candidates are separated by `|`)
  * `url_id` is replaced with the `iiif_url`
  * `left`,`top`,`width`,`height` hold the token OCR coordinates as absolute pixel values
  
Example:
```tsv
No.     TOKEN           NE-TAG  NE-EMB  ID              url_id  left    top     width   height 
# https://iiif.url
36 	bekannter 	O 	O 	-           	- 	157 	181 	643 	660
37 	Comédie 	B-ORG 	B-LOC 	Q61460498 	- 	197 	262 	643 	661
38 	françaiſe 	I-ORG 	I-LOC 	Q61460498 	- 	277 	345 	642 	661
39 	anvertraut 	O 	O 	-          	- 	359 	440 	644 	659
```

