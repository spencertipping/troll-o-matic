# Processing Reddit comments
You can download pretty much all of Reddit by assembling a few different
datasources, all of which ultimately come from
[PushShift](https://pushshift.io/).

- [A fairly complete torrent of
  comments](http://academictorrents.com/details/85a5bd50e4c365f8df70240ffd4ecc7dec59912b)
- [PushShift reddit files](https://files.pushshift.io/reddit/)

As datasets go, these are nearly perfect: the format is consistent, concise, and
self-explanatory. I want to do three things:

1. Split files by size (~1GB uncompressed) instead of month
2. Unify the compression to `gzip`
3. Sort JSON keys consistently within each object

(3) opens the door for some interesting optimizations I'd like to research later
on. I could `je(jd(a))` to get there, but that's slower than is ideal; better is
to destructure and restructure the object as a series of strings.

I made a list of input archives in `sources`; ni will shard those out using
`xargs -P`. The main logic here is to start a new file every 1GiB of
uncompressed text. We'll always split down to the month, but we'll split to
finer granularity as reddit acquires more traffic.

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
$ ls 20* | xargs -P24 -I{} ni {} e[xz -9e] \>{}.xz | cat
```
