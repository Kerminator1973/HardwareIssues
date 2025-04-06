# Проблемы с операционной системой М ОС

М ОС - это форк операционной системы ROSA, изначально основанной на Mandriva. Рабочее окружение - KDE Plasma 5, или 6.

После обновлений компонентов 1 января 2025 г перестал запускаться рабочий стол, при том, что значительная часть апплетов работоспособна. Так, например, нажимая символы на "чётном экране рабочего стола" запускается апплет, который позволяет найти и запустить приложение. В частности, можно запустить:

- Chromium (браузер)
- Dolphine (файловый менеджер)
- KTerminal (shell)

Если разбить Desktop Environment на отдельные составляющие, то работоспособность следующая:

- Window Manager - работоспособен, т.к. приложения запускаются, окна перемещаются, всё отлично
- Panel - не отображается
- Dock - вроде-бы работоспособен, т.к. окна можно размещать произвольным образом
- Desktop Background - не отображается

В KDE Plasma Desktop Environment, который используется в Mandriva/ROSA, используются следующие компоненты, которые могут быть причинами сбоя:

- Plasma Shell: основной интерфейс, который обеспечивает доступ к рабочему столу, панелям и виджетам. Управляет раскладкой и отображением рабочего стола
- KWin: оконный менеджер для KDE Plasma, отвечает за управление расположением и видом окон, включая эффекты и перемещение окон
- KDE Plasma Widgets: небольшие приложения, которые могут быть добавлены на рабочий стол, или в панели управления

Больше всего похоже на то, что на ноутбуке сломался рабочий стол.

Из-за отсутствия администраторских прав, возможностей по изменению операционной системы не много.

Можно выполнить команду обновления списка зависимостей:

```shell
dnf makecache
```

Также можно запустить front-end менеджера пакетов **Dragora**:

```shell
dnfdragora
```

## Попытки решения Проблемы

По всем признакам, проблемы на ноутбуку с М ОС связаны с `plasmashell`.

Команда `plasmashell --replace` привела к выдаче следующей информации:

```output
plasmashell[1432]: Invalid corona package metadata
plasmashell[1432]: Could not set containment property on rootObject
```

В общем случае, эта проблема может быть связана с описание темы, одна из которых находится в папке `/usr/share/plasma/desktoptheme/`. Скорее всего, какие-то из тем содержат ошибки в конфигурации и именно из-за этого появляются ошибки в журнале аудита.

Следует заметить, что часть тем содержит файлы с метаданными "metadata.desktop" (формат данных - ini), а часть - "metadata.json". Я доустанавливал темы (oxygen и air) - их не видно в режиме "Оформление рабочей среды", но они есть в "Оформлении рабочего стола". Удалить темы вручную, или через графический пользовательский интерфейс система не даёт.

Но более серьёзной проблемой выглядит вот это сообщение об ошибке:

```output
plasmashell[1432]: The Wayland connection broke. Did the Wayland compositor die?
```

Почитать логи kwin можно командой:

```shell
journalctl -xe | grep kwin
```

И в этих логах можно найти следующие сообщения:

```output
kwin_wayland_wrapper[1326]: (EE) failed to read Wayland events: Connection reset by peer
kwin_wayland_wrapper[1326]: (EE) failed to read Wayland events: Broken pipe
kwin_wayland[1284]: QtDBus: cannot relay signals from parent QObject(0x1a1d150 "") unless they are emitted in the object's thread QThread(0x18b3ef8 "libinput-connection"). Current thread is QThread(0x18433c0 "").
```

Если просто запустить команду `journalctl -xe` без выделения строк с kwin, то можно увидеть, что journalctl выводит данные только для текущего пользователя, скрывая сообщения от системных процессов. И в логах есть ошибки при работе Qt:

```output
Qt Quick Layouts: Detected recursive rearrange. Aborting after two interations.
```

Запустив утилиту `htop` можно найти процесс plasmashell и удалить его (F9, Enter). Затем можно снова попытаться запустить plasmashell командой:

```shell
plasmashell &
```

Амперсанд в конце команды позволяет дождаться её завершения, а точнее - увидеть сообщения об ошибке.

Логов с ошибками, связанными с plasmashell довольно много:

```shell
journalctl -xe | grep plasmashell
```

Одна из потенциально важных проблем:

```shell
Aborting shell load: The activity manager daemon (kactivitymanagerd) is not running.
```

Попробовал проверить состояние видео-карты:

```shell
lspci -k | grep -EA3 'VGA|3D|Display'
```

Установлен и работает `Intel Corporatin TigerLake-LP GT2 [Iris Xe Graphics]`

plasma-desktop работает, в чём можно убедиться командой:

```shell
ps aux | grep plasma-desktop
```

Посмотреть содержимое журнала можно так:

```shell
journalctl -xe
```

В журнале ничего особенно не нашёл.

Пересоздание конфигурационных файлов не помогло:

```shell
mv ~/.config/plasma-org.kde.plasma.desktop-appletsrc ~/.config/plasma-org.kde.plasma.desktop-appletsrc.bak
mv ~/.config/kwinrc ~/.config/kwinrc.bak
```

Переинсталлировать plasma-desktop система мне не даёт, т.к. мой пользователь не входит в группу sudoer. Команда вот такая:

```shell
sudo dnf reinstall plasma-desktop
```

По той же причине я не могу использовать команду:

```shell
sudo dnf upgrade
```

Утилита ROSA "Индикатор обновлений" (это как бы applet) написана на Python 3: 

```shell
/usr/bin/python3 /usr/bin/update_applet
```

Можно выполнять обновления вручную (из консоли):

```shell
/usr/bin/update_applet.helper check
/usr/bin/update_applet.helper refresh
/usr/bin/update_applet.helper listupd
/usr/bin/update_applet.helper update
```

### Что ещё было испробовано с plasma-desktop

Я попробовал запустить plasma-desktop вручную, командами: `startplasma-x11` и `startkde`. В обоих случаях я получаю сообщение: "_Plasma seems to be already running on this display_". И это похоже на то - при выполнении команды: `ps aux | grep plasma`  я вижу, что запущен и процесс `/usr/bin/startplasma-x11` и `/usr/bin/plasmashell`.

Можно удалить следующий процесс:

```shell
killall plasmashell kwin_x11
```

Это приведёт к тому, что по горячим клавишам нельзя будет запускать приложения. Т.е. этот процесс работает и он нам вполне помогает. Перезапустить процесс можно запустив это приложение ещё раз `/usr/bin/plasmashell`. И мы снова выходим на вероятный корень проблемы:

```output
Invalid corona package metadata
Could not set containment property on rootObject
```

В папке "~/.local/share/sddm/" есть файл "wayland-session.log" в котором есть следующая информация:

```output
Error: could not determine $DISPLAY.
Error: Can not contact kdeinit5!
org.kde.startup: "kdeinit5_shutdown" () exited with code 255
startplasma-wayland: Shutting down...
startplasmacompositor: Shutting down...
startplasmacompositor: Done.
```

Если выполнить команду `echo $DISPLAY`, то в консоль будет выведена строка ":0", т.е. переменная есть и она корректно настроена.

При выполнении команды `ps aux | grep Xorg` получаю информацию о том, что Xorg запущен.

Можно найти путь к исполняемым файлам:

```shell
which kdeinit5
which startplasma-x11
```

Путь к файлам - корректный.

По картинке на стартовой странице можно сделать вывод, что нужно использовать Wayland (буква "М" в квадрате), а не X11 (эко-листочек).

Да, действительно, я загружался в X11, а при переходе на Wayland получил пустой файл "wayland-session.log" (нет проблем) и работающий "startplasma-wayland".

Проверил права доступа к конфигурационным файлам командой: `ls -la ~/.config/plasma*` - все права принадлежат нужному пользователю.

В логах пользовательской сессии, отображение которых осуществляется командой `cat ~/.xsession-errors`, вижу буквальное следующее:

```shell
startkde: Starting up...
startkde: Starting down...
startkde: Done
```

Анализ журнала может быть полезным. Например, можно посмотреть, были ли сбои при загрузке системы (фильтрацию по сообщениям загрузки возволяет указать флаг `-b`):

```shell
journalctl -b | grep -i "error\|fail\|warn"
```

Важно отметить, что поскольку пользователь (teacher) не входит в группу администраторов "adm", то отображаются только те записи в журнале, которые были сделаны при запуске приложений из под учётной записи текущего пользователя.

В журнале аудита есть несколько записей о сбое в работе системы, и вот некоторые из сообщений об ошибках:

```output
kwin_wayland_wrapper[1354]: Errors from xkbcomp are not fatal to the X server
xdg-desktop-por[1291]: Failed connect to PipeWire: Couldn't create PipeWire main loop
systemd[1167]: app=hplip\x2dsystray@autostart.service: Main process exited, code=exited, status=1/FAILURE
```

xkbcomp - отвечает за раскладку клавиатуры. PipeWire - это аналог PulseAudio и JACK - отвечает за работу аудио. hplip - похоже на сервис, связанные с драйверами принтеров HP. Т.е. эти сообщения не выглядят реальными проблемами.

Если смотреть не только на сообщения времени загрузки, а использовать команду `journalctl -xe | grep -i "error\|fail\|warn"`, то можно увидеть сообщения:

```
The unit UNIT was skipped due to an ExecutionCondition= command failure, and has
...
The unit UNIT has entered the 'failed' state with result 'exit-code'.`
```

Это значит, что не были запущены некоторые сервисы исходя из условий запуска. За запуск этих сервисов отвечает systemd.

## Что доступно в дистрибутиве M OS 12?

Работают следующие комбинации клавиш:

- Ctrl + Alt + F1 = графическая консоль Wayland
- Ctrl + Alt + F2 = графическая консоль X11 (не точно). Заблокирована, если активирован Wayland
- Ctrl + Alt + F3 = текстовая консоль Linux
