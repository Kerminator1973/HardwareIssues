# Проблемы с "железом" и как с ними работать

Репозитарий содержит текстовое описание проблем с аппаратным обеспечением (а также с драйверами к нему), с которыми пришлось столкнуться в компьютерным оборудованием в домашних условиях. Проблемы могут иметь достаточно сложную природу, их решение таких проблем имеет собственную ценность, в особенности, когда время стирает с коры головного мозга детали решения.

## Biostar RX6600 8 Gb (компьютер Евгения)

Выявлено несколько проблем:

1. FPS (_frames per second_) падает на некоторых сценах в Destiny 2 до аномально низкого уровня (20-25 FPS)
2. Периодически возникают лаги в ресурсоёмких играх
3. Периодические мерцания на экране настроек персонажа (Destiny 2) с частотой раз в 2-3 секунды
4. В магазине игры Destiny 2 - разовое кратковременное появление зелёной полосы
5. Интерфейс UEFI сильно лагает - пользоваться мышкой можно с сильным дискомфортом. Замечу, что интегрированный GPU отключен, используется дискретная графика

Особенности железа:

1. CPU: Intel Core i5 7500, 4 ядра без hyper-threading. Базовая частота - 3,4 ГГц, максимальная - 3,8 ГГц. Cache-память - 6 Гб
2. Cooler: боксовый, для офисного применения
3. Motherboard: ASUS Strix B250F Gaming. Поддержка PCI Express (PCIe) 3.0. Заметим, что современные видеокарты рассчитаны на работу по шине PCIe 4.0. Firmware не была обновлена до версии от 2021/08/10, из-за статуса Firmware - бета, без ответственности за работоспособность. Ключевая особенность Firmware - поддержка Windows 11
4. Memory: 16 Gb DDR4
5. Постоянная память: Intel Optane 16 Gb + 1 Gb SATA SSD + 1 Gb Hybrid HDD (с SSD Cache). Intel Optane - кэширующий SSD-накопитель, обеспечивающий скорость считывания 1,050 Гб/секунду. Предназначен для ускорения работы системы, оснащённой медленными накопителями

Уровни кэширования в случае, когда видеокарте необходимо загрузить данные с жесткого диска:

1. SSD Cache на гибридном HDD от Seagate
2. SSD Intel Optane (копируемые данные попадают в статистику использования диска)
3. Оперативная память (?)

Анализируемые игры:

1. Elden Ring (сетевой режим)
2. Destiny 2: The Witch Queen (сетевой режим)

## Проблемы в первые дни после установки видео-карты

Сразу после установки карты был установлен [набор драйверов](https://www.amd.com/ru/support) для RX 6600 с Adrenaline. Загрузка осуществлялась в режиме "Full Install" со сбросом до заводских настроек.

Следует обратить внимание, что Adrenaline создают профили работы видеокарты для разных игр, что требует либо удаления "лишних" профилей, либо настройки и их, в дополнение к "глобальным настройкам".

Альтернативой решению Adrenaline может быть установка только драйверов стандартным инсталлятором и использование утилиты [MSI Afterburner](https://ru.msi.com/page/AFTERBURNER), которая позволяет не только разгонять видео-карту, но и настраивать кривую скорости вращения вентиляторов. Вместе с тем, и с этой утилитой есть определённые проблемы. Разработчиком утилиты является Алексей Николайчук - российский программист, которому MSI перестала выплачивать зарплату за развитие проекта (после начала СВО). Как результат - утилита перестала обновляться и её новых версий не выходит уже больше года.

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

1. Мы не собирали информацию о температуре процессора. Возможно, что он перегревается, т.к. cooler - боксовый, рассчитанный на офисную работу
2. Мы не знаем, как Optane влияет на загрузку процессора. Драйвер Optane точно собирает статистику об операциях ввода/вывода и копирует данных с SSD/HDD в свой cache
3. Используется ли swap file и какого размера достигает swap?
4. Есть ли учетка памяти в играх? В случае возникновения лагов следует посмотреть текущий размер ОЗУ. Если есть утечка памяти, то переход на swap будет гарантированно приводить к лагам
5. Дисковая подсистема, однозначно, не самая быстрая. Optane даёт до 1 Гб/секунду их cache на чтение, а SSD - около 500 Мб/секунду. Имеет смысл посмотреть, на какой из носителей установлена игра: HDD, или SSD. Вместе с тем, текстуры должны быть загружены в ОЗУ видео-карты и после их загрузки, нагрузка на систему ввода/вывода должна уменьшится
6. Мы не знаем как греется чип, отвечающий за PCI Express
7. Мы не знаем, как влияет различие версий PCI Express поддерживаемых motherboard (3.0) и видео-картой (4.0). Теоретически, потери производительности должны составлять 5-10%

## Второй этап оптимизации системы

Ключевая идея состояла в том, чтобы заменить Intel Optane на быстрый NVMe накопитель. Т.е. мы попытались разменять скрость загрузки ОС на скорость загрузки текстур и других _assets_ в играх.

Отключение Optane заняло 5-10 минут. Скрость загрузки ОС ощутимо ухудшилась - кэширование, несомненно, даёт положительный эффект.

Кроме установки нового NVMe-накопителя (был выбран недорогой M.2 Netac NV3000 1TB PCIe 3.0 x4 3D NAND), был **обновлен BIOS** на последную официальную версию.

После переноса Destiny 2: The Witch Queen на NVMe-накопитель скорость загрузки выросла в разы: в частности, при переходе по локациям, практически, исчезли сообщения об подгрузке assets, при переходе между зонами локации.

### Возврат Optane во второй M2-слот

При анализе технических спецификаций был найден ещё один слот M.2, который работает по интерфейсу SATA (т.е. относительно медленный), но совместимый с Optane. Рассматривается вариант, в котором Optane будет установлен в SATA M.2 и активирован. Ожидаемый результат - возрастёт скорость загрузки операционной системы, при том, что скорость загрузки в играх будет максимальной.

## Третий этап анализа системы

Анализ автозагрузки позволял установить, что при старте машину очень сильно нагружает Microsoft Teams. После его отключения загрузка системы с гибридного HDD осуществляется за 40-50 секунд и ещё 40-60 секунд догружаются компоненты операционной системы после предоставления доступа к пользовательскому интерфейсу. В этот момент жёсткий диск занят на 100% (Диспетчер задач -> Производительность).

Так же экспериментально установлено, что после обновления драйвера видео-карты от AMD необходимо выключить компьютер, а не перезагрузить операционную систему. Если этого не сделать, то система может вести себя глючно и оценки её работы могут быть сильно искажены.

Поскольку система используется и для обучения (сейчас сессия), и для отдыха, принятие кардинальных мер, связанных с перестановкой операционной системы практически исключено. Из наименее кардинальных мер можно рассматривать две:

- вернуть Optane. Optane особенно хорош при работе с мелкими файлами, но сейчас это направление бизнеса Intel уже продано SK Hynix. **Update**: не помогло, т.к. система не увидела Optane подключенный ко второму M.2
- клонировать операционную систему с HDD на SSD (возможно - на SATA SSD для которой может быть использован второй разъём M.2). В интернет рекомендуют [использовать решения от Acronis](https://remontka.pro/windows-10-to-ssd/#acronis)

Возможно, имеет смысл иметь запасной компьютер (ноутбук) для оперативного решения задач обучения. В этом случае, эксперименты на несколько дней на основном компьютере могут протекать относительно безболезненно.

### Выводы по третьему этапу анализа системы

1. Использования HDD в современных компьютерах для загрузки операционной системы уже неприемлемо
2. Загрузка с HDD увеличивает время загрузки до, как минимум, двух минут
3. После обновления драйвера видео-карт от AMD нужно выключать компьютер
4. Приложения для групповой работы очень агрессивно потребляют вычислительный ресурс. Это может быть как телеметрия (например, Discord может разрешить отключить телеметрию только после общения с администратором компании), так и тёмная магия (Microsoft Teams)

## Перенос операционной системы на SSD

Был выбран наиболее консервативный подход - чистая установка на SSD. Был подготовлен USD-диск с системой восстановления, затем физический HDD был удалён и выполнена загрузка с USB-диска. К сожалению, после загрузки с USB, управляющий код сообщил о том, что восстановить систему не удасться из-за внутренней ошибки. Вероятно, это связано с тем, что "исчез" один из накопителей.

Альтернативное решение - установка с носителя, входившего в коробку с официальной Windows 10. Система загрузилась, но не увидела NVMe-диск. Было принято решение установить систему на SATA SSD. Установилась сборка от 2018 года, которая последовательно устанавливала патчи до актуальной версии, приблизительно в течение шести часов.

Правильной стратегией, которая не была выполнена, было бы создание USB-диска с актуальной сборкой Windows 10. Для этих целей может быть использован Microsoft Media Creation Tools. Такой носитель был мной параллельно подготовлен, но к моменту его формирования установка обновлений Windows уже фактически завершилась.

Проблема с NVMe-диском была связана с тем, что в UEFI было указано, что M.2-диск работает в режиме Intel RST. После переключения диска в режим "не RST" его увидел и UEFI, и операционная система.

Стоит заметить, что актуальные драйвера для AMD RX6600 не установились на Windows 10 от 2018 года - приложению, вероятно, потребовался актуальный набор .NET Runtime. Драйвера успешно установились после установки всех патчей для Windows 10.

Попытка установки Microsoft Office 365 в Online-режиме не была успешной - приложение "OfficeSetup.exe" выдало информацию о внутренней ошибке и отказалось устанавливать ПО. Однако, при использовании установки в Offline-режиме удалось установить Office 365 и применить корпоративную лицензию. Для установки Office 365 был скачан файл "O365HomePremRetail.img", который является ISO-образом диска в Microsoft Office 365. Сделать это удалось через официальный сайт Microsoft, см. раздел загрузок и "Other Options".

Возможно, что установка Office 365 в Online-режиме также может быть успешна при использовании VPN-сервиса. Например, может быть использован HideMy.name, который предоставляет возможность бесплатного тестового использования на одни сутки. Рекомендуется использовать московский сервер Z2.

Как результат - скорость загрузки и отзывчивость системы критически улучшились!

### Последующие этапы решения проблем

Для копирования файлов с HDD-диска, который забыли скопировать перед исключением из системы HDD была использована док-станция для HDD 2.5"/3.5" **Gembird HD32-U3S-4**.

## Проблемы на май 2023 года

Систематически возникает шум вентилятора процессора при выполнении задач с высокой вычислительной нагрузкой. Чаще всего это связано с обновлением операционной системы Windows 10. Ошибка в сборке компьютера состояла в выборе коробочного вентилятора, который входил в состав товара вместе с процессором Intel. Коробочный вентилятор подходит для офисных задач, но приводит к максимальным оборотам и высокому уровню шума при интенсивных задачах, использующих все ядра: компиляция кода, обновление ПО. Фактически, для основных вариантов использования компьютера Евгением, коробочный вентилятор не подходит.

Приблизительно раз в месяц возникает критичный сбой. Анализ сбоя по Event Log показывает, что сбой связан с работой браузера Yandex. Похоже на то, что сбой случается при обновлении брайзера, но однозначные подтверждения этой гипотезы пока не получены.

## Проблемы в июле 2023 года

Систематически, при игре в Team Fortress 2 с минимальными настройками, возникает падение FPS с 300 до 15 кадров/секунду. Следует заметить, что TF2 установлен на NVMe-диск, который не содержит мощного радиатора для отвода тепла.

Гипотезы по поводу причин проблем с TF2:

- перегрев NVMe, с последующим throttling. Можно проверить переносом TF2 на более медленный и менее горячий SATA-SSD
- перегрев процессора, с последующим throttling. Нужны срезы температуры процессора
- ошибки планировщика процессора, связанные с низкой нагрузкой, создаваемой TF2
- сбои в сетевой подсистеме (системетические сбои в сети имеют место быть), влияющие на вычисление FPS
- влияение антивируса ESET, который периодически глючил на машинах в той же домашней сети

Периодически компьютер начинал завывать и эта проблема не всегда решалась выключением и повторным включением компьютера.

Для решения проблемы с завываниями, был куплен новый кулер для процессора PCCooler GI-X5B V2. Попытка установки кулера оказалась не удачной - вентилятор запускался, но система не запускалась. LED-ы на материнской плате не информировали об ошибках, но, например, питание на USB не подавалось. Возможно, проблемы были связаны с прижимным механизмом кулера.

Вместе с тем, удалось установить, что завывания были связаны не с кулером процессора, а с вентилятором на задней стенки корпуса.

В конце концов, старый кулер был снова установлен в систему, а вентилятор с нового кулера был установлен вместо завывающего вентилятора на задней стенке корпуса. В результате, компьютер стал работать значительно тише, завывания исчезли, а температура процессора в _stand-by_ уменьшилась с 44-46 до 36-40 градусов.

Также был установлен радиатор для M2 SSD диска M.2 Netac NV3000 с термопрокладками и массивными аллюминиевыми рёбрами. Температура без нагрузки до установки диска было 54-56 градусов Цельсия, а после установки стала 50-54 градусов. Возможно, причина в плохом прилегании термопрокладки. Для информации: температура SATA SSD без нагрузки изменялась от 36 до 44 градусов Цельсия.

Update 2023, August: после установки радиатора для M2 SSD прекратились сбои в работе Windows - перезагрузка при обновлении Yandex-браузера (см. проблемы мая 2023 года).

## Заметки на будущее

Купить лицензию на Microsoft Windows можно на сервисе https://plati.market/. Для активации потребуется VPN-доступ. В инструкции к активации лицензии будет указано, какую страну следует выбрать и именно эту страну следует указать в качестве точки приземления VPN. Затем следует поменять страну в аккаунте Microsoft, активировать лицензию на Windows и вернуть аккаунт обратно с свою страну.

Update март 2024: стоимость DDR5 снизилась до приемлемого уровня - $60 за 16 Мб. Уже нет значимых причин не использовать эту память. Более того, использование DDR4 может стать блокирующим фактором при покупке современных процессоров и материнских плат.

## Сборка нового компьютера

7 июля 2024 года была выполнена сборка нового компьютера:

- Процессор Core i5 13400
- Кулер для процессора DeepCool GAMMAXX GTE V2
- Материнская плата ASROCK B760 PRO RS (LGA1700, ATX)
- Оперативная память Kingston DDR5 16Gb (2x8Gb) 5600MHz pc-44800 FURY Beast Black (KF556C40BBK2-16)

Серьёзная проблема - не заработал M.2 Netac NV3000. Попытки его использования приводят с критичным сбоям, с необходимостью восстановления операционной системы. При замене NV3000 на "PATRIOT MEMORY Viper M.2 2280 Scorch 512 Гб PCI-E 3.0x4 NVMe NAND 3D TLC (VPN100-512GM28H)", система взлетела и начала работать без сбоев.

На компьютере отца, в котором так же используется чипсет B760, симптомы при попытке использования NV3000 - те же самые, система зависает с критическим сбоем.

Производительность в играх значительно выросла. Так, например, Destiny стал показывать на сложных сценах до 150 FPS, что можно оценивать как рост производительности в 2,5 раза по сравнению с системой на Core i5 7500. Видео-карта начала раскрывать свой потенциал.

Из спасительных неожиданностей стоит отметить тот факт, что операционная система была установлена на SATA-диск, а не NVMe Netac. Благодаря ошибке на прошлой итерации, переустанавливать ОС не пришлось - всё подхватилось само.

Второй SSD Netac NV3000 успешно установился в компьютере папы и заработал "из коробки". Очень похоже, что проблемы связаны с контроллером конкретного экземпляра SSD. По симптомам ситуация выглядела так: в BIOS информация о диске отображается корректно, Windows его видит в менеджере дисков, но его нельзя ни проинициализировать, ни создать partition. При этом, менеджер дисков считает, что SSD имеет размер 2 ТБ, а не 1 ТБ. Возможно, что проблему можно было бы решить, если стереть диск (Erase) фирменной утилитой Netac.

Подробное обсуждение проблемы SSD на [форуме DNS](https://club.dns-shop.ru/blog/t-477-ssd-nakopiteli-m-2/27654-komputer-ne-vidit-ssd-m-2-prichinyi-i-reshenie-problemyi/?utm_referrer=https%3A%2F%2Fwww.google.com%2F).

**Update August 17, 2024**: осуществлена успешная попытка установить "сбойный" NV3000 в машину папы. Диск сразу же корректно распознался и его можно было эксплуатировать. Была выполнен прошивка микрокода утилитой NetacSSD. Далее следует собрать статистику работоспособности "старого" SSD на "новой" машине.

При обсуждении данной проблемы с ChatGPT 4-o Mini, одной из причин проблемы могло быть остаточное статическое напряжение:

- **Static Discharge**: If the SSD was handled without proper anti-static precautions, a static discharge could have affected the drive's internal components, leading to erratic behavior or failure to initialize correctly
- **Residual Charge**: Sometimes, components can retain a small amount of static charge. If the SSD was left disconnected for a period of time, this charge might dissipate, allowing the drive to function normally when reconnected
- **Connection Issues**: Static electricity can sometimes cause poor connections or interference in electronic components. If the SSD was not seated properly in the M.2 slot due to static-related issues, reseating it after some time might have resolved the connection problem

Из вещей, на которые следует обратить внимание в будущем - замена блока питания с 500W на 800-1000W.

### Kernel-Power

3 августа 2024 года компьютер два выходил в нештатный режим: экран гас, клавиатура блокировалась, но при этом ещё несколько секунд продолжал работать Discord. В Event Log появилось сообщение о критичной ошибке "Kernel-Power" с указанием кодов: (70368744177664),(2)

Следует заметить, что в системе установлен блок питания Be!Quite мощностью 500W, что с учётом отдельной видео-карты, буквально, в притык.

Поиск в интернет по коду 70368744177664 приводит на разные обсуждения, например, [ixbt.com](https://forum.ixbt.com/topic.cgi?id=22:80759-6).

Проблема возникает с разными процессорами (Intel, AMD), разными типами устройств (desktop, notebook). Довольно часто помогает установка `Core C6 State` в значение `Disabled` в BIOS. Основные предположения - проблемы с блоком питания, который ведёт себя не адекватно при попытке процессора экономить энергию в простое отправкой команд C6.

Некоторые люди указывают на то, что система сваливается через ~30 минут работы, что также намекает на энергосберегающий режим.

Как вариант - замена блока питания на новый и подключение второго кабеля питания процессора, который в документации указан как опциональный. В системе сейчас используется только один кабель питания процессора.

## Использование Discord для общения с друзьями

В 2024 году РКН заблокировал Discord, что оставило значительную часть фанатов online-игр без адекватного инструмента группового взаимодействия.

Решение проблемы - использовать VPN для подключение к серверам Discord. При этом важно сохранить максимально короткий маршрут для доступа к игровым серверам. Решение предполагает использование второго компьютера. Можно активировать VPN на маломощном ноутбуке и использовать аудио-микшер для объединение звука с ноутбука и звука с основной игровой системы. Микрофон также должен быть подключен к ноутбуку. Решение требует второго компьютера и дополнительных затрат на микшер. Стоимость микшера - около 1500 рублей. Стоимость надёжного и быстрого VPN - около 5000 рублей/год.

Альетрнативное решение - создать виртуальную машину VMware/VirtualBox внутри которой разместить VPN и Discord. Решение не требует второго компьютера и микшера, но будет забирать часть вычислительных ресурсов основного компьютера. Т.е. основная система должна быть достаточно производительной и обладать значимым объёмом памяти (для ориентира - 32 ГБ).
