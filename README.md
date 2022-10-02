# Проблемы с "железом" и как с ними работать

Репозитарий содержит текстовое описание проблем с аппаратным обеспечением (а также с драйверами к нему), с которыми пришлось столкнуться в компьютерным оборудованием в домашних условиях. Проблемы могут иметь достаточно сложную природу, их решение таких проблем имеет собственную ценность, в особенности, когда время стирает с коры головного мозга детали решения.

## Biostar RX6600 8 Gb (компьютер Евгения)

Выявлено несколько проблем:

1. FPS (_frames per second_) падает на некоторых сценах до аномально низкого уровня (20-25 FPS)
2. Периодически возникают лаги в ресурсоёмких играх
3. Периодические мерцания на экране настроек персонажа (Destiny 2) с частотой раз в 2-3 секунды
4. В магазине игры Destiny 2 - разовое кратковременное появление зелёной полосы

Особенности железа:

1. CPU: Intel Core i5 7400, 4 ядра без hyper-threading. Базовая частота - 3 ГГц, максимальная - 3,5 ГГц. Cache-память - 6 Гб
2. Cooler: боксовый, для офисного применения
3. Motherboard: ASUS Strix B250F Gaming. Поддержка PCI Express (PCIe) 3.0. Заметим, что современные видеокарты рассчитаны на работу по шине PCIe 4.0. Firmware не была обновлена до версии от 2021/08/10, из-за статуса Firmware - бета, бе ответственности за работоспособность. Ключевая особенность Firmware - поддержка Windows 11
4. Memory: 16 Gb DDR4
5. Постоянная память: Intel Optane 16 Gb + 1 Gb SATA SSD + 1 Gb Hybrid HDD (с SSD Cache). Intel Optane - кэширующий SSD-накопитель, обеспечивающий скорочть считывания 1,050 Гб/секунду. Предназначен для ускорения работы системы, оснащённой меденными накопителями

Анализируемые игры:

1. Elden Ring (сетевой режим)
2. Destiny 2: The Witch Queen (сетевой режим)

## Проблемы в первые дни после установки видео-карты

Сразу после установки карты был установлен [набор драйверов](https://www.amd.com/ru/support) для RX 6600 с Adrenaline. Загрузка осуществлялась в режиме "Full Install" со сбросом до заводских настроек.

Следует обратить внимание, что Adrenaline создают профили работы видеокарты для разных игр, что требует либо удаления "лишних" профилей, либо настройки и их, в дополнение к "глобальным настройкам".

Альтернативой решению Adrenaline может быть установка только драйверов стандартным инсталлятором и использование утилиты [MSI Afterburner](https://ru.msi.com/page/AFTERBURNER), которая позволяет не только разгонять видео-карту, но и настраивать кривую скорости вращения вентиляторов.

Для первой установке использовались драйвера "Adrenalin 22.9.1 Optional", но потом мы откатились на "Adrenalin 22.5.1 Recommended (WHQL)". WHQL = Windows Hardware Quality Labs, т.е. версия драйверов, прошла сертификацию в лаборатории компании Microsoft и стадильно работают на системе с ОС Windows.

По умолчанию, вентиляторы не активируются до достижения определённой температуры чипов - это следствие наличия настройки "порога тишины", который "по умолчанию" установлен.

Кривая скорости вращения вентиляторами "по умолчанию" настроена в пользу вентиляторов - вращаются они не сильно. Система работает тихо. Корректировка кривой с повышением скорости вращения вентиляторов приводит к лучшему охлаждению чипов. Температура падает на 10-15 градумов. При лучшем охлаждении FPS вырос. Тем не менее, очень странно ожидать _throttling_ на температуре в 60 градусов.

## Типовые метрики

Сразу после загрузки операционной системы занято 6 Гб оперативной памяти.

### Elden Ring

При запуске игры занято 11,2 Гб ОЗУ.

На высоких настройках FPS 45-48. GPU: 52-77%, CPU: 87%

На максимальных настройках FPS 40-43. GPU: 64-88%, CPU: 81-89%

При перехода на высокие настройки температура GPU упала.

### Destiny 2: The Witch Queen

Метрики были получены на карте "Гавань скорби". При запуске игры занято около 10 Гб ОЗУ.

На высоких настройках FPS 50-60. GPU: ~47%, CPU: 85%. Ethernet: 100%

В сложных сценах FPS падает до 20-25. GPU: 70%, CPU: 100%.

При снижении настроек чётко видно, что FPS не поднимается, но загрузка GPU падает до 30-35%.

### Выводы

1. Видео-карта недозагружена. Возможно, что не хватает производительности процессора
2. Странная ситуация с Ethernet. Возможно, следует повысить приоритет игрового трафика для маршрутизатора
3. Вероятно, критичное падение FPS и лаги связаны с достижением загрузки процессора в 100%, либо с перегревом процессора

Вместе с тем, процессор Core i5 7400 считается достаточно производительным, т.к. обладает четырьмя полноценными ядрами. Возможно, проблема связана с дополнительными приложениями, такими как: Steam, Discord, анти-вирус, Яндекс-Музыка. Имеет смысл провести анализ запущенных приложений и оптимизировать подборку работающих приложений.

### Что ещё имеет смысл посмотреть

1. Мы не собирали информацию о температуре процессора. Возможно, что он перегревается
2. Мы не знаем, как Optane влияет на загрузку процессора. Драйвер Optane точно собирает статистику об операциях ввода/вывода и копирует данных с SSD/HDD в свой cache
3. Используется ли swap file и какого размера достигает swap?
4. Есть ли учетка памяти в играх? В случае возникновения лагов следует посмотреть текущий размер ОЗУ. Если есть утечка памяти, то переход на swap будет гарантированно приводить к лагам
5. Дисковая подсистема, однозначно, не самая быстрая. Optane даёт до 1 Гб/секунду их cache на чтение, а SSD - около 500 Мб/секунду. Имеет смысл посмотреть, на какой из носителей установлена игра: HDD, или SSD. Вместе с тем, текстуры должны быть загружены в ОЗУ видео-карты и после из загрузки, нагрузка на систему ввода/вывода должна уменьшится
6. Мы не знаем как греется чип, отвечающий за PCI Express
7. Мы не знаем, как влияет различие версий PCI Express поддерживаемых motherboard (3.0) и видео-картой (4.0). Теоретически, потери производительности должны составлять 5-10%
