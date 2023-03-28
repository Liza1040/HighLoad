# Проектирование платформы для хостинга IT-проектов и совместной разработки (GitHub)

## 1. Тема и целевая аудитория
GitHub - это сервис для хостинга IT-проектов и их совместной разработки.

### MVP
- Работа с репозиторием: создание, клонирование, загрузка новых данных из репозитория, редактирование
- Открытие Pull Request
- Комментарии к Pull Request
- Открытие и закрытие Issues

### Целевая аудитория
Согласно [информации из официальных источников](https://github.com/about):
- 100+ миллионов активных (зарегистрированных) пользователей
- 424 миллиона посещений в месяц
- 14 миллионов пользователей в день (DAU)

#### Географическое распространение
- США - 15.39%
- Китай - 14.33%
- Индия - 8.67%
- Германия - 3.7%
- Япония - 3.6%

(Источник: [hypestat](https://hypestat.com/info/github.com))


## 2. Расчет нагрузки
### Продуктовые метрики
- 14 миллионов пользователей в день (DAU) ([hypestat])(https://hypestat.com/info/github.com)
- 424 миллиона посещений в месяц ([hypestat])(https://hypestat.com/info/github.com)
- Согласно [информации из официальных источников](https://github.com/about) на платформе создано 330 миллионов репозиториев. Предположим, каждый пользователь в среднем имеет 330 млн/100 млн=3.3 (округляя 3) репозитория. Из них один имеет наибольший размер ~40 Мб, а остальные в среднем ~2 Мб. Тогда средний размер репозитория 14.7 Мб.

Кроме того, в репозитории хранятся:
  - Pull Request. В результате опроса знакомых было выявлено, что один пользователь создает около 2 Pull Request в неделю, а каждый репозиторий имеет в среднем 5 Pull Request ~ 100 байт;
  - Issues. В результате опроса знакомых было выявлено, что один пользователь создает около 2 Issues в месяц, а каждый репозиторий имеет в среднем 4 Issues ~ 512 байт (считаем, что размер одного 128 байт);
  - Комментарии к Pull Request и Issues. В результате опроса знакомых было выявлено, что в одном репозитории около 10 комментариев, а средняя длина комментария 85 символов ~ 850 байт;

В результате получаем, что средний размер хранилища пользователя ~ 44 Мб

#### Среднее количество действий пользователя по типам:
Средние значения подсчитаны в результате опроса группы людей.
| Тип действия  | Количество/период  |
| ------------- |:------------------:|
| Создание репозитория | 3 в месяц   |
| Клонирование репозитория | 4 в месяц |
| Загрузка новых данных из репозитория (git pull) | 3 в день |
| Редактирование репозитория | 5 в день|
| Открытие Pull Request | 2 в неделю|
| Просмотр Pull Request | 1 в день|
| Открытие Issue | 2 в месяц|
| Просмотр Issue | 1 в неделю|
| Добавление комментария | 2 в день|
| Просмотр комментария | 2 в день|

### Технические метрики
#### Размер хранилища
1. Репозиториеи: 330 000 000 * 14.7 Мб = 4851 Тб
2. Pull Requests: 330 000 000 * 5 * 20 байт = 33 Гб
3. Issues: 330 000 000 * 4 * 128 байт = 169 Гб
4. Комментарии: 330 000 000 * 10 * 85 байт = 280.5 Гб

#### RPS и сетевой трафик
Определим коэффициент для перевода RPS на одного человека в нагрузку на дневную аудиторию:
```
k = 14 000 000 / 24 * 60 * 60 = 162 пользователя в секунду
```
1. Создание репозитория

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 3.9 |3*k/30=16.2 | 63.2 Кб/с |

2. Клонирование репозитория

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| GET | 15340 | 4*k/30=21.6 | 331 Мб/с |

3. Загрузка новых данных из репозитория (git pull)

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| GET | 3.3 | 3*k=486 | 1.6 Мб/с |

4. Редактирование репозитория

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 3.3 | 5*k=810 | 2.7 Мб/с |

5. Открытие Pull Request

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 3.4 | 2*k/7=46.3 | 157 Кб/с |

6. Просмотр Pull Request

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| GET | 46.6 | k=162 | 7.5 Мб/с |

7. Открытие Issues

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 3.4 | 2*k/30=10.8 | 36.7 Кб/с |

8. Просмотр Issues

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| GET | 36.4 | k/7=23.1 | 841 Кб/с |

9. Добавление комментария

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 18.9 | 2*k=324 | 6 Мб/с |

10. Просмотр комментария

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 95.5 | 2*k=324 | 31 Мб/с |

#### В итоге

| Действие  | RPS  | Трафик |
| -------|:----------:|:-------:|
| Создание репозитория | 16.2 | 63.2 Кб/с |
| Клонирование репозитория | 21.6 | 331 Мб/с |
| Загрузка новых данных из репозитория (git pull) | 486 | 1.6 Мб/с |
| Редактирование репозитория | 810 | 2.7 Мб/с |
| Открытие Pull Request | 46.3 | 157 Кб/с |
| Просмотр Pull Request | 162 | 7.5 Мб/с |
| Открытие Issues | 10.8 | 36.7 Кб/с |
| Просмотр Issues | 23.1 | 841 Кб/с |
| Добавление комментария | 324 | 6 Мб/с |
| Просмотр комментария | 324 | 31 Мб/с |
| Общие значения | 2224 | 0.379 Гб/с |
| При пиковой нагрузке, х2 | 4448 | 0.758 Гб/с |

## 3. Логическая схема
<kbd>
  <img src="/images/logic_diagram_bd.jpg" alt="Логическая схема БД">
</kbd>
