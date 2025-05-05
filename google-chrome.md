# Google Chrome

## Отключение уведомления об обновлении Google Chrome

Добавить параметр `--simulate-outdated-no-au` со значением превышающем текущую дату.

```ini
# /usr/share/applications/google-chrome.desktop

Exec=sh -c "/usr/bin/google-chrome-stable --simulate-outdated-no-au='Fri, 27 Dec 2025 02:00:00 +0000' %U"
```
