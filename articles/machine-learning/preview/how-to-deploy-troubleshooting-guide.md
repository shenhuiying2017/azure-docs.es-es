---
title: "Guía de solución de problemas de implementación de Azure Machine Learning | Microsoft Docs"
description: "Guía de solución de problemas para la implementación y creación de servicios"
services: machine-learning
author: raymondl
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/05/2017
ms.openlocfilehash: 066a6a223692055c7855abc63667e345ee8dc2d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="model-management-troubleshooting"></a>Solución de problemas de administración de modelos
La siguiente información le puede ayudar a determinar la causa de los errores que se producen durante la implementación o al llamar al servicio web.
 
## <a name="1-service-logs"></a>1. Registros de servicios
La opción `logs` de la CLI del servicio proporciona datos de registro de Docker y Kubernetes.

```
az ml service logs realtime -i <web service id>
```

Para una configuración adicional del registro, use la opción `--help` (o `-h`).

```
az ml service logs realtime -h
```

## <a name="2-debug-and-verbose-options"></a>2. Opciones de depuración y detallado
Use la marca `--debug` para mostrar los registros de depuración a medida que se implementa el servicio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

Use la marca `--verbose` para consultar detalles adicionales a medida que se implementa el servicio.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

## <a name="3-app-insights"></a>3. Detalles de la aplicación
Establezca la marca `-l` en true cuando cree un servicio web para habilitar el registro en el nivel de solicitud. Los registros de solicitudes se escriben en la instancia de App Insights del entorno en Azure. Busque esta instancia mediante el nombre del entorno que utilizó al usar el comando `az ml env setup`.

- Establezca `-l` en true al crear el servicio.
- Abra App Insights en Azure Portal. Use el nombre del entorno para encontrar la instancia de App Insights.
- Una vez en App Insights, haga clic en Buscar en el menú superior para ver los resultados.
- O vaya a `Analytics` > `Exceptions` > `exceptions take | 10`.


## <a name="4-error-handling-in-script"></a>4. Control de errores en el script
Use el control de excepciones en la función **run** del script `scoring.py` para devolver el mensaje de error como parte de la salida del servicio web.

Ejemplo de Python:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="5-other-common-problems"></a>5. Otros problemas comunes
- Si el comando `env setup` produce un error, asegúrese de que tiene suficientes núcleos disponibles en su suscripción.
- No utilice el guión bajo (_) en el nombre del servicio web (como en *my_webservice*).
- Vuelva a intentarlo si se produce un error **502 - Puerta de enlace no válida** al llamar al servicio web. Normalmente esto significa que el contenedor no se ha implementado todavía en el clúster.
- Si obtiene el error **CrashLoopBackOff** al crear un servicio, compruebe los registros. Normalmente es el resultado de dependencias que faltan en la función **init**.