---
layout: post
title: Ремонт Mitsubishi MDS-DH-CV-300
---
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/logo_MDS_DH_CV_300.jpg)


## Вступление

Это источник питания сервопривода. Принесли с проблемой - при включении часто загорается код 'E', вместо нормальной последовательности кодов 'AbC. Нормальный запуск носит случайный характер. 

Из документации становиться понятно что код 'E' сигнализирует о проблеме с памятью или АЦП. 
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/errE_mem_ad.jpg)

Запуская блок на столе мне удалось увидеть проблему. Примерно на 20 запусков с ошибкой 'E', приходится один с последовательностью кодов 'Ab' - это нормальный запуск. (код 'C' загорается когда есть связь по интерфейсу, у меня она отсутствовала). Ну что ж, проблема действительно носит плавающий характер.

Устройство состоит из платы управления и силовой части. Межплатное соединение осуществляется шлейфом. 
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/mitsu_0.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/mitsu_1.jpg)

В качестве управляющего контроллера используется TMS320F240. Анализ сигналов на шлейфе и тщательный осмотр силовой части дали понять что внимание надо сконцентрировать на плате управления (RK415D BN638A309G51).
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/mitsu_cpu_a.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/mitsu_cpu_b.jpg)


## Поиск проблемы 

Было проверено все по списку:
 * Целостность пайки;
 * Качество питания;
 * Стабильность опорного напряжения АЦП;
 * Сигналы с ОУ и на входах АЦП (встроен в контроллер);
 * Внешняя EEPROM;
 * Влияние температуры;

Все, кроме последнего пункта было в норме. Я заметил что при комнатной температуре бывают нормальные запуски, хоть и редко, а вот при температуре > 30°C - все запуски с ошибкой. Причем удалось локализовать "термозависимый" элемент - им оказался TMS320. Заморозка чипа [фризером](https://www.google.com/search?q=%D0%B0%D1%8D%D1%80%D0%BE%D0%B7%D0%BE%D0%BB%D1%8C+%D1%84%D1%80%D0%B8%D0%B7%D0%B5%D1%80&source=lmns&bih=676&biw=1280&client=safari&hl=ru&sa=X&ved=2ahUKEwjAzOzMw4TuAhUCuCoKHTgKBNUQ_AUoAHoECAEQAA) подтвердила догадку - проблема либо в RAM, либо в ПЗУ. Устройство с охлажденным чипом всегда запускается корректно. По мере прогрева TMS320, начинают появляться запуски с ошибкой. Возможным был вариант нарушения соединения внутри корпуса чипа, но, как правило, охлаждение фризером микросхем с памятью, позволяет выявлять в них сбойные ячейки. Не понятно только, пока, какая именно память дает сбой. 

## Инструментарий

Процессор не имеет Lock-бита. На плате имеется разведенный JTAG, но с не запаянным разъемом (P2). Распиновка стандартная:

![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/ti_jtag_14.jpg)

(Контакты 9, 11 замкнуты на стороне эмулятора)

Было прочитано много информации по этому процессору. Ввиду того что TMS320F240 является древним чипом, причем конкретно эта модель вынесена в отдельную категорию, поддержка со стороны софта прекратилась еще в начале 2000х годов. Многие источники информации были недоступны, некоторые приходилось просматривать через [архив интернета](https://web.archive.org). В конечном счете все оказалось просто. Далее я кратко и пошагово опишу процесс чтения прошивки и записи ее в новый проц.

### JTAG эмулятор
С TMS320F240 может работать только XDS510. Вариант с LPT интерфейсом является предпочтительным. С USB интерфейсом нужно совершить больше движений по софту. 

В процессоре может быть bootloader, в новом он точно есть, но его наличие зависит от разработчика. Его могли не оставить в рабочей версии прошивки. Я не стал тратить время на этот вариант чтения/записи прошивки. 

Судя по отзывам, китайские клоны XDS510 подходят для работы с процом, но я не проверял. 

Есть предложения от компании Sauris, в частности [SAU-XDS510-USB Lite](http://sauris.de/ru/zagruzki/?SECTION_ID=95&ELEMENT_ID=417). Без проблем эмулятор добавляется в CSS и через него считывается прошивка, но для записи F240 нужно самостоятельно написать драйвер. Утилита sauflash содержит документацию как это сделать, в поставке есть несколько примеров для других процессоров. 

Я же работал с [SDSP-PP](http://www.elkos.com.ua/setdsp/sdsppp.html). Является клоном XDS510PP.
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_a.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_b.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_c.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_bot.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_top.jpg)
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/xds510_schild.jpg)

### Чтение прошивки  
[Code Composer Studio version 3.3](https://software-dl.ti.com/dsps/forms/self_cert_export.html?prod_no=CCS_3.3.83.20_Platinum.zip&ref_url=https://software-dl.ti.com/dsps/dsps_public_sw/sdo_ccstudio/CCSv3/CCS_3_3/) - это последняя версия поддерживающая чип TMS320F240. CCS нужна только для получения прошивки из чипа.
Сохранять в формате COFF (расширение файла *.out). Пошаговая инструкция по сохранению была выложена на форуме ti.com[3].

А теперь главный вопрос, что же происходит в памяти при различных температурах? Я слил прошивку с горячего и холодного процессора (по несколько десятков раз) и получил 2 отличающихся файла. Адреса 0x4 и 0x5 относятся к заголовку файла в формате которого сохранена прошивка, по этому на это не обращайте внимание, а вот данные по адресу 0x1C2A - это содержимое прошивки. Видно что в байте по этому адресу первый бит поплыл. Правильное его состояние - 0.  
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/diff_fw_f240.jpg)

### Запись прошивки
Ввиду того что такой же новый процессор был доступен для заказа, я не стал экспериментировать с перезаписью старого. Допускаю что после перезаписи он бы успешно продолжил работу. 

Запись прошивки существенно отличается от её чтения, даже без учета того что это противоположные по логике операции. Как было указано выше, для чтения нужен только JTAG эмулятор и CCS 3.3. Причем в CCS уже есть нужный пункт меню, достаточно нажать пару кнопок. 

Для записи нужно загрузить в RAM, через JTAG, программу которая бы выполняла команды по программированию FLASH памяти. 
Ввиду того что памяти мало, программа разбивается на блоки:
 * Очистка памяти;
 * Стирание памяти;
 * Запись данных во FLASH;
 
Очистка памяти и стирание памяти - это различные операции. В одной из статей какого-то журнала указывалось что FLASH память перед операцией стирания (перевод всех байт в FF) нужно обязательно очистить (все байты в 00), иначе если стереть уже стертую память, её можно повредить. Так что неудивительно что память со временем крошится.

Программа поддерживающая TMS320F240 - [PRG2xx DOS Command Line Utility](https://secureservercdn.net/198.71.233.107/y4s.7d5.myftpupload.com/wp-content/uploads/files/drv/SetupPRG2xx_v332.zip)
Для работы с ней потребуется Windows XP или что-то постарше. Это вариант для LPT программатора.  

Устанавливается софт. Настраивается программатор, сохраняются настройки. Далее распаковать архив Algos/f240_02Jan2001.zip. 
Я заменил оригинальный файл L16K0.OUT на свой, прочитанный из холодного проца. Последовательно запустить *.bat файлы:
 1. BC0.BAT
 2. BE0.BAT
 3. BP16K.BAT

Верификацию сделал через чтение посредством CCS. Все.

Если программатор USB, то нужно действовать по [этой инструкции](http://spectrumdigital.com/sdflash-faq/#1.4). Здесь уже другой софт - SDFlash.

[Дамп FLASH]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/bin/MDSDHCV300_f240_cold.out)  
[Дамп EEP AT25020N]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/bin/at25020n.BIN)
## Разное

Если подавать питание (5В) только на плату с процессором, без силовой части, то на индикаторе отображается символ "A". Это происходит на битой прошивке и на рабочей. 

Помимо трех фаз на входные силовые клеммы, нужно завести две фазы на питание самого устройства. 
![_config.yml]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/power_input.jpg)

Видео запуска отремонтированного устройства. Для просмотра, кликните на картинку.
[![Запуск MDS-DH-CV-300.](https://img.youtube.com/vi/vfsXHPIJ0d0/0.jpg)](https://www.youtube.com/watch?v=vfsXHPIJ0d0 "Запуск MDS-DH-CV-300")

[Фото MDS_DH_CV_300](https://photos.app.goo.gl/b5tCzQpJs6hpbAmVA)
### Ссылки:

1. [TMS320F243 Flash programming problems](https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/106074)
2. [serial flash programming LF2407A](https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/110179)
3. [TMS320F240: How to download a .hex file to TMS320F241FNA](https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/712791)  
4. [CCS/TMS320F240: Unable to download file to TMS320F240 DSP](https://e2e.ti.com/support/microcontrollers/c2000/f/171/t/857081)  
5. [Эксперименты с Xilinx XC9572](https://igorkov.org/cpldexp)
6. [MDS-D/DH series instruction manual](https://dl.mitsubishielectric.com/dl/fa/document/manual/cnc/ib1500025/ib1500025engh.pdf)
7. [Различные документы и программы по теме. Накопились за время ремонта этого устройства]({{ site.baseurl }}/images/mitsubishi_mds_dh_cv_300/doc/MDS_DH_CV_300_files.zip)





