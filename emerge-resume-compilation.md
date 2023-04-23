# Сборка и установка пакета Gentoo при сбое

Сборка некоторых пакетов занимает очень много времени и может быть прервана из-за нехватки оперативной памяти (chromium собирается несколько часов и требует примерно 16ГБ ОЗУ). Что бы сохранить промежуточный результат компиляции и продолжить сборку с момента сбоя, можно воспользоваться следующей командой (на примере пакета `www-client/chromium` версии `109.0.5414.74-r1`):
```console
# ebuild /var/db/repos/gentoo/www-client/chromium/chromium-109.0.5414.74-r1.ebuild compile
```

После успешной сборки, пакет нужно установить:
```console
# emerge /var/db/repos/gentoo/www-client/chromium/chromium-109.0.5414.74-r1.ebuild merge
```

Если установка пакета производилась впервые, нужно добавить пакет в world set:
```console
# emerge -ask --noreplace www-client/chromium
```


## При нехватке ОЗУ

При нехватке ОЗУ можно временно подключить swap раздел с usb флешки (`sda1`):
```console
# mkswap /dev/sda1
# swapon /dev/sda1
```

Если достаточно свободного места на диске и файловая система позволяет размещать файл подкачки (напр. ext4, на btrfs нельзя), то можно создать и подключить swapfile:
```console
# dd if=/dev/zero of=/path/to/swapfile bs=1M count=8k
# mkswap /path/to/swapfile
# swapon /path/to/swapfile
```

Отключение подкачки:
```console
# swapoff /dev/sda1
# # или
# swapoff /path/to/swapfile
# rm /path/to/swapfile
```


## При нехватке диска

Промежуточные результаты компиляции пакетов сохраняются в `/var/tmp/portage`. При нехватке свободного пространства в корневой файловой системе достаточно смонтировать свободный раздел (напр. c usb флешки) в `/var/tmp/portage`, предварительно скопировав содержимое директории на этот раздел:
```console
# mkdir /mnt/usb-stick
# mount /dev/sda1 /mnt/usb-stick
# cp -a /var/tmp/portage/* /mnt/usb-stick
# umount /mnt/usb-stick
# mount /dev/sda1 /var/tmp/portage
```

### Альтернатива: OverlayFS

```console
# mkdir /mnt/usb-stick
# mount /dev/sda1 /mnt/usb-stick
# mkdir /mnt/usb-stick/{data,work}
# mount -t overlay overlay -o lowerdir=/var/tmp/portage,upperdir=/mnt/usb-stick/data,workdir=/mnt/usb-stick/work /var/tmp/portage
```

После успешной установки нужно вручную удалить временные файлы компиляции оставшиеся с предыдущей сборки:
```console
# umount /var/tmp/portage
# rm -r /var/tmp/portage/*
```


## References

- [Resume compilation of a package from where it failed under Gentoo](https://ahelpme.com/linux/gentoo/resume-compilation-of-a-package-from-where-it-failed-under-gentoo/)
