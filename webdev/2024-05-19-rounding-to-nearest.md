# Rounding to nearest k

I want to round numbers down to the nearest 1000. My motivating case is to round
timestamps from `performance.now()` to the nearest second, but this note is
about the general problem.

Which should I use?

```js
var k = 1000; // round down to nearest k

function roundWithModulo(n) {
  n = Math.floor(n);
  return n - (n % k);
}

function roundWithDivision(n) {
  return Math.floor(n / k) * k;
}
```

My intuition is thaht `roundWithModulo` should be much faster, by virtue of
avoiding the floating point division. BUT every single answer I saw on
StackOverflow suggested the `roundWithDivision` implementation. So I decided to
test it.

## Benchmarking

```js
var testCeiling = 1_000_000_000;
var trialCount = 101; // odd to make median straightforward

function test() {
  const data = { modTimes: [], divTimes: [] };

  for (let trial = 0; trial < trialCount; trial++) {
    console.log(`running trial ${trial}...`);
    let start = performance.now();
    for (let i = 0; i < testCeiling; i++) {
      roundWithModulo(i);
    }
    let end = performance.now();
    data.modTimes.push(end - start);

    start = performance.now();
    for (let i = 0; i < testCeiling; i++) {
      roundWithDivision(i);
    }
    end = performance.now();
    data.divTimes.push(end - start);
  }

  data.modTimes.sort();
  data.divTimes.sort();

  const modMedian = data.modTimes[Math.ceil(trialCount / 2)];
  const divMedian = data.divTimes[Math.ceil(trialCount / 2)];

  console.log(`round to nearest ${k} with modulo median time: ${modMedian}`);
  console.log(`round to nearest ${k} with division median time: ${divMedian}`);
}
```

Running this benchmark on my machine yields:

```
round to nearest 1000 with modulo median time: 0.30000001192092896
round to nearest 1000 with division median time: 0.699999988079071
```

I conclude that I was right, modulo is much faster than division!

(But, if anyone can show that my benchmarking code is flawed, I'm happy to
accept corrections.)

## With bigger numbers

I tried bumping `testCeiling` to a billion:

```js
const testCeiling = 1_000_000_000;
```

This yielded similar results (division ~2x the running time):

```
round to nearest 1000 with modulo median time: 293.2999999821186
round to nearest 1000 with float operations median time: 619.8000000119209
```

## Other runtimes

All the above may be highly language/platform/runtime-dependent. Here's what I
got in Safari:

```
round to nearest 1000 with modulo median time: 11
round to nearest 1000 with division median time: 12
```

@me1000 reports these numbers on node vs Bun:

```
$ node ./bench.js
round to nearest 1000 with modulo median time: 0.24670898914337158
round to nearest 1000 with division median time: 0.8194580078125
$ bun run ./bench.js
round to nearest 1000 with modulo median time: 0.4555000000000007
round to nearest 1000 with division median time: 0.5449589999999915
```

Languages with distinct int/float types probably behave differently, and other
languages may provide other methods. For example, Python has a built-in `round`
function with a second argument for precision, and you can pass in _negative_
numbers, eg `round(n, -3)` rounds to the nearest thousand.
[Docs here](https://docs.python.org/3/library/functions.html#round).
