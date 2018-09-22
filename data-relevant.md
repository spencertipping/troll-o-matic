# Relevant users and subreddits
There's a lot of data that we can't use for various reasons:

- Unidentifiable users (we see them as `[deleted]`)
- Users that haven't made enough comments
- Subreddits that don't have enough comments

We actually could deal with the not-enough-comments problem, but we'll end up
throwing a lot of computation at it for diminishing returns.

## First export: author+subreddit+date by year
We'll access this dataset several times, so let's generate it once in LZ4.

```sh
$ mkdir -p author-subreddit-date
$ for y in {2005..2018}; do
    ni reddit-comments r/\\/$y/ \
       e[ xargs -P12 -n128 sh -c '
          xz -dc $* \
          | ni : D:author,:subreddit,:created_utc rABC rp"a ne \"[deleted]\"" \
               z4\>author-subreddit-date/'$y'.`uuid`' -- ] \
    | cat
  done
```
