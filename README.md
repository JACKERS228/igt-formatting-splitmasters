
# IGT Data Examples
This repository contains examples illustrating how the same data (in different languages)  is formatted in various computational, archival, and fieldwork software formats.
## Computational Data Formats
* Universal Dependencies CoNLL-U (*.conllu*); [Schema](https://universaldependencies.org/format.html)
## Archival Data Formats
* Cross-Linguistic Data Formats CLDF (*-CLDF.csv*); [GitHub](https://github.com/cldf/cldf), [Ontology](https://cldf.clld.org/v1.0/terms.rdf) (Download)
## Linguistic Field Work Software Data Formats
* Max Planck Institute for Psycholinguistics, ELAN (*.eaf*) (XML)
* Summer Institute of Linguistics, FieldWorks Language Explorer (FLEx)
	* Interlinear Texts (*.flextext*) (XML)
		* Represent, in XML, the structure of an interlinear glossed text, a method linguists use to break words into smaller units of meaning.
	* FLEx Project Files (*.fwbackup*, *.fwdata*)
## Metadata
The examples in this repository fall into 2 categories; **Simplified** and **Realistic**. **Simplified** data is much less extensive and is meant to quickly and simplistically represent how the same data can appear in the various formats. **Realistic** data is significantly more extensive and is meant to realistically illustrate how real project data would look converted between formats.
|Type| Language (ISO-639-3)      | Source                                        | Source Original Format  | Converted Formats | Original Source File Name |
|:-------------:|:-------------:            |:-------------:                                |:-------------: | :---------------:| :------------------------:|
|Simp.| Akan (aka)                | [TypeCraft Akan Corpus 1.0]((https://typecraft.org/tc2wiki/TypeCraft_Akan_Data_Collection_Release_1.0)) via [CLDF Cookbook](https://github.com/cldf/cookbook)                | TypeCraft->CLDF| ELAN, FLEx, CLDF, CoNLL-U       |Release 1.0.zip |
|Simp.| Arabic (ara)              | [UD Arabic-PADT Treebank](http://hdl.handle.net/11234/1-6149)                       | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Simp.| English (eng)             | [UD Georgetown University Multilayer Corpus (GUM)](http://hdl.handle.net/11234/1-6149) | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Simp.| Hindi (hin)               | [UD Hindi Treebank](http://hdl.handle.net/11234/1-6149)                             | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Simp.| Mandarin Chinese (cmn)    | [UD Chinese Beginner Treebank](http://hdl.handle.net/11234/1-6149)                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Simp.| Russian (rus)             | [UD Taiga Corpus](http://hdl.handle.net/11234/1-6149)                                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Simp.| Xibe (sjo)                | [UD Xibe Treebank](http://hdl.handle.net/11234/1-6149)                              | CoNLL-U        | ELAN, FLEx CLDF, CoNLL-U | ud-treebanks-v2.18.tgz|
|Real.| Upper Tanana (tau)        | [Olga Lovick](https://artsandscience.usask.ca/profile/OLovick)                                   | ELAN           |   ELAN, FLEx, CLDF               | UTOFLA09Aug1201-18620.eaf |
|Real.| Dene Suline (chp)         | [Olga Lovick](https://artsandscience.usask.ca/profile/OLovick)                                   | ELAN           |   ELAN, FLEx, CLDF, CoNLL-U               | NET-2020-05-29-TDC.eaf    |
|Real.| Alas Kluet (btz)          | [Andrew Brumleve](ajbrumleve.github.io)                               | FLEx           |                 --- | btz-all_txts_ORIGINAL.flextext |
  

## Notes
* UD annotation can identify morphological information but it does not perform morphological segmentation, which obscures morpheme breakdown (see any CoNLL-U-sourced example). 
    * UD may identify minimal morphological information from analytical languages (see Mandarin example) 
* Transliterations in CoNLL-U are provided as a word-level attribute (see Arabic in master.conllu) and/or a sentence-level comment (ex. Hindi)
    * Transliterations may instead provide phonological transcriptions? (see Xibe in master.conllu, where transliterations are under the 'text[phon]' comment and 'Translit' attribute)
* Untokenized data is typically put in the 'text_orig' comment. However, the Mandarin example uses it to contain tokenized data (with spaces).
* CoNLL-U's XPOS field contains morphological, syntactic (ex. adposition type) and semantic information (ex. abbreviation, type of name).
* Components of contractions and other multiword tokens as parsed by UD are separated in ELAN at the WORD level, and in FLEx at the lexical level under one 'Word'.
* The chosen Akan example contained 'ɛɛbayɛ' instead of 'ɛɛba yɛ' in its primary text, though it was analyzed as 'ɛɛ-ba yɛ' as normal. Meanwhile, 'ɛɛ-' is segmented but has a blank gloss. These are retained as an edge case.
* CLDF lacks fields to input surface and underlying morphemes, a distinction possible in ELAN and FLEx.
* TAU, CHP, and BTZ files are examples of realistic samples documentary language data originating in linguistic fieldwork.
