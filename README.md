# Let's find organized Reddit trolls
In particular, coordinated armies of trolls like those managed by the [Internet
Research Agency](https://en.wikipedia.org/wiki/Internet_Research_Agency). Some
Reddit users have compiled lists of suspect domains and behavior like [this
one](https://www.reddit.com/r/Fuckthealtright/comments/9hspmo/the_donald_is_actively_promoting_russian/),
and I'm interested to see if troll accounts show any patterns of coordination or
other measurable indicators.

## Data processing
- [Comment ingestion and sharding](data-comment-ingestion.md)
- [Submission ingestion and sharding](data-submission-ingestion.md)
- [Relevant users and subreddits](data-relevant.md)
- [TSV extraction](data-tsv.md)

## Troll identification experiments
Let's start with Russian trolls because they should be easy to identify [just by
the timezone](https://twitter.com/LamarWhiteJr/status/1040138113279045632), and
there's a lot of
[documentation](https://en.wikipedia.org/wiki/Timeline_of_Russian_interference_in_the_2016_United_States_elections)
around exactly how they operate.

### IRA-specific heuristics
- [Thought experiment: how the IRA is structured, maybe](ira-structure.md)
