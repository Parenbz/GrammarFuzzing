# GrammarFuzzing

Фаззинг [OpenSCAD](https://github.com/openscad/openscad) с помощью [Grammar Mutator](https://github.com/AFLplusplus/Grammar-Mutator) для AFL++.

Результат фаззинга для AFL++ без мутаций:
<img src=https://github.com/Parenbz/GrammarFuzzing/blob/main/res/Parallel_without_mutators.png>

Почти все мутации здесь нарушают синтаксис .scad, поэтому фаззится только парсер OpenSCAD (Фаззинг с готовыми мутаторами bit flip, byte flip, ... работает намного медленнее).

Пишем упрощённую грамматику .scad в grammatics/scad.json, по ней с помощью Grammar Mutator получаем custom_mutator/libgrammarmutator-scad.so

Фаззинг только с кастомным мутатором:
<img src=https://github.com/Parenbz/GrammarFuzzing/blob/main/res/Parallel_grammar_mutator_only.png>

Количество найденных путей приблизительно одинаковое, но все мутации проходят парсер без ошибок и поэтому фаззится более интересная часть программы (в том числе, найдены несколько падений приложения)

Фаззинг кастомным мутатором + havoc:
<img src=https://github.com/Parenbz/GrammarFuzzing/blob/main/res/Parallel_grammar_havoc.png>

Grammar Mutator плохо работает на некорректных инпутах, которые генерирует havoc, поэтому скорость поиска путей падает.

Во всех случаях afl-fuzz запускался на 6 процессах
```
afl-fuzz -i aflin -o aflout -s 123 -M master -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 124 -S slave0 -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 125 -S slave1 -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 126 -S slave2 -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 127 -S slave3 -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 128 -S slave4 -- ./openscad -o out.svg @@
afl-fuzz -i aflin -o aflout -s 129 -S slave5 -- ./openscad -o out.svg @@
```

При запуске любой из этих комбинаций мутаторов на одном процессе, скорость значительно падает.
