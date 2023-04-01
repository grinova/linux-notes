# Шрифты в Gentoo

## Эмодзи

### Установка пакета шрифтов с эмодзи

```console
# emerge -avq media-fonts/noto-emoji
```

### Проверка рендеринга эмодзи

```console
$ pango-view --text="2137 (😥) y3110w pop3"
```


## Шрифт Powerline

### Установка пакета media-fonts/powerline-symbols

```console
# emerge -avq media-fonts/powerline-symbols
```

### Подключение шрифта

Найти шрифт powerline:
```console
# eselect fontconfig list
# # Output:
# # Available fontconfig .conf files (* is enabled):
# #   ...
# #   [9]   10-powerline-symbols.conf *
# #   ...
```

Включить шрифт:
```console
# eselect fontconfig enable 10-powerline-symbols.conf
# # или
# eselect fontconfig enable 9
```

## Консольные шрифты

### Доступные шрифты
```console
$ ls /usr/share/consolefonts
```

### Временная установка шрифта
```console
$ setfont default8x16
```

### Вывод таблицы глифов
```console
$ showconsolefont
```

### Персистентная установка шрифтов
**`/etc/conf.d/consolefont`**:
```
consolefont="default8x16"
```


## References

- [Gentoo Wiki: Fonts](https://wiki.gentoo.org/wiki/Fonts)
- [Gentoo Wiki: Fontconfig](https://wiki.gentoo.org/wiki/Fontconfig)
- [Arch Wiki: Linux console](https://wiki.archlinux.org/title/Linux_console)
- [adeverteuil.github.io: Linux Console Fonts](https://adeverteuil.github.io/linux-console-fonts-screenshots/)
