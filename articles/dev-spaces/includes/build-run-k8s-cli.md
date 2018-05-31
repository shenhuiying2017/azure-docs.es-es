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
ms.openlocfilehash: f77a036d41ce551d9eab0250eaf4dc16444b24da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371140"
---
## <a name="build-and-run-code-in-kubernetes"></a>Compilación y ejecución de código en Kubernetes
Vamos a ejecutar el código. En la ventana de terminal, ejecute este comando desde la **carpeta de código raíz** WebFrontEnd:

```cmd
azds up
```

Esté atento en la salida del comando, ya que observará varios aspectos a medida que avanza:
- El código fuente se sincroniza con el entorno de desarrollo en Azure.
- Se crea una imagen de contenedor en Azure según lo que especifiquen los recursos de Docker en la carpeta de código.
- Los objetos de Kubernetes que se crean utilizan la imagen de contenedor según lo que especifica el gráfico de Helm en la carpeta de código.
- Se muestra información acerca de los puntos de conexión del contenedor. En nuestro caso, se espera una dirección URL HTTP pública.
- Suponiendo que las fases anteriores se completen correctamente, debería empezar a ver la salida `stdout` (y `stderr`) a medida que se inicia el contenedor.

> [!Note]
> Estos pasos tardarán más tiempo la primera vez que se ejecute el comando `up`, pero las ejecuciones posteriores deberían ser más rápidas.

## <a name="test-the-web-app"></a>Prueba de la aplicación web
Examine la salida de la consola para obtener información sobre la dirección URL pública que creó el comando `up`. Tendrá el formato siguiente: 

`Running at public URL: http://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Abra esta dirección URL en una ventana del explorador y verá la aplicación web de carga. Mientras se ejecuta el contenedor, las salidas `stdout` y `stderr` se transmiten a la ventana de terminal.
