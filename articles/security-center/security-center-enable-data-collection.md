---
title: "Habilitación de la recolección de datos en Azure Security Center | Microsoft Docs"
description: " Aprenda a habilitar la recolección de datos en Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 536759cd4c6dbbfd73491422d426223cd71f8ce0


---
# <a name="enable-data-collection-in-azure-security-center"></a>Habilitación de la recolección de datos en Azure Security Center
Para ayudar a los clientes a evitar, detectar y responder a las amenazas, Azure Security Center recopila y procesa los datos sobre las máquinas virtuales de Azure, incluida la información de configuración, metadatos, registros de eventos y mucho más. La primera vez que se accede a Azure Security Center la recopilación de datos se habilita en todas las máquinas virtuales de la suscripción. Se recomienda utilizar la recopilación de datos, pero se puede cancelar desactivando la recopilación de datos en la directiva de Azure Security Center (consulte [Deshabilitación de la recopilación de datos](#disabling-data-collection)). Si desactiva la recopilación de datos, Security Center le recomendará activar la recopilación de datos en la directiva de seguridad de la suscripción.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.
> 
> 

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. Seleccione el icono **Recomendaciones** de la hoja **Security Center**.  Se abrirá la hoja **Recomendaciones** .
   ![Hoja Security Center][1]
2. En la hoja **Recomendaciones**, seleccione **Habilitar la recopilación de datos de las suscripciones**.  Se abrirá la hoja **Activar recolección de datos** .
   ![Hoja Recomendaciones][2]
3. En la hoja **Activar recolección de datos** , seleccione la suscripción. Se abre la hoja **Directiva de seguridad** para esa suscripción.
4. En la hoja **Directiva de seguridad**, seleccione **Activar** en **Recolección de datos** para recopilar registros de forma automática. La activación de la recolección de datos también proporcionará la extensión de supervisión en todas las máquinas virtuales actuales y nuevas en la suscripción.
   ![Hoja Directiva de seguridad][3]
5. Seleccione **Guardar**.
6. Seleccione **Elija una cuenta de almacenamiento por región**. Para cada región en la que disponga de máquinas virtuales en funcionamiento, elija la cuenta de almacenamiento en la que se almacenan los datos recopilados de esas máquinas virtuales. Si no elige una cuenta de almacenamiento para cada región, se creará automáticamente. En este ejemplo, elegiremos **newstoracct**. Puede cambiar la cuenta de almacenamiento más adelante, para ello vuelva a la directiva de seguridad de la suscripción y elija otra cuenta de almacenamiento diferente.
   ![Seleccionar una cuenta de almacenamiento][4]
7. Seleccione **Aceptar**.

> [!NOTE]
> Se recomienda que antes active la recopilación de datos y elija una cuenta de almacenamiento en el nivel de suscripción. Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero la configuración de la recopilación de datos y la cuenta de almacenamiento tiene lugar solo en el nivel de suscripción.
> 
> 

## <a name="after-data-collection-is-enabled"></a>Después de habilitar la recolección de datos
La colección de datos se habilita a través de la extensión Supervisión de seguridad de Azure y el Agente de supervisión de Azure. La extensión Supervisión de seguridad de Azure detecta diversas configuraciones de seguridad importantes y las envía a [Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Además, el sistema operativo crea las entradas del registro de eventos. El Agente de supervisión de Azure lee las entradas de los registros de eventos y los seguimientos de ETW y los copia en la cuenta de almacenamiento para su análisis. El agente de supervisión también copia los archivos de volcado de memora en la cuenta de almacenamiento. Esta es la cuenta de almacenamiento que configuró en la directiva de seguridad.

## <a name="disabling-data-collection"></a>Deshabilitación de la recopilación de datos
Puede deshabilitar la recolección de datos en cualquier momento, esto quitará a todos los agentes de supervisión que Security Center instaló previamente.  Tiene que seleccionar una suscripción para desactivar la recolección de datos.

> [!NOTE]
> Las directivas de seguridad se pueden establecer en el nivel de suscripción y el nivel de grupo de recursos de Azure, pero tiene que seleccionar una suscripción para desactivar la recolección de datos.
> 
> 

1. Vuelva a la hoja **Security Center** y seleccione el icono **Directiva**. Esto abre la hoja **Directiva de seguridad: definir directiva por suscripción o grupo de recursos** .
   ![Seleccionar el icono de directiva][5]
2. En la hoja **Directiva de seguridad: definir directiva por suscripción o grupo de recursos** , seleccione la suscripción en la que desea deshabilitar la recolección de datos.
   ![Seleccione la suscripción para deshabilitar la recolección de datos][6]
3. Se abre la hoja **Directiva de seguridad** para esa suscripción.  Seleccione **Desactivar** en recolección de datos.
4. Seleccione **Guardar** en la cinta de opciones superior.
5. Seleccione la opción **Eliminar agentes** en la cinta de opciones superior para quitar los agentes de las máquinas virtuales existentes.

## <a name="see-also"></a>Consulte también
En este documento, mostramos cómo implementar la recomendación "Habilitar recopilación de datos" de Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/security-center-blade.png
[2]: ./media/security-center-enable-data-collection/recommendations.png
[3]: ./media/security-center-enable-data-collection/data-collection.png
[4]: ./media/security-center-enable-data-collection/storage-account.png
[5]: ./media/security-center-enable-data-collection/policy.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png



<!--HONumber=Nov16_HO3-->


