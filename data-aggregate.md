# Aggregate look at users/subreddits
There are two parts to this analysis. First, we want a general look at the state
of reddit at large: user/domain/etc trends, breakdown of up/downvotes by
user+subreddit, etc.

Second, we want to get a sense for which users and subreddits make up the bulk
of reddit as a whole. There's a lot of data that we can't use for various
reasons:

- Unidentifiable users (we see them as `[deleted]`)
- Users that haven't made enough comments to be meaningful
- Subreddits that don't have enough comments to be meaningful

(**NB:** we can actually use `[deleted]` data in some cases: we may be able to
deanonymize users by looking at historical activity.)

This aggregate export should give us enough to identify relevant
users/subreddits for future steps.

## First export: author+subreddit+date by year, with metadata
We'll access this dataset several times, so let's generate it once in LZ4. I'm
doing two exports, one for comments and one for submissions; here are the fields
for each:

- Comments
    - Author
    - Subreddit
    - Date
    - Comment ID
    - Parent ID
    - Score
    - Controversiality
- Submissions
    - Author
    - Subreddit
    - Date
    - Post ID
    - Domain
    - Upvotes
    - Downvotes

```sh
$ mkdir -p agg-comments agg-submissions

$ ni reddit-comments r/\\/20/ F:/fB \
     e[ xargs -P24 -I{} \
        ni reddit-comments/{} \
           D:author,:subreddit,:created_utc,:link_id,:parent_id,:score,:controversiality \
           rABC z4\>agg-comments/{} ] \
  | cat

$ ni reddit-submissions r/\\/20/ F:/fB \
     e[ xargs -P24 -I{} \
        ni reddit-submissions/{} \
           D:author,:subreddit,:created_utc,:name,:domain,:ups,:downs rABC \
           z4\>agg-submissions/{} ] \
  | cat
```

## User/subreddit counts
Now let's count up activity by user and subreddit. I'm keeping comments and
posts separate because disrepancies between the two might tell us something
interesting.

(**NB:** I could use the same query structure for all of these, but there are
few enough subreddits that it's worth bulk-sorting the counts to get some
parallelism.)

```sh
$ ni agg-comments    \<fA Uxz\>agg-usercomments
$ ni agg-submissions \<fA Uxz\>agg-userposts
$ ni agg-submissions \<S12[fB Ux] g,sgA z\>agg-subposts
$ ni agg-comments    \<S12[fB Ux] g,sgA z\>agg-subcomments
```
