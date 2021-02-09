---
layout: post
title: Aqara relay (LLKZMK11LM). Схема платы с реле.
---
![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/relay_fon_ii.png) 
Продолжение исследования устройства Aqara реле... 

План работ остается прежним:
1. [Восстановление схемы платы с контроллером;]({{ site.baseurl }}/aqara_relay_mcu/)
2. [Восстановление схемы платы с реле;]({{ site.baseurl }}/aqara_relay_pwr/)
3. Исследование девайса со стороны zigbee (ep, кластера и т.п.);
4. Извлечение прошивки из JN5169;
5. Исследование измерительной микросхемы PL7211;

### Новые возможности
Восстанавливая схему, обнаружил несколько интересных особенностей устройства о которых ранее не знал: 
1. Широкий диапазон питающего напряжение (от 15 вольт). Я провел эксперименты с источником питания 24В постоянного тока. 
	N - плюс, L - общий. Устройство уверенно запускается. Вторичные напряжения составляют 5.2В и 3.3В.   
	В момент переключения реле (для поляризованного реле это единственный момент для потребления тока) нет провала вторичных напряжений.   
	Надо еще провести климатические испытания, особенно при минусовой температуре. Сфера применения этого устройства, в оригинальном виде, расширяется. Вот только напряжение и ток измеряются некорректно.    

2. Входы для кнопок S1 и S2 не имеют аппаратной 'заточки' быть только входами. В альтернативной прошивке их можно использовать как дискретные выходы, PWM, а при небольшой аппаратной доработке — как ЦАП.    
	
3. JN5169 можно прошить через разъем J6 на плате с реле. Это удобнее чем через разъем JP2 на плате процессора. К тому же на J6 выведены сигналы для взаимодействия с PL7211.   
	
4. PL7211 имеет ряд незадействованных GPIO. Я еще не вникал в особенности работы с этой микросхемой, но вероятно этими GPIO можно рулить из JN5169. Это так же расширяет функционал для альтернативной прошивки.  
	
	
Так же на [схеме](#sch) видно как организована измерительная часть тока/напряжения и для чего нужна красная перемычка.   
Некоторые элементы на схеме имеют серый фон. Это значит что они небыли смонтированы на плате. Возможно тип этих компонентов опознан некорректно. 

Интересна надпись ***Frank&Will*** на слое Top. Похоже на пасхалку. Возможно это как-то связано с [этим человеком](https://fr.wikipedia.org/wiki/Frank-Will). 
### Стек слоёв

Плата двухслойная. Толщина платы 1.5 мм. Ниже представлены сканы каждого слоя расположенных 'на просвет' (слой bottom отзеркален). 

*  [Top components](#lay0);
*  [Top mask, silk](#lay1);
*  [Top](#lay2);
*  [Bottom](#lay3);
*  [Bottom mask](#lay4);
*  [Bottom components](#lay5);
<a name="lay0"></a>

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/0_top_comp.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/0_top_comp.jpg) Top components.
<a name="lay1"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/1_top_silk.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/1_top_silk.jpg) Top mask, silk.
<a name="lay2"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/2_top_cup.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/2_top_cup.jpg) Top.
<a name="lay3"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/3_bot_cup.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/3_bot_cup.jpg) Bottom.
<a name="lay4"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/4_bot_silk.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/4_bot_silk.jpg) Bottom mask, silk.
<a name="lay5"></a>

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/layer/5_bot_comp.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/layer/5_bot_comp.jpg) Bottom components.
<a name="lay6"></a> 

### Схема
<a name="sch"></a>
[В формате pdf]({{ site.baseurl }}/images/aqara-relay-part2/aqara_relay_sch_pwr_v1.pdf)
[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/aqara_relay_sch_pwr_v1.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/aqara_relay_sch_pwr_v1.png)
Схема.

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/top_assembly.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/top_assembly.jpg)
Сборочный чертеж. Сторона top.

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/bot_assembly.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/bot_assembly.jpg)
Сборочный чертеж. Сторона bottom.
### Остальная документация
* [Gerber;]({{ site.baseurl }}/images/aqara-relay-part2/aqara_pwr_gerber.zip)
* [BOM;]({{ site.baseurl }}/images/aqara-relay-part2/bom_aqara_relay_pwr1.pdf)
* [Все файлы одним архивом;]({{ site.baseurl }}/images/aqara-relay-part2/aqara-relay-part2_all.zip)

Gerber файлы я пока не проверял в железе. DRC запускал с допусками JLCPCB.   

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/top_3d.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/top_3d.jpg)
Сторона top.

***

[![_config.yml]({{ site.baseurl }}/images/aqara-relay-part2/bot_3d.jpg)]({{ site.baseurl }}/images/aqara-relay-part2/bot_3d.jpg)
Сторона bottom.
