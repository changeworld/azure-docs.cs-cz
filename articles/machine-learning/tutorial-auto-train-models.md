---
title: 'Kurz regrese: Automatické ML'
titleSuffix: Azure Machine Learning
description: V tomto kurzu se dozvíte, jak generovat model strojového učení pomocí automatizovaného strojového učení. Azure Machine Learning můžete provádět předběžné zpracování dat, výběr algoritmů a hyperparametrvýběr automatizovaným způsobem pro vás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 75e61ea3f4fa6c2b346f912a9effd66ad94e7e93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77116446"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Kurz: Pomocí automatizovaného strojového učení předvídejte tarify taxislužby
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu použijete automatizované strojové učení v Azure Machine Learning k vytvoření regresního modelu k předvídání cen jízdného taxi NYC. Tento proces přijímá trénovací data a nastavení konfigurace a automaticky iteruje prostřednictvím kombinací různých metod normalizace/standardizace funkcí, modelů a nastavení hyperparametrů, aby se dospělo k nejlepšímu modelu.

![Vývojový diagram](./media/tutorial-auto-train-models/flow2.png)

V tomto kurzu se dozvíte následující úkoly:

> [!div class="checklist"]
> * Stahování, transformace a čištění dat pomocí otevřených datových sad Azure
> * Trénování automatizovaného regresního modelu strojového učení
> * Vypočítat přesnost modelu

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, než začnete. Vyzkoušejte [bezplatnou nebo placenou verzi](https://aka.ms/AMLFree) Azure Machine Learning ještě dnes.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte pracovní prostor Azure Machine Learning nebo virtuální počítač s notebookem, dokončete kurz [nastavení.](tutorial-1st-experiment-sdk-setup.md)
* Po dokončení kurzu nastavení otevřete *výukové programy/regrese-automl-nyc-taxi-data/regression-automated-ml.ipynb* notebook pomocí stejného notebooku.

Tento kurz je také k dispozici na [GitHubu,](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) pokud ho chcete spustit ve svém vlastním [místním prostředí](how-to-configure-environment.md#local). Spuštěním `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` získáte požadované balíčky.

## <a name="download-and-prepare-data"></a>Stahování a příprava dat

Importujte potřebné balíčky. Balíček Open Datasets obsahuje třídu představující`NycTlcGreen` každý zdroj dat (například) pro snadné filtrování parametrů data před stažením.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Začněte vytvořením datového rámce pro uložení dat taxi. Při práci v prostředí, které není součástí sparku, umožňují otevřené datové sady stahovat `MemoryError` pouze jeden měsíc dat najednou s určitými třídami, aby se zabránilo velkým datovým souborům.

Chcete-li stáhnout data taxi, iterativně načíst jeden měsíc `green_taxi_df` v době, a před připojením k náhodně vzorku 2000 záznamů z každého měsíce, aby se zabránilo nadýmání datového rámce. Pak náhled dat.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

<div>
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>id_dodavatele</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripVzdálenost</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupDlouhá nost</th>
      <th>pickupLatitude</th>
      <th>dropoffZeměpisná</th>
      <th>...</th>
      <th>paymentType</th>
      <th>fareAmount</th>
      <th>Extra</th>
      <th>mtaTax</th>
      <th>zlepšeníSurcharge</th>
      <th>tipAmount</th>
      <th>mýtnéČástka</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>15.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>2</td>
      <td>4.50</td>
      <td>1,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>2</td>
      <td>4,00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>2</td>
      <td>6.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>2</td>
      <td>6.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>2</td>
      <td>12.50</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>1</td>
      <td>7.00</td>
      <td>0,00</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>2</td>
      <td>5.00</td>
      <td>0,50</td>
      <td>0,50</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
    </tr>
  </tbody>
</table>
<p>10 řádků × 23 sloupců</p>
</div>


Nyní, když jsou počáteční data načtena, definujte funkci pro vytvoření různých funkcí založených na čase z pole data vyzvednutí. Tím se vytvoří nová pole pro číslo měsíce, den v měsíci, den v týdnu a hodinu dne a umožní modelu faktor v časové osvojnosti. Použijte `apply()` funkci na datovém rámci k opakovanému použití `build_time_features()` funkce pro každý řádek v datech taxi.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

<div>
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>id_dodavatele</th>
      <th>lpepPickupDatetime</th>
      <th>lpepDropoffDatetime</th>
      <th>passengerCount</th>
      <th>tripVzdálenost</th>
      <th>puLocationId</th>
      <th>doLocationId</th>
      <th>pickupDlouhá nost</th>
      <th>pickupLatitude</th>
      <th>dropoffZeměpisná</th>
      <th>...</th>
      <th>zlepšeníSurcharge</th>
      <th>tipAmount</th>
      <th>mýtnéČástka</th>
      <th>ehailFee</th>
      <th>totalAmount</th>
      <th>tripType</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>131969</th>
      <td>2</td>
      <td>2015-01-11 05:34:44</td>
      <td>2015-01-11 05:45:03</td>
      <td>3</td>
      <td>4.84</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.88</td>
      <td>40.84</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>16.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1129817</th>
      <td>2</td>
      <td>2015-01-20 16:26:29</td>
      <td>2015-01-20 16:30:26</td>
      <td>1</td>
      <td>0.69</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.81</td>
      <td>-73.96</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>20</td>
      <td>1</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1278620</th>
      <td>2</td>
      <td>2015-01-01 05:58:10</td>
      <td>2015-01-01 06:00:55</td>
      <td>1</td>
      <td>0.45</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>4,80</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>348430</th>
      <td>2</td>
      <td>2015-01-17 02:20:50</td>
      <td>2015-01-17 02:41:38</td>
      <td>1</td>
      <td>0,00</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.81</td>
      <td>40.70</td>
      <td>-73.82</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>17</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1269627</th>
      <td>1</td>
      <td>2015-01-01 05:04:10</td>
      <td>2015-01-01 05:06:23</td>
      <td>1</td>
      <td>0,50</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.92</td>
      <td>40.76</td>
      <td>-73.92</td>
      <td>...</td>
      <td>0</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>5.00</td>
      <td>1,00</td>
      <td>1</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
    </tr>
    <tr>
      <th>811755</th>
      <td>1</td>
      <td>2015-01-04 19:57:51</td>
      <td>2015-01-04 20:05:45</td>
      <td>2</td>
      <td>1.10</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>7.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>4</td>
      <td>6</td>
      <td>19</td>
    </tr>
    <tr>
      <th>737281</th>
      <td>1</td>
      <td>2015-01-03 12:27:31</td>
      <td>2015-01-03 12:33:52</td>
      <td>1</td>
      <td>0,90</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.88</td>
      <td>40.76</td>
      <td>-73.87</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>3</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>113951</th>
      <td>1</td>
      <td>2015-01-09 23:25:51</td>
      <td>2015-01-09 23:39:52</td>
      <td>1</td>
      <td>3.30</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.96</td>
      <td>40.72</td>
      <td>-73.91</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>13.80</td>
      <td>1,00</td>
      <td>1</td>
      <td>9</td>
      <td>4</td>
      <td>23</td>
    </tr>
    <tr>
      <th>150436</th>
      <td>2</td>
      <td>2015-01-11 17:15:14</td>
      <td>2015-01-11 17:22:57</td>
      <td>1</td>
      <td>1.19</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.95</td>
      <td>...</td>
      <td>0.3</td>
      <td>1,75</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>9.55</td>
      <td>1,00</td>
      <td>1</td>
      <td>11</td>
      <td>6</td>
      <td>17</td>
    </tr>
    <tr>
      <th>432136</th>
      <td>2</td>
      <td>2015-01-22 23:16:33</td>
      <td>2015-01-22 23:20:13</td>
      <td>1</td>
      <td>0.65</td>
      <td>Žádný</td>
      <td>Žádný</td>
      <td>-73.94</td>
      <td>40.71</td>
      <td>-73.94</td>
      <td>...</td>
      <td>0.3</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>Nan</td>
      <td>6.30</td>
      <td>1,00</td>
      <td>1</td>
      <td>22</td>
      <td>3</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
<p>10 řádků × 27 sloupců</p>
</div>

Odeberte některé sloupce, které nebudete potřebovat pro školení nebo další vytváření funkcí.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Vyčistit data

Spuštěním `describe()` funkce v novém datovém rámci zobrazíte souhrnné statistiky pro každé pole.

```python
green_taxi_df.describe()
```

<div>
<style scoped>.dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>id_dodavatele</th>
      <th>passengerCount</th>
      <th>tripVzdálenost</th>
      <th>pickupDlouhá nost</th>
      <th>pickupLatitude</th>
      <th>dropoffZeměpisná</th>
      <th>dropoffLatitude</th>
      <th>totalAmount</th>
      <th>month_num</th>
      <th>day_of_month</th>
      <th>day_of_week</th>
      <th>hour_of_day</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
      <td>48000.00</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1.78</td>
      <td>1.37</td>
      <td>2.87</td>
      <td>-73.83</td>
      <td>40.69</td>
      <td>-73.84</td>
      <td>40.70</td>
      <td>14.75</td>
      <td>6.50</td>
      <td>15.13</td>
      <td>3.27</td>
      <td>13.52</td>
    </tr>
    <tr>
      <th>Standardní</th>
      <td>0,41</td>
      <td>1,04</td>
      <td>2.93</td>
      <td>2.76</td>
      <td>1,52</td>
      <td>2.61</td>
      <td>1.44</td>
      <td>12.08</td>
      <td>3.45</td>
      <td>8.45</td>
      <td>1.95</td>
      <td>6.83</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-74.66</td>
      <td>0,00</td>
      <td>-300.00</td>
      <td>1,00</td>
      <td>1,00</td>
      <td>0,00</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>25 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.06</td>
      <td>-73.96</td>
      <td>40.70</td>
      <td>-73.97</td>
      <td>40.70</td>
      <td>7.80</td>
      <td>3,75</td>
      <td>8.00</td>
      <td>2,00</td>
      <td>9.00</td>
    </tr>
    <tr>
      <th>50 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>1.90</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>-73.94</td>
      <td>40.75</td>
      <td>11.30</td>
      <td>6.50</td>
      <td>15.00</td>
      <td>3,00</td>
      <td>15.00</td>
    </tr>
    <tr>
      <th>75 %</th>
      <td>2,00</td>
      <td>1,00</td>
      <td>3,60</td>
      <td>-73.92</td>
      <td>40.80</td>
      <td>-73.91</td>
      <td>40.79</td>
      <td>17.80</td>
      <td>9.25</td>
      <td>22.00</td>
      <td>5.00</td>
      <td>19.00</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2,00</td>
      <td>9.00</td>
      <td>97.57</td>
      <td>0,00</td>
      <td>41.93</td>
      <td>0,00</td>
      <td>41.94</td>
      <td>450.00</td>
      <td>12.00</td>
      <td>30.00</td>
      <td>6.00</td>
      <td>23.00</td>
    </tr>
  </tbody>
</table>
</div>


Ze souhrnných statistik zjistíte, že existuje několik polí, která mají odlehlé hodnoty nebo hodnoty, které sníží přesnost modelu. Nejprve filtrujte lat/long pole, která mají být v mezích oblasti Manhattanu. Tím se odfiltrují delší cesty taxíkem nebo výlety, které jsou odlehlé, pokud jde o jejich vztah k jiným funkcím.

Navíc filtrujte `tripDistance` pole tak, aby bylo větší než nula, ale menší než 31 mil (vzdálenost haversine mezi dvěma dvojicemi lat/long). Tím se eliminují dlouhé odlehlé cesty, které mají nekonzistentní náklady na cestu.

A konečně, `totalAmount` pole má záporné hodnoty pro taxi tarify, které nemají smysl `passengerCount` v kontextu našeho modelu, a pole má špatná data s minimální hodnoty jsou nulové.

Odfiltrovat tyto anomálie pomocí funkce dotazu a potom odebrat několik posledních sloupců zbytečné pro školení.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Zavolejte `describe()` znovu na data, aby zajistily čištění pracoval podle očekávání. Nyní máte připravenou a vyčištěnou sadu dat taxi, dovolené a počasí, která můžete použít pro školení modelů strojového učení.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurace pracovního prostoru

Vytvořte objekt pracovního prostoru z existujícího pracovního prostoru. Pracovní [prostor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) je třída, která přijímá vaše informace o předplatném Azure a o prostředcích. Také vytvoří cloudový prostředek pro sledování a sledování spuštění modelu. `Workspace.from_config()`Přečte soubor **config.json** a načte podrobnosti ověřování do objektu s názvem `ws`. `ws` se používá ve zbývající části kódu v tomto kurzu.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Rozdělení dat do vlakových a testovacích souprav

Rozdělte data do trénovacích `train_test_split` a `scikit-learn` testovacích sad pomocí funkce v knihovně. Tato funkce odděluje data do datové sady x (**funkce**) pro trénování modelu a sadu dat y (**hodnoty pro předpověď)** pro testování.

Parametr `test_size` určuje procento dat, která mají být přidělena testování. Parametr `random_state` nastaví osivo na náhodný generátor, tak, aby vaše rozdělení vlak-test jsou deterministické.

```python
from sklearn.model_selection import train_test_split

y_df = final_df.pop("totalAmount")
x_df = final_df

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
```

Účelem tohoto kroku je mít datové body k testování dokončeného modelu, které nebyly použity k trénování modelu, za účelem měření skutečné přesnosti.

Jinými slovy dobře trénovaný model by měl být schopen přesně provádět předpovědi z dat, která ještě neviděl. Nyní máte data připravená pro automatické trénování modelu strojového učení.

## <a name="automatically-train-a-model"></a>Automatické trénování modelu

Chcete-li model automaticky trénovat, postupujte takto:
1. Definujte nastavení pro spuštění experimentu. Připojte trénovací data ke konfiguraci a upravte nastavení, která řídí proces školení.
1. Odešlete experiment pro ladění modelu. Po odeslání experimentu proces iteruje prostřednictvím různých algoritmů strojového učení a nastavení hyperparametrů, dodržování definovaných omezení. Optimalizuje metriku přesnosti.

### <a name="define-training-settings"></a>Definování nastavení školení

Definujte parametr experimentu a nastavení modelu pro trénování. Zobrazení úplného seznamu [nastavení](how-to-configure-auto-train.md). Odeslání experimentu s těmito výchozími nastaveními bude trvat přibližně 5-20 minut, `experiment_timeout_minutes` ale pokud chcete kratší dobu běhu, snižte parametr.

|Vlastnost| Hodnota v tomto kurzu |Popis|
|----|----|---|
|**iteration_timeout_minutes**|2|Časový limit v minutách pro každou iteraci. Snižte tuto hodnotu, abyste snížili celkovou dobu běhu.|
|**experiment_timeout_minutes**|20|Maximální množství času v minutách, které mohou trvat všechny iterace v kombinaci před ukončením experimentu.|
|**enable_early_stopping**|True|Příznak, který umožní předčasné ukončení, pokud se skóre v krátkodobém horizontu nezlepšuje.|
|**primary_metric**| spearman_correlation | Metrika, kterou chcete optimalizovat Nejvhodnější model bude vybrán na základě této metriky.|
|**featurization**| auto | Pomocí **automatického**experimentu lze předem zpracovat vstupní data (zpracování chybějících dat, převod textu na číselné atd.)|
|**Podrobnost**| logging.INFO | Řídí úroveň protokolování.|
|**n_cross_validations**|5|Počet rozdělení křížového ověření, které má být provést, pokud nejsou zadána ověřovací data.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_minutes": 20,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Jako parametr `**kwargs` `AutoMLConfig` objektu použijte definovaná nastavení školení. Kromě toho zadejte trénovací data a `regression` typ modelu, který je v tomto případě.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             X=x_train.values,
                             y=y_train.values.flatten(),
                             **automl_settings)
```

> [!NOTE]
> Automatizované kroky předběžného zpracování strojového učení (normalizace funkcí, zpracování chybějících dat, převod textu na číselné atd.) se stanou součástí základního modelu. Při použití modelu pro předpovědi, stejné kroky předběžného zpracování použít během trénování se použijí na vstupní data automaticky.

### <a name="train-the-automatic-regression-model"></a>Trénování automatického regresního modelu

Vytvořte objekt experimentu v pracovním prostoru. Experiment funguje jako kontejner pro vaše jednotlivé běží. Předajte definovaný `automl_config` objekt experimentu a `True` nastavte výstup pro zobrazení průběhu během běhu.

Po spuštění experimentu se zobrazí aktualizace výstupu živě při spuštění experimentu. Pro každou iteraci se zobrazí typ modelu, doba trvání běhu a přesnost školení. Pole `BEST` sleduje nejlepší skóre běžeckého tréninku na základě typu metriky.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

    Running on local machine
    Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
    Current status: DatasetFeaturization. Beginning to featurize the dataset.
    Current status: DatasetEvaluation. Gathering dataset statistics.
    Current status: FeaturesGeneration. Generating features for the dataset.
    Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
    Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
    Current status: ModelSelection. Beginning model selection.

    ****************************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ****************************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
             1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
             3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
             4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
             5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
             6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
             7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
             8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
             9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
            10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
            11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
            12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
            13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
            14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
            15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
            17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
            18   VotingEnsemble                                 0:00:23       0.9471    0.9471
            19   StackEnsemble                                  0:00:27       0.9463    0.9471

## <a name="explore-the-results"></a>Kontrola výsledků

Prozkoumejte výsledky automatického tréninku pomocí [widgetu Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Widget umožňuje zobrazit graf a tabulku všech jednotlivých iterací spuštění spolu s metrikami přesnosti školení a metadaty. Kromě toho můžete filtrovat na různé metriky přesnosti než primární metriky s rozbalovacím voličem.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter widget](./media/tutorial-auto-train-models/automl-dash-output.png)
![spustit podrobnosti Jupyter widget spiknutí](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Načtení nejlepšího modelu

Vyberte nejlepší model z iterací. Funkce `get_output` vrátí nejlepší spuštění a namontovaný model pro poslední vyvolání uložení. Pomocí přetížení na `get_output`, můžete načíst nejlepší spustit a vybavené model pro všechny protokolované metriky nebo konkrétní iteraci.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Otestujte nejlepší přesnost modelu

Pomocí nejlepšímodel ke spuštění předpovědi na testovací datové sady předpovědět taxi tarify. Funkce `predict` používá nejlepší model a předpovídá hodnoty y, náklady `x_test` na **cestu**, ze sady dat. Tisk prvních 10 hodnot předpokládaných nákladů z `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Vypočítejte `root mean squared error` výsledky. Převeďte `y_test` datový rámec na seznam a porovnejte je s předpovídanou hodnotami. Funkce `mean_squared_error` trvá dvě pole hodnot a vypočítá průměrnou kvadračovou chybu mezi nimi. Vezmeme-li druhou odmocninu výsledku dává chybu ve stejných jednotkách jako proměnná y, **náklady**. To ukazuje zhruba, jak daleko taxi jízdné předpovědi jsou od skutečné jízdné.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Spusťte následující kód pro výpočet střední absolutní procentuální chyby (MAPE) pomocí úplných `y_actual` a `y_predict` datových sad. Tato metrika vypočítá absolutní rozdíl mezi každou předpokládanou a skutečnou hodnotou a sečte všechny rozdíly. Pak vyjadřuje tento součet jako procento součtu skutečných hodnot.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.14353867606052823

    Model Accuracy:
    0.8564613239394718


Ze dvou metrik přesnosti předpovědi zjistíte, že model je poměrně dobrý v předpovídání tarifů taxi z funkcí datové sady, obvykle v rozmezí +- $ 4.00 a přibližně 15% chyby.

Tradiční proces vývoje modelu strojového učení je vysoce náročný na zdroje a vyžaduje významné znalosti domény a investice do času ke spuštění a porovnání výsledků desítek modelů. Použití automatizovaného strojového učení je skvělý způsob, jak rychle otestovat mnoho různých modelů pro váš scénář.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nedokončete tuto část, pokud plánujete spustit další kurzy Azure Machine Learning.

### <a name="stop-the-compute-instance"></a>Zastavení výpočetní instance

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít prostředky, které jste vytvořili, odstraňte je, abyste vám nevznikly žádné poplatky.

1. Úplně nalevo na webu Azure Portal vyberte **Skupiny prostředků**.
1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.
1. Vyberte **Odstranit skupinu prostředků**.
1. Zadejte název skupiny prostředků. Vyberte **Odstranit**.

Skupinu prostředků můžete také zachovat, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto automatizovaném kurzu strojového učení jste provedli následující úkoly:

> [!div class="checklist"]
> * Nakonfiguroval pracovní prostor a připravil data pro experiment.
> * Trénovaný pomocí automatizovanéregresní model místně s vlastní parametry.
> * Prozkoumané a zrecenzované výsledky školení.

[Nasaďte svůj model](tutorial-deploy-models-with-aml.md) pomocí Azure Machine Learning.
