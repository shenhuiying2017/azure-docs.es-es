---
title: archivo de inclusión
description: archivo de inclusión
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367377"
---
### <a name="run-the-service"></a>Ejecución del servicio

1. Presione la tecla F5 (o escriba `azds up` en la ventana de terminal) para ejecutar el servicio. El servicio se ejecutará automáticamente en el espacio recién seleccionado `scott`. 
1. Puede confirmar que el servicio se ejecuta en su propio espacio mediante la ejecución de `azds resource list` de nuevo. En primer lugar, observará que se ejecuta una instancia de `mywebapi` en el espacio `scott` (la versión que se ejecuta en `default` sigue ejecutándose, pero no aparece). En segundo lugar, la dirección URL del punto de acceso para `webfrontend` tiene como prefijo el texto *scott.s.*. Esta dirección URL es única para el espacio `scott` y significa que las solicitudes enviadas a la "dirección URL de scott" intentarán, en primer lugar, redirigir los servicios del espacio `scott` y realizarán la conmutación por recuperación en los servicios del espacio `default`.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Esta funcionalidad integrada de Azure Dev Spaces le permite probar el código de un extremo a otro en un entorno compartido sin necesidad de que cada desarrollador vuelva a crear la pila completa de servicios en su espacio. Este enrutamiento requiere que el código de la aplicación reenvíe encabezados de propagación, como se ilustra en el paso anterior de esta guía.

## <a name="test-code-in-a-space"></a>Prueba de código en un espacio
Para probar la nueva versión de `mywebapi` junto con `webfrontend`, abra el explorador en la dirección URL del punto de acceso público de webfrontend y vaya a la página Acerca de. Debería ver el nuevo mensaje.

Ahora, quite la parte "scott.s." de la dirección URL y actualice el explorador. Debería ver el comportamiento anterior (mostrado por la versión de `mywebapi` que se ejecuta en `default`)