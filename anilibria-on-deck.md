## Установка приложения на консоль Steam Deck со сборкой из исходников

*Данный набор команд написан для полностью чистой системы.*

**Внимание! Если найдете ошибки в командах или будут предложения по улучшению то создавайте ишью!**

Данное руководство рассчитано на подготовленого пользователя консоли, который знаком с режимом рабочего стола. Базовые вещи описаны на [портале поддержки Steam](https://help.steampowered.com/ru/faqs/view/671A-4453-E8D2-323C). Убедитесь, что на консоли установлена последняя версия SteamOS. На момент написания статьи актуальной была версия 3.8.16. Так же необходимо иметь базовые представления об терминале Linux.

**Внимание! Все манипуляции проводятся в режиме рабочего стола**

**Этап 1. Подготовка DistroBox**

Начиная с версии SteamOS 3.5 DistroBox включен в состав системы. Необходимо только добавить возможность работы программ с графическим интерфесом. Для этого в файле ~/.distroboxrc добавить следующую строку:

```bash
nano ~/.distroboxrc
xhost +si:localuser:$USER >/dev/null
```

**Этап 2. Установка Ubuntu в DistroBox**

Далее команды выполняются в терминале

```bash
distrobox create --image docker.io/library/ubuntu:26.04 ubuntu
```

**Этап 3. Переход в Ubuntu**

После команды

```bash
distrobox enter ubuntu
```

**Этап 4. Внутренняя установка**

Устанавливаем зависимости необходимые для сборки

```markdown
sudo apt install git build-essential git libmpv-dev pkg-config cmake qt6-base-dev qt6-declarative-dev qt6-websockets-dev qt6-svg-dev libxkbcommon-dev qml6-module-*

```

**Этап 5. Установка приложения из исходников**

Воспользуйтесь инструкцией для сборки. Обязательно собирайте с поддержкой mpv, также необходимо установить дополнительные кодеки:

```bash
sudo apt install ubuntu-restricted-extras
```

Если хотите добиться работы стандартного плеера, то необходимо установить ещё несколько пакетов:

```bash
sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
```

Для того чтобы приложение могло открывать браузер необходимо выполнить следующую команду:

```bash
sudo ln -s /usr/bin/distrobox-host-exec /usr/local/bin/xdg-open
```

**Этап 6. Создаем папку для проекта, переходим в нее и извлекаем исходники (предполагается что Вы находитесь в домашней папке)**

```bash
mkdir anilibria
cd anilibria/
git clone https://github.com/anilibria/anilibria-winmaclinux.git
cd anilibria-winmaclinux/src/
```

*Этап 7. Выполняем сборку и установку Подготовка сборки:*
```bash
cmake -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/AniLibria
cmake --build build
sudo cmake --install build
```

*Затем надо проверить работоспособность приложения не покидая гостевой системы. Для этого необходимо выполнить в терминале следующую команду:*

```bash
/opt/AniLibria/bin/AniLiberty
```
**Этап 8. Экспорт приложения:**
```bash
distrobox-export --app /opt/AniLibria/bin/AniLiberty
```

**Этап 9. Создаем ярлык на рабочий стол (в пуск так называймый)**

Экспорт иконки

```bash
mkdir -p ~/.local/share/icons
distrobox-enter ubuntu -- cat /opt/AniLibria/share/icons/hicolor/256x256/apps/aniliberty.png > ~/.local/share/icons/aniliberty.png
```

После этого создание .desktop файла:

```bash
nano ~/.local/share/applications/AniLiberty.desktop
```
Суть файла

```bash
[Desktop Entry]
Version=1.0
Type=Application
Name=AniLiberty
Comment=AniLiberty
Exec=distrobox-enter ubuntu -- /opt/AniLibria/bin/AniLiberty
Icon=/home/deck/.local/share/icons/aniliberty.png
Terminal=false
Categories=AudioVideo;Video;
StartupNotify=true
```

**Этап 10. Концовка**

Теперь можно спокойно выйти из гостевой системы командой

```bash
exit
```
