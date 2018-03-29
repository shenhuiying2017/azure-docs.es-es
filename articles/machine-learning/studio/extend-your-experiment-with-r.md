---
title: Extender el experimento con R | Microsoft Docs
description: Cómo extender la funcionalidad de Azure Machine Learning Studio mediante el lenguaje R con el módulo Ejecutar script de R.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 99c57cc095c3eb017ada68417b7b36c8773ed3d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="extend-your-experiment-with-r"></a>Extender el experimento con R
Puede extender la funcionalidad de Azure Machine Learning Studio mediante el lenguaje R con el módulo [Ejecutar script de R][execute-r-script].

Este módulo acepta varios conjuntos de datos de entrada y genera un solo conjunto de datos como salida. Puede escribir un script de R en el parámetro **Script de R** del módulo [Ejecutar script de R][execute-r-script].

Para tener acceso a cada puerto de entrada del módulo, se usa un código similar al siguiente:

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>Enumeración de todos los paquetes actualmente instalados
La lista de paquetes instalados puede cambiar. Puede encontrar una lista de los paquetes instalados actualmente en [R Packages Supported by Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt741980.aspx) (Paquetes de R compatibles con Azure Machine Learning).

También puede obtener la lista completa y actual de los paquetes instalados especificando el código siguiente en el módulo [Ejecutar script de R][execute-r-script]:

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

Esto acción envía la lista de paquetes al puerto de salida del módulo [Ejecutar script R][execute-r-script].
Para ver la lista de paquetes, conecte un módulo de conversión, como [Convertir a CSV][convert-to-csv] con la salida izquierda del módulo [Ejecutar script R][execute-r-script], ejecute el experimento y luego haga clic en la salida del módulo de conversión y seleccione **Descargar**. 

![Descarga de los resultados del módulo Convertir en CSV](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>Importación de paquetes
Puede importar paquetes que no se hayan instalado mediante el uso de los siguientes comandos en el módulo [Ejecutar script de R][execute-r-script]:

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

donde el archivo `my_favorite_package.zip` contiene el paquete.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
