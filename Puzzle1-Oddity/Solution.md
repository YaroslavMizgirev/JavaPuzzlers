[Назад](https://github.com/YaroslavMizgirev/JavaPuzzlers/blob/main/Puzzle1-Oddity/README.md)

Определение нечетного числа можно дать следующее: это целочисленное число, которое делится на ```2``` с остатком ```1```.
Выражение ```i%2``` вычисляет остаток от деления ```i``` на ```2```, поэтому кажется что наша программа должна работать.

К сожалению, это не так - наша программа возвращает неправильный ответ в количестве ```1/4``` от всего множества целочисленных значений ```i```.

Вы конечно же можете спросить: "Почему именно ```1/4```"?
Потому что половина из всего множества целых чисел - отрицательные, а метод ```isOdd``` возвращает неправильный ответ для всех отрицательных четных и нечетных чисел.
Это следствие определения оператора: остаток от деления (```%```) в Java.
Он определен для удовлетворения следующего тождества для всех значений ```int a``` и всех ненулевых значений ```int b```:
```(a / b) * b + (a % b) == a```

Другими словами, если вы делите ```a``` на ```b```, умножая полученный результат на ```b```, и добавляете остаток от деления ```a``` на ```b```, вы возвращаетесь к тому с чего начали [JLS 15.17.3](https://docs.oracle.com/javase/specs/jls/se17/html/jls-15.html#jls-15.17.3).

This identity makes perfect sense, but in combination with Java’s truncating integer division operator [JLS 15.17.2](https://docs.oracle.com/javase/specs/jls/se17/html/jls-15.html#jls-15.17.2), it implies that when the remainder operation returns a nonzero result, it has the same sign as its left operand.
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
