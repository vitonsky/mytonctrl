# Использование директорий MyTonCtrl

MyTonCtrl - это оболочка, которая сохраняет свои файлы в двух местах:

1. `~/.local/share/mytonctrl/` - Долгосрочные файлы, такие как логи, хранятся здесь.
2. `/tmp/mytonctrl/` - Временные файлы хранятся здесь.

MyTonCtrl также включает другой скрипт, mytoncore, который в свою очередь хранит файлы в следующих местах:

1. `~/.local/share/mytoncore/` - Постоянные файлы, главная конфигурация будет храниться здесь.
2. `/tmp/mytoncore/` - Временные файлы, параметры, используемые для выборов, будут сохранены здесь.

MyTonCtrl загружает исходный код для самого себя и валидатора в следующие директории:

1. `/usr/src/mytonctrl/`
2. `/usr/src/ton/`

MyTonCtrl компилирует компоненты валидатора в следующую директорию:

1. `/usr/bin/ton/`

MyTonCtrl создает рабочую директорию для валидатора здесь:

1. `/var/ton/`

---

## Если MyTonCtrl был установлен как root:

Конфигурации будут храниться иначе:

1. `/usr/local/bin/mytonctrl/`
2. `/usr/local/bin/mytoncore/`

---

## Как удалить MyTonCtrl:

Запустите скрипт как администратор и удалите скомпилированные компоненты TON:

```bash
sudo bash /usr/src/mytonctrl/scripts/uninstall.sh
sudo rm -rf /usr/bin/ton
```

Во время этого процесса убедитесь, что у вас есть достаточные права для удаления или изменения этих файлов или директорий.


# Обработка ошибок и изменение рабочего каталога с MyTonCtrl

Если вы столкнулись с проблемами при выполнении MyTonCtrl от другого пользователя или хотите изменить рабочий каталог валидатора, эта инструкция предлагает несколько решений.

## Запуск MyTonCtrl от другого пользователя

Запуск MyTonCtrl от другого пользователя может вызвать следующую ошибку:

```
Error: expected str, bytes or os.PathLike object, not NoneType
```

Для ее решения вы должны запустить MyTonCtrl от пользователя, который его установил.

## Изменение рабочего каталога валидатора перед установкой

Если вы хотите изменить рабочий каталог валидатора до установки, есть два способа это сделать:

1. **Сделайте форк проекта** - Вы можете сделать форк проекта и внести свои изменения туда. Узнайте, как сделать форк проекта с помощью `man git-fork`.
2. **Создайте символическую ссылку** - Вы также можете создать символическую ссылку с помощью следующей команды:

    ```bash
    ln -s /opt/ton/var/ton
    ```
Эта команда создаст ссылку `/var/ton`, которая указывает на `/opt/ton`.

## Изменение рабочего каталога валидатора после установки

Если вы хотите изменить рабочий каталог валидатора с `/var/ton/` после установки, выполните следующие действия:

1. **Остановите службы** - Вам потребуется остановить службы с помощью этих команд:

    ```bash
    systemctl stop validator
    systemctl stop mytoncore
    ```

2. **Переместите файлы валидатора** - Затем вам нужно переместить файлы валидатора с помощью этой команды:

    ```bash
    mv /var/ton/* /opt/ton/
    ```

3. **Обновите пути в конфигурации** - Замените пути в конфигурации, расположенной в `~/.local/share/mytoncore/mytoncore.db`.

4. **Примечание об опыте** - Предыдущего опыта такого переноса не было, поэтому учитывайте это при продвижении вперед.

Не забудьте убедиться, что у вас достаточно прав для внесения этих изменений или выполнения этих команд.

# Как понять статус валидатора и перезапустить валидатор в MyTonCtrl

Этот документ поможет вам понять, как подтвердить, стала ли MyTonCtrl полноценным валидатором, и как перезапустить ваш валидатор.

## Проверка статуса валидатора в MyTonCtrl

Вы можете подтвердить, что ваша нода стала полноценным валидатором, проверив следующие условия:

1. **Десинхронизация валидатора** - Десинхронизация локального валидатора должна быть меньше 20.
2. **Индекс валидатора** - Индекс валидатора должен быть больше -1.

Чтобы просмотреть коэффициент работы своего валидатора, вы можете использовать команду `vl` в MyTonCtrl:

1. Найдите свой валидатор в списке по его ADNL-адресу (`adnlAddr`).
2. Если коэффициенты `mr` и `wr` близки к 1, это означает, что ваш валидатор работает правильно.

## Перезапуск вашего валидатора

Если вам нужно перезапустить ваш валидатор, вы можете сделать это, выполнив следующую команду:

```bash
systemctl restart validator
```

Убедитесь, что у вас есть достаточные права для выполнения этих команд и сделайте необходимые корректировки. Всегда помните о резервном копировании важных данных перед выполнением операций, которые могут потенциально повлиять на ваш валидатор.