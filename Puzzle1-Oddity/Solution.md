[Назад](https://github.com/YaroslavMizgirev/JavaPuzzlers/blob/main/Puzzle1-Oddity/README.md)

An odd number can be defined as an integer that is divisible by 2 with a remainder of 1.
The expression ```i%2``` computes the remainder when i is divided by 2, so it would seem that this program ought to work.
Unfortunately, it doesn’t; it returns the wrong answer one quarter of the time.
Why one quarter?
Because half of all int values are negative, and the ```isOdd``` method fails for all negative odd values.
It returns false when invoked on any negative value, whether even or odd.
This is a consequence of the definition of Java’s remainder operator (%).
It is defined to satisfy the following identity for all int values a and all nonzero int values b:
```(a / b) * b + (a % b) == a```
In other words, if you divide a by b, multiply the result by b, and add the remainder, you are back where you started [JLS 15.17.3].
This identity makes perfect sense, but in combination with Java’s truncating integer division operator [JLS 15.17.2], it implies that when the remainder operation returns a nonzero result, it has the same sign as its left operand.
The isOdd method and the definition of the term odd on which it was based both assume that all remainders are positive.
Although this assumption makes sense for some kinds of division [Boute92], Java’s remainder operation is perfectly matched to its integer division operation, which discards the fractional part of its result.
When ```i``` is a negative odd number, ```i%2``` is equal to ```-1``` rather than ```1```, so the ```isOdd``` method incorrectly returns false.
To prevent this sort of surprise, test that your methods behave properly when passed negative, zero, and positive values for each numerical parameter.
The problem is easy to fix. Simply compare ```i%2``` to ```0``` rather than to ```1```, and reverse the sense of the comparison:

```java
public static boolean isOdd(int i) {
    return i % 2 != 0;
}
```

Puzzle 2: Time for a Change 7
If you are using the ```isOdd``` method in a performance-critical setting, you would be better off using the bitwise AND operator ```&``` in place of the remainder operator:

```java
public static boolean isOdd(int i) {
    return (i & 1) != 0;
}
```

The second version may run much faster than the first, depending on what platform and virtual machine you are using, and is unlikely to run slower.
As a general rule, the divide and remainder operations are slow compared to other arithmetic and logical operations.
It’s a bad idea to optimize prematurely, but in this case, the faster version is as clear as the original, so there is no reason to prefer the original.
In summary, think about the signs of the operands and of the result whenever you use the remainder operator.
The behavior of this operator is obvious when its operands are nonnegative, but it isn’t so obvious when one or both operands are negative.
