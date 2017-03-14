---
title: "Inspección de paquetes con Azure Network Watcher | Microsoft Docs"
description: "En este artículo se describe cómo usar Network Watcher para realizar la inspección profunda de paquetes desde una máquina virtual."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 78f367de862e4fa9203cc794549abb935f117848
ms.openlocfilehash: f7e4f1832a8edd029fcef08e295b6c79c033b664
ms.lasthandoff: 02/22/2017

---

# <a name="packet-inspection-with-azure-network-watcher"></a>Inspección de paquetes con Azure Network Watcher

Mediante la característica de captura de paquetes de Network Watcher, puede iniciar y administrar sesiones de captura en las máquinas virtuales de Azure desde el portal, PowerShell, la CLI y mediante programación con el SDK y la API de REST. La captura de paquetes le permite abordar escenarios en los que se requieren datos de nivel de paquete y poder leer la información en un formato fácil de usar. Aprovechando las herramientas disponibles gratuitamente para inspeccionar los datos, puede examinar las comunicaciones enviadas a y desde las máquinas virtuales y obtener datos detallados sobre el tráfico de red. Algunos ejemplos de uso de datos de captura de paquetes son: investigación de los problemas de la red o de las aplicaciones, detección del uso incorrecto de la red y de los intentos de intrusión o mantenimiento del cumplimiento normativo. En este artículo se muestra cómo abrir un archivo de captura de paquetes proporcionado por Network Watcher mediante una conocida herramienta de código abierto. También proporcionaremos ejemplos que muestran cómo calcular la latencia de una conexión, identificar el tráfico anómalo y examinar las estadísticas de red.

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se recorren algunos escenarios preconfigurados en una captura de paquetes que se ha ejecutado anteriormente. Estos escenarios ilustran funcionalidades a las que se puede acceder mediante la revisión de una captura de paquetes. En este escenario se usa [WireShark](https://www.wireshark.org/) para inspeccionar la captura de paquetes.

Se asume también que ya se ejecutó una captura de paquetes en una máquina virtual. Para aprender a crear una captura de paquetes, consulte [Administración de capturas de paquete con el portal](network-watcher-packet-capture-manage-portal.md) o, si es con REST, consulte [Administración de capturas de paquete con la API de REST](network-watcher-packet-capture-manage-rest.md).

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Escenario

En este escenario, podrá:

* Revisar una captura de paquetes

## <a name="calculate-network-latency"></a>Calcular la latencia de red

En este escenario, se muestra cómo ver el tiempo de ida y vuelta (RTT) inicial de una conversación de Protocolo de control de transmisión (TCP) que se produce entre dos puntos de conexión.

Cuando se establece una conexión TCP, los primeros tres paquetes enviados en la conexión siguen un patrón que se conoce comúnmente como protocolo de enlace de tres vías. Mediante el examen de los dos primeros paquetes enviados en este protocolo de enlace, una solicitud inicial del cliente y una respuesta del servidor, es posible calcular la latencia de cuando se estableció esta conexión. Esta latencia se conoce como tiempo de ida y vuelta (RTT). Para más información sobre el protocolo TCP y el protocolo de enlace de tres vías, consulte el siguiente recurso. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Paso 1

Inicie WireShark.

### <a name="step-2"></a>Paso 2

Cargue el archivo **.cap** desde la captura de paquetes. Este archivo puede encontrarse en el blob que se guardó localmente en la máquina virtual, en función de cómo la haya configurado.

### <a name="step-3"></a>Paso 3

Para ver el tiempo de ida y vuelta (RTT) inicial en las conversaciones de TCP, solo se examinarán los dos primeros paquetes que participan en el protocolo de enlace TCP. Usaremos los dos primeros paquetes en el protocolo de enlace de tres vías, que son [SYN], [SYN, ACK]. Estos se nombran por las marcas establecidas en el encabezado TCP. El último paquete del protocolo de enlace, [ACK], no se usará en este escenario. El cliente envía el paquete [SYN]. Cuando se recibe, el servidor envía el paquete [ACK] como confirmación de la recepción de SYN del cliente. Aprovechando el hecho de que la respuesta del servidor requiere muy poca sobrecarga, calculamos el RTT sumando la hora a la que el cliente recibió el paquete [SYN, ACK] y la hora a la que el cliente envió el paquete [SYN].

Con WireShark este valor se calcula automáticamente.

Para ver más fácilmente los dos primeros paquetes en el protocolo de enlace de tres vías TCP, se usará la funcionalidad de filtrado proporcionada por WireShark.

Para aplicar el filtro en WireShark, expanda el segmento "Protocolo de control de transmisión" de un paquete [SYN] en la captura y examine las marcas establecidas en el encabezado TCP.

Puesto que lo que buscamos es filtrar por todos los paquetes [SYN] y [SYN, ACK], bajo las marcas compruebe que el bit Syn está establecido en 1 y luego haga clic con el botón derecho en el bit Syn -> Apply as Filter (Aplicar como filtro) -> Selected (Seleccionado).

![figura 7][7]

### <a name="step-4"></a>Paso 4

Ahora que ha filtrado la ventana para ver solo los paquetes con el conjunto de bits [SYN], puede seleccionar fácilmente conversaciones para las que le interesa ver el RTT inicial. Una manera sencilla de ver el RTT en WireShark es hacer clic en la lista desplegable marcada como análisis "SEQ/ACK". A continuación, verá que se muestra el RTT. En este caso, el RTT era 0,0022114 segundos o 2,211 ms.

![figura 8][8]

## <a name="unwanted-protocols"></a>Protocolos no deseados

Puede tener muchas aplicaciones ejecutándose en una instancia de máquina virtual que haya implementado en Azure. Muchas de estas aplicaciones se comunican a través de la red, quizás sin su permiso explícito. Con la captura de paquetes para almacenar comunicaciones de red, podemos investigar cómo la aplicación se comunica en la red y buscar posibles problemas.

En este ejemplo, revisamos una captura de paquetes ejecutada anteriormente para ver si existen protocolos indeseados que podrían indicar la comunicación no autorizada desde una aplicación que se ejecuta en su máquina.

### <a name="step-1"></a>Paso 1

Con la misma captura usada en el escenario anterior, haga clic en **Estadísticas** > **Protocol Hierarchy** (Jerarquía de protocolos).

![menú de jerarquía de protocolos][2]

Aparece la ventana de jerarquía de protocolos. Esta vista proporciona una lista de todos los protocolos que estaban en uso durante la sesión de captura y el número de paquetes transmitidos y recibidos con los protocolos. Puede ser útil para buscar el tráfico de red no deseado en las máquinas virtuales o la red.

![jerarquía de protocolo abierto][3]

Como puede ver en la siguiente captura de pantalla, había tráfico usando el protocolo BitTorrent, que se utiliza para compartir archivos de punto a punto. Como administrador, no espera ver tráfico de BitTorrent en esta máquina virtual en particular. Ahora que ya sabe de este tráfico, puede quitar el software de punto a punto instalado en esta máquina virtual o bloquear el tráfico mediante grupos de seguridad de red o un firewall. Además, puede elegir ejecutar capturas de paquetes según una programación, por lo que puede revisar el uso del protocolo en sus máquinas virtuales con regularidad. Para ver un ejemplo sobre cómo automatizar las tareas de red en Azure, visite [Supervisión de recursos de red con Azure Automation](network-watcher-monitor-with-azure-automation.md).

## <a name="finding-top-destinations-and-ports"></a>Búsqueda de puertos y destinos más frecuentes

Comprender los tipos de tráfico, los puntos de conexión y los puertos a través de los cuales se comunica es importante al supervisar las aplicaciones y los recursos de la red y solucionar los problemas asociados. Mediante un archivo de captura de paquetes como se ha indicado anteriormente, puede conocer rápidamente los destinos principales con los que se comunican nuestras máquinas virtuales y los puertos que se usan.

### <a name="step-1"></a>Paso 1

Con la misma captura usada en el escenario anterior, haga clic en **Estadísticas** > **IPv4 Statistics** (Estadísticas de IPv4)  > **Destinations and Ports** (Destinos y puertos).

![ventana de captura de paquetes][4]

### <a name="step-2"></a>Paso 2

Como observamos en los resultados, una línea destaca: había muchas conexiones en el puerto 111. El puerto más usado fue 3389, que es el escritorio remoto, y el resto son puertos dinámicos de RPC.

Aunque puede que este tráfico no signifique nada, es un puerto que se usó para muchas conexiones y es desconocido para el administrador.

![figura 5][5]

### <a name="step-3"></a>Paso 3

Ahora que hemos determinado un puerto fuera de lugar, podemos filtrar nuestra captura en función del puerto.

El filtro en este escenario sería:

```
tcp.port == 111
```

Escribimos el texto de filtro de arriba en el cuadro de texto de filtro y presionamos Entrar.

![figura 6][6]

Por los resultados, podemos ver que todo el tráfico procede de una máquina virtual local en la misma subred. Si sigue sin comprender por qué se produce este tráfico, podemos inspeccionar más a fondo los paquetes para determinar por qué se están realizando estas llamadas en el puerto 111. Con esta información podemos emprender la acción apropiada.

## <a name="next-steps"></a>Pasos siguientes

Puede encontrar más información sobre las características de diagnóstico de Network Watcher, en [Azure network monitoring overview](network-watcher-monitoring-overview.md) (Introducción a la supervisión de red de Azure).

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














