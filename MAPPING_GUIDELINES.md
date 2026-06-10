# Introduction
This is a guide for programmers looking to understand how different linguistic phenomena and metadata map in various common linguistic data formats. This guide will also note the quirks of converting between the formats. The "common formats" under focus in this guide are: **FLEx**<sup>1</sup> (*.fwdata*, *.flextext*, and *.fwbackup*), **ELAN**<sup>2</sup> (*.eaf*), **CLDF**<sup>3</sup> (*.csv*), and **CoNLL-U**<sup>4</sup> (*.conllu*). We will also provide consideration of various conversion directions (FLEx ↔ ELAN, FLEx ↔ CLDF, etc)
# Converting from FLEx
**Foreign Language Explorer**, or **FLEx**, is a linguistic software tool used to build dictionaries (lexicons), organize texts, and perform linguistic analysis. FLEx uses 3 primary file formats to store information related to linguistic analyses and language data:
* *.fwdata* and *.fwbackup*: These files store the FLEx project data. These contain the basic files necessary to launch a FLEx project and configure settings without any associated texts. *.fwdata* is the main version of the project file, while *.fwbackup* are generated using the **Backup Project** function. Converting the file to a *.zip* file reveals additional files, but there is no clear indication where any grammatical data is stored, preventing seeming any low-level manipulation of that data.
* *.flextext*: These files store the *interlinear glossed texts* (IGTs) and are independent of the project. However, they do depend on certain settings within the FLEx project in order to function properly and display properly. *.flextext* files follow an XML schema.
	* Parts of speech need to be added to the project the .flextext is to be imported to. Otherwise they will appear as "Not sure" when loaded in the program.
	* Currently, only baseline text (raw text) and certain word categories are preserved when importing a .flextext. Everything else is stripped from the import (likely as a compatability measure) since a good number of imported .flextexts are moved between different linguistics software.
Here, we show how the *.flextext* formatted IGT is mapped to the three other linguistic data formats: ELAN, CoNLL-U, and CLDF. Each subsection provides some background and considerations for mapping between the two formats, as well as generalizing "what maps to what."
## FLEx to ELAN
ELAN's formatting is flexible in that tier structures and hierarchies are widely variable and linguists very infrequently have the same tier structure. So this flexibility is worth considering as a blessing and a curse. This applies to all instances of conversion *to ELAN*. As FLEx does not natively perform time alignment, placeholder time slots would need to be added by the converter. However, for this guide, we will be assuming you are trying to get just an Interlinear Glossed Text in *.flextext* format into ELAN and are not concerned with time alignments just yet.
### Document-level
At the Document level, FLEx represent the individual text as an `<interlinear-text>` tag. This maps to the ELAN `<ANNOTATION_DOCUMENT>` tag. 
```xml title="FLEx -> ELAN"
<document version="3">
	<interlinear-text guid="">
		<!-- Other Data... -->
 
 <!-- MAPS TO -->

<ANNOTATION_DOCUMENT AUTHOR="" DATE="" FORMAT="3.0" VERSION="3.0">
```
### Notes on Time-Alignment
When converting to ELAN from FLEx, a project will likely have no associated media files for time alignment. As ELAN requires time alignments regardless whether the data is aligned to media or not, a converter will need to be mindful to create appropriate time alignments. However, this only pertains to *non-symbolic subdivision* tiers such as a tier that contains the Word data. Morphemes only need to be mapped via an `ANNOTATION_REF` attribute within a `<REF_ANNOTATION>` tag in the *.eaf* XML. For example, the word *taking* and its underlying form *take*:
```xml
<ANNOTATION>
    <ALIGNABLE_ANNOTATION ANNOTATION_ID="a9"
        TIME_SLOT_REF1="ts14" TIME_SLOT_REF2="ts15">
        <ANNOTATION_VALUE>taking</ANNOTATION_VALUE>
    </ALIGNABLE_ANNOTATION>
</ANNOTATION>

<ANNOTATION>
    <REF_ANNOTATION ANNOTATION_ID="a41" ANNOTATION_REF="a9">
        <ANNOTATION_VALUE>take</ANNOTATION_VALUE>
    </REF_ANNOTATION>
</ANNOTATION>
```

As you can see, the first annotation is the surface form that is time aligned to time slots `ts14` and `ts15`. These time slots correspond to defined time slots in the `<TIME_ORDER>` defined after the `<HEADER>` at the beginning of the `<ANNOTATION_DOCUMENT>`. The different references to one time slot and another define the range of time that the annotation covers. The reference annotation, or the underlying morpheme in this case, has its own unique annotation ID and that is linked to the surface form via an `ANNOTATION_REF` attribute which is set to the surface form's annotation ID.
Thus, a converter from FLEx must be able to create this structure and define appropriate time slots and unique annotation IDs, while maintaining relationships between surface and citation forms to ensure easy mapping.
### Metadata & Interlinearization (WIP)
#### Metadata
FLEx metadata would be considered information contained in a text's **Info** tab, including the Title in its various languages, Abbreviation, Source, Text is a translation boolean, Media association, Genres, Comments, and creation/modification timestamps. All of this data is found in the XML as `<item type="">` tags just under `<interlinear-text>`, as these elements define the text's metadata.
Since a lot of linguistic metadata is recorded in in the ELAN tier structure, the best way for FLEx metadata to be converted is into a series of tiers.
ELAN represents the tier hierarchy, in the *.eaf* XML, as a list all items in depth order; all items at one level of depth are listed in the order of their hierarchies and then it proceeds to the next level of depth. 
![[image-6.png]]
As far as mapping for FLEx to ELAN, annotations in each tier would be defined based on where they appear in the FLEx IGT with the hierarchy. For example a document's metadata is rendered in FLEx as:
```xml title="ENGLISH.flextext"
<interlinear-text guid="506a93a2-8037-4081-bb64-af83ef0e1565">
	<item type="title" lang="en">Wonderful Abstract Notions</item>
    <item type="source" lang="en">https://www.linguistics.ucsb.edu/research/santa-barbara-corpus#SBC017</item>
    <item type="comment" lang="en">(human1) Two friends discuss a cartoon called 'The Scientist' that symbolizes the limitations of the scientific method, contrasting unquestioned adherence to the scientific method with Einstein's new ways of doing things, as well as how technology today is soulless and tied to consumerism.</item>
    <item type="title-abbreviation" lang="en">GUM_conversation_scientist</item>
    <item type="notebook-record" guid="d2fe4964-f7be-4a8e-92ad-634c004bdc72" lang="en"></item>
    <item type="genre" guid="c14b2590-ea5e-11de-85fc-0013722f8dec" lang="en">Conversation</item>
    <item type="date-created" lang="en">2026-05-11 13:12:08.931</item>
    <item type="date-modified" lang="en">2026-05-15 13:20:47.218</item>
	<!-- More Metadata... -->
	<paragraphs>
		<!-- Interlinear Data... -->
```
However, in ELAN, this data is rendered as part of the tier hierarchy. For example, the `type="title"` metadata may be listed in a top level metadata tier following the syntax:
```xml
<TIER ANNOTATOR="Jarrod" LINGUISTIC_TYPE_REF="default-lt" TIER_ID="title">
	<ANNOTATION>
		<ALIGNABLE_ANNOTATION ANNOTATION_ID="a3" TIME_SLOT_REF1="ts1" TIME_SLOT_REF2="ts2">
			<ANNOTATION_VALUE>Wonderful Abstract Notions</ANNOTATION_VALUE>
		</ALIGNABLE_ANNOTATION>
	</ANNOTATION>
</TIER>
```
I even applied this code directly to a *.eaf* file and it successfully displayed with the correct time alignment:
![[image-5.png]]

Thus, to preserve all of the metadata on transfer, **each element of the metadata in the `<item>` tags under `<interlinear-text>` must be mapped to an associated tier in the *.eaf* structure**.
#### Interlinearization
Moving to interlinearization, in FLEx, there are the "default" lines in the IGT. In the UI, these are:
* Word
* Morphemes
* Lexical Entries ("Lexeme Form")
* Lexeme Gloss
* Lexeme Grammatical Info
* Word Gloss
* Word Category
* Free Translation
In *.flextext* format, these are represented by various XML tags which form a hierarchy that breaks a word into its various morphemes. For example, the word *scientist -s* in the simplified previous case would be represented in the XML as:
```xml title="ENGLISH.flextext"
<paragraphs>
	<paragraph guid="xxx">
		<phrases>
			<phrase guid="xxx">
				<item type="txt" lang="en">scientists</item>
				<item type="segnum">1</item>
				<words>
					<word guid="xxx">
						<item type="txt" lang="en">scientists</item>
						<morphemes>
							<morph type="stem" guid="d7f713e8-e8cf-11d3-9764-00c04f186933">
			                    <item type="txt" lang="en">scientist</item>
			                    <item type="cf" lang="en">scientist</item>
			                    <item type="gls" lang="en"></item>
			                    <item type="msa" lang="en">NOUN</item>
			                </morph>
			                <morph type="suffix" guid="d7f713dd-e8cf-11d3-9764-00c04f186933">
				                <item type="txt" lang="en">-s</item>
			                    <item type="cf" lang="en">-s</item>
			                    <item type="gls" lang="en">Plur</item>
			                    <item type="msa" lang="en">NOUN:(Number)</item>
			                </morph>
						</morphemes>
					</word>
				</words>
			</phrase>
		</phrases>
	</paragraph>
</paragraphs>
```
In *.eaf* format, this maps to a similar tier structure like we saw with the metadata, only there is the interior structure which is again handled by reference annotations back to the surface morpheme. As far as raw mappings:
* You have a **Baseline** text tier with the raw text in one annotation; the *.flextext* interlinear `paragraph`/`phrase` values map to this. They can be multiple annotations to separate the text into paragraphs or sentences.
* **Word** maps to some top level tier beneath that with each word segmented equidistantly from the previous and following (As this would be a conversion from FLEx with no connection to ELAN, there would be no need for traditional time-alignment, however, time alignment categories are still a necessity per the section [[#Notes on Time-Alignment]])
* **Morphemes** map to a subtier of the Word tier that references the Word annotation. For morpheme segments, they are split into two annotations that align within the Word's time slot. This is handled in the Morphemes tier by adding the attribute `PREVIOUS_ANNOTATION` to the `REF_ANNOTATION` tag. For example, the word *hakhap -en* would be represented in the XML as:
```xml title="Batak_btk.eaf"
<!-- Word tier-->
<TIER ANNOTATOR="" LINGUISTIC_TYPE_REF="" PARENT_REF= TIER_ID="Word">
	<ANNOTATION>
		<ALIGNABLE_ANNOTATION ANNOTATION_ID="a1" TIME_SLOT_REF1="ts1" TIME_SLOT_REF2="ts2">
			<ANNOTATION_VALUE>hakhapen</ANNOATION_VALUE>
	</ANNOTATION>
</TIER>

<!-- Morpheme tier-->
<TIER ANNOTATOR="" LINGUISTIC_TYPE_REF="" PARENT_REF= TIER_ID="Morphemes">
	<ANNOTATION>
		<REF_ANNOTATION ANNOTATION_ID="a2" ANNOTATION_REF="a1">
			<ANNOTATION_VALUE>hakhap</ANNOTATION_VALUE>
		</REF_ANNOTATION>
	</ANNOTATION>
	<ANNOTATION>
		<REF_ANNOTATION ANNOTATION_ID="a3" ANNOTATION_REF="a1" PREVIOUS_ANNOTATION="a2">
			<ANNOTATION_VALUE>-en</ANNOTATION_VALUE>
		</REF_ANNOTATION>
	</ANNOTATION>
</TIER>
<!-- 
We see here how the REF_ANNOTATIONS are pointing back to the Word annotation. This goes for both morpheme segments of the word. 
However, each subsequent segment in the morpheme segmentation must point to the previous one which is where PREVIOUS_ANNOTATION comes in handy.
-->
```

* **Lexical Entries** map to a *symbolic subdivision*<sup>5</sup> of the Morpheme tier that references the Morpheme annotation for alignment.
* **Lexeme Gloss** maps to a symbolic subdivision of the Lexical Entry that references the related Lexical Entry segment.
* **Lexeme Grammatical Information** maps to a symbolic subdivision of the Lexical Entry that references the Lexical Entry.
* **Word Gloss** maps to a symbolic subdivision of the Word tier that references the Word.
* **Word Category** maps to a symbolic subdivision of the Word that references the Word.
* **Free Translation** maps to a symbolic subdivision of the Baseline tier that references the Baseline token (sentence or paragraph; whichever is relevant).
## FLEx to CoNLL-U
The terminology in this section follows the [CoNLL-U Schema](https://contacts.google.com/u/1/widget/hovercard/v/2?origin=https%3A%2F%2Fdocs.google.com&usegapi=1&jsh=m%3B%2F_%2Fscs%2Fabc-static%2F_%2Fjs%2Fk%3Dgapi.lb.en.ch0Jz-JlMrQ.O%2Fd%3D1%2Frs%3DAHpOoo_YD4KoV8fTh_kLhktsiThAm3yJ5A%2Fm%3D__features__#id=I__HC_94253229&_gfid=I__HC_94253229&parent=https%3A%2F%2Fdocs.google.com&pfname=&rpctoken=59389538).
### Metadata
FLEx metadata can be stored using custom labels following CoNLL-U metadata syntax or they can be made to follow a common labelling convention that many CoNLL-U Files follow.
* **Title** maps to `# Title = ` (Custom Format) or `# title =` (Common Format) 
* **Abbreviation** maps to `# Abbreviation = ` (Custom Format)
* **Source** maps to `# Source =` (Custom Format) or `# sourceURL = ` (Common Format)
### Interlinearization
With CoNLL-U format, mapping is much easier since there is very often a one-to-one correspondence between data types in it and FLEx:
* The Baseline or **Word** maps to FORM
* The **Morphemes** map to *either* `MSeg` in MISC *or* as a sub-word in a multi-word expression (which is common for phrases, but could be extended to map complex morphology). Use `MSeg` in MISC when you simply wish to mark morpheme breaks on one line, but use a multi-word expression when you wish to give greater detail about each morpheme.
	* As an example, say you just want to show the morpheme segmentation for the word *cats*. You would have a line in the file for *cats* and in the MISC column, just supply the `MSeg` as `MSeg=cat-s`. On the other hand. You could define *cat* as one line in a multi-word expression and the *-s* as another and define details for each of them:
	```conllu
	1-2 cats [Data...]
	1 cat [Morpheme data...]
	2 s [Morpheme data...]
	```
* **Lexical Entries** map to LEMMA
* **Lexeme Gloss** maps to `lGloss` in MISC column.
* **Lexeme Grammatical Information** maps to XPOS if possible, or fallback to UPOS, but only if that tagset is used.
* **Word Gloss** maps to `Gloss` in MISC column.
* **Word Category** maps to UPOS if using the UPOS tagset; fallback is the POS tag set or other convention being used.
* **Free Translation** maps to `# text[lang_ISO] =` as metadata. `lang_ISO` refers to the **ISO 639-3** code for the language of the translation.
>[!warning] UPOS
>UPOS is a required column in CoNLL-U format. If this cannot be supplied, supply the tags you are using (your XPOS tags) in the UPOS column to prevent errors. Of course, none of this matters if your FLEx database uses UPOS tags already.
## FLEx to CLDF
This section follows the ontology and schema outlined in the `terms.rdf` file on the [CLDF GitHub](https://github.com/cldf/cldf).
* Baseline or **Word** maps to `Primary_Text`
* **Morphemes** map to `Analyzed_Word`
* **Lexical Entries** map to singular lemmas in `Analyzed Word`
* **Lexeme Gloss** maps to `Gloss`
* **Lexeme Grammatical Information** maps to `Part_of_Speech`
* **Word Gloss** maps to `Gloss`
* **Word Category** maps to `wPOS`
* **Free Translation** maps to `Translated_Text`
# Converting from ELAN
**ELAN** is a linguistic software designed specifically for producing time-aligned annotations of media files. ELAN is highly flexible, allowing users to define custom tier structures which creates numerous opportunities for structuring linguistic data. ELAN's data model is XML-based and all data is stored in one file:
* *.eaf*: Stores all tier, annotation, and time slot data.
	* *Tiers* are groups of annotations that can be either **time-aligned** (mark stretches of time in a media file) or **symbolic subdivisions** (smaller divisions of a time aligned annotation).
	* *Annotations* are labelled sections of time or of other annotations that denote linguistic data or metadata.
	* *Time Slots* are programmatically-defined stretches of time that annotations can reference to define what time they occupy in the media file.
The following section, for simplicity, uses a basic ELAN template to illustrate a project. It is likely by no means typical. The hierarchy and tier settings are shown below.
![[image-8.png]]
Here, we show how the *.eaf* formatted IGT is mapped to the three other linguistic data formats: FLEx, CoNLL-U, and CLDF. Each subsection provides some background and considerations for mapping between the two formats, as well as generalizing "what maps to what."
## ELAN to FLEx
>[!note] Work in Progress

Conversion to FLEx from ELAN requires parsing the tiers into the *.flextext* hierarchical format in the manner described in the section on converting FLEx to ELAN. Since ELAN is flexible with what data can be added to a project, we are limiting the mappable items to what can be mapped *from* a FLEx project. That information can be found in [[#To ELAN|the section on mapping FLEx to ELAN]]. There has already some guides explaining how to accomplish an ELAN → FLEx → ELAN workflow <sup>6</sup>.

## ELAN to CoNLL-U
* **Source** metadata tier maps to `# meta::sourceURL = `
* **Text** or **Utt** tier maps to `# text = `
* **Word** maps to Column 2 on a new wordline
* **MB** maps to Column 3 on a wordline
* **GL** maps to `lGloss` or `Gloss` in MISC
* **POS** maps to XPOS
* **TRANSLIT** maps to `Translit` or `LTranslit` in MISC
* **PHON** maps to `Translit` or `LTranslit` in MISC
* **FT** maps to `# text[Lang_ISO] = `
## ELAN to CLDF
* **Source** metadata tier maps to `Source` which must be added to the default `cldf-metadata.json` file in `"columns": [`. 
```json title="cldf-metadata.json"
{
	"datatype": "string",
	"propertyUrl": "http://cldf.clld.org/v1.0/terms.rdf#source",
	"required": false,
	"name": "Source"
}
```
* **Text** or **Utt** maps to `Primary_Text`
* **MB** maps to `Analyzed_Word`
* **GL** maps to `Gloss`
* **POS** maps to `wPOS`.
* **FT** maps to `Translated Text` or `Other Translation`
# Converting from CoNLL-U
![[image-12.png|Example of CoNLL-U data; metadata lines are defined using hashtags, labels and then defined using an equals sign. Then, individual word elements as they appear in the text are defined in the order outlined in the CoNLL-U Schema]]

**CoNLL-U** is a structured data format maintained by Universal Dependencies. It follows a tab-separated format to outline specific information for each word or *FORM* element. CoNLL-U is serialized as *.conllu* files.
Here, we show how the *.conllu* formatted IGT is mapped to the three other linguistic data formats: FLEx, ELAN, and CLDF. Each subsection provides some background and considerations for mapping between the two formats, as well as generalizing "what maps to what."
## CoNLL-U to FLEx
* `# meta::sourceURL = ` maps to Source
* `# newpar` and the wordlines within that maps to `<item type='txt'>` within `<phrase>`
* `MSeg` maps to `<morphemes>` and `<morph><item type='cf'>`
* `Gloss=` maps to Lexeme Gloss or `<item type='gls'>`
* XPOS maps to Lexeme Grammatical Information `<item type='msa'>`
* `# text[Lang_ISO] =` maps to Free Translation `<item type='ft'>`
## CoNLL-U to ELAN
* `# meta::sourceURL = ` maps to Source tier
* `# newpar` and the wordlines within that maps to Utt tier
* FORM maps to WORD
* `MSeg` maps to MB
* `Gloss=` maps to GL
* XPOS maps to POS
* `# text[Lang_ISO] =` maps to FT
## CoNLL-U to CLDF
* `# meta::sourceURL = ` maps to `Source`
* `# newpar` and the wordlines within that maps to `Primary_Text`
* `MSeg` maps to `Analyzed_Text`
* `Gloss=` maps to `Gloss`
* XPOS maps to `Part_of_Speech` or `wPOS`
* `# text[Lang_ISO] =` maps to `Translated_Text`
# Converting from CLDF
![[image-13.png|Partial example of .csv data that conforms to a default CLDF metadata. As it is .csv formatted, data with quotation marks must have those internal double quotes doubled (" becomes "") and the entire comma-separated value must be quoted out (using ") to avoid errors.]]
**Cross-Linguistic Data Formats**, or **CLDF**, is a linguistic data model that represents linguistic data as a series of `ExampleTable` objects that are transferrable between other formats. CLDF uses one serialization, but with two components:
* *.json* Metadata<sup>7</sup>: CLDF stores its metadata in *.json* format and while the documentation states that metadata is not required, it is heavily recommended by the developer.
* *.csv* Data Files: CLDF stores actual linguistic data as a group of *.csv* files that conform to the defined table structure in the *.json* metadata.
Here, we show how the *.csv* formatted IGT is mapped to the three other linguistic data formats: FLEx, ELAN, and CoNLL-U. Each subsection provides some background and considerations for mapping between the two formats, as well as generalizing "what maps to what."
## CLDF to FLEx
* `Source` maps to Source in `<interlinear-text><item>`
* `Primary_Text` maps to Baseline or concatenated `<phrase><item type='txt'>`
* `Analyzed_Text` maps to `<morph><item type='txt'>`
* `Gloss` maps to `<morph><item type='gls'>`
* `Part_of_Speech` maps to Lex. Grammatical Information or `<item type='msa'>`
* `wPOS` maps to Word Category
* `Translated_Text` maps to Free or `<morph><item type='ft'>`
## CLDF to ELAN
* `Source` maps to Source
* `Primary_Text` maps to Utt
* `Analyzed_Text` maps to WORD and MB
* `Gloss` maps to GL
* `Part_of_Speech` maps to POS
* `wPOS` maps to POS
* `Translated_Text` maps to FT
## CLDF to CoNLL-U
* `Source` maps to `# meta::sourceURL`
* `Primary_Text` maps to `# text = `
* `Analyzed_Text` maps to wordlines and FORM
* `Gloss` maps to `Gloss=` in MISC
* `Part_of_Speech` maps to XPOS
* `wPOS` maps to XPOS
* `Translated_Text` maps to `# text[Lang_ISO] = `
# Resources

<sup>1</sup> Foreign Language Explorer (FLEx): [Site](https://software.sil.org/fieldworks/), [GitHub](https://github.com/sillsdev/FieldWorks/tree/main)

<sup>2</sup> ELAN: [Site](https://archive.mpi.nl/tla/elan), "ELAN (Version 7.1) [Computer software]. (2026). Nijmegen: Max Planck Institute for Psycholinguistics, The Language Archive. Retrieved from [https://archive.mpi.nl/tla/elan](https://archive.mpi.nl/tla/elan)"
	
* Sloetjes, H., & Wittenburg, P. (2008). Annotation by category - ELAN and ISO DCR. In: Proceedings of the 6th International Conference on Language Resources and Evaluation (LREC 2008).

<sup>3</sup> CLDF: [Site](https://cldf.clld.org/), [GitHub](https://github.com/cldf/cldf)
* Forkel, R., List, J.-M., Greenhill, S. J., Rzymski, C., Bank, S., Cysouw, M., Hammarström, H., Haspelmath, M., Kaiping, G. A., & Gray, R. D. (2018). Cross-Linguistic Data Formats, advancing data sharing and re-use in comparative linguistics. _Scientific Data_, _5_(1), 180205. [https://doi.org/10.1038/sdata.2018.205](https://doi.org/10.1038/sdata.2018.205)

<sup>4</sup> CoNLL-U: [Site](https://universaldependencies.org/format.html), [GitHub (Universal Dependencies)](https://github.com/UniversalDependencies)

<sup>5</sup> A smaller division of a time-aligned annotation (annotation aligned with a segment of time in an ELAN media file); configured in tier type settings.

<sup>6</sup> [Example ELAN $\rightarrow$ FLEx Workflow](https://05560102713003402641.googlegroups.com/attach/10df1584f1a326/ELAN-Flex-workflow.pdf?part=0.1&vt=ANaJVrGgFmQaF_VBloK9fuMX4xP2YEl675Zi17W865tu7U5eM3E4pIeb3N8hu1_OIWDQmLEwKFGnxCiKJ9SEmw4Gx4_mmCPEl7YhRacgJ5LlKL_SUCNwcME)

<sup>7</sup> [Metadata File](https://github.com/cldf/cldf?tab=readme-ov-file#cldf-metadata-file)