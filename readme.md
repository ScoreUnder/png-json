PNG to JSON tools
=================

png-breakdown
-------------

Pass `png-breakdown` a valid PNG file on stdin and it will output a
JSON list containing the information inside the PNG file. The list
contains objects with the `name`, `data` and `crc` fields. `name`
contains the type of the chunk (exactly 4 characters), `data` contains
the base64-encoded raw content of the chunk, and `crc` contains a
lowercase hexadecimal number representing the CRC32 checksum of that
chunk (as provided in the PNG file).

The `-c` (`--check-crc`) argument can be passed on the command line, in
which case each object will gain two additional fields: `crc_calc`, the
calculated checksum of the chunk, and `crc_ok` (for convenience) which
is true if and only if `crc_calc == crc`.

png-construct
-------------

Pass `png-construct` a JSON list similar to the output of
`png-breakdown` and it will output a PNG file. It will use the `name`,
`data` and `crc` fields in each object, ignoring all others. If `crc` is
present, it shall be inserted into the PNG as the expected CRC for that
chunk. If not, the CRC will be calculated automatically when writing the
PNG.
As with `png-breakdown`, `data` is the base64-encoded data contained by
chunk and `name` is the chunk type.

When inserting new chunks, especially tEXt, ensure that you remember to
base64-encode the data you provide so that the format conforms to what
these tools expect.

But why?
========

Because it's easier than using a hex editor to do the same. PNG
metadata edits can be hacked together with a shell script, and you can
extract arbitrary metadata with `jq` or similar utilities.

If you used a hex editor to add raw chunks, you would also need to deal
with the CRC and length values, while this tool automatically populates
those fields with the correct values.
