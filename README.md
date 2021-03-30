# File Info Extension Specification

- **Title:** File Info
- **Identifier:** <https://stac-extensions.github.io/file/v1.0.0/schema.json>
- **Field Name Prefix:** file
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @m-mohr
- **History**: [Prior to March 2nd 2021](https://github.com/radiantearth/stac-spec/commits/4a841605ad83a16f45fcb88ed90117d6c77a7f04/extensions/file)

Provides a way to specify file related details such as checksum, data type and size for assets in
[STAC Items](https://github.com/radiantearth/stac-spec/blob/master/item-spec/item-spec.md) as well as [STAC Collections](https://github.com/radiantearth/stac-spec/blob/master/collection-spec/collection-spec.md) 
that implement collection-level assets.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## *Asset Object* fields

The following fields can be used for assets (in the [`Asset Object`](https://github.com/radiantearth/stac-spec/blob/master/item-spec/item-spec.md#asset-object)).

| Field Name           | Type                                    | Description                                                  |
| -------------------- | --------------------------------------- | ------------------------------------------------------------ |
| file:bits_per_sample | integer                                 | Bits per sample, e.g. 8, 16, 32, ...                         |
| file:byte_order      | string                                  | The byte order of integer values in the file. One of `big-endian` or `little-endian`. |
| file:checksum        | string                                  | Provides a way to specify file [checksums](#checksums) (e.g. BLAKE2, MD5, SHA1, SHA2, SHA3). The hashes are self-identifying hashes as described in the [Multihash specification](https://github.com/multiformats/multihash) and must be encoded as hexadecimal (base 16) string with lowercase letters. |
| file:data_type       | string                                  | The data type of the file. One of the [data types](#data-types) below. |
| file:header_size     | integer                                 | The header [size](#sizes) of the file, specified in bytes.   |
| file:nodata          | \[any]                                  | Value(s) for no-data.                                        |
| file:size            | integer                                 | The file [size](#sizes), specified in bytes.                 |
| file:unit            | string                                  | The unit of measurement for the values in the file, preferably compliant to [UDUNITS-2](https://ncics.org/portfolio/other-resources/udunits2/) units (singular). |
| file:values          | \[[Mapping Object](#mapping-object)\]   | Lists the value that are in the file and describes their meaning. See the [Mapping Object](#mapping-object) chapter for an example. If given, at least one array element is required. |

**Note:** File specific details should not be part of the Item Assets Definition extension to Collections.

### Mapping Object

Value maps are used by assets that are used as classification layers and give details about the values in the asset and their meanings.

| Field Name | Data Type | Description                                                  |
| ---------- | --------- | ------------------------------------------------------------ |
| values     | \[any]    | **REQUIRED.** The value(s) in the file. At least one array element is required. |
| summary    | string    | **REQUIRED.** A short description of the value(s).           |

 For example for a cloud cover mask, `file:values` property could contain the following data:

```json
[
	{"value": [0], "summary": "clear"},
	{"value": [1], "summary": "clouds"},
	{"value": [2], "summary": "cloud shadows"}
]
```

### Sizes

Please be aware that the integer values (always
unsigned) given for the sizes (especially `file:size`)
may exceed the maximum value for the default integer
data type in your environment / programming language.
In this specification `integer` specifies
a integer number without an upper limit. You might
need to use other data types to store the values in.
For example, files with a size larger than around 2,14
GB would exceed the maximum value for int32 and in
JavaScript `BigInt` could be used then.

### Data Types

The data type gives information about the values in the file.
This can be used to indicate the (maximum) range of numerical values expected.
For example `uint8` indicates that the numbers are in a range between 0 and 255,
they can never be smaller or larger. This can help to pick the optimal numerical
data type when reading the files to keep memory consumption low.
Nevertheless, it doesn't necessarily mean that the expected values fill the whole range.
For example, there can be use cases for `uint8` that just use the numbers 0 to 10 for example.
Through other extensions it might be possible to specify an exact value range so
that visualizations can be optimized.
The allowed values for `file:data_type` are:

- `int8`: 8-bit integer
- `int16`: 16-bit integer
- `int32`: 32-bit integer
- `int64`: 64-bit integer
- `uint8`: unsigned 8-bit integer (common for 8-bit RGB PNG's)
- `uint16`: unsigned 16-bit integer
- `uint32`: unsigned 32-bit integer
- `uint64`: unsigned 64-bit integer
- `float16`: 16-bit float
- `float32`: 32-bit float
- `float64`: 64-big float
- `cint16`: 16-bit complex integer
- `cint32`: 32-bit complex integer
- `cfloat32`: 32-bit complex float
- `cfloat64`: 64-bit complex float
- `other`: Other data type than the ones listed above (e.g. boolean, string, higher precision numbers)

### Checksums

`file:checksum` was previously defined in the
[`checksum` extension](https://github.com/radiantearth/stac-spec/tree/v1.0.0-beta.2/extensions/checksum/README.md)
and the field name was `checksum:multihash` before
STAC v1.0.0-beta.3. The specification of the field has
not changed.

Checksum examples for some algorithms supported by
[Multihash](https://github.com/multiformats/multihash)
in `file:checksum`. The examples are given for a text
file with file content `test`.

- Algorithm `sha1` (160 bits): `1114a94a8fe5ccb19ba61c4c0873d391e987982fbbd3`
- Algorithm `sha2` (256 bits): `12209f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08`
- Algorithm `sha2` (256 bits truncated to 160 bits): `12149f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b`
- Algorithm `blake2b-128`: `90e4021044a8995dd50b6657a037a7839304535b`

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```