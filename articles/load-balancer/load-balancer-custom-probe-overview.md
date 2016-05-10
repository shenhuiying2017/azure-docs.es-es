<properties
   pageTitle="Sondeos personalizados del Equilibrador de carga y supervisión de estado de mantenimiento | Microsoft Azure"
   description="Aprenda a usar sondeos personalizados para el Equilibrador de carga de Azure para supervisar las instancias detrás de dicho Equilibrador."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma" />


# Sondeos del Equilibrador de carga

El Equilibrador de carga de Azure ofrece la capacidad de supervisar el estado de las instancias del servidor mediante sondeos. Cuando un sondeo no responde, el Equilibrador de carga deja de enviar nuevas conexiones a la instancia incorrecta. Las conexiones existentes no resultan afectadas y las nuevas conexiones se envían a instancias en buen estado.

Deben configurarse sondeos TCP o HTTP personalizados al usar máquinas virtuales detrás del Equilibrador de carga. Los roles del servicio en la nube (los roles de trabajo y los roles web) son las únicas instancias de servidor con supervisión del sondeo del agente invitado.

## Descripción del número y el tiempo de espera de los sondeos

El comportamiento de los sondeos depende de:
- El número de sondeos correctos que permiten que una instancia se etiquete como en ejecución.
- El número de sondeos erróneos que permiten que una instancia se etiquete como no en ejecución.

El tiempo de espera y el valor de frecuencia establecidos en SuccessFailCount determinan si una instancia está en ejecución o en no ejecución. En el portal de Azure, el tiempo de espera se establece en dos veces el valor de la frecuencia.

La configuración de los sondeos de todas las instancias con carga equilibrada para un punto de conexión (es decir, un conjunto de carga equilibrada) debe ser igual. Esto significa que no puede tener una configuración de sondeo distinta para cada instancia de rol o máquina virtual en el mismo servicio hospedado para una combinación determinada de puntos de conexión. Por ejemplo, cada instancia debe tener puertos locales y tiempos de espera idénticos.


>[AZURE.IMPORTANT] Un sondeo del Equilibrador de carga utiliza la dirección IP 168.63.129.16. Esta dirección IP pública facilita un canal de comunicación a los recursos internos de plataforma para el escenario de Red virtual de Azure que permite especificar su propia IP. La dirección IP pública virtual 168.63.129.16 se utiliza en todas las regiones y no cambiará. Le recomendamos que permita esta dirección IP en cualquiera de las directivas de firewall local. No se debe considerar un riesgo de seguridad porque la plataforma interna de Azure es la única que puede originar un mensaje procedente de esa dirección. Si no hace esto, se producirá un comportamiento inesperado en varios escenarios, como la configuración del mismo intervalo de direcciones IP de 168.63.129.16 y el hecho de contar con direcciones IP duplicadas.


## Obtención de información sobre el tipo de sondeo

### Sondeo de agente invitado

Este sondeo solo está disponible para Servicios en la nube de Azure. El Equilibrador de carga usa al agente invitado que hay dentro de la máquina virtual y después escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está en estado Preparada (es decir, no se encuentra en otro, como Ocupada, Reciclando, Deteniendo, etc.).

Para más información, consulte [Configuring the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/jj151530.asp) (Configuración del archivo de definición de servicio (csdef) para los sondeos de estado) o [Introducción a la creación de un equilibrador de carga orientado a Internet para servicios en la nube](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### ¿Qué hace que un sondeo de agente invitado marque una instancia como en mal estado?

Si el agente invitado no responde con HTTP 200 OK, el Equilibrador de carga marca la instancia como sin respuesta y deja de enviar tráfico a esa instancia. El Equilibrador de carga continúa para hacer ping a la instancia. Si el agente invitado responde con un HTTP 200, el Equilibrador de carga envía de nuevo tráfico a esa instancia.

Cuando se usa un rol web, el código de sitio web normalmente se ejecuta en w3wp.exe, que no está supervisado por el agente invitado o el tejido de Azure. Esto significa que los errores en w3wp.exe (por ejemplo, respuestas HTTP 500) no se notificarán al agente invitado y el Equilibrador de carga no sacará esa instancia de la rotación.


### Sondeo HTTP personalizado

El sondeo HTTP personalizado del Equilibrador de carga invalida el sondeo del agente invitado predeterminado, lo que significa que usted puede crear su propia lógica personalizada para determinar el estado de la instancia de rol. El Equilibrador de carga sondea el punto de conexión cada 15 segundos de forma predeterminada. Se considera que la instancia está en la rotación del Equilibrador de carga si responde con un HTTP 200 dentro del período de tiempo de espera (que, de forma predeterminada, es 31 segundos).

Esto puede resultar útil si desea implementar su propia lógica para quitar instancias de rotación del Equilibrador de carga. Por ejemplo, podría decidir quitar una instancia si está por encima del 90 % de la CPU y devuelve un estado no 200. Si tiene roles web que usan w3wp.exe, esto también significa que dispone de supervisión automática de su sitio web, porque los errores en el código de su sitio web devolverán un estado diferente de no 200 al sondeo del Equilibrador de carga.

>[AZURE.NOTE] El sondeo personalizado HTTP admite solo rutas de acceso relativas y protocolo HTTP. HTTPS no es compatible.


### ¿Qué hace que un sondeo HTTP personalizado marque una instancia como en mal estado?

- La aplicación HTTP devuelve un código HTTP de respuesta distinto de 200 (por ejemplo, 403, 404 o 500). Se trata de una confirmación positiva de que la instancia de la aplicación debe desconectarse del servicio inmediatamente.

-  El servidor HTTP no responde en absoluto después del período de tiempo de espera. Dependiendo del valor del tiempo de espera establecido, esto podría significar que varias solicitudes de sondeo van sin respuesta antes de que el sondeo se marque como no en ejecutando (es decir, antes de que se envíen sondeos SuccessFailCount).
- 	El servidor cierra la conexión a través de un restablecimiento TCP.

### Sondeo TCP personalizado

Los sondeos TCP inician una conexión mediante la realización de un protocolo de enlace de tres vías con el puerto definido.

### ¿Qué hace que un sondeo TCP personalizado marque una instancia como en mal estado?

- El servidor TCP no responde en absoluto después del período de tiempo de espera. Cuando el sondeo se marca como no en ejecución depende del número de solicitudes de sondeo con error que se configuraron para ir sin respuesta antes de marcar el sondeo como no en ejecución.
- 	El sondeo recibe un restablecimiento TCP de la instancia de rol.

Para más información sobre cómo configurar un sondeo de estado HTTP o un sondeo TCP, consulte [Introducción a la creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## Incorporación de instancias en buen estado de nuevo en el Equilibrador de carga

Los sondeos TCP y HTTP se consideran en buen estado y marcan la instancia de rol como en buen estado en los casos siguientes:

-  El Equilibrador de carga obtiene un sondeo positivo la primera vez que se inicia la VM.
- El número SuccessFailCount (descrito anteriormente) define el valor de los sondeos correctos que son necesarios para marcar la instancia de rol como en buen estado. Si se quitó una instancia de rol, el número de sondeos correctos y sucesivos debe ser igual o superior al valor de SuccessFailCount para marcar la instancia de rol como en ejecución.

>[AZURE.NOTE] Si el estado de una instancia de rol fluctúa, el Equilibrador de carga espera más tiempo antes de devolver dicha instancia al estado correcto. Esto se hace mediante la directiva para proteger al usuario y a la infraestructura.

## Uso del análisis de registros para el Equilibrador de carga

Puede usar el [análisis de registros para el Equilibrador de carga](load-balancer-monitor-log.md) para comprobar el estado de mantenimiento de un sondeo y el número de sondeos. El registro se puede utilizar con Power BI o con Visión operativa de Azure para proporcionar estadísticas del estado de mantenimiento del Equilibrador de carga.

<!---HONumber=AcomDC_0427_2016-->