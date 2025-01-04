# Проблемы с операционной системой М ОС

М ОС - это форк операционной системы ROSA, изначально основанной на Mandriva. Рабочее окружение - KDE Plasma 5.

После обновлений компонентов 1 января 2025 г перестал запускаться рабочий стол, при том, что значительная часть апплетов работоспособна. Так, например, нажимая символы на "чётном экране рабочего стола" запускается апплет, который позволяет найти и запустить приложение. В частности, можно запустить:

- Chromium (браузер)
- Dolphine (файловый менеджер)
- KTerminal (shell)

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

Посмотреть содержимое экрана можно так:

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
Утилита ROSA "Индикатор обновлений" написана на Python 3: 

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
