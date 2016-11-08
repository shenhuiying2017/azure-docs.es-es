---
title: Información general sobre Estado de los recursos de Azure | Microsoft Docs
description: Estado de los recursos de Azure. Información general
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''

ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz

---
# Información general sobre Estado de los recursos de Azure
Estado de los recursos de Azure es un servicio que expone el estado de los recursos individuales de Azure y proporciona instrucciones para solucionar problemas. En un entorno de nube en el que no es posible tener acceso directo a los servidores o a los elementos de la infraestructura, el objetivo de Estado de los recursos es reducir el tiempo que los clientes gastan en solucionar problemas, especialmente reducir el tiempo que dedican a determinar si la raíz del problema reside dentro de la aplicación o si la causa se encuentra en un evento dentro de la plataforma de Azure.

## ¿Qué se considera que es un recurso y cómo Estado de los recursos decide si el estado de un recurso es correcto?
Un recurso es una instancia creada por un usuario de un tipo de recurso proporcionado por un servicio, por ejemplo: una máquina virtual, una aplicación web o una base de datos SQL.

Estado de los recursos se basa en las señales emitidas por el recurso y/o en el servicio para determinar si el estado de un recurso es correcto o no. Es importante tener en cuenta que en la actualidad Estado de los recursos solo lleva el mantenimiento de un tipo específico de recursos y no tiene en cuenta otros elementos que pueden contribuir al estado general. Por ejemplo, cuando realiza un informe sobre el estado de una máquina virtual, solo se considera la parte de proceso de la infraestructura, es decir, los problemas en la red no se mostrarán en Estado de los recursos, a menos que haya una interrupción del servicio declarada, en cuyo caso aparecerá en un mensaje emergente en la parte superior de la hoja. Más adelante en este artículo se ofrece más información sobre interrupciones del servicio.

## ¿Cuál es la diferencia entre Estado de los recursos y el panel de estado del servicio?
La información proporcionada por el servicio Estado de los recursos es más granular que la que se proporciona en el panel de estado del servicio. Mientras que el panel de estado comunica los eventos que afectan a la disponibilidad de un servicio en una región, Estado de los recursos expone información relevante de un recurso específico, por ejemplo, mostrará los eventos que afectan a la disponibilidad de una máquina virtual, una aplicación web o una base de datos SQL. Por ejemplo, si un nodo se reinicia de forma inesperada, los clientes cuyas máquinas virtuales se estaban ejecutando en ese nodo pueden conocer el motivo por el que su máquina virtual no estuvo disponible durante un período específico.

## Acceso a Estado de los recursos
Para los servicios disponibles a través de Estado de los recursos, existen 2 formas de acceder.

### Portal de Azure
La hoja Estado de los recursos en el Portal de Azure proporciona información detallada sobre el estado del recurso, así como las acciones recomendadas que varían dependiendo del estado actual del recurso. Esta hoja proporciona la mejor experiencia a la hora de consultar Estado de los recursos, ya que facilita el acceso a otros recursos dentro del portal. Como se mencionó antes, el conjunto de acciones recomendadas en la hoja Estado de los recursos varían según el estado en ese momento:

* Recursos correctos: puesto que no se ha detectado ningún problema que podría afectar al estado del recurso, las acciones se centran en ayudar al proceso de solución de problemas. Por ejemplo, proporciona acceso directo a la hoja de solución de problemas, que se ofrece orientación sobre cómo resolver los problemas más comunes con los que se puede encontrar el cliente.
* Recurso incorrecto: en el caso de los problemas causados por Azure, la hoja mostrará las acciones que Microsoft está realizando (o ya ha realizado) para recuperar el recurso. Para los problemas causados por acciones iniciadas por el usuario, la hoja presentará una lista de acciones que los clientes pueden realizar para solucionar el problema y recuperar el recurso.

Una vez que haya iniciado sesión en el Portal de Azure, hay dos maneras de obtener acceso a la hoja de Estado de los recursos:

### Abrir la hoja del recurso
Abra la hoja de recursos para un recurso determinado. En la hoja Configuración que se abre junto a la hoja del recurso, haga clic en Estado de los recursos para abrir la hoja Estado de los recursos.

![Hoja de Estado de los recursos](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### Abrir la hoja de ayuda y soporte técnico
Abra la hoja de ayuda y soporte técnico haciendo clic en el signo de interrogación en la esquina superior derecha y seleccionando Ayuda y soporte técnico.

**Desde la barra de navegación superior**

![Ayuda y soporte técnico](./media/resource-health-overview/HelpAndSupport.png)

Al hacer clic en el icono se abrirá la hoja de la suscripción de Estado de los recursos que mostrará una lista de todos los recursos en su suscripción. Junto a cada recurso hay un icono que indica su estado. Al hacer clic en cada recurso se abrirá la hoja de Estado de los recursos.

**Icono Estado de los recursos**

![Icono Estado de los recursos](./media/resource-health-overview/resourceHealthTile.png)

## ¿Qué significa el estado de mantenimiento de los recursos que veo en la pantalla?
Hay 4 estados de mantenimiento distintos que pueden aparecer para un recurso.

### Disponible
El servicio no ha detectado ningún problema en la plataforma que podría estar afectando a la disponibilidad del recurso. Esto se indica mediante un icono de marca de verificación verde.

![Recurso está disponible](./media/resource-health-overview/Available.png)

### No disponible
En este caso el servicio ha detectado un problema en curso en la plataforma que afecta a la disponibilidad de este recurso, por ejemplo, el nodo donde se ejecuta la máquina virtual se reinició de forma inesperada. Esto se indica mediante un icono de advertencia rojo. En la sección intermedia de la hoja, se proporciona información adicional sobre el problema que incluye:

1. Las acciones que está realizando Microsoft para recuperar el recurso
2. Una escala de tiempo detallada del problema, incluyendo el tiempo de resolución esperado
3. Una lista de medidas recomendadas para los usuarios

![Recurso no está disponible](./media/resource-health-overview/Unavailable.png)

### No disponible: iniciado por el cliente
El recurso no está disponible debido a una solicitud de cliente, como la detención de un recurso o la petición de reinicio. Esto se indica mediante un icono azul de información.

![Recurso no está disponible debido a una acción iniciada por el usuario](./media/resource-health-overview/userInitiated.png)

### Desconocido
El servicio no ha recibido información sobre este recurso durante más de 5 minutos. Esto se indica mediante un icono de signo de interrogación gris.

Es importante tener en cuenta que esto no es una indicación definitiva de que hay algún problema con un recurso, por lo que los clientes deben seguir estas recomendaciones:

* Si el recurso se está ejecutando según lo esperado, pero su estado aparece como Desconocido en Estado de los recursos, no hay problemas y cabe esperar que el estado del recurso se actualice a correcto después de unos minutos.
* Si hay problemas de acceso al recurso y su estado es Desconocido en Estado de los recursos, esto podría ser una indicación temprana de hay un problema y deben realizarse investigaciones adicionales hasta que el estado se actualice a correcto o incorrecto

![Estado del recurso es desconocido](./media/resource-health-overview/unknown.png)

## Eventos que afectan al servicio
Si está en curso un evento que afecte al servicio que pueda tener repercusiones en el recurso, se mostrará un mensaje emergente en la parte superior de la hoja de Estado de los recursos. Al hacer clic en el mensaje se abrirá la hoja Eventos de auditoría, que mostrará más información de la interrupción.

![Estado del recursos puede verse afectado por un evento que afecta al servicio](./media/resource-health-overview/serviceImpactingEvent.png)

## ¿Qué más necesito saber sobre Estado de los recursos?
### Latencia de señal
Las señales que alimentan Estado de los recursos pueden tener un retraso de hasta 15 minutos, lo que puede producir discrepancias entre el estado de mantenimiento actual del recurso y su disponibilidad real. Es importante tener esto presente para eliminar en lo posible tiempo innecesario en investigar problemas no confirmados.

### Caso especial de SQL
Estado de los recursos informa sobre el estado de la base de datos SQL, no de SQL Server. Aunque esta opción proporcione una idea más realista del estado, también requiere que se tengan en cuenta varios componentes y servicios para determinar el estado de la base de datos. La señal actual se basa en los inicios de sesión en la base de datos, lo que significa que en el caso de las bases de datos con inicios de sesión regulares (lo que incluye entre otras cosas, recibir solicitudes de ejecución de consulta) el estado de mantenimiento se mostrará con regularidad. Si no ha habido accesos a la base de datos durante un período de 10 minutos o más, se moverá al estado desconocido. Esto no significa que la base de datos no está disponible, simplemente que no se ha emitido ninguna señal porque no se han realizado inicios de sesión. La conexión a la base de datos y la ejecución de una consulta emitirá las señales necesarias para determinar y actualizar el estado de mantenimiento de la base de datos.

## Comentarios
Siempre estamos abiertos a todo tipo de comentarios y sugerencias. No dude en enviarnos sus [sugerencias](https://feedback.azure.com/forums/266794-support-feedback). Además, puede ponerse en contacto con nosotros a través de [Twitter](https://twitter.com/azuresupport) o los [foros de MSDN](https://social.msdn.microsoft.com/Forums/azure).

<!---HONumber=AcomDC_0831_2016-->