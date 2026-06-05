# CLDF Metadata
The [CLDF Schema](https://github.com/JACKERS228/igt-formatting-splitmasters/tree/main/CLDF%20Metadata) requires mapping of column headers in the dataset file (*.csv*) to existing metadata (defined in `terms.rdf`) in a `cldf-metadata.json` file. This folder was created to separate the metadata from the main files.

## Editing Example
By default, the example JSON file provided by the CLDF schema does not include a `Source` column and therefore must be added to the *.json* manually. To add the column, locate the `columns` list inside `tableSchema` object in the JSON hierarchy and add the following JSON object to the list:
```json
{
	"datatype": "string",
	"propertyUrl": "http://cldf.clld.org/v1.0/terms.rdf#source",
	"required": false,
	"name": "Source"
}
```
* `"datatype"` indicates what type of data is expected in the CSV column.
	* This can be expanded with regex to determine whether a given string is valid input.
* `"propertyUrl"` maps the column to an existing property in the CLDF ontology defined in `terms.rdf`.
* `"required"` is a boolean that indicates whether a column is required to be valid CLDF.
* `"name"` is the name of the column.