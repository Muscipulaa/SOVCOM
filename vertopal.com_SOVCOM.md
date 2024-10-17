---
jupyter:
  colab:
  kernelspec:
    display_name: Python 3
    name: python3
  language_info:
    name: python
  nbformat: 4
  nbformat_minor: 0
---

::: {.cell .code execution_count="8" id="vZMIBxZ3aoOG"}
``` python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```
:::

::: {.cell .code execution_count="25" id="YH70Sm0YbjhP"}
``` python
data = pd.read_csv(r"/content/test_atm_analysis.csv")
```
:::

::: {.cell .code execution_count="26" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":520}" id="JiHlTQz5bnfQ" outputId="4f938bdf-a32c-4dcd-b098-cbfd71079b72"}
``` python
data.head(15)
```

::: {.output .execute_result execution_count="26"}
``` json
{"type":"dataframe","variable_name":"data"}
```
:::
:::

::: {.cell .code execution_count="27" colab="{\"base_uri\":\"https://localhost:8080/\"}" id="_1WUd9yfbwE0" outputId="faaa051a-55ea-4a97-fb36-d8155063a459"}
``` python
data['MOUNTH'] = pd.to_datetime(data['TIME']).dt.to_period('M')
data['MOUNTH'].unique()
```

::: {.output .execute_result execution_count="27"}
    <PeriodArray>
    ['2023-05', '2023-06', '2023-07']
    Length: 3, dtype: period[M]
:::
:::

::: {.cell .markdown id="ZJqzsX4IcAPA"}
Данные представлены за три месяца: МАЙ, ИЮНЬ, ИЮЛЬ

Найдем номинальный оборот в разрезе по месяцам
:::

::: {.cell .markdown id="M75PORXXucqu"}
**1. ЭКОНОМИЧЕСКИЕ ПОКАЗАТЕЛИ**
:::

::: {.cell .code execution_count="28" id="B4B9PjNhcHWY"}
``` python
nominal = data.groupby(['MOUNTH', 'BANK_ID'])['AMOUNT'].sum().reset_index()
```
:::

::: {.cell .markdown id="ZIiCUrUNdL40"}
Определим количество операций в разрезе по месяцам и банкам
:::

::: {.cell .code execution_count="29" id="631Oy0HddKBx"}
``` python
count_of_month = data.groupby(['BANK_ID', 'MOUNTH'])['TRANSACTION_ID'].count().reset_index()
```
:::

::: {.cell .markdown id="gJ23di8wduog"}
Первоначально (*pivot_of_dynamic_1*), объединим исходный датасет с
новыми столбцами, имеющими численные значения номинального оборота по
общим столбцам \'BANK_ID\'и \'month_and_year\'. Затем
(*pivot_of_dynamic_2*) к объединенному датасету добавим информацию о
количестве операций. Переименуем новые столбцы для лучшего понимания:
NOMINAL_TRANSACT - номинальный оборот, COUNT_TRANSACT - число операций.
И все в срезе по месяцам
:::

::: {.cell .code execution_count="30" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":520}" id="OFyPEKF5dZWw" outputId="5ff98a80-4066-43fe-fbcf-46c4dc855c1f"}
``` python
pivot_of_dynamic_1 = data.merge(nominal, on=['BANK_ID', 'MOUNTH'])
pivot_of_dynamic_2 = pivot_of_dynamic_1.merge(count_of_month, on=['BANK_ID', 'MOUNTH'])
pivot_of_dynamic_2 = pivot_of_dynamic_2.rename(columns={'AMOUNT_y':'NOMINAL_TRANSACT', 'TRANSACTION_ID_y':'COUNT_TRANSACT'})
pivot_of_dynamic_2.head(15)
```

::: {.output .execute_result execution_count="30"}
``` json
{"type":"dataframe","variable_name":"pivot_of_dynamic_2"}
```
:::
:::

::: {.cell .code execution_count="36" colab="{\"base_uri\":\"https://localhost:8080/\"}" id="4cxSyPUXfezS" outputId="52e022a3-23e9-4c51-c8c1-6dbe2ab19c6d"}
``` python
print(pivot_of_dynamic_2.loc[pivot_of_dynamic_2['NOMINAL_TRANSACT'].idxmax(), 'MOUNTH'])
pivot_of_dynamic_2['NOMINAL_TRANSACT'].max()
```

::: {.output .stream .stdout}
    2023-05
:::

::: {.output .execute_result execution_count="36"}
    123245440
:::
:::

::: {.cell .code execution_count="38" colab="{\"base_uri\":\"https://localhost:8080/\"}" id="fQ_OvdS0g-G9" outputId="e67ef99a-ba7f-4ff2-ffe1-6e9343cf9d0d"}
``` python
print(pivot_of_dynamic_2.loc[pivot_of_dynamic_2['COUNT_TRANSACT'].idxmax(), 'MOUNTH'])
pivot_of_dynamic_2['COUNT_TRANSACT'].max()
```

::: {.output .stream .stdout}
    2023-06
:::

::: {.output .execute_result execution_count="38"}
    170483
:::
:::

::: {.cell .markdown id="aBjJEmr9iu5a"}
Посчитаем динамику роста с помощью метода diff(), который считает
разницу NOMINAL_TRANSACT текущего месяца и предыдущего. Максимальная
динамика оборотов наблюдается у банка с ID=3.
:::

::: {.cell .code execution_count="53" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":206}" id="s_SM72_OhBVO" outputId="4a4e921c-78c0-44a2-dd27-31021062a662"}
``` python
pivot_of_dynamic_2['DYNAMIC'] = pivot_of_dynamic_2.groupby('BANK_ID')['NOMINAL_TRANSACT'].diff()
pivot_of_dynamic_2.sort_values(by='DYNAMIC', ascending=False).head(5)
```

::: {.output .execute_result execution_count="53"}
``` json
{"repr_error":"0","type":"dataframe"}
```
:::
:::

::: {.cell .code execution_count="54" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":206}" id="2sH59irrmNf2" outputId="4865d9fe-4b0f-453a-f70d-e30b327ace56"}
``` python
pivot_of_dynamic_2.sort_values(by='COUNT_TRANSACT', ascending=False).head(5)
```

::: {.output .execute_result execution_count="54"}
``` json
{"repr_error":"0","type":"dataframe"}
```
:::
:::

::: {.cell .markdown id="yHJIp79umtby"}
Интересное наблюдение: у банка3 максимальное значение динамики оборотов,
но количество транзакций не велико, а у банка1 напротив - динамика
оборотов нулевая, а количество транзакций - максимально. Это может
свидетельствовать о стабильности банка1, то есть объем денежных операций
на вход равен денежным операциям на выходе. У банка3 же имеют
популярность редкие, но крупные денежные операции.
:::

::: {.cell .code execution_count="64" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":368}" id="bON3BnbFne8c" outputId="fb260c57-5c09-4b76-999c-9ccb221e6be2"}
``` python
(
    pivot_of_dynamic_2.groupby(['BANK_ID','MOUNTH'])[['NOMINAL_TRANSACT', 'COUNT_TRANSACT']].sum()
    .sort_values(by=['MOUNTH', 'NOMINAL_TRANSACT'], ascending=[True, False])
)
```

::: {.output .execute_result execution_count="64"}
``` json
{"summary":"{\n  \"name\": \")\",\n  \"rows\": 139,\n  \"fields\": [\n    {\n      \"column\": \"NOMINAL_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 2876924091194,\n        \"min\": 35490,\n        \"max\": 20442006200860,\n        \"num_unique_values\": 139,\n        \"samples\": [\n          241800,\n          56983440,\n          2352620\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"COUNT_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 4009837168,\n        \"min\": 400,\n        \"max\": 29064453289,\n        \"num_unique_values\": 126,\n        \"samples\": [\n          5184,\n          163216,\n          7056\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    }\n  ]\n}","type":"dataframe"}
```
:::
:::

::: {.cell .markdown id="bMUMv685qqLX"}
Лидером в каждом из месяцев является банк с ID==1, демонстрируя
наибольшие значения как по номинальному обороту (*NOMINAL_TRANSACT*),
так и по количеству транзакций (*COUNT_TRANSACT*). Это свидетельсвует о
высокой активности клиентов банка и эффективном использовании,
обеспечивая ему значительное конкурентное преимущество
:::

::: {.cell .code execution_count="65" id="kbTJHfogrQop"}
``` python
pivot_of_dynamic_2['PERCENTAGE_GROWTH'] = pivot_of_dynamic_2.groupby('BANK_ID')['COUNT_TRANSACT'].pct_change() * 100
```
:::

::: {.cell .code execution_count="66" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":368}" id="mVBPyh5GrX_M" outputId="712cafae-c358-41d0-a935-7e9cf5915f57"}
``` python
(
    pivot_of_dynamic_2.groupby(['BANK_ID','MOUNTH'])[['PERCENTAGE_GROWTH', 'COUNT_TRANSACT']].sum()
    .sort_values(by=['MOUNTH', 'PERCENTAGE_GROWTH'], ascending=[True, False])
)
```

::: {.output .execute_result execution_count="66"}
``` json
{"summary":"{\n  \"name\": \")\",\n  \"rows\": 139,\n  \"fields\": [\n    {\n      \"column\": \"PERCENTAGE_GROWTH\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 309.4230466142443,\n        \"min\": -1109.660337920223,\n        \"max\": 2092.208300468615,\n        \"num_unique_values\": 110,\n        \"samples\": [\n          4.729729729729737,\n          -24.019607843137273,\n          0.8877246598269606\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"COUNT_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 4009837168,\n        \"min\": 400,\n        \"max\": 29064453289,\n        \"num_unique_values\": 126,\n        \"samples\": [\n          80089,\n          1849,\n          3621409\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    }\n  ]\n}","type":"dataframe"}
```
:::
:::

::: {.cell .markdown id="ZSKqQ8Jnr8ed"}
В мае банк12 демонстрирует наивысший процент роста (*PERCENTAGE_GROWTH*)
на уровне 64.24% при количестве транзакций 988036.

В июне лидирует банк12 с процентом роста в 2092.2083% с количеством
транзакций 823977025.

Интересное примечание, что в июне банк3 показал значительное снижение
активности с отрицательным ростом (-423.71%). Однако в июле он успешно
восстановился с положительным ростом (1752.74%),став лидером этого
месяца, и увеличил количество транзакций почти на 21 % в июле месяце.
:::

::: {.cell .markdown id="mH0vBr5xu2_q"}
**2. АНАЛИЗ ФРОДОВ**
:::

::: {.cell .code execution_count="88" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":206}" id="GrV6q2P6vBb1" outputId="907bd2c2-47ad-4cce-bb18-11882ef585a4"}
``` python
fraud_table = pivot_of_dynamic_2.query('FRAUD_FLAG==1')
fraud_table.head()
```

::: {.output .execute_result execution_count="88"}
``` json
{"repr_error":"0","type":"dataframe","variable_name":"fraud_table"}
```
:::
:::

::: {.cell .code execution_count="89" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":696}" id="mQOvk4AqvK4J" outputId="e1e2b909-3c00-4e75-b836-7d96273520cb"}
``` python
(
    fraud_table.groupby(['CLIENT_ID', 'CITY', 'MOUNTH', 'OPERATION_TYPE'])[['COUNT_TRANSACT']].sum()
    .sort_values(by=['MOUNTH', 'COUNT_TRANSACT'], ascending=[True, False])
)
```

::: {.output .execute_result execution_count="89"}
``` json
{"summary":"{\n  \"name\": \")\",\n  \"rows\": 134,\n  \"fields\": [\n    {\n      \"column\": \"CLIENT_ID\",\n      \"properties\": {\n        \"dtype\": \"category\",\n        \"num_unique_values\": 62,\n        \"samples\": [\n          \"A2258BCA27691A4B1F443E6307E18978C1E5727447AC15BB0D8FE4240FD3AFE9\",\n          \"3190CC3B64DE99FD94A1773284FC43242A0DB5FC3FA302374C666919605DBD72\",\n          \"878845BF4574E76A13D362CD5EC8F0D95CAFFBCDE6E744011B76A8B0AF9E704A\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"CITY\",\n      \"properties\": {\n        \"dtype\": \"category\",\n        \"num_unique_values\": 60,\n        \"samples\": [\n          \"EKATERINBURG\",\n          \"NOVOSHAKHTINS\",\n          \"UFA\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"MOUNTH\",\n      \"properties\": {\n        \"dtype\": \"period[M]\",\n        \"num_unique_values\": 1,\n        \"samples\": [\n          \"2023-07\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"OPERATION_TYPE\",\n      \"properties\": {\n        \"dtype\": \"category\",\n        \"num_unique_values\": 3,\n        \"samples\": [\n          \"\\u0421\\u043d\\u044f\\u0442\\u0438\\u0435 \\u0447\\u0435\\u0440\\u0435\\u0437 \\u0431\\u0430\\u043d\\u043a\\u043e\\u043c\\u0430\\u0442\\u044b \\u043f\\u0430\\u0440\\u0442\\u043d\\u0435\\u0440\\u043e\\u0432\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"COUNT_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 150190,\n        \"min\": 738,\n        \"max\": 1009052,\n        \"num_unique_values\": 27,\n        \"samples\": [\n          178434\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    }\n  ]\n}","type":"dataframe"}
```
:::
:::

::: {.cell .code execution_count="87" colab="{\"base_uri\":\"https://localhost:8080/\"}" id="dnAiI-Mdyj8X" outputId="0694ef86-ea2f-480d-87c6-046ca0947616"}
``` python
client_1 = '878845BF4574E76A13D362CD5EC8F0D95CAFFBCDE6E744011B76A8B0AF9E704A'
client_2 = '878845BF4574E76A13D362CD5EC8F0D95CAFFBCDE6E744011B76A8B0AF9E704A'

client_1 == client_2
```

::: {.output .execute_result execution_count="87"}
    True
:::
:::

::: {.cell .markdown id="Ai9Y2Pct1EVN"}
Наибольшее количество транзакций зафиксировано в двух записях с
одинаковыми значениями, которые были совершены одним и тем же клиентом.
Операции снятия денег происходили в Екатеринбурге, а пополнение
осуществлялось через Visa Direct, что может свидетельствовать об
электронных переводах.
:::

::: {.cell .code execution_count="96" id="5Rwax94H3pLt"}
``` python
slice_fraud = (
    fraud_table.groupby(['MOUNTH', 'OPERATION_TYPE'])[['COUNT_TRANSACT']].sum()
    .sort_values(by=['MOUNTH', 'COUNT_TRANSACT'], ascending=[True, False])
    .rename(columns={'COUNT_TRANSACT': 'FRAUD_TRANSACT'})
)
```
:::

::: {.cell .code execution_count="99" id="lrAdWkXA4Pn4"}
``` python
slice_all = (
    pivot_of_dynamic_2.groupby(['MOUNTH', 'OPERATION_TYPE'])['COUNT_TRANSACT'].sum().reset_index()
    .rename(columns={'COUNT_TRANSACT': 'TOTAL_TRANSACT'})
)
```
:::

::: {.cell .code execution_count="101" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":203}" id="QI7C4hAQ4jMn" outputId="054e882f-a5bb-46ba-c55d-7986d2961d11"}
``` python
fraud_share = slice_fraud.merge(slice_all, on=['MOUNTH', 'OPERATION_TYPE'])
fraud_share['FRAUD_SHARE'] = fraud_share['FRAUD_TRANSACT'] / fraud_share['TOTAL_TRANSACT']
fraud_share
```

::: {.output .execute_result execution_count="101"}
``` json
{"summary":"{\n  \"name\": \"fraud_share\",\n  \"rows\": 3,\n  \"fields\": [\n    {\n      \"column\": \"MOUNTH\",\n      \"properties\": {\n        \"dtype\": \"period[M]\",\n        \"num_unique_values\": 1,\n        \"samples\": [\n          \"2023-07\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"OPERATION_TYPE\",\n      \"properties\": {\n        \"dtype\": \"string\",\n        \"num_unique_values\": 3,\n        \"samples\": [\n          \"\\u0421\\u043d\\u044f\\u0442\\u0438\\u0435 \\u0447\\u0435\\u0440\\u0435\\u0437 \\u0431\\u0430\\u043d\\u043a\\u043e\\u043c\\u0430\\u0442\\u044b \\u043f\\u0430\\u0440\\u0442\\u043d\\u0435\\u0440\\u043e\\u0432\"\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"FRAUD_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 5718143,\n        \"min\": 12445,\n        \"max\": 11446042,\n        \"num_unique_values\": 3,\n        \"samples\": [\n          11446042\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"TOTAL_TRANSACT\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 279380336,\n        \"min\": 923250620,\n        \"max\": 1464371095,\n        \"num_unique_values\": 3,\n        \"samples\": [\n          1073180883\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    },\n    {\n      \"column\": \"FRAUD_SHARE\",\n      \"properties\": {\n        \"dtype\": \"number\",\n        \"std\": 0.005377081032447265,\n        \"min\": 1.3479546864533923e-05,\n        \"max\": 0.010665529158517446,\n        \"num_unique_values\": 3,\n        \"samples\": [\n          0.010665529158517446\n        ],\n        \"semantic_type\": \"\",\n        \"description\": \"\"\n      }\n    }\n  ]\n}","type":"dataframe","variable_name":"fraud_share"}
```
:::
:::

::: {.cell .markdown id="zYYBIHnP1xWd"}
**3. АНАЛИЗ ОБЫЧНЫХ ОПЕРАЦИЙ**
:::

::: {.cell .code execution_count="90" id="XbcGEFxd11i1"}
``` python
not_fraud_table = pivot_of_dynamic_2.query('FRAUD_FLAG==0')
```
:::

::: {.cell .code execution_count="91" colab="{\"base_uri\":\"https://localhost:8080/\",\"height\":714}" id="IDVDuLZV2JYx" outputId="d8084e04-f27e-40de-8378-b3be5c1dbbd7"}
``` python
(
    not_fraud_table.groupby(['CLIENT_ID', 'CITY', 'MOUNTH', 'OPERATION_TYPE'])[['COUNT_TRANSACT']].sum()
    .sort_values(by=['MOUNTH', 'COUNT_TRANSACT'], ascending=[True, False])
)
```

::: {.output .execute_result execution_count="91"}
``` json
{"type":"dataframe"}
```
:::

::: {.output .stream .stdout}
    Warning: total number of rows (367720) exceeds max_rows (20000). Limiting to first (20000) rows.
:::
:::

::: {.cell .code execution_count="92" colab="{\"base_uri\":\"https://localhost:8080/\"}" id="IuEG9q2q2bfO" outputId="71837dcb-5e7a-421f-b677-a0dba1e64e0d"}
``` python
client_3 = '8D424518D09DA4DED80259D2042793C16BFC52CBD3A819F58D6F0AE462923010'
client_4 = 'C52587788579FDB83FA5EEB92D489294702EF3AE1097C7B023377AA9D9AA4F34'

client_3 == client_4
```

::: {.output .execute_result execution_count="92"}
    False
:::
:::
