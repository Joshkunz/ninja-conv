# ninja-conv

`ninja-conv` is a simple script for generating ninja build files that perform
media conversions. I originally wrote it so that I could have an easy way to
make lossy encodes of my music library.

Since the actual conversions are execute by ninja, we get some nice features for
free:

  * Automatic parallel conversions
  * Partial conversion (only convert changed files)
  * Support for any conversion encodable using a shell command

For example, here's how you might convert a directory of FLAC files to ogg-vorbis.

## Pre-requisites

You'll need a copy of the `ninja` binary in your path somewhere. It can be downloaded
for linux, OSX, or Windows [from here](https://github.com/ninja-build/ninja/releases).
Additionally, you'll need the standard GNU tool `install` in your path somewhere.
It should be included by default in most linux distributions and OSX. Finally,
You need `python3` in your path.

## Converting a library of FLAC music to Ogg Vorbis

First you run `ninja-conv` to generate a ninja build file, and then just run
`ninja` to execute the actual conversion.

```bash
ninja-conv --converter 'flac,ogg,oggenc -b 256 -o $out $in' <source> <dest>
ninja
```

The syntax of the `converter` argument is described later in this README.
This will copy all files in `<source>` to `<dest>` while converting any
`.flac` files to 256kb/s `.ogg` files. Any non-flac files will just be copied
unmodified.

If you add any new files, or modify any files in `<source>` you can just re-run 
__both__ commands and only the new/modified files will updated. Any deleted
files with not be deleted in `<dest>`.

## Specifying converters

Converters are how you specific what conversions you want to perform. They
have the format:

    <source-extension>,<destination-extension>,<conversion cmd>

`<source-extension>` is extension this conversion matches in the `<source>`
directory. We currently don't support extensions with `.`s in them. The extension
should not contain the leading `.`. `<destination-extension>` is the same as the
source extension but defines the type of the output file. Finally the `<conversion cmd>`
is the shell command that performs the conversion. The value `$in` will be
replaced with the name of the file to convert and `$out` will be replaced with the
computed name of the converted file. You shouldn't need to escape either of
these names.

You can specify as many converters as you want.
