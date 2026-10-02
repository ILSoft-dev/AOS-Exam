---
title: "Команды Windows (лабораторные)"
description: "Шпаргалка по командам командной строки Windows из лабораторных работ: файлы и каталоги, DiskPart, ChkDsk, процессы, службы, сетевые ресурсы, учётные записи, NetSh, установка программ."
---
# ⌨️ Команды Windows: шпаргалка к лабораторным

<div class="tip" markdown="1">
Командная строка запускается через **Win + R → `cmd`**. Для многих команд нужен запуск **от имени администратора**. Справка по любой команде: `команда /?` (например, `xcopy /?`) или `help`.
</div>

**Содержание:** [файлы и каталоги](#files) · [диски](#disks) · [процессы](#proc) · [службы](#sc) · [общие ресурсы](#share) · [учётные записи](#user) · [завершение работы](#shutdown) · [сеть](#netsh) · [программы](#install) · [полезное](#misc)

<div class="stack" markdown="1">

## Общие правила {#rules}

- Регистр букв в командах и путях **не важен**: `DIR` = `dir`.
- Пути с пробелами пишутся **в кавычках**: `cd "C:\Program Files"`.
- **Подстановочные знаки:** `*` — любое число символов, `?` — один символ: `*.txt`, `report?.doc`.
- **Перенаправление:** `>` — вывод в файл (перезапись), `>>` — дописать, `<` — ввод из файла, `|` — конвейер (вывод одной команды на вход другой), `2>` — вывод ошибок.
  ```text
  dir C:\Windows > list.txt
  tasklist | find "chrome"
  ```
- `.` — текущий каталог, `..` — родительский, `\` — корень диска.

## 1. Файлы и каталоги (лаб. 1) {#files}

| Команда | Назначение | Примеры |
|---|---|---|
| **CD** (CHDIR) | Показать или сменить **текущий каталог** | `cd` — показать текущий<br>`cd C:\Users` — перейти<br>`cd ..` — на уровень вверх<br>`cd \` — в корень<br>`cd /d D:\Data` — сменить и диск, и каталог<br>`D:` — перейти на диск D |
| **DIR** | **Список** файлов и подкаталогов | `dir` — текущий каталог<br>`dir /w` — в несколько колонок<br>`dir /p` — постранично<br>`dir /s *.txt` — с подкаталогами<br>`dir /a:h` — скрытые<br>`dir /a:d` — только каталоги<br>`dir /o:-s` — по убыванию размера<br>`dir /b` — только имена |
| **MD** (MKDIR) | **Создать** каталог | `md Docs`<br>`md C:\Work\2024\Reports` — сразу вся цепочка |
| **RD** (RMDIR) | **Удалить** каталог | `rd Docs` — только пустой<br>`rd /s Docs` — со всем содержимым<br>`rd /s /q Docs` — без подтверждения |
| **DEL** (ERASE) | **Удалить** файлы | `del file.txt`<br>`del *.tmp`<br>`del /s /q *.bak` — во всех подкаталогах, без вопросов<br>`del /f file.txt` — в том числе только для чтения<br>`del /p *.*` — с подтверждением каждого |
| **COPY** | **Копировать** файлы | `copy a.txt D:\Backup\`<br>`copy a.txt b.txt` — копия с новым именем<br>`copy *.doc D:\Docs`<br>`copy a.txt+b.txt all.txt` — объединить<br>`copy con note.txt` — создать файл с клавиатуры (Ctrl+Z, Enter — конец) |
| **XCOPY** | **Расширенное** копирование: каталоги с подкаталогами | `xcopy C:\Src D:\Dst /s` — с непустыми подкаталогами<br>`xcopy C:\Src D:\Dst /e /i /h /y` — все подкаталоги (/e), Dst считать каталогом (/i), со скрытыми (/h), без вопросов (/y)<br>`xcopy C:\Src D:\Dst /d` — только новые и изменённые |
| **MOVE** | **Переместить** или **переименовать** | `move a.txt D:\Docs\`<br>`move OldDir NewDir` — переименовать каталог |
| **REN** (RENAME) | Переименовать | `ren a.txt b.txt`<br>`ren *.txt *.bak` |
| **TYPE** | Вывести содержимое текстового файла | `type readme.txt`<br>`more < log.txt` — постранично |
| **TREE** | Дерево каталогов | `tree C:\Work /f` — с файлами |
| **ATTRIB** | Атрибуты файла | `attrib +r +h file.txt` — только чтение и скрытый<br>`attrib -h file.txt` |

<div class="tip" markdown="1">
**ROBOCOPY** — современная замена XCOPY: `robocopy C:\Src D:\Dst /e` копирует всё, `robocopy C:\Src D:\Dst /mir` зеркалирует каталог (**удаляет** лишние файлы в Dst!).
</div>

## 2. Диски: DiskPart, ChkDsk, Where (лаб. 2) {#disks}

### DISKPART — управление дисками и разделами

Интерактивная утилита. Запуск: `diskpart` (от администратора), после этого команды вводятся в приглашении `DISKPART>`.

| Команда | Назначение |
|---|---|
| `list disk` | Список физических дисков |
| `select disk 1` | Выбрать диск 1 (дальнейшие команды относятся к нему) |
| `detail disk` | Сведения о выбранном диске |
| `list partition` | Разделы выбранного диска |
| `list volume` | Список томов (логических дисков) |
| `select volume 3` / `select partition 1` | Выбрать том или раздел |
| `clean` | ⚠️ **Стереть** всю разметку выбранного диска |
| `convert gpt` / `convert mbr` | Преобразовать стиль разметки (диск должен быть пустым) |
| `create partition primary size=10240` | Создать основной раздел 10 ГБ (размер в МБ) |
| `format fs=ntfs label="Data" quick` | Быстро отформатировать в NTFS с меткой |
| `assign letter=E` | Назначить букву диска |
| `remove letter=E` | Убрать букву |
| `active` | Сделать раздел активным (загрузочным, MBR) |
| `extend size=2048` / `shrink desired=2048` | Расширить или сжать том |
| `delete partition` | Удалить раздел |
| `exit` | Выход |

**Пример: подготовить флешку**
```text
diskpart
list disk
select disk 2          ← ВНИМАТЕЛЬНО: номер флешки!
clean
create partition primary
format fs=fat32 quick label="USB"
assign
exit
```

### CHKDSK — проверка и исправление диска

| Команда | Назначение |
|---|---|
| `chkdsk C:` | Проверка без исправления (отчёт) |
| `chkdsk D: /f` | **Исправить** ошибки ФС |
| `chkdsk D: /r` | Найти **сбойные секторы** и восстановить читаемые данные (включает /f) |
| `chkdsk D: /x` | Принудительно отключить том перед проверкой |
| `chkdsk C: /scan` | Онлайн-проверка NTFS без отключения |

Системный диск C: нельзя проверить с исправлением во время работы. Будет предложено проверить при следующей перезагрузке. Подробнее: [вопрос 30]({{ '/q/30.html' | relative_url }}).

Другие полезные команды: `format E: /fs:ntfs /q` — форматирование, `vol` — метка и серийный номер, `label` — сменить метку, `fsutil fsinfo drives` — список дисков, `defrag C: /a` — анализ фрагментации ([вопрос 27]({{ '/q/27.html' | relative_url }})).

### WHERE — поиск файлов

| Команда | Назначение |
|---|---|
| `where notepad` | Где находится программа (поиск в каталогах переменной PATH) |
| `where /r C:\Users *.docx` | Рекурсивный поиск файлов по маске в каталоге |
| `where /r C:\ hosts` | Найти файл hosts на диске C |
| `where /t /r D:\ *.log` | С размером и датой |

Альтернатива: `dir C:\ /s /b report*.docx`.

## 3. Процессы: TaskList, TaskKill, QProcess (лаб. 3) {#proc}

| Команда | Назначение |
|---|---|
| `tasklist` | Список запущенных процессов (имя, **PID**, сеанс, память) |
| `tasklist /v` | Подробно (пользователь, состояние, заголовок окна) |
| `tasklist /svc` | Какие **службы** работают в каждом процессе |
| `tasklist /m kernel32.dll` | Процессы, использующие DLL |
| `tasklist /fi "imagename eq notepad.exe"` | **Фильтр** по имени |
| `tasklist /fi "memusage gt 100000"` | Процессы, использующие > 100 МБ |
| `tasklist /s PC01 /u admin` | На удалённом компьютере |
| `taskkill /pid 4312` | Завершить процесс по PID |
| `taskkill /im notepad.exe` | Завершить по имени |
| `taskkill /f /im chrome.exe /t` | **Принудительно** (/f) и вместе с дочерними процессами (/t) |
| `taskkill /fi "status eq not responding"` | Завершить зависшие |
| `qprocess` (`query process`) | Процессы **текущего сеанса** пользователя |
| `qprocess *` | Процессы всех сеансов |
| `qprocess /id:2` | Процессы сеанса 2 |
| `start notepad` | Запустить программу. `start /high calc` — с высоким приоритетом |

Связь с теорией: процессы и потоки — [вопрос 6]({{ '/q/06.html' | relative_url }}), приоритеты — [вопрос 8]({{ '/q/08.html' | relative_url }}).

## 4. Службы: SC (лаб. 3) {#sc}

**Служба (сервис)** — фоновый процесс без интерфейса, который часто запускается вместе с системой.

| Команда | Назначение |
|---|---|
| `sc query` | Список **работающих** служб |
| `sc query state= all` | Все службы (⚠️ пробел **после** `=` обязателен) |
| `sc query wuauserv` | Состояние службы (Центр обновления) |
| `sc qc Spooler` | Конфигурация службы (тип запуска, путь) |
| `sc start Spooler` / `sc stop Spooler` | Запустить / остановить |
| `sc pause` / `sc continue` | Приостановить / продолжить |
| `sc config Spooler start= disabled` | Тип запуска: `auto`, `demand` (вручную), `disabled`, `delayed-auto` |
| `sc create MySvc binPath= "C:\svc.exe"` | Создать службу |
| `sc delete MySvc` | Удалить службу |
| `sc \\PC01 query` | На удалённом компьютере |

Альтернативы: `net start` (список запущенных), `net start Spooler`, `net stop Spooler`, оснастка `services.msc`.

## 5. Общие сетевые ресурсы: Net Share (лаб. 3) {#share}

| Команда | Назначение |
|---|---|
| `net share` | Список общих ресурсов компьютера (включая административные `C$`, `ADMIN$`, `IPC$`) |
| `net share Docs=C:\Docs` | Открыть общий доступ к папке под именем Docs |
| `net share Docs=C:\Docs /grant:Все,READ` | С правами: `READ`, `CHANGE`, `FULL` |
| `net share Docs=C:\Docs /users:5 /remark:"Документы"` | Лимит пользователей и комментарий |
| `net share Docs /delete` | Закрыть общий доступ |
| `net use` | Подключённые сетевые диски |
| `net use Z: \\Server\Docs` | Подключить сетевой диск |
| `net use Z: \\Server\Docs /persistent:yes` | С восстановлением при входе |
| `net use Z: /delete` | Отключить |
| `net view \\Server` | Ресурсы удалённого компьютера |

Теория: [сетевые файловые системы, SMB]({{ '/q/36.html' | relative_url }}).

## 6. Учётные записи: Net User (лаб. 4) {#user}

| Команда | Назначение |
|---|---|
| `net user` | Список пользователей |
| `net user Ivan` | Сведения об учётной записи |
| `net user Ivan P@ssw0rd /add` | Создать пользователя |
| `net user Ivan *` | Сменить пароль (запросит ввод) |
| `net user Ivan /active:no` | Отключить учётную запись (`yes` — включить) |
| `net user Ivan /expires:31.12.2025` | Срок действия |
| `net user Ivan /times:пн-пт,8-18` | Разрешённое время входа |
| `net user Ivan /delete` | Удалить |
| `net localgroup` | Список локальных групп |
| `net localgroup Администраторы Ivan /add` | Добавить в группу (в английской Windows — `Administrators`) |
| `net accounts` | Политика паролей (длина, срок действия) |
| `whoami` / `whoami /groups` | Текущий пользователь и его группы |

Теория: [контроль доступа]({{ '/q/28.html' | relative_url }}).

## 7. Завершение работы: ShutDown (лаб. 4) {#shutdown}

| Команда | Назначение |
|---|---|
| `shutdown /s /t 0` | **Выключить** сейчас |
| `shutdown /r /t 60` | **Перезагрузить** через 60 секунд |
| `shutdown /a` | **Отменить** запланированное выключение |
| `shutdown /l` | Выйти из системы |
| `shutdown /h` | Гибернация |
| `shutdown /s /f /t 30 /c "Обновление"` | Принудительно закрыть приложения, с сообщением |
| `shutdown /r /o` | Перезагрузка в меню дополнительных параметров загрузки |
| `shutdown /m \\PC01 /r` | Перезагрузить удалённый компьютер |
| `shutdown /i` | Графический интерфейс |

## 8. Сетевой интерфейс: NetSh (лаб. 4) {#netsh}

| Команда | Назначение |
|---|---|
| `netsh interface show interface` | Список сетевых интерфейсов и их состояние |
| `netsh interface ip show config` | IP-адреса, шлюзы, DNS |
| `netsh interface ip set address "Ethernet" static 192.168.1.10 255.255.255.0 192.168.1.1` | Статический IP, маска, шлюз |
| `netsh interface ip set address "Ethernet" dhcp` | Получать адрес по DHCP |
| `netsh interface ip set dns "Ethernet" static 8.8.8.8` | Задать DNS |
| `netsh interface set interface "Ethernet" disable` | Отключить адаптер (`enable` — включить) |
| `netsh wlan show profiles` | Сохранённые Wi-Fi сети |
| `netsh wlan show profile name="MyWiFi" key=clear` | Показать пароль Wi-Fi |
| `netsh advfirewall set allprofiles state off` | Выключить брандмауэр (`on` — включить) |
| `netsh advfirewall firewall add rule name="Web" dir=in action=allow protocol=TCP localport=80` | Правило брандмауэра |
| `netsh int ip reset` / `netsh winsock reset` | Сброс стека TCP/IP и Winsock |

Полезные сетевые команды: `ipconfig /all`, `ipconfig /release` / `/renew`, `ipconfig /flushdns`, `ping`, `tracert`, `nslookup`, `netstat -ano` (соединения и порты, см. [сокеты]({{ '/q/35.html' | relative_url }})), `arp -a`, `route print`.

## 9. Установка и удаление программ (лаб. 4) {#install}

| Команда | Назначение |
|---|---|
| `msiexec /i app.msi` | Установить MSI-пакет |
| `msiexec /i app.msi /qn` | **Тихая** установка, без интерфейса |
| `msiexec /x app.msi` или `msiexec /x {GUID}` | Удалить |
| `msiexec /i app.msi /l*v log.txt` | С журналом установки |
| `setup.exe /S` или `/silent` | Тихая установка EXE (ключ зависит от установщика) |
| `winget search firefox` | Найти программу в репозитории (Windows 10/11) |
| `winget install Mozilla.Firefox` | Установить |
| `winget list` | Установленные программы |
| `winget upgrade --all` | Обновить все |
| `winget uninstall Mozilla.Firefox` | Удалить |
| `wmic product get name,version` | Список установленных MSI-программ (WMIC устарел) |
| `appwiz.cpl` | Открыть «Программы и компоненты» |
| `dism /online /get-features` | Компоненты Windows |
| `dism /online /enable-feature /featurename:Microsoft-Hyper-V /all` | Включить компонент (например, Hyper-V) |

PowerShell: `Get-Package`, `Get-AppxPackage`, `Remove-AppxPackage`.

## 10. Полезное {#misc}

| Команда | Назначение |
|---|---|
| `systeminfo` | Сведения о системе (ОС, память, обновления) |
| `ver`, `winver` | Версия Windows |
| `hostname` | Имя компьютера |
| `set` | Переменные окружения. `echo %PATH%` |
| `cls` | Очистить экран |
| `echo текст > file.txt` | Записать текст в файл |
| `find "текст" file.txt` / `findstr /s /i "error" *.log` | Поиск текста |
| `fc a.txt b.txt` | Сравнить файлы |
| `sfc /scannow` | Проверка системных файлов |
| `gpupdate /force` | Применить групповые политики |
| `eventvwr`, `taskmgr`, `services.msc`, `diskmgmt.msc`, `compmgmt.msc`, `devmgmt.msc` | Журнал событий, диспетчер задач, службы, управление дисками, управление компьютером, диспетчер устройств |

### Пакетные файлы (.bat / .cmd)

```bat
@echo off
rem Резервная копия документов с датой в имени
set DST=D:\Backup\%date:~-4%-%date:~3,2%-%date:~0,2%
md "%DST%"
xcopy "%USERPROFILE%\Documents" "%DST%" /e /i /h /y
if errorlevel 1 (echo Ошибка копирования) else (echo Готово)
pause
```

`%date:~-4%` — подстрока даты (зависит от региональных настроек). `%USERPROFILE%` — папка пользователя. `errorlevel` — код возврата последней команды.

</div>
