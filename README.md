swc-bug-repro-1
===============

To reproduce:

```
npx swc index.js -o out.js
```

Then execute index.js and out.js, and notice that you have 

```bash
$ node index.js
{ aaa: 2, bbb: 3 }
```

```
$ node out.js
{ aaa: 'bbb', bbb: 3 }
```

If swc were working correctly, then we would expect to get the same result.
However, we get different results because `out.js` ends up conflating 2
variables.

For index.js, we have:

```
'use strict';

function print() {
  var value$4 = 2;
  var value$41 = value$4 + 100;
  var c = 1;
  if (true) {
    var value$42 = "bbb";
    if (value$42 === "bbb") c = 3;
  }

  [0, 1].map(function (value) {
    var value$4 = 2;
    var x = value ? value$4 : "";
    return x;
  });
  return {aaa: value$4, bbb: c};
}

console.log(print());
```

For out.js, we have:

```
'use strict';
function print() {
    var value$42 = 2;
    var value$41 = value$42 + 100;
    var c = 1;
    if (true) {
        var value$42 = "bbb";
        if (value$42 === "bbb") c = 3;
    }
    [
        0,
        1
    ].map(function(value) {
        var value$4 = 2;
        var x = value ? value$4 : "";
        return x;
    });
    return {
        aaa: value$42,
        bbb: c
    };
}
console.log(print());


//# sourceMappingURL=out.js.map
```

Notice how `value$4` and `value$42` from index.js are combined into one var in
out.js named `value$42`, instead of being kept as separate variables.
