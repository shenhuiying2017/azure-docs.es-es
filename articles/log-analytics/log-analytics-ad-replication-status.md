---
title: "Supervisión del estado de replicación de Active Directory con Azure Log Analytics | Microsoft Docs"
description: "El paquete de la solución de Estado de replicación de Active Directory supervisa con regularidad el entorno de Active Directory para comprobar si existen errores de replicación y muestra los resultados en el panel de OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bfe52ef5d9d09ffe179faaf6ffbd90ef964fbda9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Supervisión del estado de replicación de Active Directory con Azure Log Analytics

![Símbolo de AD Replication Status](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Active Directory es un componente clave de un entorno de TI empresarial. Para garantizar la alta disponibilidad y el alto rendimiento, cada controlador de dominio tiene su propia copia de la base de datos de Active Directory. Los controladores de dominio se replican entre sí con el fin de propagar los cambios en toda la empresa. Los errores en este proceso de replicación pueden provocar una serie de problemas en toda la empresa.

El paquete de la solución de Estado de replicación de AD supervisa con regularidad el entorno de Active Directory para comprobar si existen errores de replicación y muestra los resultados en el panel de OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalación y configuración de la solución
Utilice la siguiente información para instalar y configurar la solución.

* Debe instalar los agentes en controladores del dominio que se vaya a evaluar. Como alternativa, instale los agentes en servidores miembros y configure los agentes para que envíen datos de replicación de AD a OMS. Para conocer el proceso de conexión de equipos Windows a OMS, consulte [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md). Si el controlador de dominio ya forma parte de un entorno de System Center Operations Manager que le gustaría conectar a OMS, consulte [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).
* Agregue la solución de Estado de replicación de Active Directory al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).  No es necesario realizar ninguna configuración más.

## <a name="ad-replication-status-data-collection-details"></a>Detalles de recopilación de datos de Estado de replicación de AD
En la tabla siguiente se muestran los métodos de recolección de datos y otros detalles sobre cómo se recopilan los datos para el Estado de replicación de AD.

| plataforma | Agente directo | Agente de SCOM | Azure Storage | ¿Se necesita SCOM? | Datos del agente de SCOM enviados a través del grupo de administración | Frecuencia de recopilación |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |cada cinco días |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Habilitación opcional de un controlador que no es de dominio para enviar datos de AD a OMS
Si no desea conectar ninguno de los controladores de dominio directamente a OMS, puede usar cualquier otro equipo conectado a OMS del dominio para recopilar datos para el paquete de solución de AD Replication Status y hacer que este envíe los datos.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Para habilitar un controlador que no sea de dominio para enviar datos de AD a OMS
1. Compruebe que el equipo es miembro del dominio que desea supervisar mediante la solución de Estado de replicación de AD.
2. [Conecte el equipo Windows a OMS](log-analytics-windows-agents.md) o [conéctelo con su entorno existente de Operations Manager a OMS](log-analytics-om-agents.md), si no está conectado aún.
3. En el equipo, configure la siguiente clave del Registro:

   * Clave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**
   * Valor: **IsTarget**
   * Datos del valor: **true**

   > [!NOTE]
   > Estos cambios no surten efecto hasta que reinicia el servicio Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Descripción de los errores de replicación
Una vez que se hayan enviado los datos de AD Replication Status a OMS, verá un icono similar al siguiente en el panel de OMS que indica cuántos errores de replicación hay actualmente.  
![Icono del Estado de replicación de AD](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

Los **errores críticos de replicación** son aquellos que están al 75 % de la [vigencia del marcador de exclusión](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) o por encima para el bosque de Active Directory.

Al hacer clic en el icono, se ve más información sobre los errores.
![Panel del estado de replicación de AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Estado del servidor de destino y estado del servidor de origen
Estas columnas muestran el estado de los servidores de destino y los servidores de origen que experimentan errores de replicación. El número después de cada nombre de controlador de dominio indica el número de errores de replicación en ese controlador de dominio.

Dado que algunos problemas son más fáciles de solucionar desde la perspectiva del servidor de origen y otros desde la perspectiva del servidor de destino, se muestran los errores tanto de los servidores de destino como de los servidores de origen.

En este ejemplo, verá que muchos servidores de destino tienen aproximadamente el mismo número de errores, pero hay un servidor de origen (ADDC35) que tiene muchos más errores que los demás. Es probable que haya algún problema en ADDC35 que esté provocando un error al enviar datos a sus asociados de replicación. Con la solución de los problemas de ADDC35 se podrían resolver muchos de los errores que aparecen en el área del servidor de destino.

### <a name="replication-error-types"></a>Tipos de errores de replicación
Esta área proporciona información sobre los tipos de errores detectados en toda la empresa. Cada error posee un código numérico único y un mensaje que puede ayudarle a determinar la causa del error.

El anillo de la parte superior le permite hacerse una idea de los errores que aparecen con mayor y menor frecuencia en su entorno.

Muestra si hay varios controladores de dominio que experimentan el mismo error de replicación. En este caso, es posible que pueda detectar o identificar una solución en un controlador de dominio y repetirla en los demás controladores de dominio afectados.

### <a name="tombstone-lifetime"></a>vigencia de objetos de desecho
La vigencia de objetos de desecho determina el tiempo que un objeto eliminado permanece en la base de datos de Active Directory. Cuando un objeto eliminado supera la vigencia de objetos de desecho, el proceso de recopilación de elementos no utilizados lo quita automáticamente de la base de datos de Active Directory.

La vigencia predeterminada de objetos de desecho es de 180 días para las versiones más recientes de Windows (60 días en versiones anteriores) y puede cambiarse explícitamente por un administrador de Active Directory.

Es importante saber si tiene errores de replicación próximos al fin de la vigencia del marcador de exclusión o la hayan superado. Si dos controladores de dominio experimentan un error de replicación que se mantiene una vez superada la vigencia del marcador de exclusión, se deshabilita la replicación entre esos dos controladores de dominio, aunque se corrija el error de replicación subyacente.

El área de la vigencia de marcadores de exclusión le ayuda a identificar los lugares donde existe el riesgo de que se deshabilite la replicación. Cada error en la categoría **Más del 100 % de la vigencia de objetos de desecho** representa una partición que no se ha replicado entre su servidor de origen y destino durante al menos la vigencia de objetos de desecho para el bosque.

En este caso, no bastará con corregir el error de replicación. Como mínimo, debe investigar manualmente para identificar y limpiar los objetos persistentes antes de reiniciar la replicación. Puede incluso que deba dar de baja un controlador de dominio.

Además de identificar los errores de replicación que se hayan mantenido una vez expirada la vigencia del marcador de exclusión, deberá prestar atención a los errores de las categorías **50-75 % de la vigencia del marcador de exclusión** o **75-100 % de la vigencia del marcador de exclusión**.

Estos son errores que son claramente persistentes, no transitorios, por lo que es probable que necesiten la intervención del usuario para resolverse. Lo bueno es que no han alcanzado aún el fin de la vigencia de objetos de desecho. Si soluciona estos problemas con prontitud y *antes* de que lleguen al límite de la vigencia de objetos de desecho, puede reiniciar la replicación con la mínima intervención manual.

Como se indicó anteriormente, el icono del panel de la solución de Estado de replicación de AD muestra el número de errores de replicación *críticos* en su entorno, que se definen como errores que están por encima del 75 % de la vigencia de objetos de desecho (incluidos los errores que están en el 100 % de la vigencia de objetos de desecho). El objetivo es mantener este número en 0.

> [!NOTE]
> Todos los cálculos de porcentaje de la vigencia de objetos de desecho se basan en la vigencia de objetos de desecho real para el bosque de Active Directory, por lo que puede confiar en que los porcentajes son precisos, incluso si ha configurado un valor personalizado para la vigencia de objetos de desecho.
>
>

### <a name="ad-replication-status-details"></a>Detalles del Estado de replicación de AD
Al hacer clic en cualquier elemento de las listas, con la búsqueda de registros verá detalles adicionales sobre este. Los resultados se filtran para mostrar solo los errores relacionados con ese elemento. Por ejemplo, al hacer clic en el primer controlador de dominio que aparece en **Destination Server Status (ADDC02)** (Estado del servidor de destino [ADDC02]), verá los resultados de búsqueda filtrados para mostrar errores con ese controlador de dominio cuando figura como servidor de destino:

![Errores del Estado de replicación de AD en resultados de búsqueda](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Desde aquí, puede filtrar aún más, modificar la consulta de la búsqueda, etc. Para obtener más información acerca del uso de la Búsqueda de registros, consulte [Búsquedas de registros](log-analytics-log-searches.md).

El campo **HelpLink** muestra la dirección URL de una página de TechNet con detalles adicionales acerca de ese error específico. Puede copiar y pegar este vínculo en la ventana del explorador para ver información sobre la solución de problemas y corregir el error.

También puede hacer clic **Exportar** para exportar los resultados a Excel. La exportación de datos le ayuda a visualizar los datos del error de replicación de la forma que prefiera.

![errores del Estado de replicación de AD exportados en Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>P+F del Estado de replicación de AD
**P: ¿Con qué frecuencia se actualizan los datos del Estado de replicación de AD?**
R: La información se actualiza cada cinco días.

**P: ¿Existe es una manera de configurar la frecuencia de actualización de estos datos?**
R: De momento, no.

**P: ¿Tengo que agregar todos mis controladores de dominio a mi área de trabajo de OMS para ver el estado de replicación?**
R: No, basta con que se agregue un único controlador de dominio. Si tiene varios controladores de dominio en el área de trabajo de OMS, los datos de todos ellos se envían a OMS.

**P: No quiero agregar controladores de dominio a mi área de trabajo de OMS. ¿Puedo usar la solución de estado de replicación de AD?**
R: Sí. Puede configurar el valor de una clave del Registro para habilitar esta opción. Consulte [Para habilitar un controlador que no sea de dominio para enviar datos de AD a OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**P: ¿Cuál es el nombre del proceso que realiza la recopilación de datos?**
R: AdvisorAssessment.exe

**P: ¿Cuánto tiempo se tarda en recopilar datos?**
R: El tiempo de recopilación de datos depende del tamaño del entorno de Active Directory, pero normalmente tarda menos de 15 minutos.

**R: ¿Qué tipo de datos se recopilan?**
R: La información de replicación se recopila a través de LDAP.

**P: ¿Se puede configurar el momento en que se recopilan los datos?**
R: De momento, no.

**P: ¿Qué permisos necesito para recopilar datos?**
R: Los permisos de usuario normal para Active Directory son suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Solución de problemas de recopilación de datos
A fin de recopilar datos, el paquete de solución de Estado de replicación de AD requiere que haya al menos un controlador de dominio conectado a su área de trabajo de OMS. Hasta que se conecte a un controlador de dominio, aparece un mensaje que indica que **todavía se están recopilando datos**.

Si necesita ayuda para conectarse a uno de los controladores de dominio, puede ver la documentación en [Conexión de equipos Windows a Log Analytics](log-analytics-windows-agents.md). Como alternativa, si el controlador de dominio ya está conectado a un entorno existente de System Center Operations Manager, puede ver documentación en [Conexión de Operations Manager con Log Analytics](log-analytics-om-agents.md).

Si no desea conectar ninguno de los controladores de dominio directamente a OMS o SCOM, consulte [Para habilitar un controlador que no sea de dominio para enviar datos de AD a OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Pasos siguientes
* Utilice [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para ver datos detallados de estado de replicación de Active Directory.
