# d-tips
D is a language, where code often just works. You don't need to read docs thoroughly, rather work with it by pure intuition. But sometimes there is a shorter way, which you'll never guess if you don't know. So there is a humble collection of such places.

>[!NOTE]
>Know something interesting? PRs are welcomed :)

## Attributes can be inferred
Let's imagine, you have a good old top-level function:
```d
void foo() {
  // ...
}
```
It has a type `void()`. But you know that it's `nothrow` and `@nogc`. Instead of typing them by hand, you can use [function attribute interference](https://dlang.org/spec/function.html#function-attribute-inference). The simplest way to trigger it is to make `foo` _auto function_:
```d
auto foo() {
  // ...
}
```
Its type is `pure nothrow @nogc @safe void()` as expected. The other cases are function template, nested function and function literals.

## Lazy and NOT cached
Suppose, you have simple code:
```d
auto arr = [1];
auto i = 0;
auto newArr = arr.map!((a) => i++);
writeln(newArr[0], newArr[0], newArr[0]);
```
You can expect it to output `000`, but the actual output is `012`. Each time you need a result, the function will recalculate it. This isn't a problem, but some functions (e.g., `filter` and `tee`) can trigger recalculation even if they don't need to. So, if you want to avoid recalculation there are two ways: either place `.cache`/`cacheBidirectional` after `map` and potentially lose random access, either convert it to array and store completely in memory.
