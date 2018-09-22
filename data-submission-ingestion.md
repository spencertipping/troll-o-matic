# Processing Reddit submissions
We want largely the same format here as we do for
[comments](data-comment-ingestion.md), so let's run a similar first-stage
process. The first obstacle, though, is that the format is a little different.

## Sorting submissions by time
There's not much way around this: submissions are organized into a single 252G
`full_corpus` file for most of reddit's history, then by month after that.

There are fewer submissions than there are comments, so I'm going to export
these per day rather than per hour. I'm also going to skip the JSON re-encoding
step here because it's not running concurrently with `xz`.

```sh
$ ni /mnt/v1/t9/data/reddit-submissions-2015/RS_full_corpus \
     S24p'my ($utc) = /"created_utc":"?(\d+)"?/;
          r $utc, sprintf("%04d.%02d%02d.xz", tep Ymd => $utc), a' \
     ^{row/sort-buffer=32768M} o \
     fBC W\>e[xz -9e]
```
