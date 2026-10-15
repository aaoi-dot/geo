The benefit comes from differences in age between items

The curve only does useful work when items in the pool have meaningfully different ages. The question is how far apart loads need to be compared with the curve’s main transition, which runs from about 2.5 to 9.5 months.

Six-monthly additions would work, but coarsely

With a load every six months, the pool falls into tiers:

Batch	Chance just after a new load	Chance just before the next load
Newest	~97.5%	~50%
Previous	~50%	~2.5%
Older	~2.5%	~0%

So it’s effectively a three-step system that swings a lot. Just after a load, the new batch dominates and the previous one still gets a fair share. Just before the next load, the newest batch has dropped to about 50% and anything older is essentially out.

More frequent additions give a smoother curve

Monthly or more often: items spread smoothly along the curve and the selection gently favours fresher items. This is what the curve is designed for.
Quarterly: reasonable, with about four tiers across the transition.
Six-monthly: usable, but lumpy.
Yearly or a single load: little benefit over random selection.

A rule of thumb for your notes

The gap between loads should be well under the width of the transition, which is about seven months. Monthly or more frequent gets the full benefit. Weekly, which you mentioned earlier, is ideal

One thing to check
The formula as you’ve written it, 1 / (1 + e^(−k·(d − mid))) with k = 0.02, rises with age. For older items to be penalised, one of these must be true:

k is actually −0.02, or
the exponent is e^(+k·(d − mid)), without the minus sign, or
he calculates a penalty with the rising curve and then uses 1 − f(d) as the selection chance.

All three give the same numbers as the table above. It’s worth confirming which one he uses, partly for your notes, and partly because a dropped minus sign would make the curve favour old items instead of penalising them.

Good evening! Yes, that makes sense. It’s a common pattern for “pick a few things at random, but favour newer ones.”

The sigmoid gives each entry a weight based on age. The curve f(x) = 1 / (1 + e^(−k·(x − mid))) is an S-shape that runs smoothly from 0 to 1:

mid is the tipping point: the age where an entry’s weight is exactly 0.5.
k controls how sharp the transition is. A small k gives a gentle slope, so age matters gradually. A large k makes it almost a hard cutoff, where entries are either “fresh” or “old.”

The one thing to check is the direction. As written, if x is age, f gets bigger as entries get older, which would favour old entries. To penalise them, your colleague would need either a negative k or 1 − f(x), which is the same as flipping the sign inside the exponent. They’ve probably handled this, but it’s an easy mistake to make.

Weighted random selection then uses those weights. Instead of every entry having an equal chance, an entry’s chance of being picked is proportional to its weight. A recent entry with weight 0.95 is roughly ten times more likely to be chosen than an old one with 0.09. Old entries aren’t excluded, they’re just less likely.

Why use a sigmoid rather than, say, exponential decay? It has a flat “plateau” for recent entries, so everything under a certain age is treated about equally. Then it drops off around mid. Exponential decay starts penalising from day one. Which is better depends on whether “a bit old” should count against an entry.

A few things worth asking them:

With or without replacement? If they’re picking several items, can the same entry be chosen twice? Most libraries default to one or the other, for example random.choices in Python samples with replacement.
Is there a floor? Very old entries get weights extremely close to 0, so they become effectively impossible to pick. That might be intended, or they might want a minimum weight like 0.05.
Other weights? If anything else also affects selection, the usual approach is to multiply the weights together.

If you can find out what x actually measures and what values of k and mid they’re using, I’m happy to sanity-check the numbers or sketch the curve.

-----

Assuming x is age in days, mid = 366/23 ≈ 15.9 days. That gives a curve that doesn’t really behave like a sigmoid in practice.

With k = 0.02, the “S” is very wide: it takes roughly 100–200 days to go from high to low. Centring that on day 16 means most of the S-shape falls at negative ages, which never occur. For real entries, you only see the tail. Even brand-new entries only get a weight of about 0.58, there’s no plateau for recent items, and weights fall steadily from day one. At 60 days the weight is about 0.29, at 180 days about 0.04, and at a year about 0.001. That last one means a year-old entry is roughly 600 times less likely to be picked than a new one.

That still works as a recency penalty, since weighted random only cares about relative weights. But it behaves much more like plain exponential decay than a sigmoid.

My guess about the numbers: 366/23 is an odd value, and 366/2 = 183, half a year, fits k = 0.02 almost perfectly. The S then runs neatly across a year, with weights near 0.97 for new entries, 0.5 at six months, and about 0.03 at a year old. That could be a typo, or a deliberate choice I can’t see the reason for. Here’s how the two compare:

Selection weight by entry age (k = −0.02)
Weight by Age (days)
mid = 366/23 (≈15.9)
mid = 366/2 (183)

The current setting strongly favours things from the last month or two. The mid = 183 version treats anything under about three months as roughly equally good, then fades out over the second half of the year.

Neither is wrong; it depends on what they want. I’d ask your colleague two things: what unit x is in (days, weeks, something else), and where the 23 comes from. If x turns out to be in weeks or months, the picture changes completely, and I can redo the numbers.

=========

Modeling the growth curve's plateau and scaling behavior.

Ha, that explains it! A stray brace turning “/2}” into “/23” is a very believable typo.

So the intended setup is sensible. Assuming x is in days, entries under about three months old all have weights above 0.85, so they’re treated nearly equally. Half-year-old entries sit at 0.5, and year-old entries drop to about 0.03. A brand-new entry is roughly 35 times more likely to be picked than a year-old one. Older entries aren’t impossible, just rare.

The bigger point is that if the typo is in the actual code, not just in what you passed on to me, their selection is currently far more biased towards recent entries than intended. That’s worth checking.

---------

No, there’s no point where an item becomes unpickable just by getting older. What matters is how old it is compared to everything else. In weighted random selection, an item’s chance is its weight divided by the total weight of all items, so only the ratios matter.

If new data keeps arriving, an old item is competing against fresh entries weighted around 0.97. A year-old item at 0.028 isn’t impossible, but its real chance depends on how many fresh items there are. Against 10 recent entries it still gets picked fairly often. Against 10,000, its chance on any single draw is tiny, roughly 1 in 350,000. So “realistically unpickable” is really about database size and recency mix, not a fixed age.

If no new data is ever added, everything ages together and slides down the curve. Once all items are well past six months, the curve’s tail behaves like simple exponential decay, so the ratio between two items depends only on their age gap. Items 50 days apart differ by about 2.7×, and items 100 days apart by about 7.4×. That stays true whether the items are 1 or 10 years old. Selection probabilities settle and stop changing, and the “oldest” items stay as pickable relative to the “newest” as they ever were. The model just keeps preferring the relatively newer ones.

One technical caveat: in standard 64-bit floating point, e^(0.02·(x − 183)) overflows once an item is about 35,600 days old, roughly 98 years. The weight then becomes exactly 0. If every item gets there, all weights are zero, and most weighted-random functions will throw an error. That’s not a practical worry unless the timestamps are wrong, but a bad date like 1900-01-01 would hit it immediately. A small minimum weight, or capping the age, guards against it.

---------------------------

Not feeble at all, and you’ve spotted something important: in the situation you’re describing, it probably doesn’t help much.

The sigmoid only does anything if entries have different ages. It turns age into a weight, and weighted random then favours higher weights. If most entries are about the same age, they all get about the same weight, and choosing between equal weights is just plain random selection.

Here’s a concrete example. Suppose everything was loaded at once and you run the selection a month later. Every entry is about 30 days old, so every entry has a weight of about 0.955, and every entry has an equal chance. Six months later they’re all about 180 days old with weights of about 0.5. They’re still all equal, so it’s still the same as plain random. The weights move down together, but the odds never change, because the odds only depend on how weights compare with each other.

Even a few days’ difference barely matters. Entries 5 days apart differ in weight by about 10% at most, so the selection is very close to plain random.

The one thing that could change this is what x actually measures. There are two possibilities:

When the row was added to the database. If everything was loaded at once, these are all nearly identical, and the sigmoid is doing almost nothing.
A date inside the data itself, like when an event happened or a transaction was made. Rows loaded on the same day could have dates spread across months or years. Then the sigmoid really is favouring more recent events, which would be the point of it.

So I’d ask your colleague what date the age is calculated from. If it’s the load date, the extra complexity isn’t buying anything, and a simple random pick would do the same job. If it’s a date within the data, the approach makes sense, and the curve we looked at describes how strongly recent events are favoured.
