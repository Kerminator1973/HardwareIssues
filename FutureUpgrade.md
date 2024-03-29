# Идеи о будущем Upgrade компьютера

В этом документе описываются некоторые идеи потенциального Upgrade для компьютера, а также некоторое количество слухов и домыслов, нуждающихся в проверке, но имеющиех потенциальное влияние на выбор компонентов для Upgrade.

## Intel vs AMD

В соответствии с материалами игровой прессы (в частности с английским изданием "Custom PC"), Intel и AMD выбрали разные стратегии развития своих процессоров (в статье рассмативались Intel Core 12-го поколения и AMD Ryzen 7xxx). Так, Intel фокусируется на достижении больших тактовых частот ядер, как в стандартном, так и в Boost-режимах, а AMD наращивает размер cache-памяти, в частности cache-память третьего уровня (L3), в том числе используя новые подходы в компановке чипа.

Как результат, процессоры Intel, в целом обеспечивают большую производительность на ядро, при выполнении высокоинтенсивных вычислительных операций, а процессоры AMD лучше справляются с видами нагрузки, свойственных играм, которые оказываются очень чувствительными к размеру cache-памяти процессора.

Поскольку ячейки cache-памяти сложнее и выделяют больше тепла, то процессоры AMD, обычно, гораздо более горячие, чем процессоры Intel того же поколения.

## Количество ножек процессора

Причина, по которой растёт количество контактов процессора, в первую очередь связано с количеством ядер процессора. По этой причине, маловероятно, что драматически увеличится количество производительных ядер процессора. Ещё одна причина ограниченного количества производительных ядер - сложность в отводе тепла с поверхности процессора. С высокой вероятностью, рост производительности процессоров не будет столь впечатляющим, как даже в предыдущие, не очень впечатляющие годы.

Вполне вероятно, что **big.LITTLE** будет доминировать в ближайшие годы.

## Применение памяти DDR5

Спецификации DDR5 восхитительны:

- низкое энергопотребление
- высокая тактовая частота
- широкая полоса передачи данных (с удвоением передаваемого объёма)
- в четыре раза больший объём ОЗУ, который может быть размещён на "плашке". На одной плашке DDR4 может быть 16Гб, или 32Гб (двухсторонние модули), что в совокупности даёт 128Гб ОЗУ

Однако, латентность DDR5 высокая и это минимизирует многие преимущества DDR5 по сравнению с DDR4. В синтетических тестах, DDR5 "рвёт" DDR4, но в реальных игровых замерах, разница между DDR4 и DDR5 минимальна. До тех пор, пока ценовая разница памяти является кратной, имеет смысл рассматривать качественную, брендовую память типа DDR4.

## Количество слоёв текстолита

В современных (2023 г) материнских платах чаще всего используются 6, или 8 слотов текстолита. Ориентировочно, стоимость плат с разным количеством слоёв различается в два раза. Чем больше слоёв, тем проще, в частности, изолировать линии памяти друг от друга и тем лучше работает разгон памяти. Это особенно важно для памяти DD5. Эвристическое правило: _если нужно использовать память DD5, выбирайте материнскую плату с 8 слоями текстолита_. Количество слоёв часто выводится на краю материнской плате в виде одиночной цифры.
