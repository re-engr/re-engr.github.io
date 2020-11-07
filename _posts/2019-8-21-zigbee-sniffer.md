---
layout: post
title: ZigBee sniffer
---

Для просмотра радиоканала использую CC2531 + Wireshark. Опишу процесс настройки.

#### 1. Железо
![_config.yml]({{ site.baseurl }}/images/zigbee-shiffer/300px-Cc2531-usb.png)

Что бы прошить CC2531 нужен CC-Debuger. 

#### 2. Программное обеспечение
1. [Драйвер для CC-Debuger и СС2531 Sniffer;](http://www.ti.com/lit/zip/swrc212)
2. [Программа для работы с CC-Debuger;](http://www.ti.com/tool/flash-programmer)
3. [Прошивка для CC2531 (входит в состав Packet Sniffer, старая версия);](http://www.ti.com/tool/packet-sniffer)
4. [Программа для связи железа с Wireshark (TiWsPc);](http://www.ti.com/tool/timac)
5. [Wireshark;](https://www.wireshark.org/)

#### 3. Прошивка
Устанавливаем все вышеперечисленное. 
Подключить CC2531 к CC-Debuger, подать питание на плату. Если все сделано правильно, 
то нажатие на кнопку RESET, на программаторе, приведет к включению зеленого светодиода расположенного рядом с кнопкой. 
Далее:
Запустить *\Texas Instruments\SmartRF Tools\Flash Programmer\bin\SmartRFProg.exe*
Выбрать файл прошивки *\Texas Instruments\SmartRF Tools\Packet Sniffer\bin\general\firmware\sniffer_fw_cc2531.hex*
В поле Actions выбрать Erase, program and verify, нажать кнопку Perform actions;
Если чип правильно определяется программатором и в ПО, но при прошивке вылазит ошибка, то попробуйте в начале выполнить действие Erase и после этого пункт 3. 
Драйвер на новое устройство, CC2531 Sniffer, устанавливается из той же папки что и драйвер для CC-Debuger.

#### 4. TiWsPc
Программа создает pipe для трансляции данных от CC2531 в Wireshark.
Запустить TiWsPc.exe. Нажать Device Configuration. Должно быть такое окно

![_config.yml]({{ site.baseurl }}/images/zigbee-shiffer/tis1.png)

В поле Configuration выбирается номер канала для прослушивания. Для примера 11.
Нажать Start. Run на зеленом фоне говорит об успехе. Окно конфигурации можно закрыть. В основном окне нам понадобится строка \\.\pipe\tiwspc_data , позже её нужно будет внести в настройки Wireshark.

#### 5. Настройка Wireshark

Запускаем Wireshark.exe. Переходим в Edit / Preferences... / Protocols / ZigBee / Edit / 
В поле Security Level выбрать AES-128, 32 bit
Нажать кнопку + и внести ключ **5A:69:67:42:65:65:41:6C:6C:69:61:6E:63:65:30:39**
Сохранить все изменения.

![_config.yml]({{ site.baseurl }}/images/zigbee-shiffer/z_key.png)

Это стандартный ключ, в некоторых случаях могут быть другие.

С главного окна Wireshark переходим Capture / Options... / Manage Interfaces... /  Pipes 
Нажать кнопку +. Будет создан канал с названием New Pipe. Переименовать его в *\\.\pipe\tiwspc_data* (из TiWsPc). Сохранить изменения и перейти в главное окно. 

![_config.yml]({{ site.baseurl }}/images/zigbee-shiffer/p1.png)

В качестве объекта захвата (Capture) выбрать созданный pipe и нажать старт (Ctrl+E).
Устройство готово к работе.

#### 6. Общая информация

В окне вывода Wireshark ничего не будет если на выбранном канале (TiWsPc) нет активности.

Помимо стандартного ключа, внесенного в Wireshark, есть ещё один ключ который выдается устройствам при подключении к координатору. (чаще всего так, но могут быть варианты) Wireshark умеет самостоятельно перехватывать этот ключ и применять его для расшифровки последующих сообщений. После перезапуска Wireshark перехваченный ключ будет утерян, так его лучше сохранить в случае длительной работы с созданной сетью. 

Пример вывода 

![_config.yml]({{ site.baseurl }}/images/zigbee-shiffer/log1.png)

На тему настройки Wireshark есть видео от Freescale.

После перезапуска Wireshark необходимо заново добавлять pipe. Ключ добавлять не нужно, он сохраняется. 
Либо можно запускать Wireshark так (взято из документа \Texas Instruments\TiWsPc\ReadMe.rtf):

*2b. Start Wireshark with a modified shortcut that specifies the pipe.
**"C:\<your path here>\wireshark.exe" -i\\.\pipe\tiwspc_data -k**
You must include the -i and -k options
This is the preferred way to use a pipe with Wireshark.*