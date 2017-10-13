---
title: "Herramientas de aprendizaje automático y ciencia de datos - Azure | Microsoft Docs"
description: "Herramientas de aprendizaje automático y ciencia de datos"
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: c772fa16b94d09e0fc0450ce86503553c26f8a24
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-and-data-science-tools"></a>Herramientas de aprendizaje automático y ciencia de datos
Data Science Virtual Machine (DSVM) tiene un amplio conjunto de herramientas y bibliotecas para el aprendizaje automático, disponibles en lenguajes conocidos como Python, R o Julia. 

A continuación se muestran algunas de las herramientas de aprendizaje automático y bibliotecas de DSVM. 

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| ¿Qué es?   |    Biblioteca de gradient boosting (GBDT, GBRT o GBM) rápida, portátil y distribuida para Python, R, Java, Scala, C++, etc. Se ejecuta en una única máquina, en Hadoop y en Spark    |
| Ediciones compatibles de DSVM     | Windows, Linux     |
| Usos típicos      | Biblioteca general de ML      |
| ¿Cómo se configura/instala en DSVM?      |  Se instala con la compatibilidad de GPU   |
| ¿Cómo se usa/ejecuta?      | Como biblioteca de Python (2.7 y 3.5), paquete de R y en la herramienta de línea de comandos de ruta de acceso (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows y `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Vínculos a ejemplos      | Se incluyen ejemplos en la máquina virtual, en `/dsvm/tools/xgboost/demo` en Linux y `C:\dsvm\tools\xgboost\demo` en Windows   |
| Herramientas relacionadas en DSVM      | LightGBM y MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| ¿Qué es?   |   Vowpal Wabbit (también conocido como "VW") es una biblioteca del sistema de aprendizaje de código abierto, rápida y situada fuera del núcleo    |
| Ediciones compatibles de DSVM     | Windows, Linux     |
| Usos típicos      | Biblioteca general de ML      |
| ¿Cómo se configura/instala en DSVM?      |  Windows: msi installer; Linux: apt-get |
| ¿Cómo se usa/ejecuta?      | Como en la herramienta de línea de comandos de ruta de acceso (`C:\Program Files\VowpalWabbit\vw.exe` en Windows y `/usr/bin/vw` en Linux)    |
| Vínculos a ejemplos      | [Ejemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Herramientas relacionadas en DSVM      |LightGBM, MXNet y XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| ¿Qué es?   |  Weka es una colección de algoritmos de aprendizaje automático para las tareas de minería de datos. Los algoritmos se pueden aplicar directamente a un conjunto de datos o se pueden llamar desde su propio código de Java. Weka contiene herramientas para el preprocesamiento, la clasificación, la regresión, la agrupación en clústeres, las reglas de asociación y la visualización de datos. |
| Ediciones compatibles de DSVM     | Windows, Linux     |
| Usos típicos      | Herramienta general de ML     |
| ¿Cómo se usa/ejecuta?      | En Windows, busque Weka en el menú Inicio. En Linux, inicie sesión con X2Go y vaya a Aplicaciones -> Desarrollo -> Weka. |
| Vínculos a ejemplos      | [Ejemplos de Weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Herramientas relacionadas en DSVM      |LightGBM, Rattle y XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| ¿Qué es?   |   Una interfaz gráfica de usuario para la minería de datos con R   |
| Ediciones compatibles de DSVM     | Windows, Linux     |
| Usos típicos      | Herramienta general de minería de datos de la IU para R    |
| ¿Cómo se usa/ejecuta?      | Herramienta de interfaz de usuario. En Windows, inicie un símbolo del sistema, ejecute R y, dentro de R, ejecute `rattle()`. En Linux, conéctese a X2Go, inicie un terminal, ejecute R y, dentro de R, ejecute `rattle()`. |
| Vínculos a ejemplos      | [Rattle](https://togaware.com/onepager/) |
| Herramientas relacionadas en DSVM      |LightGBM, Weka y XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Un marco de gradient boosting (GBDT, GBRT, GBM o MART) rápido, distribuido y de alto rendimiento que se basa en algoritmos de árboles de decisión, que se usa para la clasificación y muchas otras tareas relacionadas con el aprendizaje automático.    |
| Versiones de DSVM compatibles      | Windows, Linux    |
| Usos típicos      | Marco de gradient boosting de uso general      |
| ¿Cómo se configura/instala en DSVM?      | En Windows, LightGBM se instala en forma de paquete de Python. En Linux, el archivo ejecutable de la línea de comandos se encuentra en `/opt/LightGBM/lightgbm`; luego se instala el paquete de R y los paquetes de Python.     |
| Vínculos a ejemplos      | [Guía de LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Herramientas relacionadas en DSVM      | MXNet y XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Plataforma de AI de código abierto que admite el aprendizaje automático en memoria, distribuido, rápido y escalable  |
| Versiones de DSVM compatibles      | Linux   |
| Usos típicos      | ML escalable, distribuido y de uso general   |
| ¿Cómo se configura/instala en DSVM?      | H2O se instala en `/dsvm/tools/h2o`.      |
| ¿Cómo se usa/ejecuta?      | Conéctese a la máquina virtual con X2Go. Inicie un nuevo terminal y ejecute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Luego, inicie un explorador web y conéctese a `http://localhost:54321`.      |
| Vínculos a ejemplos      | Hay ejemplos disponibles en la máquina virtual de Jupyter en el directorio `h2o`.      |
| Herramientas relacionadas en DSVM      | Apache Spark, MXNet, XGBoost, Sparkling Water y Deep Water    |

Hay muchas más bibliotecas de ML en DSVM, como el famoso paquete `scikit-learn`, incluido en la distribución de Anaconda Python que se instala en DSVM. Asegúrese de revisar la lista de paquetes disponibles en Python, R y Julia ejecutando los administradores de paquetes correspondientes. 
