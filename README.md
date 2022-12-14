# Создание индивидуальной системы достижения пользователя и ее интеграция в пользовательский интерфейс
Отчет по лабораторной работе #5 выполнила:
- Завьялова Екатерина Николаевна
- РИ300023
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения.
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения.
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения.
- Выводы.

## Цель работы
создание интерактивного приложения с рейтинговой системой пользователя и интеграция игровых сервисов в готовое приложение.

## Задание 1
### Используя видео-материалы практических работ 1-5 повторить реализацию приведенного ниже функционала:
1) Практическая работа «Интеграции авторизации с помощью Яндекс
SDK»
2) Практическая работа «Сохранение данных пользователя на платформе
Яндекс Игры»
3) Практическая работа «Сбор данных об игроке и вывод их в интерфейсе»
4) Практическая работа «Интеграция таблицы лидеров»
5) Практическая работа «Интеграция системы достижений в проект»

Ход работы:
1) Создать авторизацию пользователя на платформе.

Нужно написать скрипт менеджера и повесить его на объект сцены.

```c#
public class YGManager : MonoBehaviour
{
    private void OnEnable() => YG.YandexGame.GetDataEvent += CheckAutorisation;
    private void OnDisable() => YG.YandexGame.GetDataEvent -= CheckAutorisation;


    void Start()
    {
        if (YG.YandexGame.SDKEnabled == true)
            CheckAutorisation();
    }

    private void CheckAutorisation()
    {
        if (!YG.YandexGame.auth)
        {
            YG.YandexGame.AuthDialog();
        }
    }
}

```

![Фото]()

2) Сделать сохранение данных игрока.

Отыскать класс Яндекса с информацией игрока. Добавлю необходимые мне переменные в этот скрипт.

```c#
namespace YG
{
    [System.Serializable]
    public class SavesYG
    {
        public bool isFirstSession = true;
        public string language = "ru";
        public bool feedbackDone;
        public bool promptDone;

        // Ваши сохранения
        public int RecordCount = 0;
        public string newPlayerName = "Hello!";
        public bool[] openLevels = new bool[3];
    }
}
```

Добавлю код с сохранением рекорда при окончании игры.

```c#
public static void EndGame()
    {
        if (GameCount > YG.YandexGame.savesData.RecordCount)
        {
            YG.YandexGame.savesData.RecordCount = GameCount;
            YG.YandexGame.SaveProgress();
        }
        
        GameCount = 0;
        LosePoints = 0;
    } 
```

3) Реализовать вывод сохраненных данных в интерфейсе

Создам UI элемент для вывода рекорда. При старте сцены сделаю обновление этого элемента.

```c#
Debug.Log(string.Format("Record: {0}", YG.YandexGame.savesData.RecordCount));
if (YG.YandexGame.SDKEnabled == true)
    UpdateRecord();
else
    text.text = "Рекорд не загружен";
```

Однако, если не добавить еще 1 строчку кода, то в меню будет висеть "Рекорд не загружен". 

```c#
private void OnEnable() => YG.YandexGame.GetDataEvent += UpdateRecord;
```

4) Реализовать таблицу лидеров.

Добавилю упоминание о таблице лидеров на сайте Яндекс Игр.

![Фото]()
![Фото]()

Добавлю эту строчку в скрипт с окончанием игры.

```c#
YG.YandexGame.NewLeaderboardScores("TopRecordCountPlayers", YG.YandexGame.savesData.RecordCount);
```

5) Добавить систему достижений

У меня будет всего 3 достижения, причем одно из них получается, если получены оба других. Создам 2 булевские переменные, которые будут храниться в облаке, а при загрузке сцены использоваться.

```c#
public class Achivements : MonoBehaviour
{    
    public GameObject GetGoldAchive;
    public GameObject GetWasGame;
    public GameObject GetAchives;

    void Start()
    {
        if (YG.YandexGame.savesData.isGetGold)
            GetGoldAchive.SetActive(true);
        if (YG.YandexGame.savesData.isWasGame)
            GetWasGame.SetActive(true);
        if (YG.YandexGame.savesData.isWasGame && YG.YandexGame.savesData.isGetGold)
            GetAchives.SetActive(true);
    }
}
```


## Задание 2
### Описать не менее трех дополнительных функций Яндекс SDK, которые могут быть интегрированы в игру.

Ход работы:
1) Прочитать документацию SDK и написать их

Вот что еще можно добавить в игру, используя SDK:
- Рекламные банеры
- Перевод на другие языки
- Таблицы лидеров
- Промоакции
- События, которые происходят при адблоке/читиринге
- Внутриигровые покупки

Ссылка на ЯндексИгры: https://yandex.ru/games/app/190276?draft=true&lang=ru.

## Задание 3
### Доработать стилистическое оформление списка лидеров и системы достижений, реализованных в задании 1.

Ход работы:
1) Улучшить интерфейс системы достижений.

После изменений все полученные достижения выглядят так.
![Фото]()


## Выводы
Я впервые поработала с таблицой лидеров, реализовала достижения и испарвила несколько багов. Вспомнила, как делать облачные сохранения. 

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
