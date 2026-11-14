
# VK Capsule Mini — A113X Open Research 🔧

**Can we turn an old VK smart speaker into our own local smart speaker?**

Can we get rid of the cloud dependency, bring the device back to life and connect it to Home Assistant?

That's what we're trying to find out.

This is an independent hardware/software research project around the **VK Capsule Mini**, powered by the **Amlogic A113X**.

No magic, no random firmware flashing — just curiosity, Linux, soldering, logs and a lot of experiments. :)

---

# 🇷🇺 Русская версия

## 💡 Идея проекта

У нас есть VK Капсула Мини — умная колонка примерно 2021 года выпуска.

Но что, если сделать её действительно своей?

Мы хотим исследовать возможность запуска собственной системы, которая сможет работать локально и дружить с **Home Assistant**.

В идеале хочется сохранить всё, что уже есть внутри:

- 🖥️ Экран
- 🔊 Динамик и аудиосистема
- 🎤 Микрофоны
- 🔘 Физические кнопки
- 💡 Световое кольцо
- 📡 Wi-Fi и Bluetooth

А дальше — собственный голосовой помощник, Home Assistant Assist, Music Assistant и, возможно, подключение к собственному ИИ через API.

Пока это цель, а не готовый результат. Сначала нужно понять, как устроена сама колонка.

## 🔍 Что уже знаем

- Процессор: **Amlogic A113X**
- Устройство: VK Capsule Mini
- Примерный год выпуска: 2021
- Точная ревизия платы: пока неизвестна.
- Оригинальная прошивка: пока не получена.

Мы также нашли документацию на несколько компонентов, которые могут встречаться в устройстве:

| Компонент | Что интересного |
|---|---|
| Amlogic A113X | Главный процессор колонки |
| NeoFidelity NTP8910A | Возможный аудиоусилитель |
| ISSI IS31FL3236A | Возможный контроллер светодиодов |
| Fn-Link K255B-SR | Возможный Wi-Fi/Bluetooth-модуль |

Это пока список кандидатов по документации, а не подтверждённый состав нашей платы.

## 🖥️ Первые инструменты

Для исследования подготовили Ubuntu в WSL2 и установили:

- Binwalk — поиск и анализ содержимого прошивок.
- Device Tree Compiler — работа с Device Tree.
- file — определение типов файлов.

Рабочая папка проекта уже создана. Теперь нужно получить то, что действительно интересно анализировать: оригинальную прошивку и загрузочные логи.

## 🔌 UART: мы нашли консоль!

Одна из самых интересных находок — последовательный порт UART.

Согласно практическому отчёту по установке Alpine Linux на A113X, на плате есть три площадки между процессором и Wi-Fi-модулем:

| Контакт | Назначение |
|---|---|
| Левый | GND |
| Средний | RX |
| Правый | TX |

Подключение к USB-UART:

- GND → GND
- RX платы → TX адаптера
- TX платы → RX адаптера

Настройки терминала: `115200 8N1`.

**И самое интересное:** UART показывает загрузку, но, судя по отчёту, не принимает команды.

В логах видны U-Boot 2015 и Linux 4.9. Попытки остановить загрузчик клавишами не срабатывают, а после старта Linux интерактивного ввода тоже нет.

Получается, у нас есть окно в процесс загрузки, но пока нет возможности поговорить с самой системой через эту консоль.

Источник: [Практическая установка Alpine Linux на Amlogic A113X](https://debianlive.ru/2026/08/vk-mini-legacy-amlogic-a113x-часть-1-ставим-alpine-со-свежим-майнлайном/).

Пока это сведения из стороннего исследования — нам ещё предстоит повторить эксперимент на своём экземпляре.

## 🧩 Что дальше?

Наш небольшой план:

- [ ] Получить полный UART-лог загрузки.
- [ ] Определить точную ревизию платы.
- [ ] Разобраться с USB Download Mode.
- [ ] Найти способ получить резервную копию оригинальной прошивки.
- [ ] Изучить разделы памяти и загрузчик.
- [ ] Разобраться с Device Tree.
- [ ] Найти драйверы дисплея, аудио и светодиодов.
- [ ] Проверить возможности запуска альтернативного Linux.
- [ ] Изучить варианты интеграции с Home Assistant.

## 🛠️ Полезные материалы

Несколько проектов и источников, которые помогают нам в исследовании:

- [LibreCapsula](https://librecapsula.org/) — информация по аппаратной части и UART.
- [OpenCapsule](https://github.com/yawaflua/OpenCapsule) — независимый проект по теме Capsule.
- [Home Assistant Community](https://community.home-assistant.io/) — всё про локальный умный дом.

Документация компонентов:

- [Amlogic A113X QRM](https://www.scribd.com/document/715327502/A113X-QRM-V0-9-20170810)
- [NTP8910A Datasheet](http://www.cpbay.com/Uploads/20210227/603a064dbeb93.pdf)
- [IS31FL3236A Datasheet](http://www.issi.com/WW/pdf/31FL3236A.pdf)
- [K255B-SR Datasheet](https://fr.fn-link.com/download/K255B-SR_V7.0_20210727.pdf)

## 🤝 Помочь проекту?

Если у тебя есть такая же колонка, информация о ревизии платы, фотографии, дампы, схемы или опыт работы с A113X — будем рады!

Особенно интересны:
- распиновка и аппаратные ревизии;
- UART и загрузчик;
- оригинальные образы прошивки;
- Linux-драйверы;
- управление экраном, звуком и подсветкой.

Все находки лучше сопровождать источниками и отмечать, что уже проверено, а что пока только теория.

## ⚠️ Немного осторожности

Пока мы не получили оригинальную прошивку и не разобрались с восстановлением, не стоит экспериментировать с прошивкой наугад.

Не подключайте неизвестные контакты к питанию, не подавайте 5 V на UART и не публикуйте дампы с паролями или токенами.

Сначала изучаем — потом модифицируем.

---

# 🇬🇧 English version

## 💡 The idea

We have a VK Capsule Mini smart speaker, probably manufactured around 2021.

But what if we could make it truly ours?

We want to explore running our own local system and connecting the speaker to **Home Assistant**.

Ideally, we'd like to keep the original hardware:

- 🖥️ Display
- 🔊 Speaker and audio system
- 🎤 Microphones
- 🔘 Physical buttons
- 💡 RGB light ring
- 📡 Wi-Fi and Bluetooth

The bigger idea is to build a local voice assistant with Home Assistant Assist, Music Assistant and possibly our own AI API.

This is a goal, not a finished result. First, we need to understand the hardware.

## 🔍 What we know so far

- SoC: **Amlogic A113X**
- Device: VK Capsule Mini
- Approximate manufacturing year: 2021
- Exact PCB revision: unknown.
- Original firmware: not yet acquired.

We have also collected documentation for several possible components:

| Component | Why it matters |
|---|---|
| Amlogic A113X | Main processor |
| NeoFidelity NTP8910A | Possible audio amplifier |
| ISSI IS31FL3236A | Possible LED controller |
| Fn-Link K255B-SR | Possible Wi-Fi/Bluetooth module |

These are research candidates, not yet a confirmed list of chips on our board.

## 🖥️ Our first tools

We prepared an Ubuntu environment in WSL2 and installed:

- Binwalk — firmware analysis.
- Device Tree Compiler — Device Tree inspection.
- file — file type identification.

The workspace is ready. Now we need the interesting stuff: original firmware and boot logs.

## 🔌 UART: we found the console!

One of the most interesting discoveries is the UART serial interface.

According to an Alpine Linux installation report for the A113X, three pads are located between the SoC and the Wi-Fi module:

| Pad | Function |
|---|---|
| Left | GND |
| Middle | RX |
| Right | TX |

USB-UART wiring:

- GND → GND
- Board RX → Adapter TX
- Board TX → Adapter RX

Terminal settings: `115200 8N1`.

**Here's the interesting part:** UART outputs boot logs, but apparently does not accept input.

The reported logs show U-Boot 2015 followed by Linux 4.9. Pressing keys to interrupt the bootloader does not work, and there is no interactive input after Linux starts.

So we can watch the boot process, but we cannot talk to the system through this console yet.

Source: [Practical Alpine Linux installation on Amlogic A113X](https://debianlive.ru/2026/08/vk-mini-legacy-amlogic-a113x-часть-1-ставим-alpine-со-свежим-майнлайном/).

These details come from an independent report and still need to be reproduced on our own device.

## 🧩 What's next?

- [ ] Capture the complete UART boot log.
- [ ] Identify the exact PCB revision
