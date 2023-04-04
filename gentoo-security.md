# Установка Gentoo на зашифрованный раздел и загрузка в secure boot

Данная заметка не является полным гайдом по установке Gentoo. Наиболее полное руководство описано в [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:Main_Page). Здесь лишь описаны шаги отличающиеся от оригинального способа установки Gentoo с OpenRC на файловую систему BTRFS с загрузкой через UEFI.

Вкратце для защиты данных и загрузки необходимо:
- Зашифровать раздел с пользовательскими данными (если нужен отдельный раздел)
- Зашифровать корневой раздел
- Собрать ядро с интегрированным initramfs
- Настроить Secure Boot:
  - Создать свои ключ и сертификат
  - Подписать ядро
  - Добавить сертификат в UEFI
  - Добавить загрузочную запись UEFI
  - Включить Secure Boot
- Установить пароль на UEFI


## Разметка диска

```
sda
|-sda1      vfat    /boot/efi     256MB     # EFI раздел
|-sda2      luks                  50GB      # Зашифрованный раздел с корневой файловой системой
| `-root    btrfs   /
`-sda3      luks                            # Зашифрованный раздел с домашней директорией
  `-home    btrfs   /home
```


## Форматирование разделов

```console
# # Форматироване EFI раздела в FAT32
# mkfs.vfat -F32 /dev/sda1

# # Форматирование luks раздела с корневой файловой системой
# cryptsetup luksFormat -c aes-xts-plain64 -h sha256 /dev/sda2
# cryptsetup open /dev/sda2 root
# # Форматирование корневого раздела в BTRFS
# mkfs.btrfs /dev/mapper/root
# cryptsetup close root

# # Форматирование luks раздела с домашней директорией
# cryptsetup luksFormat -c aes-xts-plain64 -h sha256 /dev/sda3
# cryptsetup open /dev/sda3 home
# # Форматирование раздела с домашней директорией в BTRFS
# mkfs.btrfs /dev/mapper/home
# cryptsetup close home
```

Для шифрования существующие разделы не обязательно форматировать, некоторые файловые системы (напр. BTRFS) можно [зашифровать вместе с данными](./partitions-encryption.md).


## Сборка ядра

### Конфигурация ядра

Конфигурация ядра при загрузке с LUKS раздела описана в [Gentoo Wiki: dm-crypt #Kernel_configuration](https://wiki.gentoo.org/wiki/Dm-crypt#Kernel_Configuration)

### Сборка ядра

```console
# genkernel --luks --btrfs --integrated-initramfs --kernel-config=/full/path/to/your/kernel/config all
```

- `--luks` - поддержка LUKS разделов
- `--btrfs` - поддержка файловой системы BTRFS
- `--integrated-initramfs` - сборка с включением initramfs в ядро (на выходе будет один файл вместо двух)

## Расшифровка раздела home при загрузке

### Создание ключа

```console
# dd if=/dev/random bs=1024 count=4 of=/etc/keys/home.key
# chmod 0400 /etc/keys/home.key
```


### Добавление ключа в слот LUKS раздела home

```console
# cryptsetup luksAddKey /dev/sda3 /etc/keys/home.key
```

### dmcrypt

**`/etc/conf.d/dmcrypt:`**
```
# Расшифровка раздела ключом
target=home
source='/dev/sda3'
key='/etc/keys/home.key'
```

Запуск сервиса `dmcrypt` на уровне `boot`:
```console
# rc-update add dmcrypt boot
```


## fstab

**`/etc/fstab:`**
```
/dev/sda1               /boot/efi   vfat    # ...
/dev/mapper/root        /           btrfs   # ...
/dev/mapper/home        /home       btrfs   # ...
```


## Secure Boot

### Создание и установка ключей в UEFI

Полный гайд по Secure Boot: [Gentoo Wiki: User:Sakaki/Sakaki's EFI Install Guide/Configuring Secure Boot](https://wiki.gentoo.org/wiki/User:Sakaki/Sakaki%27s_EFI_Install_Guide/Configuring_Secure_Boot_under_OpenRC)

### Подпись ядра

Переименование файла ядра:
```console
# mv -v /boot/vmlinuz-6.1.19-gentoo-x86_64 /boot/vmlinuz-6.1.19-gentoo-x86_64-unsigned
```

Подпись ядра:
```console
# sbsign --key /etc/efikeys/db.key --cert /etc/efikeys/db.crt --output /boot/vmlinuz-6.1.19-gentoo-x86_64 /boot/vmlinuz-6.1.19-gentoo-x86_64-unsigned
```

Если EFI раздел монтируется не в `/boot` а в `/boot/efi`, то ядро надо скопировать на этот раздел:
```console
# cp -v /boot/vmlinuz-6.1.19-gentoo-x86_64 /boot/efi/EFI/gentoo/
```


## Загрузочная запись

При создании загрузочной записи UEFI для загрузки с зашифрованного корневого раздела вместо параметра ядра `root` указывается `crypt_root`:
```console
# efibootmgr -c -d /dev/sda -p 1 -L "Gentoo [kernel 6.1.19]" -l '\EFI\gentoo\vmlinuz-6.1.19-gentoo-x86_64' -u 'crypt_root=/dev/sda2'
```

## Проверка secure boot режима

```console
$ mokutil --sb-state
$ # Output:
$ # SecureBoot enabled
```


## References

- [Gentoo Wiki: dm-crypt](https://wiki.gentoo.org/wiki/Dm-crypt)
- [Gentoo Wiki: dm-crypt full disk encryption](https://wiki.gentoo.org/wiki/Dm-crypt_full_disk_encryption)
- [Github: eleanxr/gentoo-encryption.md](https://gist.github.com/eleanxr/0cdc3f2d6a3d989f3957297929d08557)
- [Gentoo Wiki: Full Disk Encryption From Scratch Simplified](https://wiki.gentoo.org/wiki/Full_Disk_Encryption_From_Scratch_Simplified)
- [Gentoo Wiki: Full Encrypted Btrfs/Native System Root Guide](https://wiki.gentoo.org/wiki/Full_Encrypted_Btrfs/Native_System_Root_Guide)


## Видео

- [Алексей Бусыгин: "Обходим UEFI Secure Boot для компрометации полного шифрования диска в Linux"](https://youtu.be/FQIK66sYw8E)
- [How To Secure Boot with Linux (Gentoo Edition)](https://youtu.be/7SGM5cI7YhM)
