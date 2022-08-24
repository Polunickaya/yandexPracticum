# Исследование надежности заемщиков.

Заказчик — кредитный отдел банка. 

**Цель исследования** - 
Разобраться, влияет ли семейное положение и количество детей клиента на факт погашения кредита в срок. 
Входные данные от банка — статистика о платёжеспособности клиентов.

Результаты исследования будут учтены при построении модели кредитного скоринга — специальной системы, которая оценивает способность потенциального заёмщика вернуть кредит банку.

**Ход исследования**-
Данные получаем из файла data.csv. О качестве данных ничего не известно. Поэтому перед проверкой гипотез понадобится обзор данных.

Проверим данные на ошибки и оценим их влияние на исследование. На этапе предобработки поищем возможность исправить самые критичные ошибки данных.


Таким образом, исследование пройдёт в три основных этапа:

1. Обзор данных
2. Предобработка данных.
3. Ответы на вопросы



### Шаг 1. Обзор данных


```python
import warnings #импортируем управление предупреждениями
warnings.filterwarnings('ignore') #просим игнорировать предупреждения
import pandas as pd #импортируем библиотеку pandas
data = pd.read_csv('/datasets/data.csv') #чтение файла с данными и сохранение в переменной data
data.head(10) #получение первых 10 строк таблицы data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>-8437.673028</td>
      <td>42</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875.639453</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-4024.803754</td>
      <td>36</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080.014102</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>-5623.422610</td>
      <td>33</td>
      <td>Среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885.952297</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-4124.747207</td>
      <td>32</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628.550329</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616.077870</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>-926.185831</td>
      <td>27</td>
      <td>высшее</td>
      <td>0</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>255763.565419</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>-2879.202052</td>
      <td>43</td>
      <td>высшее</td>
      <td>0</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>240525.971920</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>-152.779569</td>
      <td>50</td>
      <td>СРЕДНЕЕ</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>135823.934197</td>
      <td>образование</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>-6929.865299</td>
      <td>35</td>
      <td>ВЫСШЕЕ</td>
      <td>0</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>95856.832424</td>
      <td>на проведение свадьбы</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>-2188.756445</td>
      <td>41</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>144425.938277</td>
      <td>покупка жилья для семьи</td>
    </tr>
  </tbody>
</table>
</div>




```python
data.info() #получение общей информации о данных в таблице data
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 21525 entries, 0 to 21524
    Data columns (total 12 columns):
     #   Column            Non-Null Count  Dtype  
    ---  ------            --------------  -----  
     0   children          21525 non-null  int64  
     1   days_employed     19351 non-null  float64
     2   dob_years         21525 non-null  int64  
     3   education         21525 non-null  object 
     4   education_id      21525 non-null  int64  
     5   family_status     21525 non-null  object 
     6   family_status_id  21525 non-null  int64  
     7   gender            21525 non-null  object 
     8   income_type       21525 non-null  object 
     9   debt              21525 non-null  int64  
     10  total_income      19351 non-null  float64
     11  purpose           21525 non-null  object 
    dtypes: float64(2), int64(5), object(5)
    memory usage: 2.0+ MB


Видим, что таблица состоит из 21525 строк и 12 столбцов, также замечаем пропущеные значения в двух столбцах days_employed и total_income. 

Еще присутствует аномалия данных в колонке days_employed. Указаны отрицательные значения, судя по документации, данный столбец отображает трудовой стаж в днях. Выясним по какой причине образовались данные значения.

Согласно документации к данным:

* children — количество детей в семье
* days_employed — общий трудовой стаж в днях
* dob_years — возраст клиента в годах
* education — уровень образования клиента
* education_id — идентификатор уровня образования
* family_status — семейное положение
* family_status_id — идентификатор семейного положения
* gender — пол клиента
* income_type — тип занятости
* debt — имел ли задолженность по возврату кредитов
* total_income — ежемесячный доход
* purpose — цель получения кредита

В названиях колонок нарушений стиля не обнаружено.

Выводы

В каждой строке таблицы — информация о платёжеспособности клиентов. Часть колонок описывают общую информацию о клиентах: пол, возраст, количество детей в семье, доход, общий трудовой стаж, уровень образования, семейное положение. 

Предварительно можно утверждать, что, данных достаточно для проверки гипотез. 

Чтобы двигаться дальше, нужно устранить проблемы в данных: преобразовать значения в колонке days_employed, заполнить пропуски в колонках days_employed и total_income.


### Шаг 2.1 Заполнение пропусков

Первым делом обработаем пустые значения в столбце total_income.
Методом isna() найдём все строки с пропусками в столбце total_income и просмотрим первые пять.



```python
data[data['total_income'].isna()].head() #Ищем пропущенные значения в столбце total_income
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12</th>
      <td>0</td>
      <td>NaN</td>
      <td>65</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>NaN</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0</td>
      <td>NaN</td>
      <td>41</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>госслужащий</td>
      <td>0</td>
      <td>NaN</td>
      <td>образование</td>
    </tr>
    <tr>
      <th>29</th>
      <td>0</td>
      <td>NaN</td>
      <td>63</td>
      <td>среднее</td>
      <td>1</td>
      <td>Не женат / не замужем</td>
      <td>4</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>NaN</td>
      <td>строительство жилой недвижимости</td>
    </tr>
    <tr>
      <th>41</th>
      <td>0</td>
      <td>NaN</td>
      <td>50</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>госслужащий</td>
      <td>0</td>
      <td>NaN</td>
      <td>сделка с подержанным автомобилем</td>
    </tr>
    <tr>
      <th>55</th>
      <td>0</td>
      <td>NaN</td>
      <td>54</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>1</td>
      <td>NaN</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>



Находим пустые значения типа NaN.  

Проверим, какую долю составляют пропущенные значения в каждом из столбцов с пропусками.



```python

round(data.isna().sum()*100/len(data),2) #доля пропущенных значений

```




    children             0.0
    days_employed       10.1
    dob_years            0.0
    education            0.0
    education_id         0.0
    family_status        0.0
    family_status_id     0.0
    gender               0.0
    income_type          0.0
    debt                 0.0
    total_income        10.1
    purpose              0.0
    dtype: float64



Пропуски в столбце total_income совпадают с пропусками в столбце days_employed, из чего можем сделать вывод, что отсутствие данных не случайно: в данной ситуации можем предположить, что при внесении данных, у клиентов без ежемесячного дохода по ошибке не заполнялся и трудовой стаж.

Так как значения в столбце total_income - количественные переменные - заполним пропуски характерными значениями. 

Заполним пропущенные значения медианным значением по столбцу, так как доходы клиентов могут сильно отличаться. Среднее значение, в данном случае, будет некорректно характеризовать данные.


```python
total_income_median = data['total_income'].median() #считаем медиану для столбца total_income
print(total_income_median)
```

    145017.93753253992



```python
data['total_income'] = data['total_income'].fillna(value=total_income_median) #заполняем пропуски медианным значением
data.isna().sum() #проверяем, заменились ли пропуски 
```




    children               0
    days_employed       2174
    dob_years              0
    education              0
    education_id           0
    family_status          0
    family_status_id       0
    gender                 0
    income_type            0
    debt                   0
    total_income           0
    purpose                0
    dtype: int64



### Шаг 2.2 Проверка данных на аномалии и исправления.

Теперь рассмотрим и обработаем значения в столбце days_employed. На начальном этапе знакомства с данными мы сразу заметили отрицательные значения в данном столбце.
Можем предположить, что отрицательные значения возникли вследствие сбоя при загрузке данных, но стоит отметить, что в таблице присутствуют и положительные данные, но невозможно большие.

При первом знакомстве с данными можно предположить, что положительные числа указаны преимущественно для пенсионеров, давайте проверим.


```python
data.loc[data['days_employed'] > 0, 'income_type']
```




    4        пенсионер
    18       пенсионер
    24       пенсионер
    25       пенсионер
    30       пенсионер
               ...    
    21505    пенсионер
    21508    пенсионер
    21509    пенсионер
    21518    пенсионер
    21521    пенсионер
    Name: income_type, Length: 3445, dtype: object



Появляется предположение, что данные некорректно заполняются для клиентов, которые в данный момент времени не работают.
Для этого проверим трудовой стаж для безработных клиентов. 


```python
data.loc[data['income_type'] =='безработный', 'days_employed']
```




    3133     337524.466835
    14798    395302.838654
    Name: days_employed, dtype: float64



Предположение подтвердилось, но, так как в нашем исследовании мы не будем использовать информацию о трудовом стаже, исправлять данные значения не целесообразно. На практике точные причины появления этих аномальных значений нужно уточнить у разработчиков.

Обработаем пропущенные значения в столбце days_employed.


```python
data[data['days_employed'].isna()].head() #Ищем пропущенные значения в столбце days_employed
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education</th>
      <th>education_id</th>
      <th>family_status</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12</th>
      <td>0</td>
      <td>NaN</td>
      <td>65</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>145017.937533</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <th>26</th>
      <td>0</td>
      <td>NaN</td>
      <td>41</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>M</td>
      <td>госслужащий</td>
      <td>0</td>
      <td>145017.937533</td>
      <td>образование</td>
    </tr>
    <tr>
      <th>29</th>
      <td>0</td>
      <td>NaN</td>
      <td>63</td>
      <td>среднее</td>
      <td>1</td>
      <td>Не женат / не замужем</td>
      <td>4</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>145017.937533</td>
      <td>строительство жилой недвижимости</td>
    </tr>
    <tr>
      <th>41</th>
      <td>0</td>
      <td>NaN</td>
      <td>50</td>
      <td>среднее</td>
      <td>1</td>
      <td>женат / замужем</td>
      <td>0</td>
      <td>F</td>
      <td>госслужащий</td>
      <td>0</td>
      <td>145017.937533</td>
      <td>сделка с подержанным автомобилем</td>
    </tr>
    <tr>
      <th>55</th>
      <td>0</td>
      <td>NaN</td>
      <td>54</td>
      <td>среднее</td>
      <td>1</td>
      <td>гражданский брак</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>1</td>
      <td>145017.937533</td>
      <td>сыграть свадьбу</td>
    </tr>
  </tbody>
</table>
</div>




Уберем минус в значениях для столбца days_employed. Для этого применим функцию abs, которая возвращает абсолютную величину числа.


```python
data['days_employed'] = abs(data['days_employed']) #возвращаем абсолютную величину числа
```

Значения в столбце обновили, теперь заменим пропуски медианным значением по столбцу days_employed.


```python
days_employed_median = data['days_employed'].median() #считаем медиану для столбца total_income
print(days_employed_median)
```

    2194.220566878695



```python
data['days_employed'] = data['days_employed'].fillna(value=days_employed_median) #заполняем пропуски медианным значением
data.isna().sum() #проверяем, заменились ли пропуски 
```




    children            0
    days_employed       0
    dob_years           0
    education           0
    education_id        0
    family_status       0
    family_status_id    0
    gender              0
    income_type         0
    debt                0
    total_income        0
    purpose             0
    dtype: int64



Пропуски заполнили, давайте также проверим значения в столбце children, который непосредственно будет учавствовать в исследовании. 


```python
data['children'].value_counts() #проверим уникальные значения в столбце children
```




     0     14149
     1      4818
     2      2055
     3       330
     20       76
    -1        47
     4        41
     5         9
    Name: children, dtype: int64



Замечаем аномальные значения: количество детей -1.
Предположим, что данные записаны с ошибкой, заменим -1 на 1.
Также присутствует значение 20, что маловероятно для 76 клиентов. Но тут мы точно не знаем, имелось в виду 2 или 0, поэтому заменим на медианное значение.


```python
data['children'] = abs(data['children']) #возвращаем абсолютную величину числа
children_median = data.loc[data.loc[:, 'children'] != 20]['children'].median() #считаем медиану для столбца children
data['children'] = data['children'].replace(20, children_median) #менеям значение 20 на медианное
data['children'] = data['children'].astype('int') #приводим данные в столбце children до целочисленных
data['children'].value_counts() #проверяем результат
```




    0    14225
    1     4865
    2     2055
    3      330
    4       41
    5        9
    Name: children, dtype: int64



Теперь проверим данные в столбцах gender и dob_years.


```python
data['gender'].value_counts()
```




    F      14236
    M       7288
    XNA        1
    Name: gender, dtype: int64




```python
data['dob_years'].value_counts()
```




    35    617
    40    609
    41    607
    34    603
    38    598
    42    597
    33    581
    39    573
    31    560
    36    555
    44    547
    29    545
    30    540
    48    538
    37    537
    50    514
    43    513
    32    510
    49    508
    28    503
    45    497
    27    493
    56    487
    52    484
    47    480
    54    479
    46    475
    58    461
    57    460
    53    459
    51    448
    59    444
    55    443
    26    408
    60    377
    25    357
    61    355
    62    352
    63    269
    64    265
    24    264
    23    254
    65    194
    66    183
    22    183
    67    167
    21    111
    0     101
    68     99
    69     85
    70     65
    71     58
    20     51
    72     33
    19     14
    73      8
    74      6
    75      1
    Name: dob_years, dtype: int64



Обнаружили одно значение XNA в столбце gender, в данном случае не можем точно предположить, что значение было указано ошибочно, возможно, какой-то клиент целенаправленно не заполнил пол. Так как значения столбца gender не будут учавствовать в нашем исследовании, удалять XNA не будем. 

Также выявили аномальное значение в стобце dob_years, целых 101 клиентов с возрастом 0. В этой ситуации не можем однозначно сказать в чем причина данной аномалии, возможно, при внесении данных сотрудник опечатался и не добавил цифру перед нулем, либо на каком-то этапе произошел сбой. В любом случае эту информацию необходимо уточнить у разработчиков. 

Итак, пропуски и аномальные значения исправлены, перейдем к изменению типов данных и удалению дубликатов. 

### Шаг 2.3. Изменение типов данных.

Заменим вещественный тип данных в столбце total_income на целочисленный с помощью метода astype().


```python
data['total_income'] = data['total_income'].astype('int')
```

### Шаг 2.4. Удаление дубликатов.


```python
data.duplicated().sum() #проверяем наличие явных дубликатов

```




    54



Видим, что в наших данных есть 54 явных дубликата, можем сразу их удалить 


```python
data = data.drop_duplicates().reset_index(drop=True) #удаляем явные дубликаты
data.duplicated().sum() #проверяем наличие дубликатов после удаления 
```




    0



Проверим столбец education методом value_counts(), чтобы проанализирать столбец, выбрать каждое уникальное значение и подсчитать частоту его встречаемости в списке.


```python
data['education'].value_counts() #применяем метод value_counts() к столбцу education
```




    среднее                13705
    высшее                  4710
    СРЕДНЕЕ                  772
    Среднее                  711
    неоконченное высшее      668
    ВЫСШЕЕ                   273
    Высшее                   268
    начальное                250
    Неоконченное высшее       47
    НЕОКОНЧЕННОЕ ВЫСШЕЕ       29
    НАЧАЛЬНОЕ                 17
    Начальное                 15
    ученая степень             4
    УЧЕНАЯ СТЕПЕНЬ             1
    Ученая степень             1
    Name: education, dtype: int64



Глядя на таблицу видим, что в столбце education есть одни и те же значения, но записанные по-разному: с использованием заглавных и строчных букв. Приведим их к одному регистру.


```python
data['education'] = data['education'].str.lower() #приводим столбец education к нижнему регистру
data['education'].value_counts() # повторно применяем метод value_counts() к столбцу education  
```




    среднее                15188
    высшее                  5251
    неоконченное высшее      744
    начальное                282
    ученая степень             6
    Name: education, dtype: int64



Явные дубликаты в нашем датасете удалены, неявные дубликаты в столбце education обработаны - приведены к единому нижнему регистру
Для работы с дубликатами использовали метод value_counts() для их обнаружения, и метод str.lower() для исправления. 
Дубликаты могли появиться по нескольким причинам. Например, сбой при загрузке данных, либо вследствие проявления человеческого фактора: при внесении данных несколько сотрудников могли по-разному записывать информацию в колонку. 

### Шаг 2.5. Формирование дополнительных датафреймов словарей, декомпозиция исходного датафрейма.

На данном этапе мы имеем два столбца education_id и education, первый из которых отображает уникальное значение столбца  education. 
Создадим отдельный словарь, где названию категории будет соответствовать номер. И в будущих таблицах будем обращаться уже не к длинной строке, а к её числовому обозначению.


```python
data_log = data[['children', 'days_employed', 'dob_years', 'education_id', 'family_status_id', 'gender', 'income_type', 'debt', 'total_income', 'purpose']]
data_log.head(10) #создаем и выводим первые 10 строк нашего нового датафрема

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education_id</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>1</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>1</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>926.185831</td>
      <td>27</td>
      <td>0</td>
      <td>1</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>255763</td>
      <td>покупка жилья</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>2879.202052</td>
      <td>43</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>240525</td>
      <td>операции с жильем</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>152.779569</td>
      <td>50</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>135823</td>
      <td>образование</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>6929.865299</td>
      <td>35</td>
      <td>0</td>
      <td>1</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>95856</td>
      <td>на проведение свадьбы</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>2188.756445</td>
      <td>41</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>144425</td>
      <td>покупка жилья для семьи</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_education_id = data[['education_id', 'education']] #создаем дополнительный датафрейм-словарь для education
data_education_id.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>education_id</th>
      <th>education</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>высшее</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>высшее</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>высшее</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>8</th>
      <td>0</td>
      <td>высшее</td>
    </tr>
    <tr>
      <th>9</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_education_id = data_education_id.drop_duplicates().reset_index(drop=True) #удаляем дубликаты в data_education_id
data_education_id.sort_values('education_id')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>education_id</th>
      <th>education</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>высшее</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>среднее</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>неоконченное высшее</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>начальное</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>ученая степень</td>
    </tr>
  </tbody>
</table>
</div>



Проделаем тоже самое со столбцами family_status_id и family_status


```python
data_family_status = data[['family_status_id', 'family_status']] #создаем дополнительный датафрейм-словарь для family_status
data_family_status.head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>family_status_id</th>
      <th>family_status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>гражданский брак</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1</td>
      <td>гражданский брак</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1</td>
      <td>гражданский брак</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_family_status = data_family_status.drop_duplicates().reset_index(drop=True) #удаляем дубликаты в data_family_status
data_family_status.sort_values('family_status_id')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>family_status_id</th>
      <th>family_status</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>женат / замужем</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>гражданский брак</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>вдовец / вдова</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>в разводе</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Не женат / не замужем</td>
    </tr>
  </tbody>
</table>
</div>



Готово, теперь с нашими данными будет удобнее работать. 

### Шаг 2.6. Категоризация дохода.

Для удобства анализа дохода клиентов создадим категоризацию и разделим по диапазонам:
* 0–30000 — 'E';
* 30001–50000 — 'D';
* 50001–200000 — 'C';
* 200001–1000000 — 'B';
* 1000001 и выше — 'A'.

И создадим отдельный столбец total_income_category.


```python
def total_income_group(number): #делаем функцию, которая распределит доходы клиентов по категориям
    if 0<number<= 30000:
        return 'E'
    if 30001<=number<=50000:
        return 'D'
    if 50001<=number<=200000:
        return 'C'
    if 200001<=number<=1000000:
        return 'B'
    if 1000001<=number<=2147483647:
        return 'A'
```


```python
data_log['total_income_category'] = data_log['total_income'].apply(total_income_group) #добаляем новый столбец с результатами работы функции
data_log.head(15)

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education_id</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
      <th>total_income_category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
      <td>B</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>1</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
      <td>C</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
      <td>B</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>1</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
      <td>C</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>926.185831</td>
      <td>27</td>
      <td>0</td>
      <td>1</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>255763</td>
      <td>покупка жилья</td>
      <td>B</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>2879.202052</td>
      <td>43</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>240525</td>
      <td>операции с жильем</td>
      <td>B</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>152.779569</td>
      <td>50</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>135823</td>
      <td>образование</td>
      <td>C</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>6929.865299</td>
      <td>35</td>
      <td>0</td>
      <td>1</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>95856</td>
      <td>на проведение свадьбы</td>
      <td>C</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>2188.756445</td>
      <td>41</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>144425</td>
      <td>покупка жилья для семьи</td>
      <td>C</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2</td>
      <td>4171.483647</td>
      <td>36</td>
      <td>0</td>
      <td>0</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>113943</td>
      <td>покупка недвижимости</td>
      <td>C</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0</td>
      <td>792.701887</td>
      <td>40</td>
      <td>1</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>77069</td>
      <td>покупка коммерческой недвижимости</td>
      <td>C</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0</td>
      <td>2194.220567</td>
      <td>65</td>
      <td>1</td>
      <td>1</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>145017</td>
      <td>сыграть свадьбу</td>
      <td>C</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0</td>
      <td>1846.641941</td>
      <td>54</td>
      <td>2</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>130458</td>
      <td>приобретение автомобиля</td>
      <td>C</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0</td>
      <td>1844.956182</td>
      <td>56</td>
      <td>0</td>
      <td>1</td>
      <td>F</td>
      <td>компаньон</td>
      <td>1</td>
      <td>165127</td>
      <td>покупка жилой недвижимости</td>
      <td>C</td>
    </tr>
  </tbody>
</table>
</div>



### Шаг 2.7. Категоризация целей кредита.

Создадим функцию, которая на основании данных из столбца purpose сформирует новый столбец purpose_category, в который войдут следующие категории: 'операции с автомобилем', 'операции с недвижимостью', 'проведение свадьбы', 'получение образования'.


```python
data['purpose'].value_counts() #Проверяем уникальные значения в столбце purpose
```




    свадьба                                   793
    на проведение свадьбы                     773
    сыграть свадьбу                           769
    операции с недвижимостью                  675
    покупка коммерческой недвижимости         662
    покупка жилья для сдачи                   652
    операции с жильем                         652
    операции с коммерческой недвижимостью     650
    покупка жилья                             646
    жилье                                     646
    покупка жилья для семьи                   638
    строительство собственной недвижимости    635
    недвижимость                              633
    операции со своей недвижимостью           627
    строительство жилой недвижимости          625
    покупка недвижимости                      621
    покупка своего жилья                      620
    строительство недвижимости                619
    ремонт жилью                              607
    покупка жилой недвижимости                606
    на покупку своего автомобиля              505
    заняться высшим образованием              496
    автомобиль                                494
    сделка с подержанным автомобилем          486
    свой автомобиль                           479
    на покупку подержанного автомобиля        478
    автомобили                                478
    на покупку автомобиля                     472
    приобретение автомобиля                   461
    дополнительное образование                460
    сделка с автомобилем                      455
    высшее образование                        452
    получение дополнительного образования     447
    образование                               447
    получение образования                     442
    профильное образование                    436
    получение высшего образования             426
    заняться образованием                     408
    Name: purpose, dtype: int64



Полученные цели распределим на четыре основные категории с помощью функции. 


```python
def purpose_group(name): #делаем функцию, которая распределит цели клиентов по категориям
    if 'автомоб' in name:
        return 'операции с автомобилем'
    if 'недвиж' in name:
        return 'операции с недвижимостью'
    if 'жил' in name:
        return 'операции с недвижимостью'
    if 'свадьб' in name:
        return 'проведение свадьбы'
    if 'образов' in name:
        return 'получение образования'
   
```


```python
data_log['purpose_category'] = data_log['purpose'].apply(purpose_group) #добаляем новый столбец с результатами работы функции
data_log.head(15)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>children</th>
      <th>days_employed</th>
      <th>dob_years</th>
      <th>education_id</th>
      <th>family_status_id</th>
      <th>gender</th>
      <th>income_type</th>
      <th>debt</th>
      <th>total_income</th>
      <th>purpose</th>
      <th>total_income_category</th>
      <th>purpose_category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>8437.673028</td>
      <td>42</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>253875</td>
      <td>покупка жилья</td>
      <td>B</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>4024.803754</td>
      <td>36</td>
      <td>1</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>112080</td>
      <td>приобретение автомобиля</td>
      <td>C</td>
      <td>операции с автомобилем</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>5623.422610</td>
      <td>33</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>145885</td>
      <td>покупка жилья</td>
      <td>C</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>4124.747207</td>
      <td>32</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>267628</td>
      <td>дополнительное образование</td>
      <td>B</td>
      <td>получение образования</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>340266.072047</td>
      <td>53</td>
      <td>1</td>
      <td>1</td>
      <td>F</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>158616</td>
      <td>сыграть свадьбу</td>
      <td>C</td>
      <td>проведение свадьбы</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0</td>
      <td>926.185831</td>
      <td>27</td>
      <td>0</td>
      <td>1</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>255763</td>
      <td>покупка жилья</td>
      <td>B</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>6</th>
      <td>0</td>
      <td>2879.202052</td>
      <td>43</td>
      <td>0</td>
      <td>0</td>
      <td>F</td>
      <td>компаньон</td>
      <td>0</td>
      <td>240525</td>
      <td>операции с жильем</td>
      <td>B</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>7</th>
      <td>0</td>
      <td>152.779569</td>
      <td>50</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>135823</td>
      <td>образование</td>
      <td>C</td>
      <td>получение образования</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>6929.865299</td>
      <td>35</td>
      <td>0</td>
      <td>1</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>95856</td>
      <td>на проведение свадьбы</td>
      <td>C</td>
      <td>проведение свадьбы</td>
    </tr>
    <tr>
      <th>9</th>
      <td>0</td>
      <td>2188.756445</td>
      <td>41</td>
      <td>1</td>
      <td>0</td>
      <td>M</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>144425</td>
      <td>покупка жилья для семьи</td>
      <td>C</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2</td>
      <td>4171.483647</td>
      <td>36</td>
      <td>0</td>
      <td>0</td>
      <td>M</td>
      <td>компаньон</td>
      <td>0</td>
      <td>113943</td>
      <td>покупка недвижимости</td>
      <td>C</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>11</th>
      <td>0</td>
      <td>792.701887</td>
      <td>40</td>
      <td>1</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>77069</td>
      <td>покупка коммерческой недвижимости</td>
      <td>C</td>
      <td>операции с недвижимостью</td>
    </tr>
    <tr>
      <th>12</th>
      <td>0</td>
      <td>2194.220567</td>
      <td>65</td>
      <td>1</td>
      <td>1</td>
      <td>M</td>
      <td>пенсионер</td>
      <td>0</td>
      <td>145017</td>
      <td>сыграть свадьбу</td>
      <td>C</td>
      <td>проведение свадьбы</td>
    </tr>
    <tr>
      <th>13</th>
      <td>0</td>
      <td>1846.641941</td>
      <td>54</td>
      <td>2</td>
      <td>0</td>
      <td>F</td>
      <td>сотрудник</td>
      <td>0</td>
      <td>130458</td>
      <td>приобретение автомобиля</td>
      <td>C</td>
      <td>операции с автомобилем</td>
    </tr>
    <tr>
      <th>14</th>
      <td>0</td>
      <td>1844.956182</td>
      <td>56</td>
      <td>0</td>
      <td>1</td>
      <td>F</td>
      <td>компаньон</td>
      <td>1</td>
      <td>165127</td>
      <td>покупка жилой недвижимости</td>
      <td>C</td>
      <td>операции с недвижимостью</td>
    </tr>
  </tbody>
</table>
</div>



Получили итоговую таблицу для анализа.

### Ответы на вопросы.

##### Вопрос 1: Есть ли зависимость между количеством детей и возвратом кредита в срок?

Для анализа будем использовать метод pivot_table.


```python
data_pivot_children = data_log.pivot_table(index = 'children', values= 'debt')
data_pivot_children

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>debt</th>
    </tr>
    <tr>
      <th>children</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.075513</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.091639</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.094542</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.081818</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.097561</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>



###### Вывод 1
Исходя из полученной информации мы видим, что клиенты с четырьмя детьми реже платят в срок, а у клиентов вообще без детей проблем с погашением кредита меньше.
Также можем обратить внимание, что информация по клиентам с пятью детьми выводится некорректно, поэтому мы не можем опираться на полученную информацию. Для более глубокого анализа нужно больше данных по клиентам с пятью детьми. 


##### Вопрос 2: Есть ли зависимость между семейным положением и возвратом кредита в срок?

Для анализа будем использовать метод pivot_table.


```python
data_pivot = data_log.pivot_table(index = 'family_status_id', values= 'debt')
data_pivot
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>debt</th>
    </tr>
    <tr>
      <th>family_status_id</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.075421</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.093202</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.065693</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.071130</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.097509</td>
    </tr>
  </tbody>
</table>
</div>



###### Вывод 2
Смотря на таблицу мы видим, что вдовцы и разведенные выплачивают кредиты лучше, чем те кто никогда не бывал в браке и клиенты в гражданском браке.

##### Вопрос 3: Есть ли зависимость между уровнем дохода и возвратом кредита в срок?

Для анализа будем использовать метод pivot_table.


```python
data_pivot_income = data_log.pivot_table(index = 'total_income_category', values= 'debt')
data_pivot_income
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>debt</th>
    </tr>
    <tr>
      <th>total_income_category</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>0.080000</td>
    </tr>
    <tr>
      <th>B</th>
      <td>0.070621</td>
    </tr>
    <tr>
      <th>C</th>
      <td>0.084825</td>
    </tr>
    <tr>
      <th>D</th>
      <td>0.060000</td>
    </tr>
    <tr>
      <th>E</th>
      <td>0.090909</td>
    </tr>
  </tbody>
</table>
</div>



###### Вывод 3
По данным можем сказать, что клиенты с уровнем дохода до 30 000 имеют больше проблем с возвратом кредита (Е), а клиенты с доходом от 30 001 и до 50 000 (D) погашают задолженность значительно лучше. 

##### Вопрос 4: Как разные цели кредита влияют на его возврат в срок?

Для анализа будем использовать метод pivot_table.


```python
data_pivot_purpose = data_log.pivot_table(index = 'purpose_category', values= 'debt')
data_pivot_purpose
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>debt</th>
    </tr>
    <tr>
      <th>purpose_category</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>операции с автомобилем</th>
      <td>0.093547</td>
    </tr>
    <tr>
      <th>операции с недвижимостью</th>
      <td>0.072314</td>
    </tr>
    <tr>
      <th>получение образования</th>
      <td>0.092177</td>
    </tr>
    <tr>
      <th>проведение свадьбы</th>
      <td>0.079657</td>
    </tr>
  </tbody>
</table>
</div>



###### Вывод 4
Глядя на категории, мы видим, что те клиенты, которые берут кредит для операций с автомобилем и получения образования, испытывают больше трудностей с погашением задолженности, чем те, кто берут кредит для операций с недвижимостью и проведения свадебных церемоний.

## Общий вывод:

В ходе данного иследования мы выяснили, что семейное положение и количество детей влияет на факт погошения кредита в срок, так как клиенты овдовевшие и не имеющие детей лучше справляются с погашением задолженостей. Также мы увидели, что клиенты с доходом от 30 до 50 тысяч и те, кто берет кредит на операции по недвижимости, хорошо справляются с погашением задолженности.
