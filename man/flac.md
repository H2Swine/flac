% flac(1) Version 1.5.0 | Free Lossless Audio Codec conversion tool

# NAME

flac - Free Lossless Audio Codec

# SYNOPSIS

**flac** \[ *OPTIONS* \] \[ *infile.wav* \| *infile.rf64* \|
*infile.aiff* \| *infile.raw* \| *infile.flac* \| *infile.oga* \|
*infile.ogg* \| **-** *...* \]

**flac** \[ **-d** \| **\--decode** \| **-t** \| **\--test** \| **-a** \|
**\--analyze** \] \[ *OPTIONS* \] \[ *infile.flac* \| *infile.oga* \|
*infile.ogg* \| **-** *...* \]

# DESCRIPTION

**flac** is a command-line tool for encoding, decoding, testing and
analyzing FLAC streams.

# GENERAL USAGE

**flac** supports as input RIFF WAVE, Wave64, RF64, AIFF, FLAC or Ogg
FLAC format, or raw interleaved samples. The decoder currently can output
to RIFF WAVE, Wave64, RF64, or AIFF format, or raw interleaved samples.
flac only supports linear PCM samples (in other words, no A-LAW, uLAW,
etc.), and the input must be between 4 and 32 bits per sample.

flac assumes that files ending in ".wav" or that have the RIFF WAVE
header present are WAVE files, files ending in ".w64" or have the Wave64
header present are Wave64 files, files ending in ".rf64" or have the
RF64 header present are RF64 files, files ending in ".aif" or ".aiff" or
have the AIFF header present are AIFF files, files ending in ".flac"
or have the FLAC header present are FLAC files and files ending in ".oga"
or ".ogg" or have the Ogg FLAC header present are Ogg FLAC files.

Other than this, flac makes no assumptions about file extensions, though
the convention is that FLAC files have the extension ".flac"
(or ".fla" on ancient "8.3" file systems like FAT-16).

Before going into the full command-line description, a few other things
help to sort it out:

1.	flac encodes by default, so you must use -d to decode
2.	Encoding options -0 .. -8 (or \--fast and \--best) that control the
	compression level actually are just synonyms for different groups of
	specific encoding options (described later).  
3.	The order in which options are specified is generally not important 
	except when they contradict each other, then the latter takes 
	precedence except that compression presets are overridden by any
	option given before or after. For example, -0M, -M0, -M2 and -2M are 
	all the same as -1, and -l 12 -6 the same as -7.
4.	flac behaves similarly to gzip in the way it handles input and output 
	files

Skip to the EXAMPLES section below for examples of some typical tasks.

flac will be invoked one of four ways, depending on whether you are
encoding, decoding, testing, or analyzing. Encoding is the default
invocation, but can be switch to decoding with **-d**, analysis with
**-a** or testing with **-t**. Depending on which way is chosen,
encoding, decoding, analysis or testing options can be used, see section
OPTIONS for details. General options can be used for all.

If only one inputfile is specified, it may be "-" for stdin. When stdin
is used as input, flac will write to stdout. Otherwise flac will perform
the desired operation on each input file to similarly named output files
(meaning for encoding, the extension will be replaced with ".flac", or
appended with ".flac" if the input file has no extension, and for
decoding, the extension will be ".wav" for WAVE output and ".raw" for raw
output). The original file is not deleted unless \--delete-input-file is
specified.

If you are encoding/decoding from stdin to a file, you should use the -o
option like so:

    flac [options] -o outputfile
    flac -d [options] -o outputfile

which are better than:

    flac [options] > outputfile
    flac -d [options] > outputfile

since the former allows flac to seek backwards to write the STREAMINFO or
RIFF WAVE header contents when necessary.

Also, you can force output data to go to stdout using -c.

To encode or decode files that start with a dash, use \-- to signal the
end of options, to keep the filenames themselves from being treated as
options:

    flac -V -- -01-filename.wav

The encoding options affect the compression ratio and encoding speed. The
format options are used to tell flac the arrangement of samples if the
input file (or output file when decoding) is a raw file. If it is a RIFF
WAVE, Wave64, RF64, or AIFF file the format options are not needed since
they are read from the file's header.

In test mode, flac acts just like in decode mode, except no output file
is written. Both decode and test modes detect errors in the stream, but
they also detect when the MD5 signature of the decoded audio does not
match the stored MD5 signature, even when the bitstream is valid.

flac can also re-encode FLAC files. In other words, you can specify a
FLAC or Ogg FLAC file as an input to the encoder and it will decoder it
and re-encode it according to the options you specify. It will also
preserve all the metadata unless you override it with other options (e.g.
specifying new tags, seekpoints, cuesheet, padding, etc.).

flac has been tuned so that the default settings yield a good speed vs.
compression tradeoff for many kinds of input. However, if you are looking
to maximize the compression rate or speed, or want to use the full power
of FLAC's metadata system, see the page titled 'About the FLAC Format' on
the FLAC website.

# EXAMPLES

Some typical encoding and decoding tasks using flac:

## Encoding examples

`flac abc.wav`
:	Encode abc.wav to abc.flac using the default compression setting. abc.wav is not deleted.

`flac --delete-input-file abc.wav`
:	Like above, except abc.wav is deleted if there were no errors.

`flac --delete-input-file -w abc.wav`
:	Like above, except abc.wav is deleted if there were no errors and no warnings.

`flac --best abc.wav` or `flac -8 abc.wav`
:	Encode abc.wav to abc.flac using the highest compression preset. 

`flac --verify abc.wav` or `flac -V abc.wav`
:	Encode abc.wav to abc.flac and internally decode abc.flac to make sure it matches abc.wav.

`flac -o my.flac abc.wav`
:	Encode abc.wav to my.flac.

`flac abc.aiff foo.rf64 bar.w64`
:	Encode abc.aiff to abc.flac, foo.rf64 to foo.flac and bar.w64 to bar.flac

`flac *.wav *.aif?`
:	Wildcards are supported. This command will encode all .wav files and all 
	.aif/.aiff/.aifc files (as well as other supported files ending in 
 	.aif+one character) in the current directory.

`flac abc.flac --force` or `flac abc.flac -f`
:	Recompresses, keeping metadata like tags. The syntax is a little 
	tricky: this is an *encoding* command (which is the default: you need 
	to specify -d for decoded output), and will thus want to output the 
	file abc.flac - which already exists. flac will require the \--force 
	or shortform -f option to overwrite an existing file. Recompression 
	will first write a temporary file, which afterwards replaces the old 
	abc.flac (provided flac has write access to that file).
	The above example uses default settings. More often, recompression is
	combined with a different - usually higher - compression option.
 	Note: If the FLAC file does not end with .flac - say, it is abc.fla
	- the -f is not needed: A new abc.flac will be created and the old 
	kept, just like for an uncompressed input file.

`flac --tag-from-file="ALBUM=albumtitle.txt" -T "ARTIST=Queen" *.wav`
:	Encode every .wav file in the directory and add some tags. Every 
	file will get the same set of tags.
	Warning: Will wipe all existing tags, when the input file is (Ogg) 
	FLAC - not just those tags listed in the option. Use the metaflac 
	utility to tag FLAC files.

`flac --keep-foreign-metadata-if-present abc.wav`
:	FLAC files can store non-audio chunks of input WAVE/AIFF/RF64/W64
	files. The related option \--keep-foreign-metadata works the same
	way, but will instead exit with an error if the input has no such 
	non-audio chunks.
	The encoder only stores the chunks as they are, it cannot import 
	the content into its own tags (vorbis comments). To transfer such
	tags from a source file, use tagging software which supports them.

`flac -Vj2 -m3fo Track07.flac  -- -7.wav`
:	flac employs the commonplace convention that options in a short 
	version - invoked with single dash - can be shortened together until 
	one that takes an argument. Here -j and -o do, and after the "2" a 
	whitespace is needed to start new options with single/double dash. 
	The -m option does not, and the following "3" is the -3 compression
	setting. The options could equally well have been written out as 
	-V -j 2 -m -3 -f -o Track04.flac , or as -fo Track04.flac -3mVj2. 
	flac also employs the convention that `-- ` (with whitespace!) 
	signifies end of options, treating everything to follow as filename.
	That is needed when an input filenames could otherwise be read as an
	option, and "-7" is one such.
	In total, this line takes the input file -7.wav as input; -o will 
	give output filename as Track07.flac, and the -f will overwrite if 
	the file Track04.flac is already present. The encoder will select 
	encoding preset -3 modified with the -m switch, and use two CPU 
	threads. Afterwards, the -V will make it decode the flac file and 
	compare the audio to the input, to ensure they are indeed equal. 


## Decoding examples

`flac --decode abc.flac` or `flac -d abc.flac`
:	Decode abc.flac to abc.wav. abc.flac is not deleted. If abc.wav is
	already present, the process will exit with an error instead of 
	overwriting; use --force / -f to force overwrite.
	NOTE: A mere flac abc.flac *without --decode or its shortform -d*, 
	would mean to re-encode abc.flac to abc.flac (see above), and that 
	command would err out because abc.flac already exists.

`flac -d --force-aiff-format abc.flac` or `flac -d -o abc.aiff abc.flac`
:	Two different ways of decoding abc.flac to abc.aiff (AIFF format).
	abc.flac is not deleted. -d -o could be shortened to -do.
 	The decoder can force other output formats, or different versions 
	of the WAVE/AIFF formats, see the options below.

`flac -d --keep-foreign-metadata-if-present abc.flac`
:	If the FLAC file has non-audio chunks stored from the original
	input file, this option will restore both audio and non-audio. 
	The chunks will reveal the original file type, and the decoder 
	will select output format and output file extension accordingly 
	- note that this is not compatible with forcing a particular 
	output format except if it coincides with the original, as the
	decoder cannot transcode non-audio between formats.
	If there are no such chunks stored, it will decode to abc.wav.
	The related option \--keep-foreign-metadata will instead exit 
	with an error if no such non-audio chunks are found.

`flac -d -F abc.flac`
:	Decode abc.flac to abc.wav and don't abort if errors are found.
	This is potentially useful for recovering as much as possible from 
	a corrupted file.
	Note: Be careful about trying to "repair" files this way. Often it
	will only conceal an error, and not play any subjectively "better"
	than the corrupted file. It is a good idea to at least keep it,
	and possibly try several decoders, including the one that generated 
	the file, and hear if one has less detrimental audible errors than 
	another. Make sure output volume is limited, as corrupted audio can
	generate loud noises.


# OPTIONS

A summary of options follows; see also subsection **Negative options** for 
negating options. The **Format options** subsection includes ways to 
select format upon decoding, and upon encoding from raw or to Ogg FLAC.


## GENERAL OPTIONS

**-v**, **\--version**
:	Show the **flac** version number, and quit.

**-h**, **\--help**
:	Show basic usage and a list of all options, and quit.

**-d**, **\--decode**
:	Decode (the default is to encode, thus to re-encode if the infile
	is FLAC). Will exit with an error if the audio bit stream is not
	valid, including incorrect MD5 checksum. To ignore errors, see -F.

**-t**, **\--test**
:	Test a FLAC / Ogg FLAC encoded file. Works like -d except no
	decoded file is written, though performs some additional checks 
	including metadata validation. 

**-a**, **\--analyze**
:	Analyze a FLAC / Ogg FLAC encoded file. Works like -d except the 
	output is an analysis file (.ana), not a decoded file.

**-c**, **\--stdout**
:	Write output to stdout.

**-f**, **\--force**
:	Force overwriting of output files. The default operation is instead
	to give a warning that the output file already exists, skip it and 
	continue to the next file.

**\--delete-input-file**
:	(Ignored for -a and -t modes.) Automatically delete the input file 
	upon successful encode or decode. If there was an *error* 
	(including a verify error) the input file is left intact.   
	Use -w to retain the input file also when there is a *warning*.

**-o** *FILENAME*, **\--output-name**=*FILENAME*
:	Set output file name (usually **flac** merely changes the extension); 
	and upon decoding, set also output file *type* by file extension. 
	Quote filename as needed. Option can only be used when processing a 
	single file. May not be used in conjunction with \--output-prefix. 

**\--output-prefix**=*STRING*
:	Prefix each output file name with the given string. Quote as needed.
	This can also be useful for outputting to a different directory - if 
	so, make sure the directory exists, and that the *STRING* ends with 
	a directory slash \`/' (not a Windows-style backslash).	

**\--preserve-modtime**
:	(Enabled by default.) Output files have their timestamps/permissions 
	set to match those of their inputs. Use \--no-preserve-modtime to make
	output files have the current time and default permissions.

**\--keep-foreign-metadata**
:	Store/restore non-audio chunks of WAVE, RF64, Wave64 or AIFF files. 
	Input and output must be regular files (not stdin nor stdout). 
	Encoding: store these chunks as an APPLICATION metadata block. 
	(Upon re-encoding, any stored chunks will be retained automatically, 
	no matter whether **\--keep-foreign-metadata** is given or not.)    
	Decoding: restore any saved non-audio chunks to the decoded file,
	where output file type will be set according to metadata. 
	When this option is given, **flac** will exit with error if no such 
	chunks are found (use **\--keep-foreign-metadata-if-present** instead) 
	or if trying to decode to a file type not matching these chunks. 
	NOTE: **flac** cannot transcode foreign metadata; e.g. WAVE chunks can
	not be converted to FLAC tags, nor be restored when decoding to AIFF.

**\--keep-foreign-metadata-if-present**
:	Like \--keep-foreign-metadata, but does not throw any error (although 
	will print a warning) if no foreign metadata can be found or restored. 

**\--skip**={\#\|*MM:SS*}
:	Skip the first number of samples of the input (of each input file if 
	more), or alternatively: skip over the first *MM:SS* minutes and 
	seconds. Then there must be at least one digit on each side of the 
	colon sign. For fractions of a second, use locale-dependent decimal 
	point, e.g. \--skip=123:9,867 if your decimal point is a comma.  
	This option cannot be used with -t. When used with -a, the analysis
	file will enumerate frames starting from the \--skip point.
	
**\--until**={\#\|\[+\|\]*MM:SS*}
:	Stop at (not including) the given sample number. A negative number is 
	taken relative to the end of the audio, a \`+' (plus) sign means that 
	the \--until point is taken relative to the \--skip point.  
	For other considerations, see \--skip. 

**-s**, **\--silent**
:	Do not print runtime encode/decode statistics to console/stderr.

**\--totally-silent**
:	Do not print anything of any kind, including warnings or errors. The
	exit code will be the only way to determine successful completion.

**-w**, **\--warnings-as-errors**
:	Treat all warnings as errors, causing **flac** to terminate with a
	non-zero exit code.
	
**\--** 
:	End of options; everything following `-- ` is input file(s) even if 
	starting with "-". E.g. `flac -d *.flac` fails upon encountering the
	file "-1.flac", but `flac -d \-- *.flac` works.


## DECODING OPTIONS

For output format selection, see the **Format options** subsection.

**-F**, **\--decode-through-errors**
:	Bitstream errors will by default cause the decoder to exit with an
	error message, and remove the partially decoded file. -F overrides,  
	and will continue decoding; error messages are still printed.  
	This option cannot be used with \--decode-chained-stream with Ogg.  
	WARNING: Corrupted blocks will be muted or removed: the decoder will 
	not attempt to "reconstruct" any content of blocks with errors. 
	*Re-encoding* with -F will decode through errors and then encode the 
	decoded audio (with any errors it might have) to an output file that 
	has no information that the source was corrupted, nor how/where.

**\--cue**=\[\#.#\]\[-\[\#.#\]\]
:	Set the beginning and ending cuepoints to decode. Decimal points are
	locale-dependent (dot or comma). The first \#.# is the track and index
	point at which decoding will start; the second \#.# is the track and
	index point at which decoding will end. Both are optional, defaulting 
	to start of stream resp. end of stream. If the cuepoint does not exist, 
	the decoder will select the closest one before (for start point) or 
	after (for end point), or start of stream resp. end of stream if it
	does not exist. Example: A CD track 9 can be cued by \--cue=9.1-10.1 
	even if the CD has no 10th track. 
	This option cannot be used in conjunction with \--skip nor \--until.
	This option cannot be used with -t.

**--decode-chained-stream**
:	Decode all links in a chained Ogg stream, not just the first one.  
	Cannot be used with \--cue, \--skip, \--until, nor with -F.

**\--apply-replaygain-which-is-not-lossless**\[=*SPECIFICATION*\]
:	Alters volume of the output stream, applying ReplayGain tag values.  
	**WARNING: THIS IS NOT LOSSLESS. DECODED AUDIO WILL NOT BE 
	IDENTICAL TO THE ORIGINAL WITH THIS OPTION.** 
	This option might be useful for example in transcoding media servers 
	where the client does not support ReplayGain. For details, see the 
	subsection on **ReplayGain application specification for decoding**.


## ENCODING OPTIONS

Encoding will default to -5 with -A "tukey(5e-1)", and one CPU thread.  
The encoder will by default conform to the stricter and more compatible 
*streamable subset* of the FLAC format (see RFC 9639 section 7), and also 
exit with an error if given options that could cause non-*subset* streams.
These can be permitted by the \--lax option.

**-V**, **\--verify**
:	Verify a correct encoding by decoding the output in parallel and
	comparing the audio bit by bit to the original.

**-0**, **\--compression-level-0**, **\--fast**
:	Fastest compression preset. Currently synonymous with `-l 0 -b 1152 -r 3 --no-mid-side`

**-1**, **\--compression-level-1**
:	Currently synonymous with `-l 0 -b 1152 -M -r 3`, i.e. `-0M` 

**-2**, **\--compression-level-2**
:	Currently synonymous with `-l 0 -b 1152 -m -r 3`, i.e. `-0m`

**-3**, **\--compression-level-3**
:	Currently synonymous with `-l 6 -b 4096 -r 4 --no-mid-side`

**-4**, **\--compression-level-4**
:	Currently synonymous with `-l 8 -b 4096 -M -r 4`

**-5**, **\--compression-level-5**
:	Default. Currently synonymous with `-l 8 -b 4096 -m -r 5`

**-6**, **\--compression-level-6**
:	Currently synonymous with `-l 8 -b 4096 -m -r 6 -A "subdivide_tukey(2)"`

**-7**, **\--compression-level-7**
:	Currently synonymous with `-l 12 -b 4096 -m -r 6 -A "subdivide_tukey(2)"`

**-8**, **\--compression-level-8**, **\--best**
:	Currently synonymous with `-l 12 -b 4096 -m -r 6 -A "subdivide_tukey(3)"`

**-l** \#, **\--max-lpc-order**=\#
:	Sets the maximum LPC order. This number must be \<= 32. 
	For *subset* streams, it must be \<=12 if the sample rate is \<=48kHz. 
	If set to 0, the encoder will not attempt generic linear prediction, and
	choose only among a set of "fixed" predictors hard-coded in the FLAC 
	format. Restricting to only fixed predictors is faster, but compresses 
	weaker (typically five percentage points / ten percent larger files).

**-b** \#, **\--blocksize**=\#
:	Sets blocksize in samples, 16 \<= \# \<= 65535. Current default is
	1152 for -l 0, else 4096. For *subset* streams it must be \<= 4608 
	if the sample rate is \<= 48kHz and \<= 16384 for higher sample rates. 

**-m**, **\--mid-side**
:	Try mid-side coding for each frame in addition to left and right, and 
    select the best compression. (Stereo only, ignored otherwise.)

**-M**, **\--adaptive-mid-side**
:	Like -m, but a faster heuristic choice, compressing slightly weaker.

**-r** \[\#,\]\#, **\--rice-partition-order**=\[\#,\]\#
:	Set the \[min,\]max residual partition order (0..15). For *subset* 
	streams, "max" must be \<=8. "min" defaults to 0. Default is -r 5.
	Actual partitioning will be restricted by block size and prediction 
	order, and the encoder will silently reduce too high values. 

**-A** *FUNCTION(S)*, **\--apodization**=*FUNCTION(S)*
:	Apply apodization *FUNCTION* in the LPC analysis (if more are given: 
	try them all), see subsection **Apodization functions for encoding** 
	for how to use this option. Does nothing if using -l 0.

**-e**, **\--exhaustive-model-search**
:	Do exhaustive model search (expensive!).

**-q** \#, **\--qlp-coeff-precision**=\#
:	Set precision (in bits) of the quantized linear-predictor  
	coefficients, 5\<= \# \<=15 or the default 0 to let encoder decide. 
	Does nothing if using -l 0. The encoder may reduce the actual 
	quantization below the \# number by signal and prediction order.

**-p**, **\--qlp-coeff-precision-search**
:	Do exhaustive search of LP coefficient precision (expensive!).
	Overrides -q; does nothing if using -l 0.

**\--lax**
:	Allow encoding to non-*subset* FLAC files, see RFC 9639 section 7.  
	WARNING: may cause some applications (especially legacy hardware 
	devices) to fail decoding/streaming/playback.

**\--limit-min-bitrate**
:	Ensure that bitrate stays at least 1 bit/sample at any time (e.g. 
	48 kbit/s for 48 kHz). Mainly useful for internet streaming.

**-j** \#, **\--threads**=\#
:	By default, **flac** will encode with one thread. This option enables 
	multithreading with max \# number of threads, although "0" to let the 
	encoder decide. Currently, -j 0 is synonymous with -j 1 (i.e. no
	multithreading), and the max supported number is 64; both could change
	in the future. If \# exceeds the supported maximum (64), **flac** will 
	encode with a single thread (and throw a warning). The same happens 
	(for any \#) if **flac** was compiled with multithreading disabled.  
	NOTE: Exceeding the *actual* available CPU threads will hurt speed.

**\--ignore-chunk-sizes**
:	When encoding from WAVE or AIFF, ignore the file size headers.
	Certain applications write malformed WAVE/AIFF files allowing the 
	audio to extend past the maximum possible size of the format. 
	This option allows those files to be read to the end.  
	WARNING: Use only when needed. Even if those malformed files described
	are often intended to be read past the specified chunk size and until 
	the end, other files may have data following the audio chunk. This 
	option will force the encoder to (mis-) interpret such data as audio.  
	Thus, this option cannot be used with \--keep-foreign-metadata /
	\--keep-foreign-metadata-if-present, nor \--cue, \--cuesheet, \--until. 

**\--replay-gain**
:	Calculate ReplayGain values and store them as FLAC tags, using the 
	original ReplayGain algorithm (similar to vorbisgain; not EBU128).  
	Track gain / track peak will be computed for each input file, and 
	an album gain/peak will be computed over all input files. 
	Only mono and stereo are supported, and all files must share channel 
	count, bits per sample, and sample rate, which also must be among 
	the following: 8, 11.025, 12, 16, 18.9, 22.05, 24, 28, 32, 36, 37.8, 
	44.1, 48, 56, 64, 72, 75.6, 88.2, 96, 112, 128, 144, 151.2, 176.4, 
	192, 224, 256, 288, 302.4, 352.8, 384, 448, 512, 576, or 604.8 kHz.  
	As exact tag size is not known beforehand, a few bytes of PADDING may
	be left even if using \--no-padding.  
	NOTE: This option cannot be used when encoding to stdout nor Ogg FLAC.

**\--cuesheet**=*FILENAME*
:	Import the given cuesheet file and store it in a CUESHEET metadata
	block. This option may only be used when encoding a single file.  
	A seekpoint will be added for each index point in the cuesheet to the
	SEEKTABLE unless overridden by \--no-cued-seekpoints.

**\--picture**={*FILENAME\|SPECIFICATION*}
:	Import a picture and store it in a PICTURE metadata block, one per 
	\--picture option given (keeping existing ones upon re-encoding). 
	A *FILENAME* argument is shorthand for a *SPECIFICATION* with default 
	values applied (type set to front cover, properties to be inferred 
	from the picture file); see subsection **Picture specification**.  
	NOTE: The FLAC format is limited to 16 MiB *total* metadata. Currently
	the **flac** encoder handles up to 64 \--picture options. It is known
	that a very *total* picture count (like a thousand) in a FLAC file
	could cause problems with several applications even when the 16 MiB
	bound is met; if that many pictures is still desired, use **metaflac** 
	not **flac**.

**\--no-utf8-convert**
:	Upon tagging, do *not* convert tags from local charset to UTF-8. This 
	is useful for scripts, and for overriding a wrong locale.  
	NOTE: This option must appear *before* any tag options!

**-T** "*FIELD=VALUE*"**, \--tag**="*FIELD=VALUE*"
:	Add a FLAC tag. The comment must adhere to the Vorbis comment spec;
	i.e. the FIELD must contain only legal characters, terminated by an
	'equals' sign. Make sure to quote the content if necessary. Content
	will be converted to UTF-8 unless \--no-utf8-convert is given *first*.
	Several \--tag options may be given, to add several Vorbis comments.  
 	NOTE: all tags will be added to all encoded files. Upon re-encoding,
	all existing tags will be lost, not only those set with -T / \--tag. 

**\--tag-from-file**="*FIELD=FILENAME*"
:	Like \--tag, except populates the FIELD by the verbatim content of 
	file FILENAME, for example \--tag-from-file="LYRICS=hello.lrc"  
	NOTE: Do not try to store binary data in tag fields! Use PICTURE 
	blocks for pictures and APPLICATION blocks for other binary data.

**-S** {\#\|\#x\|\#s\|X}, **\--seekpoint**={\#\|\#x\|\#s\|X}
:	Sets seekpoint(s), overriding the default choice of one per ten seconds
	('-s 10s'). Several -S options  may be given; the resulting SEEKTABLE 
	will contain all the seekpoints (duplicates removed), max 32768.  
	Seekpoints will be added as follows: \# for one at that sample number, 
	ignored if exceeding the total sample count; \#x for \# evenly spaced 
	seek points, the first at sample 0; \#s for one every \# seconds (with 
	locale-dependent decimal point, e.g. '-s 9.5s' or '-s 9,5s'). X will 
	add a *placeholder point* at the end of the table.  
	NOTE: If the encoder cannot determine the input size before starting,
	'-S \#' results in a placeholder, while \#x and \#s will be ignored.
	Use \--no-seektable for no SEEKTABLE. 

**-P** \#, **\--padding**=\#
:	(Default: 8192, although 65536 for input above 20 minutes. A 4-byte 
    block header will come on top.) Writes a PADDING block of the given 
	length (in bytes) in the metadata section, before the audio. Useful 
	for later tagging, whereupon the PADDING block can be overwritten 
	instead of having to rewrite the entire file.


## FORMAT OPTIONS

Encoding defaults to FLAC, not Ogg FLAC. Decoding defaults to WAVE 
(selecting WAVE\_FORMAT\_PCM for mono/stereo with 8/16 bits, and
WAVE\_FORMAT\_EXTENSIBLE otherwise), except: will be overridden by chunks 
found by \--keep-foreign-metadata-if-present or \--keep-foreign-metadata 
or output filename extension selected by -o. The decoder will exit with 
an error upon format options which conflict with decoded file type.

**\--ogg**
:	When encoding, generate Ogg FLAC output instead of native FLAC. 
	Ogg FLAC streams are FLAC streams wrapped in an Ogg transport layer. 
	The resulting file defaults to '.oga' extension.  
	When decoding, force the input to be treated as Ogg FLAC (necessary
	even for files with '.oga' or '.ogg' extension).

**\--serial-number**=\#
:	When used with \--ogg, assigns the *serial number* to use for the first
	Ogg FLAC stream, which is then incremented for each additional stream. 
	If omitted upon encoding, **flac** starts at some random number, then 
	increments. If not number is given upon decoding, **flac** will use the 
	serial number of the first page.

**\--force-aiff-format**  
**\--force-rf64-format**  
**\--force-wave64-format**
:	Decoding only (encoder auto-detects): Override default output format 
	and force output to AIFF/RF64/WAVE64, respectively. 
	Option is not needed if -o sets a filename ending with *.aif* / *.aiff*
	or with *.rf64* or with *.w64*, respectively.

**\--force-legacy-wave-format**  
**\--force-extensible-wave-format**
:	Decoding only: override default choice of WAVE format version and 
	set to WAVE\_FORMAT\_PCM and WAVE\_FORMAT\_EXTENSIBLE respectively.

**\--force-aiff-c-none-format**  
**\--force-aiff-c-sowt-format**
:	Decoding only: Set output to AIFF-C with format "NONE" resp. "sowt". 
	For compatibility, sowt should likely be restricted to 16-bit signals.

**\--force-raw-format**
:	Force input (when encoding) or output (when decoding) to be treated
	as raw samples, even if filename suggests otherwise; defaults to 
	'*.raw*' output file extension. Raw format options must be provided. 

### raw format options

When encoding from raw PCM, format must be completely specified.  When 
decoding to raw PCM, parameters not known from the .flac file must be 
specified. **flac** will exit with an error upon missing a mandatory 
raw option, and also upon encountering one that should not appear. 

**\--sign**={signed\|unsigned}
:	Specify the sign of samples.

**\--endian**={big\|little}
:	Specify the byte order of samples.

**\--channels**=\#
:	(Input only) specify number of channels. The channels must be 
	interleaved, and in the order of the FLAC format (see the format
	specification); the encoder (/decoder) cannot re-order channels.

**\--bps**={8\|16\|24\|32}
:	(Input only) specify bits per sample (per channel: 16 for CDDA.)

**\--sample-rate**=\#
:	(Input only) specify sample rate (in Hz. Only integers supported.)

**\--input-size**=\#
:	(Input from stdin only) specify the size of the raw input in bytes. 
	This option can only be used when encoding from stdin, and is only 
	needed in conjunction with options that need to know the input size 
	beforehand (like, \--skip, \--until, \--cuesheet ) 
	If specified input size does not match actual size, the encoder 
	will either truncate or give warning about unexpected end-of-file. 


## ANALYSIS OPTIONS

**\--residual-text**
:	Includes the residual signal in the analysis file. This will make the
	file very big, much larger than even the decoded file.

**\--residual-gnuplot**
:	Generates a gnuplot file for every subframe; each file will contain
	the residual distribution of the subframe. This will create a lot of
	files. gnuplot must be installed separately. 


## NEGATIVE OPTIONS

The following will negate an option (a default or one previously given):

**\--no-adaptive-mid-side**  
**\--no-cued-seekpoints**  
**\--no-decode-through-errors**  
**\--no-delete-input-file**  
**\--no-preserve-modtime**  
**\--no-keep-foreign-metadata**  
**\--no-exhaustive-model-search**  
**\--no-force**  
**\--no-lax**  
**\--no-mid-side**  
**\--no-ogg**  
**\--no-padding**  
**\--no-qlp-coeff-prec-search**  
**\--no-replay-gain**  
**\--no-residual-gnuplot**  
**\--no-residual-text**  
**\--no-seektable**  
**\--no-silent**  
**\--no-verify**  
**\--no-warnings-as-errors**


## ADVANCED OPTION SPECIFICATIONS

### ReplayGain application specification for decoding
**WARNING: NOT LOSSLESS. DECODED AUDIO WILL BE IRREVERSIBLY ALTERED.**  
The option \--apply-replaygain-which-is-not-lossless\[=*SPECIFICATION*\]
reads ReplayGain values from tags and applies them to the decoded output 
stream. If required tags are missing, a warning will be printed and no 
alterations will apply.

*SPECIFICATION* is optional; if omitted, it will apply the album gain 
tag value (but if missing, fall back to track gain), hard limit the 
signal at 6 dB below digital full scale, and apply 'low' noise shaping.  
However, if a *SPECIFICATION* is given at all, only the two first of 
these serve as default values.

*SPECIFICATION* takes the form \[*PREAMP*]\[a\|t\]\[l\|L\]\[n{0\|1\|2\|3}\] 
(each optional, but order matters), where:  
- *PREAMP*: Number of dB to add to the existing gain value (default: 0). 
	Decimal point is locale-specific (comma or dot).  
- **a\|t**: Specify 'a' (default) to prefer the album gain tag, or 't' 
	to prefer the track gain tag. Will fallback to the other if preferred 
	is missing.  
- **l\|L**: Specify 'l' to peak-limit the output, so that the 
	ReplayGain peak value is full-scale. Specify 'L' to apply a hard limit
	kicking in at 6 dB below digital full scale. If a *SPECIFICATION* is 
	given but without any 'l'/'L', none will be applied.  
- **n{0\|1\|2\|3}**: Specify the amount of noise shaping. ReplayGain 
	is processed in floating-point. Quantization (with dithering) 
	back to integer adds noise, and noise shaping tries to move the 
	noise where you won't hear it as much. Value 0 means no noise 
	shaping, 1 means 'low', 2 means 'medium', 3 means 'high'. If a 
	*SPECIFICATION* is given but without any 'n', it will default to 0.

The default is 0aLn1. For more examples:  
\--apply-replaygain-which-is-not-lossless=3 means 3 dB preamp, prefer 
album gain, no limiting, no noise shaping. Note how giving a preamp 
value resets the limiting and noise shaping parameters from the "no 
*SPECIFICATION* default".  
\--apply-replaygain-which-is-not-lossless=1tl means 1 dB preamp, prefer 
track gain and limit at peak. No noise shaping; since a *SPECIFICATION* 
is given, it would have to be set explicitly, it does not fall back to 
the "n1". 

### Picture specification
The *SPECIFICATION* for **\--picture** option takes the following form:
\[*TYPE*\]\|\[*MIME-TYPE*\]\|\[*DESCRIPTION*\]\|\[*WIDTH*x*HEIGHT*x*DEPTH*\[/*COLORS*\]\]\|*FILE*  
where all arguments but *FILE* can be left empty. The fields are:  

- *TYPE* (defaults to 3, front cover) is a number from the following list 
(and there may only be one picture each of type 1 and 2 in a file):
0. Other
1. PNG file icon of 32x32 pixels (see RFC 2083)
2. General file icon
3. Front cover
4. Back cover
5. Liner notes page
6. Media label (e.g., CD, Vinyl or Cassette label)
7. Lead artist, lead performer, or soloist
8. Artist or performer
9. Conductor
10. Band or orchestra
11. Composer
12. Lyricist or text writer
13. Recording location
14. During recording
15. During performance
16. Movie or video screen capture
17. A bright colored fish (from ID3v2, use discouraged)
18. Illustration
19. Band or artist logotype
20. Publisher or studio logotype

- *MIME-TYPE* (default: detect from file). Pictures with MIME-type 
image/jpeg or image/png are most compatible. *MIME-TYPE* \--\> means 
that *FILE* is actually URI to an image, though this use is discouraged.  
- *DESCRIPTION* (defaults to empty string): free text.  
- *WIDTH*x*HEIGHT*x*DEPTH*\[/*COLORS*\] (default: attempt to detect from 
image, as typically possible for jpeg/png/gif MIME-types): specify *WIDTH* 
and *HEIGHT* in pixels, and color *DEPTH* in bits-per-pixel. Also, 
optionally (for images with indexed colors) the number of colors used. 
NOTE: The encoder will not try to verify that the information is correct.
- *FILE* is the only mandatory argument. It is either the path to the 
picture file to be imported, or the URI if MIME-type is "\--\>"

#### Specification examples: 
\--picture="\|\|\|\|../cover.jpg". The same as \--picture="../cover.jpg" 
(with *FILENAME* rather than as full specification). The file at 
../cover.jpg wil be embedded, and by default: type 3 (front cover), empty
description. The MIME-type (presumably image/jpeg), the resolution and 
color info will be retrieved from the file itself.  
\--picture="4\|\--\>\|CD\|320x300x24/173\|http://example.com/backcover.tiff" 
will store the given URI literally (the referenced file will not be 
retrieved), with type 4 (back cover), description "CD", and a manually 
specified resolution of 320x300, 24 bits-per-pixel, and 173 colors.

### Apodization functions for encoding
To improve LPC analysis, the audio data is *windowed* (/"apodized"). 
The default ("tukey(5e-1)", cosine-tapering the first and last quarter 
of each subframe) was chosen after testing against a variety of other 
functions, which are still available in the encoder; later, the higher 
presets -6 to -8 got the subdivide_tukey functions, applying similar 
tukey windows to successive subdivisions of each subframe. 

An `-A` option replaces the default by the function(s) specified (quote 
as needed). Several can also be given as comma-/semicolon-delimited list. 
E.g. -A "subdivide_tukey(2)" -A "hann" and -A "subdivide_tukey(2);hann" 
will both try the hann window in addition to the "subdivide_tukey(2)" 
used in presets -6 and -7; a mere -7A "hann" will apply that *instead* of 
subdivide_tukey(2). Multiple functions slow down encoding at diminishing 
compression gains, as the encoder will try another weighting of the data 
and then pick the one that happens to result in best compression. Even 
if subdivide_tukey(*N*) offers a cheaper way to several functions, an *N* 
beyond 4 or 5 may quickly become less efficient than other expensive 
options like the slower -p. Thus the maximum number - currently 32 - is 
beyond what is practical; yet, subdivide_tukey(*N*) counts as one of the 
max 32 no matter what *N*, and can be used for testing slow options.

Currently the following functions are implemented. bartlett, bartlett_hann, 
blackman, blackman_harris_4term_92db, connes, flattop, hamming, hann, 
kaiser_bessel, nuttall, rectangle, triangle, welch do not take any 
parameters; the following require a paramater and may admit optional ones: 
gauss(*STDDEV*), tukey(*P*), partial_tukey(*N*\[/*OV*\[/*P*\]\]), 
punchout_tukey(*N*\[/*OV*\[/*P*\]\]), subdivide_tukey(*N*\[/*P*\]). 
The encoder will silently ignore any misspecified function. 

Parameters *P*, *STDDEV* and *OV* can be given in scientific notation 
like e.g. "tukey(5e-1)", recommended to avoid locale-dependent decimal 
points (having to do tukey(0.5) or tukey(0,5) depending on system).  
- For gauss(*STDDEV*), *STDDEV* is the standard deviation (0\<*STDDEV*\<=5e-1).  
- For tukey(*P*), *P* (between 0 and 1) specifies the fraction of the window 
that is cosine-tapered; *P*=0 corresponds to "rectangle" and *P*=1 to "hann".  
- partial_tukey(*N*) and punchout_tukey(*N*), once used in higher presets, 
are arguably obsoleted by the more efficient subdivide_tukey(*N*), see next 
item. They generate *N* functions, each covering a part of the subframe 
and zero-weighting the rest. Optional arguments are an overlap *OV* (\<1, 
may be negative), for example partial_tukey(2/2e-1); and then like tukey, 
a taper parameter *P*. Example: partial_tukey(2/2e-1/5e-1).  
- subdivide_tukey(*N*), currently used in presets -6 to -8, is a more 
time-efficient re-implementation of partial_tukey and punchout_tukey from 
1 to *N*. Apart from a slightly different tapering (facilitating the 
recycling of computation), "subdivide_tukey(3)" works by employing both a
tukey, a partial_tukey(2) (punchout_tukey(2) is the same, and is skipped), 
a partial_tukey(3) and a punchout_tukey(3). A "subdivide_tukey(5)" will 
include the subdivide_tukey(3), a partial_tukey(4), a punchout_tukey(4), 
a partial_tukey(5) and a punchout_tukey(5). *P* can optionally be given 
as the second parameter (defaults to 5e-1), and the tapering is applied 
at the smallest window employed: E.g. subdivide_tukey(2) (equals 
subdivide_tukey(2/5e-1)!) tapers 25% in total (half of the 5e-1) - and a 
subdivide_tukey(5/7e-1) will at its smallest window taper as much as a 
tukey(14e-2) does. Note that as subdivide_tukey has no overlap parameter, 
*P* is the (optional) *second* parameter specified.


# SEE ALSO

**metaflac(1)**

**flac** and **metaflac** are maintained at https://github.com/xiph/flac  
Format specification: RFC 9639, https://datatracker.ietf.org/doc/rfc9639   


# AUTHOR

This manual page was initially written by Matt Zimmerman
\<mdz@debian.org\> for the Debian GNU/Linux system. It has been 
maintained by the Xiph.org Foundation.
