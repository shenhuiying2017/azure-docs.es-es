---
title: "Simulación de R en paralelo con Azure Batch"
description: "Tutorial: Instrucciones paso a paso para ejecutar una simulación financiera Monte Carlo en Azure Batch mediante el paquete doAzureParallel de R"
services: batch
author: jiata
manager: jkabat
ms.assetid: 
ms.service: batch
ms.devlang: r
ms.topic: tutorial
ms.date: 01/23/2018
ms.author: jiata
ms.custom: mvc
ms.openlocfilehash: 6aa12e38c1f2c86c44c00eb87906a7f0fe93cdca
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-run-a-parallel-r-simulation-with-azure-batch"></a>Tutorial: Ejecución de una simulación de R en paralelo con Azure Batch 

Ejecute las cargas de trabajo de R en paralelo a escala mediante [doAzureParallel](http://www.github.com/Azure/doAzureParallel), un paquete de R ligero que le permite usar Azure Batch directamente desde su sesión de R. El paquete doAzureParallel se basa en el conocido paquete de R [foreach](http://cran.r-project.org/web/packages/foreach/index.html). doAzureParallel toma cada iteración del bucle foreach y la envía como una tarea de Azure Batch.

En este tutorial se muestra cómo implementar un grupo de Batch y ejecutar un trabajo de R en paralelo en Azure Batch directamente dentro de RStudio. Aprenderá a:
 

> [!div class="checklist"]
> * Instalar doAzureParallel y configurarlo para acceder a las cuentas de Batch y Storage
> * Crear un grupo de Batch como un back-end en paralelo para la sesión de R
> * Ejecutar una simulación en paralelo sencilla en el grupo

## <a name="prerequisites"></a>requisitos previos

* Tener instalada una distribución de [R](https://www.r-project.org/), como [Microsoft R Open](https://mran.microsoft.com/open). Usar la versión 3.3.1 o posterior.

* [RStudio](https://www.rstudio.com/), la edición comercial o la edición de código abierto [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop). 

* Una cuenta de Azure Batch y una cuenta de Azure Storage. Para crear estas cuentas, consulte las guías de inicio rápido de Batch con [Azure Portal](quick-create-portal.md) o la [CLI de Azure](quick-create-cli.md). 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 
## <a name="install-doazureparallel"></a>Instalación de doAzureParallel

En la consola de RStudio, instale el [paquete doAzureParallel de Github](http://www.github.com/Azure/doAzureParallel). Los siguientes comandos descargan e instalan el paquete y sus dependencias en la sesión actual de R: 

```R
# Install the devtools package  
install.packages("devtools") 

# Install rAzureBatch package
devtools::install_github("Azure/rAzureBatch") 

# Install the doAzureParallel package 
devtools::install_github("Azure/doAzureParallel") 
 
# Load the doAzureParallel library 
library(doAzureParallel) 
```
La instalación puede tardar varios minutos.

Para configurar doAzureParallel con las credenciales de cuenta obtenidas anteriormente, genere un archivo de configuración denominado *credentials.json* en el directorio de trabajo: 

```R
generateCredentialsConfig("credentials.json") 
``` 

Rellene este archivo con los nombres y las claves de las cuentas de Batch y Storage. Deje el valor `githubAuthenticationToken` sin modificar.

Cuando haya finalizado, el archivo de credenciales se parecerá al siguiente: 

```json
{
  "batchAccount": {
    "name": "mybatchaccount",
    "key": "xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ==",
    "url": "https://mybatchaccount.mybatchregion.batch.azure.com"
  },
  "storageAccount": {
    "name": "mystorageaccount",
    "key": "xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=="
  },
  "githubAuthenticationToken": ""
}
```

Guarde el archivo. A continuación, ejecute el siguiente comando para establecer las credenciales de la sesión actual de R: 

```R
setCredentials("credentials.json") 
```

## <a name="create-a-batch-pool"></a>Crear un grupo de Batch 

doAzureParallel incluye una función para generar un grupo de Azure Batch (clúster) para ejecutar trabajos de R en paralelo. Los nodos ejecutan una [máquina virtual de ciencia de datos de Azure](../machine-learning/data-science-virtual-machine/overview.md) basada en Ubuntu. En esta imagen están instalados paquetes de R conocidos y paquetes de Microsoft R Open. Puede ver o personalizar determinada configuración del clúster, como el número y el tamaño de los nodos. 

Para generar un archivo JSON de configuración del clúster en el directorio de trabajo: 
 
```R
generateClusterConfig("cluster.json")
``` 
 
Abra el archivo para ver la configuración predeterminada, que incluye 3 nodos dedicados y 3 nodos de [prioridad baja](batch-low-pri-vms.md). Estos valores son solo ejemplos con los que puede experimentar o que puede modificar. Los nodos dedicados están reservados para el grupo. Los nodos de prioridad baja se ofrecen a precio reducido por la capacidad sobrante de las máquinas virtuales de Azure. Los nodos de prioridad baja dejan de estar disponibles si Azure no tiene capacidad suficiente. 

En este tutorial, cambie la configuración de la manera siguiente:

* Aumente el valor de `maxTasksPerNode` a *2* para aprovechar ambos núcleos en cada nodo.
* Establezca `dedicatedNodes` en *0* para que pueda probar las máquinas de prioridad baja disponibles para Batch. Establezca el valor `min` de `lowPriorityNodes` en *5*. Y establezca el valor `max` en *10*, o elija números más pequeños si así lo desea. 

Deje los valores predeterminados del resto de valores de configuración y guarde el archivo. El archivo debe tener un aspecto similar al siguiente:

```json
{
  "name": "myPoolName",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 0,
      "max": 0
    },
    "lowPriorityNodes": {
      "min": 5,
      "max": 10
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": []
}
```

Ahora, cree el clúster. Batch crea el grupo inmediatamente, pero tarda unos minutos en asignar e iniciar los nodos de proceso. Después de que el clúster esté disponible, regístrelo como back-end en paralelo para la sesión de R. 

```R
# Create your cluster if it does not exist; this takes a few minutes
cluster <- makeCluster("cluster.json") 
  
# Register your parallel backend 
registerDoAzureParallel(cluster) 
  
# Check that the nodes are running 
getDoParWorkers() 
```

La salida muestra el número de "trabajos de ejecución" de doAzureParallel. Este número es el número de nodos multiplicado por el valor de `maxTasksPerNode`. Si modificó la configuración del clúster descrita anteriormente, el número es *10*. 
 
## <a name="run-a-parallel-simulation"></a>Ejecución de una simulación en paralelo

Ahora que ya se creó el clúster, está listo para ejecutar el bucle foreach con el back-end en paralelo registrado (grupo de Azure Batch). Como ejemplo, ejecute una simulación financiera Monte Carlo, primero localmente mediante un bucle foreach estándar y luego ejecute foreach con Batch. Este ejemplo es una versión simplificada de la predicción del precio de unas acciones mediante la simulación de un gran número de resultados diferentes después de 5 años.

Suponga que las acciones de Contoso Corporation ganan como media 1,001 veces su precio de apertura cada día, pero tienen una volatilidad (desviación estándar) de 0,01. Dado un precio de partida de 100 USD, use una simulación de precios Monte Carlo para calcular el precio de las acciones de Contoso al cabo de 5 años.

Parámetros para la simulación Monte Carlo:

```R
mean_change = 1.001 
volatility = 0.01 
opening_price = 100 
```

Para simular los precios de cierre, defina la siguiente función:

```R
getClosingPrice <- function() { 
  days <- 1825 # ~ 5 years 
  movement <- rnorm(days, mean=mean_change, sd=volatility) 
  path <- cumprod(c(opening_price, movement)) 
  closingPrice <- path[days] 
  return(closingPrice) 
} 
```

Primero ejecute 10 000 simulaciones localmente con un bucle foreach estándar con la palabra clave `%do%`:

```R
start_s <- Sys.time() 
# Run 10,000 simulations in series 
closingPrices_s <- foreach(i = 1:10, .combine='c') %do% { 
  replicate(1000, getClosingPrice()) 
} 
end_s <- Sys.time() 
```


Trace los precios de cierre en un histograma para mostrar la distribución de los resultados:

```R
hist(closingPrices_s)
``` 

La salida es similar a la siguiente:

![Distribución de los precios de cierre](media/tutorial-r-doazureparallel/closing-prices-local.png)
  
Una simulación local completa en cuestión de segundos:

```R
difftime(end_s, start_s) 
```

El tiempo de ejecución estimado para 10 millones de resultados localmente, mediante una aproximación lineal, es de aproximadamente 30 minutos:

```R 
1000 * difftime(end_s, start_s, unit = "min") 
```


Ahora ejecute el código usando `foreach` con la palabra clave `%dopar%` para comparar cuánto se tarda en ejecutar 10 millones de simulaciones en Azure. Para paralelizar la simulación con Batch, ejecute 100 iteraciones de 100 000 simulaciones:

```R
# Optimize runtime. Chunking allows running multiple iterations on a single R instance.
opt <- list(chunkSize = 10) 
start_p <- Sys.time()  
closingPrices_p <- foreach(i = 1:100, .combine='c', .options.azure = opt) %dopar% { 
  replicate(100000, getClosingPrice()) 
} 
end_p <- Sys.time() 
```

La simulación distribuye las tareas a los nodos del grupo de Batch. Puede ver la actividad del grupo en el mapa térmico en Azure Portal. Vaya a **cuentas de Batch** > *myBatchAccount*. Haga clic en **Grupos** > *myPoolName*. 

![Mapa térmico del grupo que ejecuta tareas de R en paralelo](media/tutorial-r-doazureparallel/pool.png)

Al cabo de unos minutos, finaliza la simulación. El paquete combina automáticamente los resultados y los extrae de los nodos. A continuación, está listo para usar los resultados en la sesión de R. 

```R
hist(closingPrices_p) 
```

La salida es similar a la siguiente:

![Distribución de los precios de cierre](media/tutorial-r-doazureparallel/closing-prices.png)

¿Cuánto tiempo duró la simulación en paralelo? 

```R
difftime(end_p, start_p, unit = "min")  
```

Habrá visto que la ejecución de la simulación en el grupo de Batch proporciona un aumento significativo en el rendimiento sobre el tiempo esperado para la ejecución de la simulación localmente. 

## <a name="clean-up-resources"></a>Limpieza de recursos

El trabajo se elimina automáticamente una vez completado. Cuando el clúster ya no sea necesario, llame a la función `stopCluster` en el paquete doAzureParallel para eliminarlo:

```R
stopCluster(cluster)
```

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a:

> [!div class="checklist"]
Instalar doAzureParallel y configurarlo para acceder a las cuentas de Batch y Storage
> * Crear un grupo de Batch como un back-end en paralelo para la sesión de R
> * Ejecutar una simulación en paralelo sencilla en el grupo


Para más información sobre doAzureParallel, consulte la documentación y los ejemplos en GitHub.

> [!div class="nextstepaction"]
> [Paquete doAzureParallel](https://github.com/Azure/doAzureParallel/)




