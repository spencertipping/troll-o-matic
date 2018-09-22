# Let's find organized Reddit trolls
In particular, coordinated armies of trolls like those managed by the [Internet
Research Agency](https://en.wikipedia.org/wiki/Internet_Research_Agency). Some
Reddit users have compiled lists of suspect domains and behavior like [this
one](https://www.reddit.com/r/Fuckthealtright/comments/9hspmo/the_donald_is_actively_promoting_russian/),
and I'm interested to see if troll accounts show any patterns of coordination or
other measurable indicators.

## What should a troll look like?
Let's start with Russian trolls because they should be easy to identify [just by
the timezone](https://twitter.com/LamarWhiteJr/status/1040138113279045632), and
there's a lot of
[documentation](https://en.wikipedia.org/wiki/Timeline_of_Russian_interference_in_the_2016_United_States_elections)
around exactly how they operate.

I guess I could go through some heuristics, but it's probably worth doing the
thought exercise of organizing a Russian troll organization.

### Internet Research Agency v2
If we were to organize our own IRA, how would we do it? The mission statement is
probably to cause maximal political interference for minimal cost, and being
detected by a few people probably isn't a big deal. The primary objective is to
push the general tone of the conversation, or lend credibility to a set of
viewpoints.

The first question is probably who we'd hire. We need Internet-savvy people who
can hold their own on Reddit (and ideally know their way around), so college
students or similar would be good. Turnover is also ok, and probably inevitable
given that they're millennials.

Owning a troll account isn't free because huge gaps in post history is probably
a bad thing -- so someone would need to post intermittently to maintain it. This
means two things:

1. In absolute terms, the IRA doesn't operate a huge number of troll accounts
2. Any given account will probably be managed by multiple humans

The [IRA page](https://en.wikipedia.org/wiki/Internet_Research_Agency) suggests
that accounts are somewhat consistently assigned to users who work 12-hour
shifts every couple of days, but this seems suboptimal. I'd be surprised if we
found that type of consistency; we can look for it by finding accounts whose
posts match that submission pattern.

There are a few ways we could measure troll performance:

1. Post/comment score
2. General engagement with posted things
3. Engagement from users the troll replies to

**TODO:** more structure around this; what can we observe externally?

## Processing Reddit comments
You can download pretty much all of Reddit by assembling a few different
datasources, all of which ultimately come from
[PushShift](https://pushshift.io/).

- [A fairly complete torrent of
  comments](http://academictorrents.com/details/85a5bd50e4c365f8df70240ffd4ecc7dec59912b)
- [PushShift reddit files](https://files.pushshift.io/reddit/)

As datasets go, these are nearly perfect: the format is consistent, concise, and
self-explanatory. I want to do three things:

1. Split files by hour instead of month
2. Unify the compression to `xz -9e` (we're going to be IO-bound)
3. Convert from JSON to TSV

The monthly files are _almost_ sorted by time:

```sh
$ ni RC_2018-06.xz D:created_utc ,d c
1       1527811200
40      0
1       1
1       -1          # time moves backwards
1       1
48      0
1       1
34      0
1       1
46      0
1       1
1       -1          # same here
1       1
47      0
...
```

These skips are minimal, so I'll coerce timestamps into ascending order rather
than fully sorting everything. This will introduce a few seconds of inaccuracy
for about 1% of the data.

### Extracting JSONs
ni provides the `D` operator to rapidly destructure JSON objects, and I [just
pushed a
change](https://github.com/spencertipping/ni/commit/d072b4232fea4b99e579e9d226a771d15489c503)
to make it compatible with multiline/tab-embedded text.
