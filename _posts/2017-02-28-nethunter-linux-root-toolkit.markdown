---
title: NetHunter Linux Root Toolkit
layout: post
category: tutorial
tags:
- Nexus 5
- NetHunter
- Kali linux
- Hack
---

# TL;DR; Краткая инструкция для Nexus 5

1. Качаем https://yadi.sk/d/fNjM9-ma3EkLWj
2. Распаковываем: `tar -xzf nethunter-hammerhead-LRT.tgz && cd nethunter-hammerhead-LRT`.
3. Если загрузчик не разблокирован: `./oemUnlock.sh`.
4. Накатываем голую систему(**УДАЛИТ ВСЕ ДАННЫЕ С ТЕЛЕФОНА!**):  `./stockNexusFlash.sh`.
5. Ставим всё остальное: `./twrpFlash.sh`.
6. Тыкаем, что хотим поставить.
6.1. Ждём, когда повиснет на 90%.
6.2 Ждём неопределенный промежуток времени.(Или не ждём?)
6.3 Принудительно выключаем телефон зажатой кнопкой питания и включаем его.
6.4 Сносим и переустанавливаем chroot в приложении NetHunter
7. Готово! 


# Kali NetHunter Linux Root Toolkit (LRT)

**NetHunter Linux Root Toolkit** это набор bash скриптов, которые устанавливают Kali Linux NetHuner из Linux/OSX на [устройство, поддерживаемое NetHunter](https://github.com/offensive-security/kali-nethunter/wiki#10-supported-devices-and-roms).

##1. Обязательно перед установкой
 - Включенный на телефоне режим отладки (USB-debug).
 - Подтвержденный RSA ключ для USB отладки на этом компьютере.
 - `adb` и `fastboot` установленны и прописаны в `$PATH` (новее, чем v1.0.31)
 - `tar && unzip` установлены и есть в `$PATH`

*Используйте Google, если вы не знаете, как установить adb или fastboot; Android SDK позволит установить всё сразу, но также бинарники доступны здесь: http://tools.android.com/download.*

**Предположения:** Устройство нормально запущено и подключено через USB кабель к машине под управлением Linux\OSX, USB-отладка включена.

**ВАЖНОЕ ЗАМЕЧАНИЕ**: Чтобы гарантировать себе беспроблемную установку, воспользуйтесь USB2.0. Если установка неожиданно прерывается - скорее всего дело в версии USB. Мы использовали USB2.0 хаб для подключения нашего телефона при установке(*пр.пр.: не рекомендую использовать хаб - цепляйте напрямую*).

##2. Инструкции по установке LRT
1. Клонируйте репозиторий и сделайте `cd` внутрь `Nethunter-LRT/`.

2. Скачайте верный стоковый образ для вашего устройства и поместите его в `/stockImage`:
    -  *Устройства Nexus:* с [Google Nexus Images](https://developers.google.com/android/nexus/images?hl=en)
    -  *OnePlusOne:* с [cyngn.com](https://cyngn.com/support) (скачивайте fastboot версию для вашего устройства)

3. Скачайте правильный образ TWRP для вашего девайса с [TWRP WEB](https://twrp.me/Devices/) и поместите его в `/twrpImage`.

4. Скачайте последний SuperSu (BETA) с [XDA_Chainfire post](https://forum.xda-developers.com/apps/supersu/2014-09-02-supersu-v2-05-t2868133) в папку `/superSu`.

5. Поместите скаченный или собраный zip архив с NetHunter в директорию `/kaliNethunter`.

ЗАМЕЧАНИЕ: Не переиеновывайте и не распаковывайте скаченные архивы и образы. Скрипты все сделают за вас.


###2.1 Разблокировка загрузчика(OEM Unlock):
--------------------
Если ваше устройство ещё не разблокированно, вы можете использовать этот скрипт. Пропустите этот шаг, если bootloader на вашем устройстве уже разблокирован.

Скрипт запускается так: `./oemUnlock.sh`.

###2.2 Установка стокового образа:
---------------------
####Nexus Flash Stock (Только для устройств Nexus)
Чтобы накатить чистый образ системы на ваш Nexus, используйте скрипт `stockNexusFlash.sh`. **Это удалит все существующие на устройстве данные**.

Запусйкате скрипт так: `./stockNexusFlash.sh`.

После установки образа и первоначальной настройки, не забудьте включить Режим Разработчика, USB-отладку и принять RSA ключ от компьютера.

####OPO Flash Stock (Только для устройств OnePlus One)
Чтобы накатить чистый образ системы на ваш OnePlus One используйте `stockOpoFlash.sh`.  **Это удалит все существующие на устройстве данные**.

Для версии на 16гб запускайте скрипт так: `./stockOpoFlash.sh 16gb`, для 64гб так: `./stockOpoFlash.sh 64gb`.

После установки образа и первоначальной настройки, не забудьте включить Режим Разработчика, USB-отладку и принять RSA ключ от компьютера.


###2.3 Custom Recovery (TWRP) + SuperSU (root) + Kali Linux NetHunter
--------------------
Этот скрипт установит TWRP, SuperSU, и накатит Kali NetHunter на ваше Nexus или OnePlus устройство. Он должен запускаться поверх установленного чистого образа системы.

Запускайте скрипт так: `./twrpFlash.sh`.

Если вы используете Aroma-версию архива Kali Linux NetHunter, то потребуется провести небольшие манипуляции с установщиком. Скрипт завершит установку и перезагрузит устройство.

##2.3.b От меня

При использовании Aroma-исталлера прогрес бар может повиснуть на 90% с надписью

> Checking for Kali Chroot in installer...this can take a while...

И оно настолько занимает *a while*, что конца вы не дождётесь. [Говорят](https://github.com/offensive-security/nethunter-LRT/issues/6), нужно подождать некоторое время и перезагрузиться.

Так и сделайте. После этого через Chroot Manager в NetHunter'е установите Kali Chroot и всё будет гуд.

##3. Наслаждайтесь свежеустановленным NetHunter

Возрадуйтесь! Вы только что успешно установили NetHunter на своё устройство и готовы к мировому господству. Зацените [советы после установки NetHunter (en)](https://github.com/offensive-security/kali-nethunter/wiki#50-post-installation-setup) для начала.

**DISCLAIMER**

> Пользуйтесь на здоровье, но никаких гарантий. These scripts are distributed in the hope that they will be useful, but WITHOUT ANY WARRANTY, to the extent permitted by law; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. Pull requests are welcome.