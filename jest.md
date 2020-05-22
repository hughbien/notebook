# Jest

Notes on [Jest documentation](https://jestjs.io/docs/en/getting-started).

# Getting Started

Install with yard or npm:

```sh
yarn add --dev jest
npm install --save-dev jest
```

Example tests:

```js
// in sum.js
export function sum(a, b) {
  return a + b;
}

// in sum.test.js
import sum from "./sum";
test("adds 1 + 2 to equal 3", () => {
  expect(sum(1, 2)).toBe(3);
});
```

In `package.json`, add:

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

Then `yarn test` or `npm run test` to run tests.

You can run jest directly by installing it first: `yarn global add jest` or
`npm install jest --global`.

# Using Matchers

Common matchers:

```js
toBe(val); // uses Object.is to test exact equality
expect(2 + 2).toBe(4);

toEqual(obj); // tests value equality, recursively
expect(data).toEqual({one: 1, two: 2});
```

Truthiness matchers:

```js
toBeNull(); // matches only null
toBeUndefined(); // matches only undefined
toBeDefined(); // opposite of above
toBeTruthy(); // truthy values
toBeFalsy(); // falsy values

val.not.toBeNull(); // matches can be inverted with .not
```

Number matchers:

```js
toBeGreaterThan(val);
toBeGreaterThanOrEqual(val);
toBeLessThan(val);
toBeLessThanOrEqual(val);
toBeCloseTo(val); // for floating point numbers
// toBe(val) and toEqual(val) are equivalent for numbers
```

String matchers:

```js
toMatch(regex); // match against regex
expect("team").not.toMatch(/I/);
```

Arrays and iterables:

```js
toContain(val); // is value in the iterable?
expect(shoppingList).toContain("beer");
```

Exceptions:

```js
toThrow();
toThrow(Error); // optionally pass error type
toThrow("Error Message");
toThrow(/regex/);
expect(function() {
  // expect takes function
}).toThrow(Error);
```

Promises:

```js
// resolves and rejects
expect(fetchData()).resolves.toBe(val);
expect(fetchData()).rejects.toMatch("error");
```

# Testing Asynchronous Code

Async code needs to be specifically handled with Jest. Tests pass a `done` argument, when called
it will signal to Jest that the test has finished execution. This is useful for callbacks:

```js
test("the data is peanut butter", done => {
  function callback(data) {
    try {
      expect(data).toBe("peanut butter");
      done();
    } catch (error) {
      done(error);
    }
  }
  fetchData(callback);
});
```

If `done` is never called, Jest will error with a timeout.

If a promise is returned from a test, Jest will wait until the promise is resolved. If the promise
is rejected, the test will fail.

```js
test("the data is peanut butter", () => {
  return fetchData().then(data => {
    expect(data).toBe("peanut butter");
  });
});
```

If you expect a promise to be rejected, use the `.catch` method. Also add `expect.assertions` to
verify a certain number of assertions are called. Otherwise a fulfilled promise would not fail
the test:

```js
test("the fetch fails with an error", () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch("error"));
});
```

You can also use `await` and `async` in tests:

```js
test("the data is peanut butter", async () => {
  const data = await fetchData();
  expect(data).toBe("peanut butter");
  // or
  await expect(fetchData()).resolves.toBe("peanut butter");
});
```

# Setup and Teardown

Use `beforeEach` and `afterEach` for setup/teardown per test. Use `beforeAll` and `afterAll` for
one-time setup/teardown. You can group them together via `describe` blocks.

```js
beforeAll(() => {
  initializeCityDatabase();
});

afterAll(() => {
  clearCityDatabase();
});

describe(() => {
  beforeEach(() => {
    scopedInitialize();
  });
});
```

# Mock Functions

Use `jest.fn` to create a mock function.

```js
const mockCallback = jest.fn(x => 42 + x);
forEach([0, 1], mockCallback);

expect(mockCallback.mock.calls.length).toBe(2); // called twice
expect(mockCallback.mock.calls[0][0]).toBe(0); // first arg of first call to be 0
expect(mockCallback.mock.calls[1][0]).toBe(0); // first arg of second call to be 0
expect(mockCallback.mock.results[0]).toBe(42); // return value of first call to be 42
```

The `.mock` property has all the mock data associated with it. You can access all of its instances:

```js
mockCallback.mock.instances
```

You can mock out return values:

```js
// first call is 10, second call is "x", subsequent calls is true
myMock.mockReturnValueOnce(10).mockReturnValueOnce('x').mockReturnValue(true);
```

`jest.mock(...)` lets you mock functions in modules too:

```js
import axios from "axios";

jest.mock("axios");
test("a test", () => {
  axios.get.mockResolvedValue({ data: users });
  // ...
});
```

Mocks have their own matches:

```js
expect(mockFn).toHaveBeenCalled();
expect(mockFn).toHaveBeenCalledWith(arg1, arg2);
expect(mockFn).toHaveBeenLastCalledWith(arg1, arg2);
```
