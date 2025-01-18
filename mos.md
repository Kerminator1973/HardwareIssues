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
- KWin: оконный менеджер для KDE Plasma, отвечает за управление расположений и вида окно, включая эффекты и перемещение окон
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

Команда `plasmashell --replace` привела к выдаче следующей информации:

```output
Invalid corona package metadata
Could not set containment property on rootObject
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

Анализ журнала может быть полезным:

```shell
journalctl -b | grep -i "error\|fail\|warn"
```

В журнале аудита есть несколько записей о сбое в работе системы, но они не выглядят действительно важными.

## Что доступно в дистрибутиве M OS 12?

Работают следующие комбинации клавиш:

- Ctrl + Alt + F1 = графическая консоль Wayland
- Ctrl + Alt + F2 = графическая консоль X11 (не точно). Заблокирована, если активирован Wayland
- Ctrl + Alt + F3 = текстовая консоль Linux
