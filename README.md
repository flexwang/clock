clock
=====

The `clock` package provides testable replacements for `new DateTime.now()` 
and `new Stopwatch()`.

##Usage

Write some code using the top-level `now` (instead of `new DateTime.now()`) 
and `getStopwatch()` (instead of `new Stopwatch()`).  You may want to import 
with a prefix:

```dart
library time_utils;

import 'package:clock/clock.dart' as clock;

DateTime startOfDay([DateTime time]) {
  if(time == null) time = clock.now;
  return new DateTime(time.year, time.month, time.day);
}

Duration timeAction(action()) {
  var stopwatch = clock.getStopwatch()..start();
  action();
  return stopwatch.elapsed;
}
```

Then test it using `withClock` and `Clock.relative`:

```dart
library time_utils.test;

import 'package:clock/clock.dart' as clock;
import 'package:unittest/unittest.dart';

import 'time_utils.dart'; // What we're testing.

main() {
  group('time utils', () {
  
    clock.Clock fakeClock;
    Duration elapsed;
    setUp(() {
      fakeClock = new clock.Clock.relative(new DateTime(2014, 3, 6, 10, 10, 10), 
          () => elapsed);
      elapsed = Duration.ZERO;
    });
  
    test('startOfDay should default to using now', () {
      clock.withClock(fakeClock, () {
        expect(startOfDay(), new DateTime(2014, 3, 6));
      });
    });

    test('timeAction should time the action', () {
      clock.withClock(fakeClock, () {
        expect(timeAction(() {
          elapsed += const Duration(seconds: 5);
        }), const Duration(seconds: 5));
      });
    });
  });
}
```
