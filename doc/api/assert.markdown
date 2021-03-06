# Assert

    Stability: 3 - Locked

The `assert` module provides a simple set of assertion tests that can be used to 
test invariants. The module is intended for internal use by Node.js, but can be 
used in application code via `require('assert')`. However, `assert` is not a 
testing framework, and is not intended to be used as a general purpose assertion 
library.

The API for the `assert` module is [Locked][]. This means that there will be no
additions or changes to any of the methods implemented and exposed by
the module.

## assert(value[, message]), assert.ok(value[, message])

Tests if `value` is truthy. It is equivalent to
`assert.equal(!!value, true, message)`.

If `value` is not truthy, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is `undefined`, a default error message is assigned.

    const assert = require('assert');

    assert(true);  // OK
    assert(1);     // OK
    assert(false);
      // throws "AssertionError: false == true"
    assert(0);
      // throws "AssertionError: 0 == true"
    assert(false, 'it\'s false');
      // throws "AssertionError: it's false"

    assert.ok(true);  // OK
    assert.ok(1);     // OK
    assert.ok(false);
      // throws "AssertionError: false == true"
    assert.ok(0);
      // throws "AssertionError: 0 == true"
    assert.ok(false, 'it\'s false');
      // throws "AssertionError: it's false"

## assert.deepEqual(actual, expected[, message])

Tests for deep equality between the `actual` and `expected` parameters.
Primitive values are compared with the equal comparison operator ( `==` ).

Only enumerable "own" properties are considered. The `deepEqual()`
implementation does not test object prototypes, attached symbols, or
non-enumerable properties. This can lead to some potentially surprising
results. For example, the following example does not throw an `AssertionError`
because the properties on the [`Error`][] object are non-enumerable:

    // WARNING: This does not throw an AssertionError!
    assert.deepEqual(Error('a'), Error('b'));

"Deep" equality means that the enumerable "own" properties of child objects
are evaluated also:

    const assert = require('assert');

    const obj1 = {
      a : {
        b : 1
      }
    };
    const obj2 = {
      a : {
        b : 2
      }
    };
    const obj3 = {
      a : {
        b : 1
      }
    }
    const obj4 = Object.create(obj1);

    assert.deepEqual(obj1, obj1);
      // OK, object is equal to itself

    assert.deepEqual(obj1, obj2);
      // AssertionError: { a: { b: 1 } } deepEqual { a: { b: 2 } }
      // values of b are different

    assert.deepEqual(obj1, obj3);
      // OK, objects are equal

    assert.deepEqual(obj1, obj4);
      // AssertionError: { a: { b: 1 } } deepEqual {}
      // Prototypes are ignored

If the values are not equal, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is undefined, a default error message is assigned.

## assert.deepStrictEqual(actual, expected[, message])

Generally identical to `assert.deepEqual` with the exception that primitive
values are compared using the strict equality operator ( `===` ).

    const assert = require('assert');

    assert.deepEqual({a:1}, {a:'1'});
      // OK, because 1 == '1'

    assert.deepStrictEqual({a:1}, {a:'1'});
      // AssertionError: { a: 1 } deepStrictEqual { a: '1' }
      // because 1 !== '1' using strict equality

If the values are not equal, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is undefined, a default error message is assigned.

## assert.doesNotThrow(block[, error][, message])

Asserts that the function `block` does not throw an error. See
[`assert.throws()`][] for more details.

When `assert.doesNotThrow()` is called, it will immediately call the `block`
function.

If an error is thrown and it is the same type as that specified by the `error`
parameter, then an `AssertionError` is thrown. If the error is of a different
type, or if the `error` parameter is undefined, the error is propagated back
to the caller.

The following, for instance, will throw the [`TypeError`][] because there is no
matching error type in the assertion:

    assert.doesNotThrow(
      function() {
        throw new TypeError('Wrong value');
      },
      SyntaxError
    );

However, the following will result in an `AssertionError` with the message
'Got unwanted exception (TypeError)..':

    assert.doesNotThrow(
      function() {
        throw new TypeError('Wrong value');
      },
      TypeError
    );

If an `AssertionError` is thrown and a value is provided for the `message`
parameter, the value of `message` will be appended to the `AssertionError`
message:

    assert.doesNotThrow(
      function() {
        throw new TypeError('Wrong value');
      },
      TypeError,
      'Whoops'
    );
    // Throws: AssertionError: Got unwanted exception (TypeError). Whoops

## assert.equal(actual, expected[, message])

Tests shallow, coercive equality between the `actual` and `expected` parameters
using the equal comparison operator ( `==` ).

    const assert = require('assert');

    assert.equal(1, 1);
      // OK, 1 == 1
    assert.equal(1, '1');
      // OK, 1 == '1'

    assert.equal(1, 2);
      // AssertionError: 1 == 2
    assert.equal({a: {b: 1}}, {a: {b: 1}});
      //AssertionError: { a: { b: 1 } } == { a: { b: 1 } }

If the values are not equal, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is undefined, a default error message is assigned.

## assert.fail(actual, expected, message, operator)

Throws an `AssertionError`. If `message` is falsy, the error message is set as
the values of `actual` and `expected` separated by the provided `operator`.
Otherwise, the error message is the value of `message`.

    const assert = require('assert');

    assert.fail(1, 2, undefined, '>');
      // AssertionError: 1 > 2

    assert.fail(1, 2, 'whoops', '>');
      // AssertionError: whoops

## assert.ifError(value)

Throws `value` if `value` is truthy. This is useful when testing the `error`
argument in callbacks.

    const assert = require('assert');

    assert.ifError(0); // OK
    assert.ifError(1); // Throws 1
    assert.ifError('error') // Throws 'error'
    assert.ifError(new Error()); // Throws Error

## assert.notDeepEqual(actual, expected[, message])

Tests for any deep inequality. Opposite of [`assert.deepEqual`][].

    const assert = require('assert');

    const obj1 = {
      a : {
        b : 1
      }
    };
    const obj2 = {
      a : {
        b : 2
      }
    };
    const obj3 = {
      a : {
        b : 1
      }
    }
    const obj4 = Object.create(obj1);

    assert.deepEqual(obj1, obj1);
      AssertionError: { a: { b: 1 } } notDeepEqual { a: { b: 1 } }

    assert.deepEqual(obj1, obj2);
      // OK, obj1 and obj2 are not deeply equal

    assert.deepEqual(obj1, obj3);
      // AssertionError: { a: { b: 1 } } notDeepEqual { a: { b: 1 } }

    assert.deepEqual(obj1, obj4);
      // OK, obj1 and obj2 are not deeply equal

If the values are deeply equal, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is undefined, a default error message is assigned.

## assert.notDeepStrictEqual(actual, expected[, message])

Tests for deep strict inequality. Opposite of [`assert.deepStrictEqual`][].

    const assert = require('assert');

    assert.notDeepEqual({a:1}, {a:'1'});
      // AssertionError: { a: 1 } notDeepEqual { a: '1' }

    assert.notDeepStrictEqual({a:1}, {a:'1'});
      // OK

If the values are deeply and strictly equal, an `AssertionError` is thrown
with a `message` property set equal to the value of the `message` parameter. If
the `message` parameter is undefined, a default error message is assigned.

## assert.notEqual(actual, expected[, message])

Tests shallow, coercive inequality with the not equal comparison operator
( `!=` ).

    const assert = require('assert');

    assert.notEqual(1, 2);
      // OK

    assert.notEqual(1, 1);
      // AssertionError: 1 != 1

    assert.notEqual(1, '1');
      // AssertionError: 1 != '1'

If the values are equal, an `AssertionError` is thrown with a `message`
property set equal to the value of the `message` parameter. If the `message`
parameter is undefined, a default error message is assigned.

## assert.notStrictEqual(actual, expected[, message])

Tests strict inequality as determined by the strict not equal operator
( `!==` ).

    const assert = require('assert');

    assert.notStrictEqual(1, 2);
      // OK

    assert.notStrictEqual(1, 1);
      // AssertionError: 1 != 1

    assert.notStrictEqual(1, '1');
      // OK

If the values are strictly equal, an `AssertionError` is thrown with a
`message` property set equal to the value of the `message` parameter. If the
`message` parameter is undefined, a default error message is assigned.

## assert.strictEqual(actual, expected[, message])

Tests strict equality as determined by the strict equality operator ( `===` ).

    const assert = require('assert');

    assert.strictEqual(1, 2);
      // AssertionError: 1 === 2

    assert.strictEqual(1, 1);
      // OK

    assert.strictEqual(1, '1');
      // AssertionError: 1 === '1'

If the values are not strictly equal, an `AssertionError` is thrown with a
`message` property set equal to the value of the `message` parameter. If the
`message` parameter is undefined, a default error message is assigned.

## assert.throws(block[, error][, message])

Expects the function `block` to throw an error. If specified, `error` can be a
constructor, [`RegExp`][], or validation function.

Validate instanceof using constructor:

    assert.throws(
      function() {
        throw new Error('Wrong value');
      },
      Error
    );

Validate error message using [`RegExp`][]:

    assert.throws(
      function() {
        throw new Error('Wrong value');
      },
      /value/
    );

Custom error validation:

    assert.throws(
      function() {
        throw new Error('Wrong value');
      },
      function(err) {
        if ( (err instanceof Error) && /value/.test(err) ) {
          return true;
        }
      },
      'unexpected error'
    );

[Locked]: documentation.html#documentation_stability_index
[`assert.deepEqual`]: #assert_assert_deepequal_actual_expected_message
[`assert.deepStrictEqual`]: #assert_assert_deepstrictequal_actual_expected_message
[`assert.throws()`]: #assert_assert_throws_block_error_message
[`Error`]: errors.html#errors_class_error
[`RegExp`]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions
[`TypeError`]: errors.html#errors_class_typeerror
