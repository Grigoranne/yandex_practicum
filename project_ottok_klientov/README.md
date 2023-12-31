# Прогнозирование оттока клиентов

***Цель заказчика:*** научиться прогнозировать отток клиентов. Если выяснится, что пользователь планирует уйти, ему будут предложены промокоды и специальные условия.

***Данные:*** команда оператора собрала персональные данные о некоторых клиентах, информацию об их тарифах и договорах:

- contract_new.csv — информация о договоре;
- personal_new.csv — персональные данные клиента;
- internet_new.csv — информация об интернет-услугах;
- phone_new.csv — информация об услугах телефонии.

Во всех файлах столбец `customerID` содержит код клиента.

***Признаки:***

- BeginDate – дата начала пользования услугами

- Type – тип оплаты: ежемесячный, годовой и тд

- PaperlessBilling – безналичный расчет

- PaymentMethod – способ оплаты

- MonthlyCharges – ежемесячные траты на услуги

- TotalCharges – всего потрачено денег на услуги

- Dependents – наличие иждивенцев

- Senior Citizen – наличие пенсионного статуса по возрасту

- Partner – наличие супруга(и)

- MultipleLines – наличие возможности ведения параллельных линий во время звонка

- EndDate - дата окончания договора (целевой признак)

***Критерии оценки:*** AUC-ROC >= 0.85


# Используемые иснтументы:

```pandas``` ```matplotlib``` ```seaborn``` ```phik``` ```scikit-learn``` 

```catboost``` ```lightgbm``` ```LogisticRegression```

`GridSearchCV` `OneHotEncoder` `StandardScaler`

# Вывод:


В нашем проекте провели следющие этапы:

- предобработали данные: привели названия столбцов к змеиному регистр, поменяли типы данных в столбцах begin_date, end_date,total_charges, заполнили пропуски в столбце total_charges у новых клиентов ежемесячными тратами, заполнили возникшие в доп услугах пропуски при объединении значением 'No'
  - сгенерировали новый признак tenure (сколько дней клиент пользуется услугами)
  - создали целевой признак exit, где 1 - ушедшие клиенты, а 0 - лояльные клиенты
  - выявили дисбаланс классов
  - провели исследовательские анализ и выявили:
  - больше всего лояльных клиентов заключили договор в в 2020 году, то есть это новые клиенты, а ушедшие клиенты по чаще заключали договор в 2014 и 2015 годах, то есть ушло много "старых" клиентов
  - мы можем отметить тенденцию, что с каждым годом количество лояльных клиентов увеличивается, а количество ушедших уменьшается
в 2014 - 2015 году отток клиентов был самый высокий
  - ежемесячные траты лояльных клиентов в основном 20 у.е., а ушедшие чаще всего тратили от 20-25 у.е. и 100 у.е.
  - медиана общих затрат у лояльных клиентов около 1000 у.е., у ушедших клиентов чуть больше 2000 у.е., так как ушли в основном клиенты с продолжительностью договора от 1 до 4 лет
  - гендерное распрделение у двух групп почти не отличается, как и наличие иждивенцов
  - количество ушедших пенсионеров больше на 6%, чем у лояльных
  - ушедшие клиенты чаще подписывали договора на 1 и 2 года, у лояльных же преимущественно ежемесячные договора
  - среди ушедших клиентов на 10 процентов больше зарегистрированных в браке
  - распределение по интернет-услугам и услугам безопасности у ушедших и лояльных клиентов одинаковое
  - услуга облачное хранилище и защита устройства у ушедших клиентов распределена в равной степени, а лояльные клиенты чаще отказывается от этих услуг
  - технической поддержкой и лояльные, и ушедшие чаще всего не пользуются
  - Streaming tv: ушедших клиентов, которые выбирали эту доп услугу чуть больше, лояльные клиенты чаще отказываются
  - услугами параллельной линии ушедшие клиенты пользовались активнее, лояльные чаще отказывались
- провели корреляционные анализ и исключили столбцы, которые будут мешать обучению модели
- разбили наш датасет на тренирововчную и тестовую выборки (75%/25%)
- закодировали данные с помощью OneHotEncoder()
- выбрали метрику AUC-ROC — метрика, которая учитывает возможность балансировать между false posititve (клиент не собирался уходить)и false negative(клиент ушел),в качестве дополнительной метрики рассчитали accuracy
- для логистической регрессии стнадартизировали данные с помощью StandardScaler()
- подбирали гиперпараметры для моделей с помощью GridSearch
- выявили лучшую модель LGBMClassifier с показателем AUC-ROC при подборе лучших параметров: 0.895346
- провели тестирование модели с результатом AUC-ROC: 0.87988376, что соответствует заявленным требованиям
- предсказания положительного класса (1-ушедшие клиенты) на 58%, на отрицательном классе (0 - лояльные клиенты) - 94%
- самые важные признаки для модели - ежемесячные затраты и продолжительность договора
