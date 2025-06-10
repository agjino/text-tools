Collection of text manupulation utilities.

## Installation

Make the scripts executable:

```bash
chmod +x breaklines
```

Ensure the tools directory is in your system path to run it from anywhere.

## Breaklines

A Python script that processes text files by adding line breaks to any line that
exceeds a maximum character length. The line break is intelligently placed at the last space before
the maximum length is reached, preserving word boundaries. Useful for AI-generated text which often 
overflows the code editor window.

### Usage

```bash
breaklines <filename> [max_chars] [--inplace]
```

#### Parameters

- **filename**: Path to the file to process (required)
- **max_chars**: Maximum number of characters per line (optional, default: 100)
- **inplace**: Whether to create a backup of the original file (optional, if not specified, a backup
will be created)

#### Example

```bash
breaklines myfile.txt 100
```

This will process `myfile.txt` ensuring no line exceeds 100 characters.


## m3u-filter

A Python command-line tool that parses an M3U playlist file, filters its channels based on a
set of inclusive criteria defined in a JSON file, and performs optional post-processing 
actions like de-duplication and sorting.

### Usage

```bash
m3u-filter <source-filename> --specs <specs-filename> <action> [destination-filename]
```

#### Parameters

- **source-filename**: Path to the file to process (required)
- **specs-filename**: Path to the file containing the filtering criteria and actions (required)
- **action**: Can be one of the following:
  - output: write the result to a m3u file
  - count: print the number of results
  - list-values \<attribute\>: print the unique values of attribute \<attribute\> in the final
results. Useful to print out the resulting categories, which can later be added to the specs
file to filter by their name.

#### Specs File

The following specs file fulfills this query:

> Include all channels whose group-title attribute is one of: UK| NEWS, UK| MOVIES,
> UK| DOCUMENTARIES, DE| KIDS, and whose tvg-name attribute does not contain the string 
> "PPV".
> 
> Also include all channels whose name contains the string "music".
> 
> Remove any duplicate channels from the resulting list and sort the list by the tvg-name
> attribute.

```json
{
  "accept": [
    {
      "filters": [
        {
          "attribute": "group-title",
          "operator": "equals",
          "values": [
            "UK| NEWS",
            "UK| MOVIES",
            "UK| DOCUMENTARIES",
            "DE| KIDS"
          ]
        },
        {
          "attribute": "tvg-name",
          "operator": "not-contains",
          "values": [
            "PPV"
          ]
        }
      ]
    }, {
      "filters": [
        {
          "attribute": "name",
          "operator": "contains",
          "values": [
            "music"
          ]
        }
      ]
    }
  ],
  "postProcess": [
    {
      "action": "deduplicate",
      "by": "url"
    },
    {
      "action": "sort",
      "by":  "tvg-name",
      "order": "asc"
    }
  ]
}
```

`accept` is an array of objects whose only attribute is `filter`. A channel that satisfies any
of the `criteria` in the `accept` array will be included in the output. To satisfy a criterion
the channel needs to satisfy all filters of that criterion. A filter is defined by:

1. attribute. This could be any of the key-value attributes in its metadata
or it could be the special attributes `url` and `name`. `name` is the reserved attribute for
the channel name, the last string to appear in a channel's metadata that doesn't have an
associated key.
2. operator. On of: equals, contains, starts-with, not-equals, not-contains, not-starts-with
3. values. A list of values to compare to the attribute. If any value matches the operator
applied to the attribute value (ex. \[UK - KIDS\] \[starts-with\] \[UK -\]), the filter passes.

`postProcess` allows certain actions to be performed on the resulting list, after filtration.
`deduplicate` action removes channels with a URL that already exists in another channel. `sort`
sorts the list of channels by the specified attribute and order.