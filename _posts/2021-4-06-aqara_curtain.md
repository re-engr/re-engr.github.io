---
layout: post
title: Ремонт электропривода для штор Aqara Intelligent Curtain Motor (ZNCLDJ11LM)
---
![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_logo.jpg) 
Программный ремонт устройства с очисткой PDM и перепрошивкой. 

Из-за бага в оригинальной прошивке, существует вероятность получить неработоспособный прибор при попытке переподключить его к zigbee сети.  

Из-за некорректных значений в PDM, электропривод не инициирует процедуру сопряжения с zigbee сетью, хотя присоединение разрешено и емкость сети позволяет принимать новые устройства. При этом синий светодиод на электроприводе индицирует процесс поиска сети в течении отведенного для этого времени.
  
Если проанализировать трафик захваченный сниффером, то видно что устройство рассылает *Beacon Request*, получает ответ в котором *Association Permit = True*, *Router Capacity = True*, *End Device Capacity = True*, но игнорирует его и через несколько секунд отправляет *Beacon Request* снова. 

![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_sniffer.png) 

Для того что бы вернуть устройство в рабочий вид, необходимо очистить EEPROM в контроллере JN5169. Из-за установленного Lock-бита , который не позволяет получить доступ к EEPROM из бутлоадера, нужно в начале загрузить прошивку которая очистит EEPROM "изнутри" и только после этого можно записывать рабочую прошивку электропривода. 

#### Шаг 1
Извлечь плату с контроллером, отсоединив её от устройства.

#### Шаг 2 
В соответствии с распиновкой подключить UART преобразователь с уровнем сигнала TX--  +3.3 вольта. Оптимально использовать USB-UART преобразователь с источником на 3.3 вольта для того что бы запитать плату.

Сигнал /BOOT необходимо подключить к GND. Учитывайте что на определенных шагах этот сигнал должен быть отключен от GND.

GND -- GND  
+3.3V -- +3.3V  
Rx -- TX  
TX -- RX  
/BOOT -- GND  
RST -- nc

![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_pinouts.png) 

#### Шаг 3
[Скачать архив]({{ site.baseurl }}/images/aqara-curtain-repair/doc/lumi_curtain_rep_files.zip). Он содержит утилиту [JN51xx Production Flash Programmer](https://www.nxp.com/pages/jn516x-zigbee-home-automation:ZIGBEE-HOME-AUTOMATION), [прошивки](https://github.com/re-engr/zigbee_firmware/tree/main/aqara/curtain) и bat-файлы для запуска прошивальщика. 

#### Шаг 4
Подключить UART преобразователь к компьютеру.  
Подать питание на плату электропривода, если используется внешний БП.
В файле prog_erase.bat (и prog_curtain.bat) указать COM-порт к которому подключен преобразователь (в оригинальном файле указан COM5). Запустить скрипт.  
Так же процесс может быть запущен командой: 
```
JN51xxProgrammer.exe -V 1 -s COM5 -P 115200 -f lumi_curtain_erase_pdm_v1.bin
```
Процесс прошивки 
![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_flash_progress.png) 

Успешное завершение процесса
![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_flash_complete.png) 

#### Шаг 5
Обесточить плату. Отключить от GND сигнал /BOOT. Подать питание на плату. Красный светодиод должен мерцать (частота 20 Гц). 
Если это так, можно двигаться дальше (шаг 6). Отсутствие свечения красного светодиода, либо его ровное (постоянное) свечение, без мерцания, говорит о какой-то ошибке. Рекомендую начать с шага 2 внимательно повторяя все что написано.

#### Шаг 6
Обесточить плату. Сигнал /BOOT подключить к GND. Подать питание на плату. Запустить prog_curtain.bat
Так же процесс может быть запущен командой: 
```
JN51xxProgrammer.exe -V 1 -s COM5 -P 115200 -f lumi_curtain_0034_07172020.bin
```

На вопрос ответ "Y"
![_config.yml]({{ site.baseurl }}/images/aqara-curtain-repair/aqara_curtain_flash_y.png) 

Процесс прошивки такой же как на шаге 4, только длиться в 2.5 раза дольше.

#### Шаг 7
Обесточить плату. Отключить от GND сигнал /BOOT. Так же можно отключить UART преобразователь так как процесс восстановления закончен.

#### Шаг 8
Проверить работоспособность электропривода подключив его к zigbee сети.  



По мере выхода новых прошивок для электропривода будет обновляться [репозиторий](https://github.com/re-engr/zigbee_firmware/tree/main/aqara/curtain). 