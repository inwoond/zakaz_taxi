# Проект: Прогнозирование заказов такси

## Описание проекта

Компания **«Чётенькое такси»** предоставляет сервис перевозок из аэропортов. В периоды пиковой нагрузки компании не хватает водителей. Цель проекта — построить модель прогнозирования количества заказов такси на **следующий час**.

Решение позволит заранее привлекать водителей и обеспечивать бесперебойное обслуживание клиентов.

## Постановка задачи

- Построить модель для предсказания количества заказов такси на **один час вперёд**.
- Использовать данные с временным рядом заказов.
- Достичь значения **метрики RMSE ≤ 48** на тестовой выборке.
- Проанализировать сезонность и автокорреляцию временного ряда.
- Подобрать оптимальные гиперпараметры моделей.

## Описание данных

Источник: `taxi.csv`  
- `datetime` — временная метка (с шагом в 10 минут).  
- `num_orders` — количество заказов за соответствующий промежуток времени.

Перед обучением:
- Выполнено **ресемплирование** по одному часу.
- Проверено отсутствие пропусков и дубликатов.

## Как решалась задача

### 1. Предобработка

- Преобразованы временные метки в формат datetime.
- Данные агрегированы по **часам** с использованием `.resample('1H')`.
- Построены графики для анализа сезонности:
  - **Дневной паттерн** (24 часа).
  - **Недельная сезонность** (168 часов).
- Признаки:
  - Лаги: `lag_1`, `lag_24`, `lag_168`
  - Скользящее среднее

### 2. Моделирование

Обучены и протестированы несколько моделей:

- **LinearRegression**
- **RandomForestRegressor**
- **GradientBoostingRegressor**
- **CatBoostRegressor**

Подбор параметров:
- `max_depth`, `n_estimators`, `learning_rate`
- Учитывался размер окна для lag и rolling-фич

### 3. Валидация и тест

- Валидационная выборка: 10% от конца ряда.
- Лучшая модель: **CatBoostRegressor**
- Метрика:  
  - **RMSE на тесте ≈ 24.6**, что соответствует требованию.

## Использованные технологии

- `Python`, `Pandas`, `Numpy`
- `statsmodels.tsa.seasonal.STL`  
- `Matplotlib`
- `Sklearn`, `CatBoost`, `LightGBM` (`LGBMRegressor`)  

## Результаты

- Модель CatBoost показала наилучший результат на тестовой выборке.
- В модели учтены ключевые сезонные паттерны: суточные и недельные.
- Построен пайплайн прогнозирования на 1 час вперёд.
- `RMSE = 24.6`, что меньше показателя, установленного заказчиком (48), что подтверждает пригодность модели к продакшену.

## Выводы

- Прогнозирование заказов по временным рядам возможно с высокой точностью.
- Учитывая лаги и сезонность, модель эффективно улавливает динамику спроса.
- Рекомендовано регулярное переобучение модели с учётом новых данных.
- Возможна доработка для прогноза на **несколько часов вперёд** с помощью autoregressive подходов или seq2seq.
