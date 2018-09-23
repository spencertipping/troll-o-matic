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
    - Upvotes
    - Downvotes (= upvotes - score)
- Submissions
    - Author
    - Subreddit
    - Date
    - Post ID
    - Domain
    - Upvotes
    - Downvotes

```sh
$ ni reddit-comments r/\\/20/ \< \
     S24[D:author,:subreddit,:created_utc,:link_id,:parent_id,:ups,:score \
         rABC rp'a ne "[deleted]"' p'r a, b, c, d, e, f, f - g'] \
     z4\>author-subreddit-date-comments

$ ni reddit-submissions r/\\/20/ \< \
     S24[D:author,:subreddit,:created_utc,:name,:domain,:ups,:downs \
         rABC rp'a ne "[deleted]"'] \
     z4\>author-subreddit-date-submissions
```
