# Processing Reddit submissions
We want largely the same format here as we do for
[comments](data-comment-ingestion.md), so let's run a similar first-stage
process.

```sh
$ ni sources SX24 {} W\>z \
     p'^{$size = -1}
       my $newsize = $size + length;
       ($utc) = /"created_utc":"?(\d+)"?/,
       $t = sprintf "%04d.%02d%02d.%02d%02d", tep YmdHM => $utc
         if ($newsize ^ $size) >> 30;
       $size = $newsize;
       r $t,
         "{" . join(",", sort /"[^"]+":(?:[-0-9]+|"(?:[^\\"]+|\\.){0,32766}+")/g)
             . "}"' \
  | cat
```

Later, to recompress as `xz` offline:

```sh
$ mkdir xz; ls 20* | xargs -P8 -I{} ni {} e[xz -9e] \>xz/{} | cat

# Verify results before I delete the gzips. sha512sum is much faster than
# sha256sum for some reason.
$ ni . r/\\/20/e[xargs -P24 -I{} ni {} esha512sum] FSfAg \
     rIA[xz r/\\/20/e[xargs -P24 -I{} ni {} esha512sum] FSfA] | wc -l
0                                       # every sha is accounted for
$ mv xz/* . && rmdir xz
```
