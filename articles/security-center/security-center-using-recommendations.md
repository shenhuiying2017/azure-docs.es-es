---
title: Uso de las recomendaciones de Azure Security Center para mejorar la seguridad | Microsoft Docs
description: " Obtenga información sobre cómo usar las recomendaciones y directivas de seguridad en Azure Security Center para ayudar a mitigar un ataque de seguridad. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: terrylan
ms.openlocfilehash: 0616f5e501324bfd821c1455ce234602f1fcf1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Uso de las recomendaciones de Azure Security Center para mejorar la seguridad
Puede reducir las posibilidades de que se produzca un incidente de seguridad importante configurando una directiva de seguridad y, luego, implementando las recomendaciones de Azure Security Center. En este artículo se muestra cómo usar las recomendaciones y directivas de seguridad de Security Center para ayudar a mitigar un ataque de seguridad.

> [!NOTE]
> Este artículo se basa en los roles y los conceptos presentados en la [Guía de planeamiento y operaciones](security-center-planning-and-operations-guide.md) de Security Center. Recomendamos revisar a la Guía de planeación antes de continuar.
>
>

## <a name="managing-security-recommendations"></a>Administración de recomendaciones de seguridad
Las directivas de seguridad definen el conjunto de controles recomendados para los recursos de la suscripción o el grupo de recursos especificados. En Security Center, se definen directivas de acuerdo con los requisitos de seguridad de la compañía. Para aprender más, consulte [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md).

Las directivas de seguridad de este grupo de recursos se heredan del nivel de suscripción.

![Herencia de directivas de seguridad][1]

Si necesita directivas personalizadas en grupos de recursos específicos, debe deshabilitar la herencia en el grupo de recursos. Para ello, establezca Herencia en Único en la hoja Directiva de seguridad y personalice los controles para los que Security Center muestra recomendaciones.

Por ejemplo, si algunas cargas de trabajo no requieren la directiva de cifrado de datos transparente (TDE) de SQL Database, desactive la directiva en el nivel de suscripción y habilítela solo en los grupos de recursos donde se requiera TDE de SQL.

> [!NOTE]
> En caso de un conflicto entre la directiva del nivel de suscripción y la directiva del nivel de grupo de recursos, la del nivel del grupo de recursos tiene prioridad.
>
>

El Centro de seguridad analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica vulnerabilidades de seguridad potenciales, crea recomendaciones basadas en el conjunto de controles de la política de seguridad. Las recomendaciones lo guían en el proceso de configuración de los controles de seguridad necesarios.

Las recomendaciones de directiva actuales de Security Center se centran en las actualizaciones del sistema, la configuración del sistema operativo, los grupos de seguridad en subredes y máquinas virtuales, la auditoría de SQL Database, el TDE de Security Center, y los firewalls de aplicaciones web. Para recibir la cobertura más actualizada de las recomendaciones de Security Center, consulte [Administración de recomendaciones de seguridad](security-center-recommendations.md).

## <a name="scenario"></a>Escenario
Este escenario muestra cómo usar Security Center para ayudar a reducir las posibilidades de que se produzca un incidente de seguridad supervisando las recomendaciones de Security Center y tomando medidas. El escenario utiliza la compañía ficticia Contoso y los roles de la [Guía de planeamiento y operaciones](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) de Security Center. Los roles representan usuarios y equipos que pueden usar Security Center para realizar diferentes tareas relacionadas con la seguridad. Los roles son:

![Roles de escenario][2]

Contoso había migrado recientemente algunos de sus recursos locales a Azure. Asimismo, quiere implementar y mantener protecciones que reduzcan su vulnerabilidad frente a un ataque de seguridad de sus recursos en la nube.

## <a name="recommended-solution"></a>Solución recomendada
Una solución es utilizar Security Center para evitar y detectar las vulnerabilidades de seguridad. Contoso tiene acceso a Security Center a través de su suscripción de Azure. El [nivel Gratis](security-center-pricing.md) de Security Center se habilita automáticamente en todas las suscripciones de Azure y la recopilación de datos, en todas las máquinas virtuales de su suscripción.

David, del equipo de seguridad de TI de Contoso, configura una **directiva de seguridad** mediante Security Center. Security Center analiza el estado de seguridad de los recursos de Azure. Cuando Security Center identifica vulnerabilidades de seguridad potenciales, crea **recomendaciones** basadas en el conjunto de controles de la política de seguridad.

Juan, propietario de las cargas de trabajo en la nube, es el responsable de la implementación y el mantenimiento de las protecciones de acuerdo con las directivas de seguridad de Contoso. Juan puede supervisar las recomendaciones de Security Center para aplicar protecciones. Las recomendaciones guían a Juan en el proceso de configuración de los controles de seguridad necesarios.

Para que Juan pueda implementar y mantener las protecciones y eliminar vulnerabilidades de seguridad, debe realizar lo siguiente:

- Supervisar las recomendaciones de seguridad de Security Center
- Evaluar las recomendaciones de seguridad y decidir si deben aplicar o descartar
- Aplicar las recomendaciones de seguridad

Sigamos los pasos de Juan para ver cómo utiliza las recomendaciones de Security Center para guiarse por el proceso de configuración de controles y eliminar las vulnerabilidades de seguridad.

## <a name="how-to-implement-this-solution"></a>Implemento de esta solución
Juan inicia sesión en [Azure Portal](https://azure.microsoft.com/features/azure-portal/) y abre en la consola de Security Center. Como parte de sus actividades de supervisión diarias, comprueba si hay recomendaciones de seguridad mediante los pasos siguientes:

1. Juan selecciona el icono d**Recomendaciones** para abrir la hoja **Recomendaciones**.
   ![Selección del icono de Recomendaciones][3]
2. Juan revisa la lista de recomendaciones. Ve que Security Center ha proporcionado la lista de recomendaciones en orden de prioridad, de la prioridad más alta a la más baja. Decide abordar una recomendación de alta prioridad de la lista. En la hoja **Recomendaciones**, selecciona **Instalar Endpoint Protection**.
3. Se abre **Instalar Endpoint Protection** con una lista de máquinas virtuales sin antimalware habilitado. Juan revisa la lista de máquinas virtuales, las selecciona todas y, luego, hace clic en **Instalar en 3 máquinas virtuales**.
   ![Instalar Endpoint Protection][4]
4. Se abre **Seleccionar Endpoint Protection**, que proporciona a Juan dos soluciones antimalware. Juan selecciona la solución **Microsoft Antimalware**.
5. Aparece información adicional sobre la solución antimalware. Juan selecciona **Crear**.
   ![Microsoft Antimalware][5]
6. Juan escribe las opciones de configuración requeridas en la hoja **Instalar** y selecciona **Aceptar**.

[Microsoft Antimalware](../security/azure-security-antimalware.md) ahora está activo en las máquinas virtuales seleccionadas.

Juan continúa explorando las recomendaciones de prioridad alta y media, con lo que toma decisiones de implementación. Juan consulta el artículo [Administración de recomendaciones de seguridad](security-center-recommendations.md) para comprender las recomendaciones y la acción que realiza cada una.

Juan aprende que el [Centro de respuestas de seguridad de Microsoft (MSRC)](../security/azure-security-response-center.md) lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros. Si Juan proporciona los datos de contacto de seguridad de la suscripción de Azure de Contoso, Microsoft se pondrá en contacto con Contoso en caso de que el MSRC descubra que una entidad ilegal o no autorizada ha accedido a los datos de clientes de Contoso. Después, vemos que Juan aplica la recomendación **Proporcionar datos de los contactos de seguridad** (una recomendación con una gravedad Media en la lista de recomendaciones anteriores).

1. Juan selecciona **Proporcionar detalles de contacto de seguridad** en la hoja **Recomendaciones**, que abre la hoja **Proporcionar detalles de contacto de seguridad**.
2. Juan selecciona la suscripción de Azure sobre la que desea proporcionar información de contacto. Se abre una segunda hoja **Proporcionar datos de los contactos de seguridad** .
   ![Datos de los contactos de seguridad][6]
3. En **Proporcionar detalles de contacto de seguridad**, Juan escribe estos datos:

  - Las direcciones de correo electrónico de contacto de seguridad separadas por comas (no hay un límite en el número de direcciones que se pueden escribir)
  - Un número de teléfono de contacto de seguridad

4. Juan activa la opción **Enviar correos electrónicos de alertas** para recibir correos electrónicos de alertas de gravedad alta.
5. Juan selecciona **Aceptar** para aplicar la información de contacto de seguridad a la suscripción de Contoso.

Por último, Juan revisa la recomendación de baja prioridad **Corrección de vulnerabilidades del SO** y determina que esta recomendación no es aplicable. Quiere descartar la recomendación. Juan selecciona los tres puntos que aparecen a la derecha y, luego, hace clic en **Descartar**.
   ![Descartar recomendación][7]

## <a name="conclusion"></a>Conclusión
Supervisar las recomendaciones de Security Center puede ayudarlo a eliminar vulnerabilidades de seguridad antes de que se produzca un ataque. Puede evitar que ocurra un incidente de seguridad implementando y manteniendo protecciones con las directivas de seguridad de Security Center.

## <a name="next-steps"></a>Pasos siguientes
En este escenario se muestra cómo usar las recomendaciones y directivas de seguridad de Security Center para ayudar a mitigar un ataque de seguridad. Consulte el [escenario de respuesta a incidentes](security-center-incident-response.md) para saber cómo tener implantado un plan de respuesta a incidentes antes de que tenga lugar un ataque.

Para más información sobre Security Center, consulte:

* [Supervisión del estado de seguridad](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión y procesamiento de eventos de seguridad:](security-center-events-dashboard.md) obtenga información sobre cómo supervisar y procesar los eventos de seguridad recopilados con el tiempo.
* [Supervisión de las soluciones de asociados](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-using-recommendations/security-center-policy-inheritance.png
[2]: ./media/security-center-using-recommendations/scenario-roles.png
[3]: ./media/security-center-using-recommendations/select-recommendations-tile.png
[4]: ./media/security-center-using-recommendations/install-endpoint-protection.png
[5]:./media/security-center-using-recommendations/microsoft-antimalware.png
[6]: ./media/security-center-using-recommendations/provide-security-contact-details.png
[7]: ./media/security-center-using-recommendations/dismiss-recommendation.png
