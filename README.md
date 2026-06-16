
# IGT Data Examples
This repository contains examples illustrating how the same data (in different languages)  is formatted in various computational, archival, and fieldwork linguistics software formats. Primarily, this repository focuses on how various programs represent [interlinear glossed text](https://en.wikipedia.org/wiki/Interlinear_gloss) (IGT) in these formats.
# Why?
This repository is meant to serve as a starting point for programmers looking to create converters between popular linguistic data formats. They can also be used by application designers to understand the structure of certain filetypes so they can add compatability to their programs for a wider variety of linguistic softwares. Linguistic knowledge is not assumed, only knowledge that is required to understand the underlying filetypes of these formats (XML, CSV/TSV, and CoNLL-U Schema).
# Formats
## Data Formats used in Computational Linguistics / Natural Language Processing
* Universal Dependencies CoNLL-U (*.conllu*); [Schema](https://universaldependencies.org/format.html)
## Data Formats intended for Archiving
* Cross-Linguistic Data Formats CLDF (*-CLDF.csv*); [GitHub](https://github.com/cldf/cldf), [Ontology](https://cldf.clld.org/v1.0/terms.rdf) (Download)
## Data Formats from Specialized Linguistic Software 
* Max Planck Institute for Psycholinguistics's ELAN (*.eaf*) (XML)
* SIL Global's FieldWorks Language Explorer (FLEx), in three formats
	* Interlinear Texts (*.flextext*) (XML)
		* Represent, in XML, the structure of an interlinear glossed text, a method linguists use to break words into smaller units of meaning.
	* FLEx Project Files (*.fwbackup*, *.fwdata*)
## Metadata
The examples in this repository fall into 2 categories: **Curated** (Cur) and **Realistic**. **Curated** data tend to be much less extensive because we took very short examples from online curated data to quickly and simplistically represent how the data can appear in a particular format. **Realistic** data is significantly more extensive, usually provided by field linguists, containing all their data rather than a selected sample, and is meant to realistically illustrate typical linguistics project data. The `TAU`, `CHP`, and `BTZ` files serve as representative examples of realistic, low-resource documentary language data originating directly from linguistic fieldwork.
|Type| Language (ISO-639-3)      | Source                                        | Source Original Format  | Converted Formats | Original Source File Name |
|:-------------:|:-------------:            |:-------------:                                |:-------------: | :---------------:| :------------------------:|
|Cur.| Akan (aka)                | [TypeCraft Akan Corpus 1.0]((https://typecraft.org/tc2wiki/TypeCraft_Akan_Data_Collection_Release_1.0)) via [CLDF Cookbook](https://github.com/cldf/cookbook)                | CLDF from TypeCraft| ELAN, FLEx, CLDF, CoNLL-U       |Release 1.0.zip |
|Cur.| Arabic (ara)              | [UD Arabic-PADT Treebank](http://hdl.handle.net/11234/1-6149)                       | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Real.| Batak Alas-Kluet (btz)          | [Andrew Brumleve](ajbrumleve.github.io)                               | FLEx           |                 ELAN, FLEx, CoNLL-U | btz-all_txts_ORIGINAL.flextext |
|Cur.| English (eng)             | [UD Georgetown University Multilayer Corpus (GUM)](http://hdl.handle.net/11234/1-6149) | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Cur.| Hindi (hin)               | [UD Hindi Treebank](http://hdl.handle.net/11234/1-6149)                             | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Cur.| Mandarin Chinese (cmn)    | [UD Chinese Beginner Treebank](http://hdl.handle.net/11234/1-6149)                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Cur.| Russian (rus)             | [UD Taiga Corpus](http://hdl.handle.net/11234/1-6149)                                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Cur.| Xibe (sjo)                | [UD Xibe Treebank](http://hdl.handle.net/11234/1-6149)                              | CoNLL-U        | ELAN, FLEx CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Real.| Upper Tanana (tau)        | [Olga Lovick](https://artsandscience.usask.ca/profile/OLovick)                                   | ELAN           |   ELAN, FLEx, CLDF, CoNLL-U               | UTOFLA09Aug1201-18620.eaf |
|Real.| Dene Suline (chp)         | [Olga Lovick](https://artsandscience.usask.ca/profile/OLovick)                                   | ELAN           |   ELAN, FLEx, CLDF, CoNLL-U               | NET-2020-05-29-TDC.eaf    |

## Technical Notes & Schema Edge Cases

### Universal Dependencies (UD) & CoNLL-U Specifications

* **Morphological Limitations:** UD annotations identify morphological features but do not perform explicit morphological segmentation, which can obscure morpheme breakdowns (see CoNLL-U sourced examples). Conversely, UD may extract only minimal morphological information from analytical languages (see Mandarin example).
* **Transliteration Handling:** Practices vary by language repository within `master.conllu`:
* *Word-level vs. Sentence-level:* Provided as a word-level attribute (Arabic) or a sentence-level comment (Hindi).
* *Phonological Transcription:* Transliterations may occasionally serve as phonological transcriptions (see Xibe, where data resides under the `# text[phon]` comment and `Translit` attribute).


* **Untokenized Data (`text_orig`):** Typically reserved for untokenized data. However, the Mandarin example anomalously uses this field to store tokenized data containing spaces.
* **XPOS Field Utilization:** The `XPOS` field captures highly mixed data types, including morphology, syntax (e.g., adposition type), and semantics (e.g., abbreviations, specific name types).

### Cross-Framework Mapping (UD, ELAN, FLEx, CLDF)

* **Multiword Tokens & Contractions:** Components parsed as multiword tokens in UD are handled differentially across field tools:
* *ELAN:* Separated strictly at the `WORD` level.
* *FLEx:* Grouped at the lexical level under a single `Word` entry.


* **Structural Parsing Constraints (CLDF):** CLDF natively lacks dedicated fields to differentiate between surface and underlying morphemes—a distinction routinely maintained in both ELAN and FLEx workflows.

### Language-Specific Edge Cases

* **Akan:** The selected sample contains `ɛɛbayɛ` instead of `ɛɛba yɛ` in the primary text, though it undergoes standard analysis as `ɛɛ-ba yɛ`. Additionally, the segmented prefix `ɛɛ-` contains a blank gloss field. These behaviors are preserved in the data as explicit edge cases.
