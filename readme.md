# DateTimePeriod

An implementation of the datetime period type for working with temporal intervals. The library includes all possible relations on intervals. For further information see the "Usage" and "How it works" paragraphs.

## Requirements

PHP 7.1+

## Installation

    $ composer require pwm/datetime-period

## Usage

To create a datetime period:

```php
$start = new DateTimeImmutable('2010-10-10T10:10:10+00:00');
$end = new DateTimeImmutable('2011-11-11T11:11:11+00:00');
$period = new DateTimePeriod($start, $end);

// Start/end instants and their interval
$start = $period->getStart(); // DateTimeImmutable('2010-10-10T10:10:10+00:00')
$end = $period->getEnd(); // DateTimeImmutable('2011-11-11T11:11:11+00:00')
$interval = $period->getInterval(); // DateInterval('P1Y1M1DT1H1M1S')
```

Below is the list of all possible relations between 2 periods:

```php
$a = new DateTimePeriod(new DateTimeImmutable('...'), new DateTimeImmutable('...'));
$b = new DateTimePeriod(new DateTimeImmutable('...'), new DateTimeImmutable('...'));

// |--a--|
//          |--b--|
$a->precedes($b);

// |--a--|
//       |--b--|
$a->meets($b);

// |--a--|
//    |--b--|
$a->overlaps($b);

// |----a----|
//     |--b--|
$a->finishedBy($b);

// |----a----|
//   |--b--|
$a->contains($b);

// |--a--|
// |----b----|
$a->starts($b);

// |--a--|
// |--b--|
$a->equals($b);

//  |----a----|
//  |--b--|
$a->startedBy($b);

//   |--a--|
// |----b----|
$a->during($b);

//     |--a--|
// |----b----|
$a->finishes($b);

//    |--a--|
// |--b--|
$a->overlappedBy($b);

//       |--a--|
// |--b--|
$a->metBy($b);

//          |--a--|
// |--b--|
$a->precededBy($b);
```

Restrictions:

```php
// Throws TimeZoneMismatch exception
new DateTimePeriod(
    new DateTimeImmutable('2017-10-10T10:10:10+02:00'),
    new DateTimeImmutable('2017-10-10T10:10:10-05:00')
);

// Throws NegativeDateTimePeriod exception
new DateTimePeriod(
    new DateTimeImmutable('+1 day'),
    new DateTimeImmutable('-1 day')
);
```

## How it works

In order to be able to talk about periods first let's agree on the following definitions:

#### Definitions

##### 1. Instant
An anchor, ie. discrete point, on the timeline. The most basic temporal type. A "true" time instant is theoretical like a point on a continuous geometrical line. A representation of an instant, however, always has a duration, called a granule. We can thus represent the same instant using various discreet timelines of different granularities. Eg. "2017-10-10" and "2017-10-10 10:10:10" could represent the same instant.

##### 2. Interval
An unanchored, directed portion of the timeline. Unanchored means it has no absolute relation to the timeline. Examples are "2 weeks" or "1 day, 2 hours and 3 minutes". Directed means it is perfectly valid to say "-3 days".

##### 3. Period
An anchored interval on the timeline. There are several possible representations, the most common being a pair of ordered instants of identical granularity. Depending on the representation the interval of a period can be open or closed on both its start and end. A common way is to use a closed-open interval, ie. [start, end), which helps simplifying calculations. Eg. the period ["2017-10-10", "2017-11-11") includes the instant "2017-10-10" but excludes the instant "2017-11-11".

We arrived to the definition of a period. Now on to...

#### Relations

Defining relations on periods is somewhat complex as there is no [total order](https://en.wikipedia.org/wiki/Total_order). In 1983 James F. Allen wrote a paper in which he defined 13 jointly exhaustive and pairwise disjoint binary relations on intervals, meaning that any 2 intervals are related exactly one way. You can see each of the 13 relations above, in the "Usage" section. These relations and the operations on them form what is referred to as [Allen's interval algebra](https://www.ics.uci.edu/~alspaugh/cls/shr/allen.html).

## Tests

	$ vendor/bin/phpunit
	$ vendor/bin/phpstan analyse -l 7 src

## Changelog

[Click here](changelog.md)
