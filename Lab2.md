# АНАЛИЗ ДАННЫХ В РАЗРАБОТКЕ ИГР[in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Майер Наталья Сергеевна
- РИ230940
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
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Научиться передавать в Unity данные из Google Sheets с помощью Python.

## Задание 1
### Выберите одну из игровых переменных в игре СПАСТИ РТФ: Выживание (HP, SP, игровая валюта, здоровье и т.д.), опишите её роль в игре, условия изменения / появления и диапазон допустимых значений. Постройте схему экономической модели в игре и укажите место выбранного ресурса в ней.
Ход работы:
- Поиграть в СПАСТИ РТФ: Выживание, проследить, как меняются переменные
- Выбрать одну переменную и проанализировать ее
- Построить схему экономической модели

**Выбранная переменная:** RTF Coin

**Роль в игре:** Игровая валюта необходима для покупок

**Условия получения:**
-Убийство зомби
-Просмотр рекламы
-Оценка игру

**Условия траты:**
-Покупка в магазине (оружие, патроны, аптечка)

**Диапазон значений:**
От 0 до бесконечности. За просмотр одной рекламы +100 монет без ограничений раз, оценка игры +200 монет единоразово.

![image](https://github.com/user-attachments/assets/0295b0cf-9693-40de-8afa-55fd486a4083)

## Задание 2
### С помощью скрипта на языке Python заполните google-таблицу данными, описывающими выбранную игровую переменную в игре “СПАСТИ РТФ:Выживание”. Средствами google-sheets визуализируйте данные в google-таблице (постройте график / диаграмму и пр.) для наглядного представления выбранной игровой величины. Опишите характер изменения этой величины, опишите недостатки в реализации этой величины (например, в игре может произойти условие наступления эксплойта) и предложите до 3-х вариантов модификации условий работы с переменной, чтобы сделать игровой опыт лучше.

Ход работы: 
- Настроить доступ к google-таблице по api
- Генерация данных с помощью Python и их передача в google таблицу
- Визуализация данных
- Анализ

```py

import gspread
import numpy as np
gc = gspread.service_account(filename='sonorous-haven-443418-d5-1226f14308ad.json')
sh = gc.open("Lab2").sheet1
coins = np.random.randint(0, 50000, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        difference = coins[i-1]-coins[i-2]
        difference = str(difference)
        difference = difference.replace('.',',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(coins[i-1]))
        sh.sheet1.update(('C' + str(i)), str(difference))
        print(difference)

```
![coins](https://github.com/user-attachments/assets/43a654bb-4c26-449e-b989-6a5f285a453a)

Характер изменения переменной и недостатки:
Игрок начинает игру с 0 монет, по мере прохождения игры RTF coins накапливаются. При чем это довольно легко происходит (бесконечная реклама + постепенно увеличивающиеся волны зомби). Они тратятся на различные улучшения и патроны, однако улучшения конечны, поэтому со временем игроку приходится тратиться только на патроны. Это значительно снижает интерес к игре.
Варианты модификации:
Добавление в магазин новых предметов
Ограничение на количество рекламы
Увеличение стоимости нового оружия

## Задание 3
### Настройте на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения выбранной переменной. Например, если выбрано здоровье главного персонажа вы можете выводить сообщения, связанные с его состоянием.

Ход работы:
- Создать новый 3D проект на Unity
- Добавить в проект jsonPackage и soundPackage
- Создайть на сцене Unity пустой GameObject и подключить к нему .cs скрипт, в котором описывается подключение к google-таблице по API и воспроизведение звуков в игре
- Подключить файлы для воспроизведения
  
```cs
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] >= 0 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }


        if (dataSet["Mon_" + i.ToString()] < 0 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1A6Eftcj_AYvhpNMqOPegh-c3ze1ZvSYu0zBOJB8mqWw/values/Лист1?key=AIzaSyDMl6ehKmc1V0Y-epXRxR5EJokJuzer0ig");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(2);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
}
```

## Выводы
В ходе работы я узнала, как генерировать данные с помощью Python и передавать их в google таблицу, как настроить на сцене Unity воспроизведение звуковых файлов, описывающих динамику изменения переменных.



| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
