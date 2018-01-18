---
title: "Implementación de módulos para Azure IoT Edge | Microsoft Docs"
description: "Aprenda cómo se implementan los módulos en los dispositivos perimetrales."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0fb8c55937c1f4c29c542204673a2f41e3ae29db
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="understand-iot-edge-deployments-for-single-devices-or-at-scale---preview"></a>Descripción de las implementaciones de IoT Edge en un único dispositivo o a escala (versión preliminar)

Los dispositivos de Azure IoT Edge siguen un [ciclo de vida][lnk-lifecycle] que es parecido al de otros dispositivos IoT:

1. Los dispositivos de IoT Edge se aprovisionan, lo que implica crear una imagen de un dispositivo con un sistema operativo e instalar el [entorno de tiempo de ejecución de IoT Edge][lnk-runtime].
1. Los dispositivos están configurados para ejecutar [módulos de IoT Edge][lnk-modules] y luego se supervisan de cara al mantenimiento. 
1. Por último, los dispositivos se pueden retirar cuando se sustituyen o se vuelven obsoletos.  

Azure IoT Edge ofrece dos maneras de configurar los módulos para ejecutarse en dispositivos IoT Edge: una para el desarrollo y las interacciones rápidas en un único dispositivo (que ya se usó en los tutoriales de Azure IoT Edge) y otra para administrar grandes flotas de dispositivos IoT Edge. Ambos enfoques están disponibles en Azure Portal y mediante programación.

Este artículo se centra en las fases de configuración y supervisión de flotas de dispositivos, lo que se conoce en conjunto como implementaciones de IoT Edge. Los pasos de implementación general son los siguientes:   

1. Un operador define una implementación que describe un conjunto de módulos, así como los dispositivos de destino. Cada implementación tiene un manifiesto de implementación que refleja esta información. 
1. El servicio IoT Hub se comunica con todos los dispositivos de destino para configurarlos con los módulos deseados. 
1. El servicio IoT Hub recupera el estado de los dispositivos IoT Edge y los saca a la superficie para que el operador los supervise.  Por ejemplo, un operador puede ver cuándo un dispositivo Edge no está configurado correctamente o si se produce un error en un módulo en tiempo de ejecución. 
1. En cualquier momento, los nuevos dispositivos IoT Edge que satisfacen las condiciones de destino se configuran para la implementación. Por ejemplo, una implementación que tiene como destino todos los dispositivos IoT Edge del estado de Washington configura automáticamente un nuevo dispositivo IoT Edge una vez que se aprovisiona y se agrega al grupo de dispositivos del estado de Washington. 
 
Este artículo le guía por cada uno de los componentes que intervienen en la configuración y la supervisión de una implementación. Para ver un tutorial sobre la creación y la actualización de una implementación, consulte [Deploy and monitor IoT Edge modules at scale][lnk-howto] (Implementación y supervisión de módulos de IoT Edge a escala).

## <a name="deployment"></a>Implementación

En una implementación se asignan imágenes de módulos de IoT Edge para ejecutarse como instancias en un conjunto de dispositivos IoT Edge de destino. Se configura un manifiesto de implementación de IoT Edge para incluir una lista de módulos con los parámetros de inicialización correspondientes. Una implementación se puede asignar a un único dispositivo (normalmente basado en su identificador) o a un grupo de dispositivos (según etiquetas). Una vez que el dispositivo IoT Edge recibe un manifiesto de implementación, descarga e instala las imágenes de contenedor de módulos de los repositorios de contenedores respectivos, y los configura como corresponde. Después de que se crea una implementación, un operador puede supervisar el estado de implementación para ver si los dispositivos de destino están configurados correctamente.   

Para que los dispositivos se puedan configurar con una implementación, se deben aprovisionar como dispositivos IoT Edge. Los siguientes son requisitos previos y no se incluyen en la implementación:
* El sistema operativo base
* Docker 
* Aprovisionamiento del entorno de tiempo de ejecución de IoT Edge 

### <a name="deployment-manifest"></a>Manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe los módulos que se van a configurar en los dispositivos IoT Edge de destino. Contiene los metadatos de configuración de todos los módulos, incluidos los módulos del sistema necesarios (en concreto, el agente de IoT Edge y el centro de IoT Edge).  

Los metadatos de configuración de cada módulo incluyen: 
* Versión 
* type 
* Estado (p. ej., en funcionamiento o detenido) 
* Directiva de reinicio 
* Repositorio de imágenes y contenedores 
* Rutas de entrada y salida de datos 

### <a name="target-condition"></a>Condición de destino

La condición de destino se evalúa continuamente para incluir todos los nuevos dispositivos que cumplen los requisitos o para desconectar los dispositivos que ya no lo hacen durante la vigencia de la implementación. La implementación se volverá a activar si el servicio detecta cualquier cambio en la condición de destino. Por ejemplo, tiene una implementación A que tiene una condición de destino tags.environment = 'prod'. Al comenzar la implementación, hay 10 dispositivos prod. Los módulos se instalarán correctamente en estos 10 dispositivos. El estado del agente de IoT Edge se muestra con 10 dispositivos en total, 10 respuestas correctas, 0 respuestas erróneas y 0 respuestas pendientes. Ahora agregue 5 dispositivos más con tags.environment = 'prod'. El servicio detectará el cambio y el estado del agente de IoT Edge pasará a incluir 15 dispositivos en total, 10 respuestas correctas, 0 respuestas erróneas y 5 respuestas pendientes al intentar implementar los cinco nuevos dispositivos.

Use cualquier condición booleana en las etiquetas de los dispositivos gemelos o deviceId para seleccionar los dispositivos de destino. Si desea usar una condición con etiquetas, debe agregar la sección "etiquetas":{} en el dispositivo gemelo en el mismo nivel que las propiedades. [Más información acerca de las etiquetas en dispositivos gemelos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

Ejemplos de condiciones de destino:
* deviceId ='linuxprod1'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus'
* tags.environment = 'prod' OR tags.location = 'westus'
* tags.operator = 'John' AND tags.environment = 'prod' NOT deviceId = 'linuxprod1'

Estas son algunas limitaciones a la hora de construir una condición de destino:

* En dispositivos gemelos, solo puede crear una condición de destino mediante etiquetas o deviceId.
* No se permiten las comillas dobles en ninguna porción de la condición de destino. Use comillas simples.
* Las comillas simples representan los valores de la condición de destino. Por lo tanto, deberá agregar una comilla simple a otra comilla simple si esta ya forma parte del nombre del dispositivo. Por ejemplo, la condición de destino para: operator'sDevice se debería escribir como deviceId='operator''sDevice'.
* Se permiten números, letras y los siguientes caracteres en los valores de la condición de destino:-:.+%_#*?!(),=@;$

### <a name="priority"></a>Prioridad

Una prioridad define si se debe aplicar una implementación a un dispositivo de destino en relación con otras implementaciones. Una prioridad de implementación es un entero positivo, con un número más grande que indica la prioridad más alta. Si un dispositivo IoT Edge se destina a más de una implementación, se aplica aquella con una prioridad más alta.  Las implementaciones con prioridades inferiores no se aplican, ni se combinan.  Si un dispositivo se destina a dos o más implementaciones con la misma prioridad, se aplica la implementación creada más recientemente (determinada por la marca de tiempo de creación).

### <a name="labels"></a>Etiquetas 

Las etiquetas son pares de clave/valor de cadena que se pueden usar para filtrar y agrupar las implementaciones. Una implementación puede tener varias etiquetas. Las etiquetas son opcionales y no afectan a la configuración real de los dispositivos IoT Edge. 

### <a name="deployment-status"></a>Estado de implementación

Una implementación se puede supervisar para determinar si se ha aplicado correctamente a algún dispositivo IoT Edge de destino.  Un dispositivo Edge de destino puede aparecer en una o varias de las siguientes categorías de estado: 
* **Destino** muestra los dispositivos IoT Edge que coinciden con la condición de destino de implementación.
* **Real** muestra los dispositivos IoT Edge de destino que no están destinados a otra implementación de mayor prioridad.
* **Correcto** muestra los dispositivos IoT Edge que han informado al servicio que los módulos se han implementado correctamente. 
* **Incorrecto** muestra los dispositivos IoT Edge que han informado al servicio que uno o varios módulos no se han implementado correctamente. Para investigar más el error, conéctese de forma remota a esos dispositivos para examinar los archivos de registros.
* **Desconocido** muestra los dispositivos IoT Edge que no notificaron ningún estado relativo a esta implementación. Para investigar más a fondo, examine los archivos de registro e información del servicio.

## <a name="phased-rollout"></a>Lanzamiento por fases 

Un lanzamiento por fases es un proceso global en el que un operador implementa cambios en un amplio conjunto de dispositivos IoT Edge. El objetivo consiste en realizar cambios gradualmente para reducir el riesgo de realizar cambios importantes a gran escala.  

Un lanzamiento por fases se ejecuta en las fases y los pasos siguientes: 
1. Establezca un entorno de prueba de los dispositivos IoT Edge; para ello, aprovisiónelos y defina una etiqueta de dispositivo gemelo como `tag.environment='test'`. El entorno de prueba debe reflejar el entorno de producción en el que finalmente se aplicará la implementación. 
1. Cree una implementación que incluya los módulos y las configuraciones deseados. La condición de destino debe tener como objetivo probar el entorno de los dispositivos IoT Edge.   
1. Valide la nueva configuración de los módulos en el entorno de prueba.
1. Actualice la implementación para incluir un subconjunto de los dispositivos IoT Edge de producción mediante la adición de una nueva etiqueta a la condición de destino. Además, asegúrese de que la prioridad de la implementación sea mayor que la de otras implementaciones destinadas actualmente a esos dispositivos 
1. Compruebe que la implementación se realizó correctamente en los dispositivos IoT de destino examinando el estado de implementación.
1. Actualice la implementación en el resto de los dispositivos IoT Edge de producción de destino.

## <a name="rollback"></a>Reversión

Las implementaciones se pueden revertir en caso de errores o configuraciones incorrectas.  Dado que una implementación define la configuración absoluta del módulo en un dispositivo IoT Edge, las implementaciones adicionales también se deben destinar al mismo dispositivo con una prioridad inferior incluso si el objetivo es quitar todos los módulos.  

Realice las reversiones siguiendo esta secuencia: 
1. Confirme que una segunda implementación también tiene como destino el mismo conjunto de dispositivos. Si el objetivo de la reversión es quitar todos los módulos, la segunda implementación no debe incluir ningún módulo. 
1. Modifique o quite la expresión de condición de destino de la implementación que quiere revertir para que los dispositivos ya no satisfagan la condición de destino.
1. Examine el estado de implementación para comprobar que la reversión se realizó correctamente.
   * La implementación revertida ya no debe mostrar el estado de los dispositivos que se han revertido.
   * La segunda implementación ahora debe incluir el estado de implementación de los dispositivos que se han revertido.


## <a name="next-steps"></a>pasos siguientes

* Siga los pasos para crear, actualizar o eliminar una implementación en [Deploy and monitor IoT Edge modules at scale][lnk-howto] (Implementación y supervisión de módulos de IoT Edge a escala).
* Aprenda más sobre otros conceptos de IoT Edge como el [entorno de tiempo de ejecución de IoT Edge][ lnk-runtime] y los [módulos de IoT Edge][lnk-modules].

<!-- Links -->
[lnk-lifecycle]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-modules]: iot-edge-modules.md
[lnk-howto]: how-to-deploy-monitor.md