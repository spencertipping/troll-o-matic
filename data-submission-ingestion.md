# Processing Reddit submissions
We want largely the same format here as we do for
[comments](data-comment-ingestion.md), so let's run a similar first-stage
process.

```sh
$ ni sources e[ \
    xargs -I{} -P24 \
    ni {} p'^{$size = -1}
            my $newsize = $size + length;
            ($utc) = /"created_utc":"?(\d+)"?/,
            $t = sprintf "%04d.%02d%02d.%02d%02d", tep YmdHM => $utc
              if ($newsize ^ $size) >> 30;
            $size = $newsize;
            r $t,
              "{" . join(",", sort /"[^"]+":(?:[-0-9]+|"(?:[^\\"]+|\\.){0,32766}+")/g)
                  . "}"' \
          W\>z ] \
  | cat
```

Later, to recompress as `xz` offline:

```sh
$ mkdir xz; ls 20* | xargs -P8 -I{} ni {} e[xz -9e] \>xz/{} | cat
```
