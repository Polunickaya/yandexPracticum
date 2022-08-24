# Проект - Основы машинного обучения

**Описание**

Сеть фитнес-центров «Культурист-датасаентист» разрабатывает стратегию взаимодействия с клиентами на основе аналитических данных.Распространённая проблема фитнес-клубов и других сервисов — отток клиентов. Для фитнес-центра можно считать, что клиент попал в отток, если за последний месяц ни разу не посетил спортзал. 

**Задачи**

- выделить целевые группы клиентов;
- предложить меры по снижению оттока;
- определить другие особенности взаимодействия с клиентами.

**В ходе исследования предстоит:**

- научиться прогнозировать вероятность оттока (на уровне следующего месяца) для каждого клиента;
- сформировать типичные портреты клиентов: выделить несколько наиболее ярких групп и охарактеризовать их основные свойства;
- проанализировать основные признаки, наиболее сильно влияющие на отток;
- сформулировать основные выводы и разработать рекомендации по повышению качества работы с клиентами.

**Описание данных**

Данные клиента за предыдущий до проверки факта оттока месяц:
- `gender` — пол;
- `Near_Location` — проживание или работа в районе, где находится фитнес-центр;
- `Partner` — сотрудник компании-партнёра клуба (сотрудничество с компаниями, чьи сотрудники могут получать скидки на абонемент — в таком случае фитнес-центр хранит информацию о работодателе клиента);
- `Promo_friends` — факт первоначальной записи в рамках акции «приведи друга» (использовал промо-код от знакомого при оплате первого абонемента);
- `Phone` — наличие контактного телефона;
- `Age` — возраст;
- `Lifetime` — время с момента первого обращения в фитнес-центр (в месяцах).

Информация на основе журнала посещений, покупок и информация о текущем статусе абонемента клиента:

- `Contract_period` — длительность текущего действующего абонемента (месяц, 6 месяцев, год);
- `Month_to_end_contract` — срок до окончания текущего действующего абонемента (в месяцах);
- `Group_visits` — факт посещения групповых занятий;
- `Avg_class_frequency_total` — средняя частота посещений в неделю за все время с начала действия абонемента;
- `Avg_class_frequency_current_month` — средняя частота посещений в неделю за предыдущий месяц;
- `Avg_additional_charges_total` — суммарная выручка от других услуг фитнес-центра: кафе, спорттовары, косметический и массажный салон.
- `Churn` — факт оттока в текущем месяце.


## Чтение  данных и изучение общей информации


```python
import pandas as pd 
import numpy as np 
from matplotlib import pyplot as plt 
import seaborn as sns
import warnings 
warnings.filterwarnings('ignore')
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from scipy.cluster.hierarchy import dendrogram, linkage 
from itertools import combinations
from sklearn.cluster import KMeans
```


```python
df = pd.read_csv('/datasets/gym_churn.csv')
```


```python
display(df.head()) #получение первых 5 строк таблицы df
print()
display(df.info()) #получение общей информации о данных в таблице df
print()
df.duplicated().sum() #проверяем дубликаты
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>gender</th>
      <th>Near_Location</th>
      <th>Partner</th>
      <th>Promo_friends</th>
      <th>Phone</th>
      <th>Contract_period</th>
      <th>Group_visits</th>
      <th>Age</th>
      <th>Avg_additional_charges_total</th>
      <th>Month_to_end_contract</th>
      <th>Lifetime</th>
      <th>Avg_class_frequency_total</th>
      <th>Avg_class_frequency_current_month</th>
      <th>Churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>1</td>
      <td>29</td>
      <td>14.227470</td>
      <td>5.0</td>
      <td>3</td>
      <td>0.020398</td>
      <td>0.000000</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>12</td>
      <td>1</td>
      <td>31</td>
      <td>113.202938</td>
      <td>12.0</td>
      <td>7</td>
      <td>1.922936</td>
      <td>1.910244</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>28</td>
      <td>129.448479</td>
      <td>1.0</td>
      <td>2</td>
      <td>1.859098</td>
      <td>1.736502</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>12</td>
      <td>1</td>
      <td>33</td>
      <td>62.669863</td>
      <td>12.0</td>
      <td>2</td>
      <td>3.205633</td>
      <td>3.357215</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>26</td>
      <td>198.362265</td>
      <td>1.0</td>
      <td>3</td>
      <td>1.113884</td>
      <td>1.120078</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


    
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4000 entries, 0 to 3999
    Data columns (total 14 columns):
     #   Column                             Non-Null Count  Dtype  
    ---  ------                             --------------  -----  
     0   gender                             4000 non-null   int64  
     1   Near_Location                      4000 non-null   int64  
     2   Partner                            4000 non-null   int64  
     3   Promo_friends                      4000 non-null   int64  
     4   Phone                              4000 non-null   int64  
     5   Contract_period                    4000 non-null   int64  
     6   Group_visits                       4000 non-null   int64  
     7   Age                                4000 non-null   int64  
     8   Avg_additional_charges_total       4000 non-null   float64
     9   Month_to_end_contract              4000 non-null   float64
     10  Lifetime                           4000 non-null   int64  
     11  Avg_class_frequency_total          4000 non-null   float64
     12  Avg_class_frequency_current_month  4000 non-null   float64
     13  Churn                              4000 non-null   int64  
    dtypes: float64(4), int64(10)
    memory usage: 437.6 KB



    None


    





    0



После первого ознакомленния с данными видим, что информация предоставлена корректно, в полном объеме без пропусков и дубликатов. Типы даных также в нужном нам формате.

## Исследовательский анализ данных (EDA)


```python
df.describe().T
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>gender</th>
      <td>4000.0</td>
      <td>0.510250</td>
      <td>0.499957</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Near_Location</th>
      <td>4000.0</td>
      <td>0.845250</td>
      <td>0.361711</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Partner</th>
      <td>4000.0</td>
      <td>0.486750</td>
      <td>0.499887</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Promo_friends</th>
      <td>4000.0</td>
      <td>0.308500</td>
      <td>0.461932</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Phone</th>
      <td>4000.0</td>
      <td>0.903500</td>
      <td>0.295313</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Contract_period</th>
      <td>4000.0</td>
      <td>4.681250</td>
      <td>4.549706</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>12.000000</td>
    </tr>
    <tr>
      <th>Group_visits</th>
      <td>4000.0</td>
      <td>0.412250</td>
      <td>0.492301</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Age</th>
      <td>4000.0</td>
      <td>29.184250</td>
      <td>3.258367</td>
      <td>18.000000</td>
      <td>27.000000</td>
      <td>29.000000</td>
      <td>31.000000</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>Avg_additional_charges_total</th>
      <td>4000.0</td>
      <td>146.943728</td>
      <td>96.355602</td>
      <td>0.148205</td>
      <td>68.868830</td>
      <td>136.220159</td>
      <td>210.949625</td>
      <td>552.590740</td>
    </tr>
    <tr>
      <th>Month_to_end_contract</th>
      <td>4000.0</td>
      <td>4.322750</td>
      <td>4.191297</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>6.000000</td>
      <td>12.000000</td>
    </tr>
    <tr>
      <th>Lifetime</th>
      <td>4000.0</td>
      <td>3.724750</td>
      <td>3.749267</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>5.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>Avg_class_frequency_total</th>
      <td>4000.0</td>
      <td>1.879020</td>
      <td>0.972245</td>
      <td>0.000000</td>
      <td>1.180875</td>
      <td>1.832768</td>
      <td>2.536078</td>
      <td>6.023668</td>
    </tr>
    <tr>
      <th>Avg_class_frequency_current_month</th>
      <td>4000.0</td>
      <td>1.767052</td>
      <td>1.052906</td>
      <td>0.000000</td>
      <td>0.963003</td>
      <td>1.719574</td>
      <td>2.510336</td>
      <td>6.146783</td>
    </tr>
    <tr>
      <th>Churn</th>
      <td>4000.0</td>
      <td>0.265250</td>
      <td>0.441521</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



По описанию данных видим, что процент оттока клиентов составляет почти 27%. Средний возраст клиентов 29 лет, также обратим внимание, что большинство посетителей живет близко к фитнес-центру. Почти у всех внесен контактный номер телефона, 48% клиентов являются сотрудниками партнерских компаний. Можем отметить, что время с момента первого обращения в фитнес-центр в среднем составляет 3,7 месяца. 

Далее посмотрим на средние значения признаков в двух группах — тех, кто ушел в отток и тех, кто остался.


```python
df.groupby('Churn').mean().T
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Churn</th>
      <th>0</th>
      <th>1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>gender</th>
      <td>0.510037</td>
      <td>0.510839</td>
    </tr>
    <tr>
      <th>Near_Location</th>
      <td>0.873086</td>
      <td>0.768143</td>
    </tr>
    <tr>
      <th>Partner</th>
      <td>0.534195</td>
      <td>0.355325</td>
    </tr>
    <tr>
      <th>Promo_friends</th>
      <td>0.353522</td>
      <td>0.183789</td>
    </tr>
    <tr>
      <th>Phone</th>
      <td>0.903709</td>
      <td>0.902922</td>
    </tr>
    <tr>
      <th>Contract_period</th>
      <td>5.747193</td>
      <td>1.728558</td>
    </tr>
    <tr>
      <th>Group_visits</th>
      <td>0.464103</td>
      <td>0.268615</td>
    </tr>
    <tr>
      <th>Age</th>
      <td>29.976523</td>
      <td>26.989632</td>
    </tr>
    <tr>
      <th>Avg_additional_charges_total</th>
      <td>158.445715</td>
      <td>115.082899</td>
    </tr>
    <tr>
      <th>Month_to_end_contract</th>
      <td>5.283089</td>
      <td>1.662582</td>
    </tr>
    <tr>
      <th>Lifetime</th>
      <td>4.711807</td>
      <td>0.990575</td>
    </tr>
    <tr>
      <th>Avg_class_frequency_total</th>
      <td>2.024876</td>
      <td>1.474995</td>
    </tr>
    <tr>
      <th>Avg_class_frequency_current_month</th>
      <td>2.027882</td>
      <td>1.044546</td>
    </tr>
  </tbody>
</table>
</div>



Можем предположить, что в отток попадают клиенты, которые живут несколько дальше от фитнес-центра, как правило они не являются сотрудниками партнерских компаний, реже посещают групповые занятия. В среднем попадающие в отток клиенты занимаются месяц, скореее всего  абонемент они приобретали на пробу, но их что-то не усроило и они нашли вариант фитнес-центра получше. 

Построим столбчатые гистограммы и распредения признаков для тех, кто ушёл (отток) и тех, кто остался (не попали в отток).


```python
churn_1 = df.query('Churn==1')
churn_0 = df.query('Churn==0')
for j in df:
    plt.title('Признак '+j)
    plt.xlabel(j)
    plt.hist(churn_1[j], bins=20, alpha = 1, label = 'Отток')
    plt.hist(churn_0[j], bins=20, alpha = 0.50, label = 'Не попали в отток')
    plt.ylabel('Количество')
    plt.legend()
    plt.show()
```


    
![png](picture/output_13_0.png)
    



    
![png](picture/output_13_1.png)
    



    
![png](picture/output_13_2.png)
    



    
![png](picture/output_13_3.png)
    



    
![png](picture/output_13_4.png)
    



    
![png](picture/output_13_5.png)
    



    
![png](picture/output_13_6.png)
    



    
![png](picture/output_13_7.png)
    



    
![png](picture/output_13_8.png)
    



    
![png](picture/output_13_9.png)
    



    
![png](picture/output_13_10.png)
    



    
![png](picture/output_13_11.png)
    



    
![png](picture/output_13_12.png)
    



    
![png](picture/output_13_13.png)
    



```python
### CODE FROM REVIEWER

sns.displot(df, x='Avg_class_frequency_current_month', hue='Churn')
plt.title('Avg_class_frequency_current_month')
plt.show()
```


    
![png](picture/output_14_0.png)
    


По графикам можем сделать вывод, что попадают в отток в основном клиенты, которые приобретают абонемент на 1 месяц. Также следует обратить внимание на параметр `Avg_additional_charges_total`— суммарная выручка от других услуг фитнес-центра, видим, что распределение смещено в лево, причем для оттока несколько больше. Скорее всего клиенты, которые попали в отток не приобретали никаких дополнительных услуг.


```python
cr = df.corr() #построим матрицу корреляций

plt.figure(figsize=(15, 10))
#рисуем тепловую карту с подписями для матрицы корреляций
sns.heatmap(cr, annot = True)
plt.title('Матрица корреляций')
plt.show()
```


    
![png](picture/output_16_0.png)
    


По матрице корреляций мы выявили мультиколлинеарные признаки - это `Contract_period` и `Month_to_end_contact`, `Avg_class_frequency_total` и `Avg_class_frequency_current_month`. 
Чтобы в дальнейшем не возникли проблемы с линейной регрессией для каждой пары сильно скоррелированных признаков удалим один из них вручную.


```python
df = df.drop(['Month_to_end_contract', 'Avg_class_frequency_total'], axis=1)
```

## Строим модель прогнозирования оттока клиентов

Построим модель бинарной классификации клиентов, где целевой признак — факт оттока клиента в следующем месяце.


```python
# разделяем данные на признаки (матрица X) и целевую переменную (y)
X = df.drop('Churn', axis = 1)
y = df['Churn']

# разделяем модель на обучающую и валидационную выборку
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, stratify = y)
```


```python
# определим функцию, которая будет выводить наши метрики
def print_all_metrics(y_true, y_pred, title='Метрики классификации'):
    print(title)
    print('\tAccuracy: {:.2f}'.format(accuracy_score(y_true, y_pred)))
    print('\tPrecision: {:.2f}'.format(precision_score(y_true, y_pred)))
    print('\tRecall: {:.2f}'.format(recall_score(y_true, y_pred)))
```


```python
# зададим алгоритм для модели логистической регрессии
lr_model = LogisticRegression(random_state=0)
# обучим модель
lr_model.fit(X_train, y_train)
# воспользуемся уже обученной моделью, чтобы сделать прогнозы
lr_predictions = lr_model.predict(X_test)

# выведем все метрики
print_all_metrics(
    y_test,
    lr_predictions,
    title='Метрики для модели логистической регрессии:',
)
```

    Метрики для модели логистической регрессии:
    	Accuracy: 0.91
    	Precision: 0.85
    	Recall: 0.79



```python
# зададим алгоритм для новой модели на основе алгоритма случайного леса
rf_model = RandomForestClassifier(n_estimators = 100, random_state = 0)
# обучим модель случайного леса
rf_model.fit(X_train, y_train)
# воспользуемся уже обученной моделью, чтобы сделать прогнозы
rf_predictions = rf_model.predict(X_test)
# выведем все метрики
print_all_metrics(
    y_test,
    rf_predictions,
    title='Метрики для модели случайного леса:',
)
```

    Метрики для модели случайного леса:
    	Accuracy: 0.92
    	Precision: 0.88
    	Recall: 0.80


На основании полученных метрик нам больше подойдет модель логистической регрессии, так как доля правильных прогнозов несколько выше, чем у модели случайного леса.

## Кластеризация клиентов


```python
# стандартизируем данные
sc = StandardScaler()
x_sc = sc.fit_transform(X)
```


```python
linked = linkage(x_sc, method = 'ward') 
plt.figure(figsize=(15, 10))  
dendrogram(linked, orientation='top')
plt.title('Дендрограмма')
plt.show() 
```


    
![png](picture/output_28_0.png)
    


На основании полученной дендрограммы можем выделить 4 кластера. 

Далее обучим модель кластеризации на основании алгоритма K-Means и спрогнозируем кластеры клиентов. Число кластеров примем за n=5, как было указано в задании.


```python
# задаём модель k_means с числом кластеров 5 и фиксируем значение random_state
km = KMeans(n_clusters=5, random_state=0)
# прогнозируем кластеры для наблюдений (алгоритм присваивает им номера от 0 до 2)
labels = km.fit_predict(x_sc)

# сохраняем метки кластера в поле нашего датасета
df['cluster_km'] = labels

# выводим статистику по средним значениям наших признаков по кластеру
df.groupby('cluster_km').mean().T
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>cluster_km</th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>gender</th>
      <td>0.496447</td>
      <td>0.500000</td>
      <td>0.500940</td>
      <td>0.534260</td>
      <td>0.523316</td>
    </tr>
    <tr>
      <th>Near_Location</th>
      <td>0.995939</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.996028</td>
      <td>0.862694</td>
    </tr>
    <tr>
      <th>Partner</th>
      <td>0.892386</td>
      <td>0.489247</td>
      <td>0.217105</td>
      <td>0.379345</td>
      <td>0.471503</td>
    </tr>
    <tr>
      <th>Promo_friends</th>
      <td>1.000000</td>
      <td>0.078853</td>
      <td>0.072368</td>
      <td>0.009930</td>
      <td>0.305699</td>
    </tr>
    <tr>
      <th>Phone</th>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>Contract_period</th>
      <td>6.922843</td>
      <td>2.994624</td>
      <td>2.010338</td>
      <td>6.208540</td>
      <td>4.777202</td>
    </tr>
    <tr>
      <th>Group_visits</th>
      <td>0.524873</td>
      <td>0.232975</td>
      <td>0.277256</td>
      <td>0.538232</td>
      <td>0.427461</td>
    </tr>
    <tr>
      <th>Age</th>
      <td>29.606091</td>
      <td>28.679211</td>
      <td>27.583647</td>
      <td>30.699106</td>
      <td>29.297927</td>
    </tr>
    <tr>
      <th>Avg_additional_charges_total</th>
      <td>153.424651</td>
      <td>137.125763</td>
      <td>119.339956</td>
      <td>176.259567</td>
      <td>144.208179</td>
    </tr>
    <tr>
      <th>Lifetime</th>
      <td>4.283249</td>
      <td>2.974910</td>
      <td>1.922932</td>
      <td>5.415094</td>
      <td>3.940415</td>
    </tr>
    <tr>
      <th>Avg_class_frequency_current_month</th>
      <td>1.919520</td>
      <td>1.597146</td>
      <td>1.203319</td>
      <td>2.324220</td>
      <td>1.723967</td>
    </tr>
    <tr>
      <th>Churn</th>
      <td>0.119797</td>
      <td>0.403226</td>
      <td>0.563910</td>
      <td>0.014896</td>
      <td>0.266839</td>
    </tr>
  </tbody>
</table>
</div>



У кластера 0 самый маленький процент оттока и самый длительный текущий абонемент, что не сложно сопоставить. Еще видим, что у нулевого кластера самая большая суммарная выручка от других услуг фитнес-центра, и с большей вероятностью клиент - сотрудник компании-партнёра клуба.

Первый и второй кластер несколько похожи, наблюдается различие в степени оттока. Второй кластер более подвержен оттоку клиентов. Еще можем выделить признаки `Near_Location` и `Contract_period`, у второго кластера длительность текущего действующего абонемента ниже, чем у первого. И также второй кластер гораздо ниже по проживанию или работе в районе, где находится фитнес-центр

У третьего кластера самый большой отток клиентов, также видно, что у данного кластера самый низкий показатель посещений за последний месяц и количество посещений в целом.

Четвертый кластер имеет небольшой процент оттока, как и нулевой кластер. Стоит обратить внимание на признак `Lifetime`, у данного кластера он самый высокий, как и признак `Avg_class_frequency_current_month` -  средняя частота посещений в неделю за предыдущий месяц.



```python
for t in ['gender', 'Near_Location', 'Partner', 'Promo_friends', 'Phone', 'Contract_period', \
          'Group_visits', 'Age', 'Lifetime', 'Avg_class_frequency_current_month', 'Avg_additional_charges_total']:
    plt.figure(figsize=(10, 5))
    plt.title('Признак '+t)
    plt.xlabel(j)
    sns.barplot(x ='cluster_km', y = t, data=df)
    plt.show()
```


    
![png](picture/output_32_0.png)
    



    
![png](picture/output_32_1.png)
    



    
![png](picture/output_32_2.png)
    



    
![png](picture/output_32_3.png)
    



    
![png](picture/output_32_4.png)
    



    
![png](picture/output_32_5.png)
    



    
![png](picture/output_32_6.png)
    



    
![png](picture/output_32_7.png)
    



    
![png](picture/output_32_8.png)
    



    
![png](picture/output_32_9.png)
    



    
![png](picture/output_32_10.png)
    


По построеным графикам дополнительно можем сказать, что распределние по возрасту и полу примерно одинаковое, также примерно на одном уровне у всех кластеров признак `Avg_additional_charges_total`.


```python
# Для каждого полученного кластера посчитаем долю оттока
df.groupby('cluster_km')['Churn'].mean().reset_index().sort_values(by='Churn', ascending=False)
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cluster_km</th>
      <th>Churn</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>0.563910</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0.403226</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>0.266839</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0.119797</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>0.014896</td>
    </tr>
  </tbody>
</table>
</div>



Как мы и выявили ранее, больше всего подвержен оттоку третий кластер 51%. Далее следует второй кластер - 44%, первый кластер занимает 26% оттока, а четвертый и нулевой всего 6% и 2% оттока клиентов. 

## Вывод и рекомендации 

В ходе исследования мы спрогнозировали вероятность оттока для каждого клиента, используя модель логистической регрессии, сформировали типичные портреты клиентов, а также проанализировали основные признаки, наиболее сильно влияют на отток. Ими оказались такие признаки, как `Near_Location` — проживание или работа в районе, где находится фитнес-центр, `Contract_period` — длительность текущего действующего абонемента, `Group_visits` — факт посещения групповых занятий и `Avg_class_frequency_current_month` — средняя частота посещений в неделю за предыдущий месяц.

Клиенты, живущие поблизости и посещающие групповые занятия реже попадают в отток. В среднем попадающие в отток клиенты занимаются месяц, вероятнее абонемент они приобретали на пробу, и по истечении действия контракта принимали решение не продливать абонемент.

Проведя кластеризацию клиентов мы выявили 5 кластеров:

`Кластер 0:` самый маленький процент оттока и самый длительный текущий абонемент, самая большая суммарная выручка от других услуг фитнес-центра, и с большей вероятностью клиент - сотрудник компании-партнёра клуба.

`Кластер 1:` достаточно долгий Lifetime и срок до окончания текущего действующего абонемента, чаще посещают групповые занятия. 

`Кластер 2:` самый низкий показатель признака Promo_friends, более подвержен оттоку клиентов, в сравнении с первым, низкая длительность текущего действующего абонемента.

`Кластер 3:`  самый большой отток клиентов, еще у данного кластера самый низкий показатель длительности текущего абонемента, посещений за последний месяц и количество посещений в целом.

`Кластер 4:`  имеет небольшой процент оттока, как и нулевой кластер, Lifetime у данного кластера самый высокий, как и признак Avg_class_frequency_current_month - средняя частота посещений в неделю за предыдущий месяц.

На основании проведенного анализа можно рекомендовать стараться продавать абонементы на более длительный срок, так как наблюдается тенденция оттока клиентов при небольшом сроке действия абонемента.  
Можно предлагать потенциальным клиентам приобретать абонементы на 6-12 месяцев на более выгодных условиях. 
Мы выявили, что лояльные клиенты более склонны пользоваться дополнительнымы услугами фитнес-центра, для удержания клиентов будет целесообразно ввести дополнительные скидки или бонусную систему на дополнительные услуги.

Можно посоветовать стараться привлекать клиентов из близлежащих районов, некоторые фитнес-центры практикуют дополнительные "бонусы" для жителей района, в котором располагается сам центр.  

Еще следут ввести процедуру обзвона клиентов за несколько дней до окончания действия месячного абонемента для новых клиентов и предлагать единоразовое продление на более выгодных условиях. 
