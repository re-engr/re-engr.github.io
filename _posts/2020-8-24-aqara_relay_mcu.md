---
layout: post
title: Aqara relay (LLKZMK11LM). Схема платы с контроллером.
---
![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/relay_fon.png) 
Исследуемое устройство состоит из двух плат. Назову их "плата с контроллером" и "плата с реле". Эта статья будет посвящена первой плате. 

Я планирую цикл статей по реверс-инжинирингу обозначенного устройства. План следующий:
1. [Восстановление схемы платы с контроллером;]({{ site.baseurl }}/aqara_relay_mcu/)
2. Восстановление схемы платы с реле;
3. Исследование девайса со стороны zigbee (ep, кластера и т.п.);
4. Извлечение прошивки из JN5169;
5. Исследование измерительной микросхемы PL7211;

### Стек слоёв

Плата состоит из 4 слоёв. Толщина платы 0.8 мм. Ниже представлены сканы каждого слоя расположенных "на просвет" (слой bottom отзеркален). Изображения слоёв с компонентами ([первое](#lay0) и [последнее](#lay7) фото) получены на плате с другой датой производства, но ревизия та же. 

*  [Top components](#lay0);
*  [Top mask, silk](#lay1);
*  [Top](#lay2);
*  [Int 1](#lay3);
*  [Int 2](#lay4);
*  [Bottom](#lay5);
*  [Bottom mask](#lay6);
*  [Bottom components](#lay7);
<a name="lay0"></a>

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/0_top_comp.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/0_top_comp.jpg) Top components.
<a name="lay1"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/1_top_mask_silk.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/1_top_mask_silk.jpg) Top mask, silk.
<a name="lay2"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/2_top.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/2_top.jpg) Top.
<a name="lay3"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/3_int2.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/3_int2.jpg) Int 1.
<a name="lay4"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/4_Int3.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/4_Int3.jpg) Int 2.
<a name="lay5"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/5_bot.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/5_bot.jpg) Bottom.
<a name="lay6"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/6_bot_mask_silk.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/6_bot_mask_silk.jpg) Bottom mask.
<a name="lay7"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/layer/7_bot_comp.jpg)]({{ site.baseurl }}/images/aqara-relay-part1/layer/7_bot_comp.jpg) Bottom components.

### Схема

Номиналы компонентов измерены прибором [Smart Tweezers Bluetooth ST5S-BT](https://www.smarttweezers.com/colibri-pro-specs/). 

[В формате pdf]({{ site.baseurl }}/images/aqara-relay-part1/aqara_relay_mcu_sch1.pdf)
[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/aqara_relay_mcu_sch1.png)]({{ site.baseurl }}/images/aqara-relay-part1/aqara_relay_mcu_sch1.png)
Схема.

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/top_pos.png)]({{ site.baseurl }}/images/aqara-relay-part1/top_pos.png)
Сторона top.

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part1/bot_pos.png)]({{ site.baseurl }}/images/aqara-relay-part1/bot_pos.png)
Сторона bottom.
### Остальная документация
* [Gerber;]({{ site.baseurl }}/images/aqara-relay-part1/aqara_relay_mcu1.zip)
* [BOM;]({{ site.baseurl }}/images/aqara-relay-part1/bom_aqara_relay_mcu1.pdf)
* [Все файлы одним архивом;]({{ site.baseurl }}/images/aqara-relay-part1/aqara-relay-part1_all.zip)

Основная задача - это восстановление схемы. Восстановление трассировки платы выполнялось в оставшееся время и до какого-то разумного предела. Работоспособность в железе я не проверял. Учитывайте это если вдруг вы решите повторить устройство. 