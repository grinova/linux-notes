# Подключение наушников AirPods Pro

1. Изменить опцию `ControllerMode` на `bredr`:

    **`/etc/bluetooth/main.conf`**:
    ```
    ControllerMode = bredr
    ```

2. Перезапустить сервис `bluetooth`:

    ```console
    # rc-service bluetooth restart
    ```

3. Подключить наушники

4. После подключения наушников, опции `ControllerMode` можно вернуть старое значение
