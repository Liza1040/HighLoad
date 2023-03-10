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
- Согласно [информации из официальных источников](https://github.com/about) на платформе создано 330 миллионов репозиториев. Предположим, каждый пользователь в среднем имеет 330 млн/100 млн=3.3 (округляя 3) репозитория. Из них один имеет наибольший размер ~40 Мб, а остальные в среднем ~2 Мб. Тогда средний размер репозитория 14.7 Мб. А средний размер хранилища пользователя ~44 Мб.

#### Среднее количество действий пользователя по типам:
| Тип действия  | Количество/период  |
| ------------- |:------------------:|
| Создание репозитория | 3 в месяц   |
| Клонирование репозитория | 4 в месяц |
| Загрузка новых данных из репозитория (git pull) | 3 в день |
| Редактирование репозитория | 5 в день|
| Открытие Pull Request | 2 в неделю|
| Открытие Issues | 2 в месяц|
| Добавление комментария | 2 в день|

### Технические метрики
#### Размер хранилища
Для хранения репозиториев 330 000 000 * 14.7 Мб = 4851 Тб

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

6. Открытие Issues

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 3.4 | 2*k/30=10.8 | 36.7 Кб/с |

4. Добавление комментария

| Метод  | Размер ответа, Кб | RPS  | Трафик |
| -------|:----------:|:----------:|:-------:|
| POST | 18.9 | 2*k=324 | 6 Мб/с |

#### В итоге

| Действие  | RPS  | Трафик |
| -------|:----------:|:-------:|
| Создание репозитория | 16.2 | 63.2 Кб/с |
| Клонирование репозитория | 21.6 | 331 Мб/с |
| Загрузка новых данных из репозитория (git pull) | 486 | 1.6 Мб/с |
| Редактирование репозитория | 810 | 2.7 Мб/с |
| Открытие Pull Request | 46.3 | 157 Кб/с |
| Открытие Issues | 10.8 | 36.7 Кб/с |
| Добавление комментария | 324 | 6 Мб/с |
| Общие значения | 1714.9 | 0.34 Гб/с |
| При пиковой нагрузке, х2 | 3429.8 | 0.68 Гб/с |

## 3. Логическая схема
<kbd>
  <img src="/images/logic_diagram_bd.jpg" alt="Логическая схема БД">
</kbd>
