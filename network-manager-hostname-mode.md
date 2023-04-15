# Отключение hostname mode для NetworkManager

При подключении к wi-fi сети NetworkManager может менять hostname. Для отключения этой функции нужно установить значение `none` опции `hostname-mode` в `/etc/NetworkManager/NetworkManager.conf`:

```
[main]
hostname-mode=none
```
