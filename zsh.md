# ZSH

## Пакеты Gentoo
  - `[app-shells/zsh]` - оболочка zsh
  - `[app-shells/zsh-completions]` - авто-дополнение
  - `[app-shells/gentoo-zsh-completions]` - специфичное для gentoo авто-дополнение
  - `[app-shells/zsh-syntax-highlighting]` - подсветка синтаксиса


## Оболочка по умолчанию

Узнать какая оболочка установлена:

    grep "^${USER}" /etc/passwd

Найти путь до нужной оболочки:

    type -a zsh

Установить нужную оболочку:

    chsh -s /bin/zsh


## Настройки

Приглашение:

    PROMPT='%B%K{blue}%F{white}%n@%m%k:%F{blue}%~%F{white}$%f%b '


# Плагины

## [zsh-autocomplete](https://github.com/marlonrichert/zsh-autocomplete) (реалтаймовое авто-дополнение)

  1. Склонировать репозиторий:

```bash
git clone --depth 1 -- https://github.com/marlonrichert/zsh-autocomplete.git ~/.zsh/zsh-autocomplete
```

  2. Добавить в начале файла `.zshrc` (до любого вызова `compdef`):

```bash
source ~/.zsh/zsh-autocomplete/zsh-autocomplete.plugin.zsh
```

  3. Удалить все вызовы `compinit` из `.zshrc`


## [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) (реалтаймовые подсказки)

  1. Склонировать репозиторий:

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
```

  2. Добавить в `.zshrc`:

```bash
source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
```


## [zsh-command-time](https://github.com/popstas/zsh-command-time)

  1. Склонировать репозиторий:
```bash
git clone https://github.com/popstas/zsh-command-time.git ~/.zsh/zsh-command-time
```

  2. Добавить в `.zshrc`:
```bash
source .zsh/zsh-command-time/command-time.plugin.zsh
```


## [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.zsh/zsh-syntax-highlighting
```

**`~/.zshrc`**:
```
source ~/.zsh/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```
