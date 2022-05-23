---
layout: post
title: Программное восстановление ZigBee устройств Aqara/Xiaomi
---
![_config.yml]({{ site.baseurl }}/images/jn5169-monitor-repair/jn5169-mon-rep-logo.png)  
Замечено, что некоторые устройства (роутеры) подвержены программному сбою. Перезагрузка или удерживание кнопки для входа/выхода из сети проблемы не решает. Признаками программного сбоя могут быть:
 * Индикация присутствует, нет реакции на кнопку (клавиши, входы);
 * Устройство не подключается к сети;
 * Индикация отсутствует, нет реакции на кнопку. По "железу" полный порядок;

Ранее была опубликована статья по ремонту привода штор. ([ссылка]({{ site.baseurl }}/aqara_curtain/)) Способ универсальный, стирающую прошивку можно заливать в другие устройства, но это более трудоемкий процесс по сравнению с описываемым.  
В версии ```May 17 2022``` [**JN5169-monitor**](https://github.com/re-engr/jn5169-monitor) добавлены функции: стирания PDM, стирание EEPROM и восстановление ***MAGIC NUMBER***. Подробно как работает monitor написано [здесь]({{ site.baseurl }}/jn5169-fw-lock/#Wire_exploit). 

Стирание PDM и стирание EEPROM различаются тем, что первая производит очистку памяти функциями "файловой системы", а вторая стирает память вместе с "файловой системой".  

Функция восстанволения ***MAGIC NUMBER*** нужна для возврата работоспособности девайса без процедуры перепрошивки памяти микроконтроллера JN5169.

### Шаги
 1. Разберите устройство и подключите USB-UART адаптер к пинам порта. Распиновку для некоторых устройств можно найти [здесь](https://github.com/re-engr/zigbee_firmware/);
 2. Переведите JN5169 в режим bootloader замкнув вывод /BOOT на GND и подав питание;
 3. Скачайте проект [**JN5169-monitor**](https://github.com/re-engr/jn5169-monitor);
 4. В папке `/wire` найдите скрипт `upload.py`. Откройте его в текстовом редакторе и укажите правильный номер порта (port='COMx',\);
 5. Запустите скрипт. При правильном подключении и настройке Вы увидите следующий результат:
  [![_config.yml]({{ site.baseurl }}/images/jn5169-monitor-repair/jn5169-mon-load.png)](/images/jn5169-monitor-repair/jn5169-mon-load.png)  
 6. Перезагрузите устройство в нормальный режим (высокий уровень /BOOT);
 7. Подключитесь к последовательному порту с помощью Putty или подобной программы. Параметры порта ```115200 8n1```;
 8. Нажмите Enter. Вы увидите сообщение:
  [![_config.yml]({{ site.baseurl }}/images/jn5169-monitor-repair/jn5169-mon-start.png)](/images/jn5169-monitor-repair/jn5169-mon-start.png)  
 9. Нажмите на клавиатуре последовательно .p (символ точка, символ 'p'). Эта команда выполнит стирание PDM.
  [![_config.yml]({{ site.baseurl }}/images/jn5169-monitor-repair/jn5169-mon-p.png)](/images/jn5169-monitor-repair/jn5169-mon-p.png) 
 10. Нажмите на клавиатуре последовательно .r (символ точка, символ 'r'). Эта команда вернет устройство в рабочее состояние. 
  [![_config.yml]({{ site.baseurl }}/images/jn5169-monitor-repair/jn5169-mon-r.png)](/images/jn5169-monitor-repair/jn5169-mon-r.png)  

Если после перезагрузки устройство не заработало, то либо это был не программный сбой, либо в процессе восстановления что-то пошло не так.  В любом случаи обращайтесь в [Telegram-чат](https://t.me/reengrru), постараемся помочь. 
