# IGT Data Examples
Examples illustrating how interlinear glossed text (IGT) data is formatted in various computational (UD CoNLL-U TXT), archival (CLDF CSV) and fieldwork software formats (ELAN & FLEx XML).

*MELD = Machine Learning for Endangered Language Documentation Lab @ University of Florida*

## Metadata
| Language (ISO-639-3)      | Source                                        | Source Original Format  | Converted Formats | Original Source file name          |
|:-------------:            |:-------------:                                |:-------------: | :---------------:| :------------------------:|
| Akan (aka)                | Akan Corpus 1.0 $\rightarrow$ CLDF Cookbook                | TypeCraft->CLDF| ELAN, FLEx, CLDF, CoNLL-U       | |
| Arabic (ara)              | Arabic-PADT UD Treebank                       | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | |
| English (eng)             | Georgetown University Multilayer Corpus (GUM) | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | |
| Hindi (hin)               | Hindi UD Treebank                             | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | |
| Mandarin Chinese (cmn)    | UD Chinese Beginner Treebank                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | |
| Russian (rus)             | Taiga Corpus                                  | CoNLL-U        | ELAN, FLEx, CLDF, CoNLL-U | |
| Xibe (sjo)                | UD Xibe Treebank                              | CoNLL-U        | CLDF, ELAN, FLEx | |
| Upper Tanana (tau)        | Olga Lovick                                   | ELAN           |   ELAN, FLEx, CLDF               | UTOFLA09Aug1201-18620.eaf |
| Dene Suline (chp)         | Olga Lovick                                   | ELAN           |   ELAN, FLEx, CLDF, CoNLL-U               | NET-2020-05-29-TDC.eaf    |
| Alas Kluet (btz)          | Adnrew Brumleve                               | FLEx           |                  | btz-all_txts_ORIGINAL.flextext |
  

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
