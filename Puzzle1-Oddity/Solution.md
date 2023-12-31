[Назад](https://github.com/YaroslavMizgirev/JavaPuzzlers/blob/main/Puzzle1-Oddity/README.md)

Можно дать следующее определение нечетного числа - это целочисленное число, которое делится на ```2``` с остатком ```1```.
Выражение ```i%2``` вычисляет остаток от деления ```i``` на ```2```, поэтому наша программа должна работать.

Но ... к сожалению - это не так. Наша программа возвращает неправильный ответ в ```1/4``` случаев от всего множества целочисленных значений ```i```.

Вы конечно же спросите: "Почему именно ```1/4```"?
Потому что половина из всего множества целых чисел - отрицательные, а метод ```isOdd``` возвращает неправильный ответ для всех отрицательных нечетных чисел.

Это следствие определения оператора: остаток от деления (```%```) в Java.
Он определен для удовлетворения следующего тождества для всех значений ```int a``` и всех ненулевых значений ```int b```:
```(a / b) * b + (a % b) == a```

Другими словами, если вы делите ```a``` на ```b```, умножая полученный результат на ```b```, и добавляете остаток от деления ```a``` на ```b```, вы возвращаетесь к тому с чего начали [JLS 15.17.3](https://docs.oracle.com/javase/specs/jls/se17/html/jls-15.html#jls-15.17.3).

Это тождество имеет смысл, но в сочетании с оператором Java: усеченное целочисленное деление (```/```) [JLS 15.17.2](https://docs.oracle.com/javase/specs/jls/se17/html/jls-15.html#jls-15.17.2), оно означает, что когда операция остатка возвращает ненулевой результат, он
имеет тот же знак что и левый операнд в выражении.

Результат же выполнения метода ```isOdd``` предполагает, что все остатки положительны, на основании определения термина нечетное число.

Хотя это предположение имеет смысл для некоторых видов деления [Boute92](https://dl.acm.org/doi/10.1145/128861.128862?dl=ACM&coll=portal "Boute, Raymond. “The Euclidean definition of the functions div and mod.” In ACM Transactions on Programming Languages and Systems, Vol. 14, No. 2 (April 1992): 127–144."), операция вычисления остатка от деления (```%```) в Java идеально соответствует операции целочисленного деления, которая отбрасывает дробную часть результата.

Когда ```i``` отрицательное нечетное число, операция ```i%2``` возвращает ```-1``` вместо ```1```, а в итоге метод ```isOdd``` возвращает некорректное значение.

Чтобы предотвратить такого рода неожиданности, проверяйте правильность работы ваших методов при передаче отрицательных, нулевых и положительных значений для каждого числового параметра.

С другой стороны эту проблему легко исправить: просто сравнивайте результат операции ```i%2``` с ```0``` вместо ```1```, и заменяйте логическую операцию сравнивания на противоположную:

```java
public static boolean isOdd(int i) {
    return i % 2 != 0;
}
```

Если вы используете метод ```isOdd``` в критичной для производительности настройке, вам предпочтительнее пользоваться побитовым логическим оператором ```AND``` (```&```) вместо оператора остаток от деления (```%```):

```java
public static boolean isOdd(int i) {
    return (i & 1) != 0;
}
```

В этом случае по отношению к целочисленным типам они являются поразрядными операторами. Целочисленное выражение ```i``` переводится из десятичного (```DEC```) в двоичный (```BIN```) формат и поразрядно (побитово) логически складывается с ```1``` также переведенной из десятичного в двоичный формат.

![Побитовое логическое сложение четного и нечетного левого операнда](https://github.com/YaroslavMizgirev/JavaPuzzlers/blob/main/Puzzle1-Oddity/bitwise%20logical%20addition.png)

Вторая версия может работать намного быстрее первой, в зависимости от того, какую платформу и виртуальную машину вы используете, и вряд ли будет работать медленнее.
Как правило, операция деление(```/```) и возвращение остатка от операции деления(```%```) выполняются медленнее, чем другие арифметические и логические операции.
Преждевременная оптимизация — плохая идея, но в этом случае более быстрая версия так же понятна, как и оригинал, поэтому нет причин предпочесть оригинал.

Таким образом, всякий раз, когда вы используете оператор возвращения остатка от операции деления (```%```) вспоминайте о знаках операндов и результата.
Поведение оператора возвращение остатка от деления (```%```) очевидно, когда его операнды неотрицательны, но не так очевидно, когда один или оба операнда отрицательны.
