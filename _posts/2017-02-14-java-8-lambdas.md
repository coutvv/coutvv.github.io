---
layout: post
title:  "Java 8: λambdas"
date:   2017-02-14 00:00:00 +0300
categories: lifelog
preview: "TRAHIT SUA QUEMQUE VOLUPTAS! Скоро Java 9 релизят, а Я ещё к 8ой не притрагивался(на самом деле притрагивался, но до победного понимания так и не дошёл). Особенно с лямбдами вечно хожу по кругу."
picture: /resources/posts/java8lambdas.jpg
---
![]({{ site.url }}{{ page.picture }}){: .right-image }

> TRAHIT SUA QUEMQUE VOLUPTAS

Скоро Java 9 релизят, а Я ещё к 8ой не притрагивался(на самом деле притрагивался, но до победного понимания так и не дошёл). Особенно с лямбдами вечно хожу по кругу.

1). λambdas. Основной проблемой с лямбдами для меня было непонимание того, ЧТО ЗА УЖАС ТУТ ТВОРИТСЯ:

```java
Runnable r = () -> System.out.println("hello");
Thread th = new Thread(r);
th.start();
```

Всё встанет на свои места как только дадим определение функциональному интерфейсу. Это такой интерфейс, который содержит ровно один абстрактный метод. И вот именно его реализация идёт после стрелочки “->”, в нашем случае реализация метода run() интерфейса Runnable выводит просто “hello”. В скобках указываются параметры метода, в нашем случае их нет, но если бы они были, тогда надо указывать только названия — без типов, компилятор сам разберётся что чему там присвоить. Например:

```java
@FunctionalInterface
public interface MyConverter<T, F> {
    F run(T from);
}

...
@Test
public void testLambdaWithFunctionInterface() {
    MyConverter<String, Integer> mfi = (from) -> Integer.valueOf(from);
    Integer i = mfi.run("324");
    assertEquals(i, (Integer)324);
}
...
```

Тут тип параметра from метода run является String. Аннотация “FunctionalInterface” указывает компилятору, что в этом интерфейсе может быть только один абстрактный метод(иначе просто не скомпилируется).

2). Scope и плюхи.

В нормальной java можно использовать окружающие переменные внутри анонимного класса, только если на них есть модификатор final. В Java 8 есть послабления на этот счёт. Модификатор final не обязателен у используемой в лямбдах переменной, но её нельзя трогать(компилятор не даст) и поэтому она по факту final.

Так же, теперь можно использовать diamond-штуку в вызываемых методах:

```java
useHasmmap(new HashMap<>());//в Java 7 надо было указывать типы


private void useHasmmap(Map<String, String> vals) {

}
```

А ещё, вместо лямбд можно кидать ссылки на методы(если сигнатуры одинаковые) посредством “::”, пример:

```java
Consumer c = System.out::println;
c.accept("fuck");//выведет в консоль fuck
```

3). Встроенные функциональные интерфейсы. Полезные в хозяйстве штуки. Приведены в таблице:

---

| Название          | Аргументы | Возвращаемое | Use-case                            |
|-------------------|:-----------:|:--------------:|-------------------------------------|
| Predicate&lt;T&gt;      | T         | boolean      | Объект обладает свойством А ?       |
| Consumer&lt;T&gt;       | T         | void         | Вывод в консоль объекта             |
| Function&lt;T,R&gt;     |    T      |  R           | Мутирование одного объекта в другой |
| Supplier&lt;T&gt;       | void      | T            | Поставщик объектов |
| UnaryOperator&lt;T&gt;  |  T        |  T           | Преобразование объекта |
| BinaryOperator&lt;T&gt; |  (T, T)   | T            | Слияние двух объектов в один |  
  
---
<br>
Пример их использования на [github'e][example-link]

В следующих сериях:

+ Stream API
+ Ассоциативные массивы
+ Новые Date/Time API

[example-link]: https://github.com/coutvv/java-eight-features/tree/master/src/main/java/ru/coutvv/j8f/blog/lambda