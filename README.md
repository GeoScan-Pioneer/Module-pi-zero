# Aerophotography

## Список задач
* Образ raspberry
* как запустить все без образа
* отказаться от hyper
* Починить модели и держать их в step
* убрать тестовые скрипты


## Описание
Описание реализации АФС с помощью квадрокоптера Пионер

---

## Список необходимых программ
* [Pioneer Station](https://pioneer-doc.readthedocs.io/ru/master/programming/pioneer_station/pioneer_station_main.html)
    * Данная программа служит для загрузки управляющих скрирптов на управляющую плату квадрокоптера
* [Agisoft Metashape](https://www.agisoft.com/downloads/installer/)
    * Сшивание фотографий в один общий план
* [WinSCP](https://winscp.net/eng/download.php)
    * Скачивание/загрузка файлов с raspberry pi zero w. Данная программа лишь рекомендация и
    весь ее функционал можно выполнить через Windows PowerShell. Дальнейшие действия будут рассмотрены
      через WinSCP.
  
---

## Правила реализации АФС

АФС представляет из себя процесс полета квадрокоптера по необходимой траектории с
созданием фотографий с необходимым перекрытием по горизонтали и вертикали.

---

## Алгоритм проведения АФС

1) Написание управляющей программы
2) Загрузка управляющей программы
3) Запуск программы на raspberry
4) Полет
5) Выгрузка фотографий с коптера
6) Обработка фото с помощью Metashape

### Написание управляющей программы

Для написания управляющей программы необходимо воспользоваться заготовкой для скрипта.

Данный скрип разделен на следующие блоки:
* Блок обработки работки светодиодов
  * В данном блоке объявляется количество светодиодов и функция их включения
Для включения вызывается функция ```changeColor(r, g, b)```.
  
* Блок работы с uart
  * Для создания фотографий по uart отправляется сообщение с 3мя координатами квадрокоптера 
    в данный момент. Чтобы реализовать АФС необходимо делать фотографии с определенной частотой,
    которая задается параметром ```local time_photo = 0.5```. Это время между созданием фотографий в секундах.
    Для начала создания фотографий необходимо 1 раз в начале полета вызвать ```timer_uart:start()```.
    Для оконачания создания фотографий необходимо вызвать в конце полета ```timer_uart:stop()```.
    
* Блок обработки движения
  * В блоке обработки движения описывается функция ```local function nextPoint()```, которая вызывается
    каждый раз после достижения точки полетного задания. Для реализации полета по точкам
    внутри вызывается функция ```ap.goToLocalPoint(x, y, z) ```
    
Для написнаия управляющей программы необходимо реализовать блок обработки движения и запустить\остановить
процесс создания фотографий.

### Загрузка управляющей программы
Загрузка управляющей программы происходит с помощью Pioneer Station.
[Пример загрузки управляющей программы](https://pioneer-doc.readthedocs.io/ru/master/programming/pioneer_station/pioneer_station_upload.html)

### Запуск программы на raspberry
Для старта программы на raspberry необходимо подключиться к ней и выполнить команду ```python3 aero.py```

Для подключения необходимо открыть консоль Windows PowerShell (можно ввести в поисковике в пуске) 
и ввести ```ssh pi@"ip raspberry"```, где "ip raspberry" берется с экрана квадрокоптера. 
Например, необходимо ввести ```ssh pi@192.168.1.24```.
Далее нужно будет ввести пароль ```raspberry```.

### Полет
Для проведения полета необходимо выполнить следующие действия:
* Подключить аккумуляторную батарею и связать коптер с пультом управления;
* Запустить Lua скрипт на каждом коптере;
* На пульте опустить тумблер SWB в самое нижнее положение (полет по программе);
* Опустить тумблеры SWA и SWD.

### Выгрузка фотографий с коптера
После завершения потела необходимо выгрузить фотографии с raspberry pi zero w на компьютер.
Если используется компьютер под управлением ОС windows, то необходимо воспользоваться дополнительной программой.
Алгоритм выгрузки фотографий:
* Запустить WinSCP
* Соединение -> Новое соединение
* Ввести следующие значения:
  * Имя хоста: "ip raspberry (отображается на экране коптера)"
  * Имя пользователя: pi
  * Пароль: raspberry
  
Далее нужно выделить необходимые файлы и перетащить в удобную для себя папку на компьютере.

### Обработка фото с помощью Metashape
[руководство по работе в программе Metashape](https://www.agisoft.com/pdf/metashape-pro_1_7_ru.pdf)