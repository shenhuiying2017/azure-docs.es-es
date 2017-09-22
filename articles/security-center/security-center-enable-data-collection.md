---
title: "Recolección de datos en Azure Security Center | Microsoft Docs"
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
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---
# <a name="data-collection-in-azure-security-center"></a>Recolección de datos en Azure Security Center
Security Center recopila datos de las máquinas virtuales de Azure y de los equipos que no son de Azure para supervisar las amenazas y vulnerabilidades de seguridad. Los datos se recopilan con Microsoft Monitoring Agent, que lee distintas configuraciones relacionadas con la seguridad y distintos registros de eventos de la máquina y copia los datos en el área de trabajo para analizarlos. Estos son algunos ejemplos de dichos datos: tipo y versión del sistema operativo, registros del sistema operativo (registros de eventos de Windows), procesos en ejecución, nombre de la máquina, direcciones IP, usuario conectado e identificador de inquilino. Asimismo, copia los archivos de volcado de memoria en dicha área de trabajo.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Habilitar el aprovisionamiento automático de Microsoft Monitoring Agent     
Si el aprovisionamiento automático está habilitado, Security Center aprovisiona Microsoft Monitoring Agent en todas las máquinas virtuales de Azure compatibles y en las que se creen. El aprovisionamiento automático es muy recomendable y es necesario para las suscripciones del nivel Estándar de Security Center.

> [!NOTE]
> La deshabilitación del aprovisionamiento automático limita la supervisión de seguridad de los recursos. Para obtener más información, vea [Deshabilitar el aprovisionamiento automático](security-center-enable-data-collection.md#disable-automatic-provisioning) en este artículo. Las instantáneas de disco de máquina virtual y la recopilación de artefactos seguirán habilitadas, aunque se deshabilite el aprovisionamiento automático.
>
>

Para habilitar el aprovisionamiento automático de Microsoft Monitoring Agent:
1. En el menú principal de Security Center, seleccione **Directiva de seguridad**.
2. Seleccione la suscripción.
3. En **Directiva de seguridad**, seleccione **Recopilación de datos**.
4. En **Incorporación**, seleccione **Activado** para habilitar el aprovisionamiento automático.
5. Seleccione **Guardar**.

![Habilitar el aprovisionamiento automático][1]

## <a name="default-workspace-configuration"></a>Configuración predeterminada del área de trabajo
Los datos recopilados por Security Center se almacenan en áreas de trabajo de Log Analytics.  Puede optar por que los datos se recopilen de las máquinas virtuales de Azure almacenadas en áreas de trabajo creadas por Security Center o en un área de trabajo que haya creado.

Para usar el área de trabajo existente de Log Analytics:
- El área de trabajo debe estar asociada con la suscripción de Azure seleccionada.
- Como mínimo, el usuario debe tener permisos de lectura para obtener acceso al área de trabajo.

Para seleccionar un área de trabajo existente de Log Analytics:

1. En **Directiva de seguridad: Recopilación de datos**, seleccione **Usar otra área de trabajo**.

   ![Seleccionar el área de trabajo existente][2]

2. En el menú desplegable, seleccione un área de trabajo para almacenar los datos recopilados.

> [!NOTE]
> En el menú desplegable solo se muestran las áreas de trabajo a las que tiene acceso y las que están en su suscripción de Azure.
>
>

3. Seleccione **Guardar**.
4. Después de hacer clic en **Guardar**, se le preguntará si quiere volver a configurar las máquinas virtuales supervisadas.

   - Haga clic en **No** si quiere que la nueva configuración del área de trabajo solo se aplique a las máquinas virtuales nuevas. La nueva configuración del área de trabajo solo se aplica a las nuevas instalaciones de agente, aquellas máquinas virtuales recién detectadas que no tengan instalado Microsoft Monitoring Agent.
   - Haga clic en **Sí** si quiere que la nueva configuración del área de trabajo se aplique a todas las máquinas virtuales. Además, todas las máquinas virtuales conectadas a un área de trabajo creada por Security Center se volverán a conectar a la nueva área de trabajo de destino.

   > [!NOTE]
   > Si hace clic en Sí, no debe eliminar los espacios de trabajo creados por Security Center hasta que todas las máquinas virtuales se hayan reconectado a la nueva área de trabajo de destino. Esta operación no se lleva a cabo si se elimina un área de trabajo demasiado pronto.
   >
   >

   - Haga clic en **Cancelar** para cancelar la operación.

   ![Seleccionar el área de trabajo existente][3]

## <a name="data-collection-tier"></a>Nivel de recopilación de datos
Security Center puede reducir el volumen de eventos mientras mantiene suficientes eventos para la investigación, la auditoría y la detección de amenazas. Puede elegir la directiva de filtrado adecuada para sus suscripciones y áreas de trabajo de cuatro conjuntos de eventos que recopilará el agente.

- **Todos los eventos**: Para los clientes que quieren asegurarse de que se recopilan todos los eventos. Este es el valor predeterminado.
- **Común**: Se trata de un conjunto de eventos que satisfacen a la mayoría de los clientes y les permite efectuar una prueba de auditoría completa.
- **Mínimo**: Es un conjunto más pequeño de eventos para los clientes que quieren reducir el volumen de eventos.
- **Ninguno**: Se deshabilita la recopilación de eventos de seguridad de los registros de seguridad y de AppLocker. Para los clientes que elijan esta opción, sus paneles de seguridad solo tendrán registros del Firewall de Windows y evaluaciones preventivas, como el antimalware, la línea base y las actualizaciones.

> [!NOTE]
> Estos conjuntos se han diseñado para abordar escenarios típicos. Asegúrese de evaluar cuál se ajusta a sus necesidades antes de implementarlo.
>
>

Para determinar los eventos que formarán parte de los conjuntos de eventos **Común** y **Mínimo**, hemos colaborado con los clientes y los estándares del sector para obtener información sobre la frecuencia sin filtrar de cada evento y su uso. En este proceso se han empleado las siguientes directrices:

- **Mínimo**: Asegúrese de que este conjunto abarque solamente los eventos que podrían indicar una brecha correcta y eventos importantes que tengan un volumen muy bajo. Por ejemplo, este conjunto contiene un inicio de sesión de usuario correcto y uno erróneo (identificadores de evento 4624 y 4625), pero no contiene el cierre de sesión, que es importante para la auditoría pero no lo es para la detección y tiene un volumen relativamente alto. La mayor parte del volumen de datos de este conjunto son los eventos de inicio de sesión y el evento de creación de proceso (Id. de evento 4688).
- **Común**: Proporcione una pista de auditoría de usuario completa en este conjunto. Por ejemplo, este conjunto contiene los inicios de sesión y los cierres de sesión de usuario (Id. de evento 4634). Se incluyen acciones de auditoría como cambios en los grupos de seguridad, operaciones de Kerberos en los controladores de dominio de clave y otros eventos que recomiendan las organizaciones del sector.

Los eventos que tienen un volumen muy bajo se han incluido en el conjunto Común, ya que la motivación principal para elegirlo respecto de todos los eventos pasa por reducir el volumen, y no por filtrar eventos específicos.

A continuación se muestra un desglose completo de los identificadores de evento de seguridad y de AppLocker para cada conjunto:

   ![Id. de evento][4]

Para elegir la directiva de filtrado:
1. En la hoja **Security policy & settings** (Directiva de seguridad y configuración), seleccione la directiva de filtrado en **Eventos de seguridad**.
2. Seleccione **Guardar**.

   ![Elegir la directiva de filtrado][5]

## <a name="disable-automatic-provisioning"></a>Deshabilitar el aprovisionamiento automático
Puede deshabilitar en cualquier momento el aprovisionamiento automático de los recursos desactivando esta opción en la directiva de seguridad. El aprovisionamiento automático es muy recomendable para poder obtener alertas de seguridad y recomendaciones sobre las actualizaciones del sistema, vulnerabilidades del sistema operativo y la protección de puntos de conexión.

> [!NOTE]
> La deshabilitación del aprovisionamiento automático no quita Microsoft Monitoring Agent de las máquinas virtuales de Azure en las que se ha aprovisionado el agente.
>
>

1. Vuelva al menú principal de Security Center y seleccione la directiva de seguridad.

   ![Deshabilitar el aprovisionamiento automático][6]

2. Seleccione la suscripción en la que quiere deshabilitar el aprovisionamiento automático.
3. En la hoja **Directiva de seguridad: Recopilación de datos**, en **Incorporación**, seleccione **Desactivado** para deshabilitar el aprovisionamiento automático.
4. Seleccione **Guardar**.  

## <a name="next-steps"></a>Pasos siguientes
En este artículo le hemos mostrado cómo efectuar un aprovisionamiento automático y una recopilación de datos en Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): aprenda a supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png

