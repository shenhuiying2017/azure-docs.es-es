---
title: "Controles de aplicación adaptables en Azure Security Center | Microsoft Docs"
description: "Este documento le ayuda a usar el control de aplicación adaptable en Azure Security Center para incluir en la lista de permitidos las aplicaciones que se ejecutan en máquinas virtuales de Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 9c3a9a7255bbbdab8f4c356eb07022d7f1d242d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Controles de aplicación adaptables en Azure Security Center (versión preliminar)
Obtenga información acerca de cómo configurar el control de aplicación en Azure Security Center con este tutorial.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>¿Qué son los controles de aplicación adaptables en Security Center?
Los controles de aplicación adaptables ayudan a controlar qué aplicaciones se pueden ejecutar en las máquinas virtuales que se encuentran en Azure y, entre otras ventajas, le ayuda a proteger las máquinas virtuales frente a malware. Security Center usa el aprendizaje automático para analizar los procesos que se ejecutan en la máquina virtual y le ayuda a aplicar reglas de inclusión en listas de permitidos con esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware
- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.
- Evitar el uso de software no deseado en el entorno.
- Evitar la ejecución de aplicaciones anteriores y no compatibles.
- Impedir herramientas de software específicas no permitidas en la organización.
- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

> [!NOTE]
> Los controles de aplicación adaptables están disponibles para los clientes de Azure Security Center Estándar como una versión preliminar pública limitada. Envíe un correo electrónico con los identificadores de suscripción a [nuestro equipo](mailto:ASC_appcontrol@microsoft.com) para unirse a la versión preliminar.

## <a name="how-to-enable-adaptive-application-controls"></a>¿Cómo habilitar los controles de aplicación adaptables?
Los controles de aplicación adaptables ayudan a definir un conjunto de aplicaciones que se pueden ejecutar en los grupos de recursos configurados. Esta característica solo está disponible para equipos Windows (todas las versiones, clásica o Azure Resource Manager). Siga los pasos siguientes para configurar la inclusión en listas de aplicaciones permitidas en Security Center:

1.  Abra el panel **Security Center** y haga clic en **Información general**.
2.  En **Protección en la nube avanzada**, el icono **Controles de aplicación adaptables** muestra cuántas máquinas virtuales tienen actualmente el control en comparación con todas las máquinas virtuales. También muestra el número de problemas que se encontraron en la última semana: 

    ![Controles de aplicación adaptables](./media/security-center-adaptive-application\security-center-adaptive-application-fig1.png)

3. Haga clic en el icono **Controles de aplicación adaptables** para ver más opciones.

    ![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

4. La sección Grupos de recursos contiene tres pestañas:
    * **Recomendados**: lista de grupos de recursos para los que se recomienda el control de aplicación. Security Center utiliza el aprendizaje automático para identificar las máquinas virtuales que son buenas candidatas para el control de aplicación en función de si las máquinas virtuales ejecutan de forma coherente las mismas aplicaciones.
    * **Configurados**: lista de grupos de recursos con máquinas virtuales configuradas con control de aplicación. 
    * **Ninguna recomendación**: lista de grupos de recursos que contienen máquinas virtuales sin ninguna recomendación sobre el control de aplicación. Por ejemplo, máquinas virtuales en las que las aplicaciones cambian constantemente y no han alcanzado un estado estable.

Las secciones siguientes abordarán más detalles sobre cada opción y su utilización.

### <a name="configure-a-new-application-control-policy"></a>Configuración de una nueva directiva de control de aplicación
Haga clic en la pestaña **Recomendados** para obtener una lista de grupos de recursos con las recomendaciones de control de aplicación:

![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

La lista incluye:
- **NOMBRE**: el nombre de la suscripción y el grupo de recursos
- **MÁQUINAS VIRTUALES**: el número de máquinas virtuales en el grupo de recursos
- **ESTADO**: el estado de las recomendaciones, que en la mayoría de los casos será abierto
- **GRAVEDAD**: el nivel de gravedad de las recomendaciones

Seleccione un grupo de recursos para abrir la opción **Crear reglas de control de aplicación**:

![Reglas de control de aplicación](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

En **Seleccionar máquinas virtuales**, revise la lista de máquinas virtuales recomendadas y desactive cualquiera a la que no desee aplicar el control de aplicación. En **Seleccionar procesos para reglas de inclusión en lista blanca**, revise la lista de aplicaciones recomendadas y desactive aquellas a las que no desea que se aplique. La lista incluye:

- **NOMBRE**: la ruta de acceso completa de la aplicación
- **PROCESOS**: el número de aplicaciones que residen dentro de cada ruta de acceso
- **COMÚN**: true indica que estos procesos se han ejecutado en la mayoría de las máquinas virtuales de este grupo de recursos.
- **INFRINGIBLE**: un icono de advertencia indicará si las aplicaciones podrían ser usadas por un atacante para evitar las listas de aplicaciones permitidas. Se recomienda encarecidamente que se revisen estas aplicaciones antes de su aprobación. 

Cuando haya terminado las selecciones, haga clic en el botón **Crear**. De forma predeterminada, Security Center siempre habilita el control de aplicación en modo *Auditoría*. Después de comprobar que la lista de permitidos no tenga efectos negativos en la carga de trabajo, puede cambiar al modo *Forzar*.

> [!NOTE]
> Como procedimiento recomendado de seguridad, Security Center siempre intenta crear una regla de publicador para las aplicaciones que deben estar en la lista de permitidos y solo si una aplicación no tiene información del publicador (también conocido como no firmada), se creará una regla de ruta de acceso para la ruta de acceso completa del archivo EXE específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edición y supervisión de un grupo configurado con control de aplicación

Para editar y supervisar un grupo configurado con control de aplicación, haga clic en **CONFIGURADOS** en **Grupos de recursos**:

![Grupos de recursos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

La lista incluye:

- **NOMBRE**: el nombre de la suscripción y el grupo de recursos
- **MÁQUINAS VIRTUALES**: el número de máquinas virtuales en el grupo de recursos
- **MODO**: el modo Auditoría registrará los intentos de ejecución de aplicaciones que no están en la lista de permitidos; el modo Bloquear no permitirá la ejecución de aplicaciones que no están en la lista de permitidos.
- **GRAVEDAD**: el nivel de gravedad de las recomendaciones

Seleccione un grupo de recursos para realizar cambios en la página **Editar directiva de control de aplicación**.

![Protección](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

En **Modo de protección**, puede seleccionar entre las siguientes opciones:
- **Auditoría**: en este modo, la solución de control de aplicación no exige el cumplimiento de las reglas y solo audita la actividad en las máquinas virtuales protegidas. Esto se recomienda para escenarios donde desea primero observar el comportamiento general antes de bloquear la ejecución de una aplicación en la máquina virtual de destino.
- **Forzar**: en este modo, la solución de control de aplicación exigirá el cumplimiento de las reglas y se asegurará de bloquear las aplicaciones cuya ejecución no está permitida. 

Como se mencionó anteriormente, una nueva directiva de control de aplicación siempre se configurará en modo *Auditoría* de forma predeterminada. En **Extensión de directiva** puede agregar sus propias rutas de acceso de aplicación que desea incluir en la lista de permitidos. Una vez que agregue estas rutas de acceso, Security Center creará las reglas adecuadas para estas aplicaciones, además de las reglas que ya están configuradas. En la sección **Problemas** se enumeran las infracciones actuales.

![Issues](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

Esta lista incluye:

- **PROBLEMAS**: cualquier infracción que se haya registrado, incluidas las siguientes:
    - **ViolationsBlocked**: cuando la solución está activada en modo Exigir y se intenta ejecutar una aplicación que no está en la lista de permitidos.
    - **ViolationsAudited**: cuando la solución está activada en modo Auditoría y se ejecuta una aplicación que no está en la lista de permitidos.
    - **RulesViolatedManually**: cuando un usuario ha intentado configurar manualmente reglas en las máquinas virtuales y no mediante el portal de administración de ASC.
- **NÚMERO DE MÁQUINAS VIRTUALES**: número de máquinas virtuales con este tipo de problema.

Si hace clic en cada una de estas líneas, se le redirigirá a la página [Registro de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), donde puede ver información acerca de todas las máquinas virtuales con este tipo de infracción. Si hace clic en los tres puntos al final de cada línea podrá eliminar esa entrada determinada. En la sección **Máquinas virtuales configuradas** se enumeran las máquinas virtuales a la que se aplican las reglas. 

![Máquinas virtuales configuradas](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

**Reglas de inclusión en lista blanca de publicadores** enumera las aplicaciones para las que se ha creado una regla de publicador en función de la información del certificado que se ha encontrado para cada aplicación. Consulte [Descripción de las reglas de publicador en Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) para más información.

![Reglas de inclusión en listas de permitidos](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

Si hace clic en los tres puntos al final de cada línea podrá eliminar esa regla específica. **Reglas de inclusión en lista blanca de rutas** muestra la ruta de acceso completa (incluido el archivo ejecutable) de las aplicaciones que no están firmadas con un certificado digital pero están en las reglas de inclusión en listas de permitidos actuales. 

> [!NOTE]
> De forma predeterminada, como procedimiento recomendado de seguridad, Security Center siempre intenta crear una regla de publicador para los archivos EXE que deben estar en la lista de permitidos, y solo si un archivo EXE no tiene información del publicador (también conocido como no firmado), se creará una regla de ruta de acceso para la ruta de acceso completa del archivo EXE específico.

![Reglas de inclusión en listas de rutas de acceso permitidas](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

La lista contiene:
- **NOMBRE**: la ruta de acceso completa del archivo ejecutable
- **INFRINGIBLE**: true indica que las aplicaciones podrían ser usadas por un atacante para evitar las listas de aplicaciones permitidas.  

Si hace clic en los tres puntos al final de cada línea podrá eliminar esa regla específica. Después de realizar los cambios puede hacer clic en el botón **Guardar** o, si decide no aplicar los cambios, haga clic en **Descartar**.

### <a name="not-recommended-list"></a>Lista Ninguna recomendación

Security Center solo recomendará las listas de aplicaciones permitidas para las máquinas virtuales que ejecutan un conjunto estable de aplicaciones. No se crearán recomendaciones si las aplicaciones en las máquinas virtuales asociadas cambian continuamente. 

![Recomendación](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

La lista contiene:
- **NOMBRE**: nombre de la suscripción y del grupo de recursos.
- **MÁQUINAS VIRTUALES**: número de máquinas virtuales en el grupo de recursos.

## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a usar el control de aplicación adaptable en Azure Security Center para incluir en la lista de permitidos las aplicaciones que se ejecutan en máquinas virtuales de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

