Batch LCCN Fixer
===

Fixes incorrect LCCNs in chronam / ONI batches:

    go build
    ./batch-lccn-fixer /path/to/some/batch_xxx_yyyyyyy_ver01/ \
      /path/to/fixed/batch_xxx_yyyyyyy_ver01 \
      12345678 \
      sn12345678

This would fix LCCN "12345678" to be "sn12345678" across all files in the
batch, and put it into the "fixed" path.  The fix does the following:

- Rewrites XML by just replacing *all* occurrences of the bad LCCN with the
  good.  This means if the bad LCCN was something like "1", you should not use
  this tool!  It'll simply match too many things it shouldn't.
- Renames any directory paths that are an exact match of the bad LCCN
- Replaces all PDF EXIF data that matches the bad LCCN.  As is the case with
  the XML, if the bad LCCN is something likely to occur in a lot of places,
  this tool must not be used.

On most failures, the tool will attempt to retry the job.  There are a lot of
careful error checks as this tool needs to be able to correct batches at any
time in the future if we have to reload from our archive (rather than
re-archiving a second batch and hoping we didn't create new problems).

This tool relies on `exiftool` existing on the system on which it's run.  If
that isn't a possibility, the PDFs won't get their EXIF data fixed.  We'll have
to decide if this is acceptable, as there's always a risk that `exiftool` is
simply not an option in the future.  If this happens, a dummy `exiftool` binary
would need to be put in the system path, but not reading or writing to the
files.  Otherwise this application will take a much longer time than it should,
as it will try to process the PDFs 5 times each, and it will log a great number
of false errors.
