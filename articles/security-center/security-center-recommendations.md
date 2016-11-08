---
title: Administración de recomendaciones de seguridad en el Centro de seguridad de Azure | Microsoft Docs
description: Este documento explica cómo las recomendaciones del Centro de seguridad de Azure ayudan a proteger los recursos de Azure y a cumplir con las directivas de seguridad.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2016
ms.author: terrylan

---
# Administración de recomendaciones de seguridad en el Centro de seguridad de Azure
En este documento se explica cómo usar las recomendaciones del Centro de seguridad de Azure para proteger los recursos de Azure.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.
> 
> 

## ¿Cuáles son las recomendaciones de seguridad?
El Centro de seguridad analiza periódicamente el estado de seguridad de los recursos de Azure. Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios.

## Implementación de recomendaciones de seguridad
### Obtención de recomendaciones
En [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md), aprenderá lo siguiente:

* Configurar directivas de seguridad.
* Activar la recopilación de datos.
* Elegir las recomendaciones que verá como parte de la directiva de seguridad.

Las recomendaciones de directiva actuales se centran en las actualizaciones del sistema, las reglas de línea de base, los programas antimalware, los [grupos de seguridad de red](../virtual-network/virtual-networks-nsg.md) en subredes y las interfaces de red, la auditoría de bases de datos SQL, el cifrado de datos transparente de bases de datos SQL y los firewalls de aplicaciones web. [Establecimiento de directivas de seguridad](security-center-policies.md) proporciona una descripción de cada opción de recomendación.

### Supervisión de recomendaciones
Después de establecer una directiva de seguridad, el Centro de seguridad analiza el estado de seguridad de los recursos, con el fin de identificar vulnerabilidades potenciales. El icono **Recomendaciones** de la hoja **Security Center** permite conocer el número total de recomendaciones que identifica Security Center.

![Icono Recomendaciones][1]

Para ver los detalles de cada recomendación:

1. Haga clic en el **icono Recomendaciones** de la hoja **Centro de seguridad**. Se abrirá la hoja **Recomendaciones**.

Las recomendaciones aparecen en un formato de tabla, donde cada línea representa una recomendación determinada. Las columnas de esta tabla son:

* **DESCRIPCIÓN**: se explica la recomendación y lo que es preciso hacer para abordarla.
* **RECURSO**: enumera los recursos a los que se aplica la recomendación.
* **ESTADO**: describe el estado actual de la recomendación:
  * **Abierta**: la recomendación aún no se ha abordado.
  * **En curso**: la recomendación se está aplicando actualmente a los recursos y no se requiere ninguna acción por su parte.
  * **Resuelta**: se ha completado la recomendación (en este caso la línea aparecerá atenuada).
* **GRAVEDAD**: describe la gravedad de una recomendación concreta:
  * **Alta**: existe una vulnerabilidad en un recurso importante (como una aplicación, una máquina virtual o un grupo de seguridad de red) y requiere atención.
  * **Media**: existe una vulnerabilidad y se requieren pasos adicionales o no críticos para eliminarla o completar un proceso.
  * **Baja**: existe una vulnerabilidad que se debe abordar, pero no requiere una atención inmediata. (De manera predeterminada no se muestran las recomendaciones bajas, pero si desea verlas, puede filtrar por ellas).

Use la tabla siguiente como referencia para ayudarle a entender las recomendaciones disponibles y lo que hará cada uno si se aplica.

> [!NOTE]
> Deberá comprender los [modelos de implementación clásico y de Resource Manager](../azure-classic-rm.md) para los recursos de Azure.
> 
> 

| Recomendación | Description |
| --- | --- |
| [Habilitar la colección de datos de las suscripciones](security-center-enable-data-collection.md) |Recomienda activar la recopilación de datos en la directiva de seguridad para cada una de las suscripciones y para todas las máquinas virtuales de la suscripción. |
| [Corrección de vulnerabilidades del SO](security-center-remediate-os-vulnerabilities.md) |Recomienda armonizar las configuraciones del SO con las reglas de configuración recomendadas; por ejemplo, no permitir guardar las contraseñas. |
| [Aplicar actualizaciones del sistema](security-center-apply-system-updates.md) |Recomienda implementar las actualizaciones críticas y de seguridad del sistema en las máquinas virtuales. |
| [Reiniciar tras actualizar el sistema](security-center-apply-system-updates.md#reboot-after-system-updates) |Se recomienda que reinicie una máquina virtual para completar el proceso de aplicación de actualizaciones del sistema. |
| [Agregar un firewall de aplicaciones web](security-center-add-web-application-firewall.md) |Recomienda implementar un Firewall de aplicaciones web (WAF) para los puntos de conexión web. Puede proteger varias aplicaciones web del Centro de seguridad si agrega estas aplicaciones a las implementaciones de WAF existentes. Los dispositivos WAF (creados mediante el modelo de implementación de Resource Manager) deben implementarse en una red virtual independiente. Los dispositivos WAF (creados mediante el modelo de implementación clásica) están limitados a usar un grupo de seguridad de red. Esta compatibilidad se extenderá a una implementación completamente personalizada de dispositivos WAF (clásica) en el futuro. El Centro de seguridad le recomendará que aprovisione un WAF para defenderse de ataques dirigidos a las aplicaciones web que se encuentran tanto en las máquinas virtuales como en App Service Environment (ASE). Para más información acerca de ASE, consulte [Documentación de App Service Environment](../app-service/app-service-app-service-environments-readme.md). |
| [Finalización de la protección de la aplicación](security-center-add-web-application-firewall.md#finalize-application-protection) |Para completar la configuración de un WAF, el tráfico se debe redirigir a la aplicación del WAF. Si se sigue esta recomendación, se completarán los cambios necesarios en la configuración. |
| [Agregar un firewall de próxima generación](security-center-add-next-generation-firewall.md) |Recomienda agregar un firewall de próxima generación (NGFW) de un asociado de Microsoft para aumentar la protección. |
| [Enrutar el tráfico solo a través de NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Recomienda configurar reglas de grupos de seguridad de red (NSG) que fuercen que el tráfico entrante pase a su máquina virtual mediante el NGFW. |
| [Instalación de Endpoint Protection](security-center-install-endpoint-protection.md) |Recomienda aprovisionar programas antimalware a las máquinas virtuales (solo máquinas virtuales de Windows). |
| [Resolver alertas de estado de Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md) |Recomienda resolver los errores de Endpoint Protection. |
| [Habilitar grupos de seguridad de red en subredes o máquinas virtuales](security-center-enable-network-security-groups.md) |Recomienda habilitar NSG en subredes o máquinas virtuales. |
| [Restringir el acceso a través de puntos de conexión accesibles desde Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Recomienda configurar reglas de tráfico de entrada para los NSG. |
| [Habilitar Auditoría SQL de servidor](security-center-enable-auditing-on-sql-servers.md) |Recomienda activar la auditoría de servidores SQL Server de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales). |
| [Habilitar Auditoría SQL de base de datos](security-center-enable-auditing-on-sql-databases.md) |Recomienda activar la auditoría de bases de datos SQL de Azure (solo el servicio SQL de Azure; no incluye la instancia SQL que se ejecuta en sus máquinas virtuales). |
| [Habilitar Cifrado de datos transparente en bases de datos SQL](security-center-enable-transparent-data-encryption.md) |Recomienda habilitar el cifrado de bases de datos SQL (solo el servicio SQL de Azure). |
| [Habilitar el Agente de máquina virtual](security-center-enable-vm-agent.md) |Permite ver las VM que requieren el Agente de VM. El agente de máquina virtual debe estar instalado en las máquinas virtuales para aprovisionar la detección de revisiones, la detección de línea de base y los programas antimalware. De manera predeterminada, el agente de máquina virtual está instalado en las máquinas virtuales que se implementan desde Azure Marketplace. El artículo [VM Agent and Extensions – Part 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (Agente de VM y extensiones, parte 2) proporciona información sobre cómo instalar el Agente de VM. |
| [Aplicar cifrado de discos](security-center-apply-disk-encryption.md) |Se recomienda cifrar los discos de la máquina virtual mediante Cifrado de discos de Azure (máquinas virtuales Linux y Windows). Se recomienda cifrar tanto los volúmenes de datos como los del sistema operativo en la máquina virtual. |
| [Proporcionar datos de los contactos de seguridad](security-center-provide-security-contact-details.md) |Recomienda que proporcione información de los contactos de seguridad para cada una de sus suscripciones. La información de los contactos es una dirección de correo electrónico y un número de teléfono. La información se usará para establecer contacto con usted si nuestro equipo de seguridad descubre que sus recursos están en peligro. |
| [Actualizar versión del sistema operativo](security-center-update-os-version.md) |Recomienda actualizar la versión del sistema operativo para el servicio en la nube a la versión más reciente disponible para la familia del sistema operativo. Para obtener más información sobre los servicios en la nube, consulte la [información general sobre los servicios en la nube](../cloud-services/cloud-services-choose-me.md). |
| [Evaluación de vulnerabilidades no instalada](security-center-vulnerability-assessment-recommendations.md) |Se recomienda instalar una solución de evaluación de vulnerabilidades en la máquina virtual. |
| [Corrección de vulnerabilidades](security-center-vulnerability-assessment-recommendations.md#review-recommendation) |Permite ver las vulnerabilidades tanto del sistema como de las aplicaciones que ha detectado la solución de evaluación de vulnerabilidades instalada en la máquina virtual. |

Puede filtrar y descartar las recomendaciones.

1. Haga clic en **filtro** en la hoja **Recomendaciones**. Se abrirá la hoja **Filtro**, donde podrá seleccionar los valores de gravedad y de estado que quiera ver.
   
    ![Filtrar recomendaciones][2]
2. Si determina que una recomendación no corresponde, puede descartar la recomendación y, luego, filtrarla para que no aparezca en la vista. Hay dos formas de descartar una recomendación. Una forma es hacer clic con el botón derecho en un elemento y seleccionar **Descartar**. La otra es mover el puntero sobre un elemento, hacer clic en los tres puntos que aparecen a la derecha y seleccionar **Descartar**. Para ver las recomendaciones descartadas, haga clic en **Filtro** y seleccione **Descartadas**.
   
    ![Descartar recomendación][3]

### Aplicación de recomendaciones
Después de revisar todas las recomendaciones, decida cuáles son las primeras que debe aplicar. Se recomienda usar la clasificación de gravedad como parámetro principal para evaluar las recomendaciones que se deben aplicar primero.

En la tabla de las recomendaciones anteriores, seleccione una recomendación y tómela como ejemplo de aplicación de una recomendación.

## Otras referencias
En este documento, se han presentando las recomendaciones de seguridad del Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png

<!---HONumber=AcomDC_0928_2016-->