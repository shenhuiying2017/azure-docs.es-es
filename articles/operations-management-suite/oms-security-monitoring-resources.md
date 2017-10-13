---
title: "Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite | Microsoft Docs"
description: "Este documento le ayuda a usar las capacidades de Seguridad y auditoría de OMS para supervisar los recursos e identificar los problemas de seguridad."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite
Este documento le ayuda a usar las capacidades de Seguridad y auditoría de OMS para supervisar los recursos e identificar los problemas de seguridad.

## <a name="what-is-oms"></a>¿Qué es OMS?
Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura en la nube y local. Para más información sobre OMS, lea el artículo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Supervisión de recursos
Siempre que sea posible, deseará evitar que se produzcan incidentes de seguridad en primer lugar. Sin embargo, es imposible evitar todos los incidentes de seguridad. Cuando se produce un incidente de seguridad, deberá asegurarse de que se minimice su repercusión.  Hay tres recomendaciones importantes que pueden utilizarse para reducir el número y minimizar el impacto de los incidentes de seguridad:

* Evaluar de forma regular las vulnerabilidades del entorno.
* Comprobar con regularidad todos los sistemas de equipos y dispositivos de red para asegurarse de que tienen todas las revisiones más recientes instaladas.
* Comprobar con regularidad todos los registros y mecanismos de registro, incluidos los registros de eventos del sistema operativo, los registros específicos de la aplicación y los registros del sistema de detección de intrusiones.

La solución Seguridad y auditoría de OMS permite a la TI supervisar activamente todos los recursos, lo que puede ayudar a minimizar el impacto de los incidentes de seguridad. Auditoría y seguridad de OMS tiene dominios de seguridad que pueden utilizarse para la supervisión de recursos. Los dominios de seguridad proporcionan acceso rápido a opciones. Para supervisar la seguridad se proporcionará más información de los siguientes dominios:

* Evaluación de malware
* Evaluación de la actualización
* Identidad y acceso

> [!NOTE]
> Para obtener una descripción general de todas estas opciones, lea [Introducción a la solución Seguridad y auditoría de Operations Management Suite](oms-security-getting-started.md).
> 
> 

### <a name="monitoring-system-protection"></a>Supervisión de la protección del sistema
En una defensa en el enfoque en profundidad, cada nivel de protección es importante para el estado general de la seguridad del recurso. Los equipos con amenazas detectadas y con una protección insuficiente se muestran en el icono Evaluación de malware en Dominios de seguridad. Con la información que se muestra en Evaluación de malware, puede desarrollar un plan para aplicar protección a los servidores que lo necesiten. Para obtener acceso a esta opción, siga los pasos siguientes:

1. En el panel principal de **Microsoft Operations Management Suite**, haga clic en el icono **Seguridad y auditoría**.
   
    ![Seguridad y auditoría](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. En el panel de **Seguridad y auditoría**, haga clic en **Evaluación antimalware** en **Dominios de seguridad**. Aparecerá el panel **Evaluación antimalware** como se muestra a continuación:

![Evaluación de malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Puede usar el panel **Evaluación de Malware** para identificar los siguientes problemas de seguridad:

* **Amenazas activas**: equipos que se han visto comprometidos y tienen amenazas activas en el sistema.
* **Amenazas corregidas**: equipos que se han visto comprometidos pero se han corregido las amenazas.
* **Firma desactualizada**: equipos que tienen habilitada la protección contra malware, pero la firma está desactualizada.
* **Sin protección en tiempo real**: equipos que no tienen instalado el antimalware.

### <a name="monitoring-updates"></a>Supervisión de actualizaciones
La aplicación de las actualizaciones de seguridad más recientes es una práctica recomendada de seguridad y debe incorporarse a su estrategia de administración de actualizaciones. El servicio Microsoft Monitoring Agent (HealthService.exe) lee información de actualización de los equipos supervisados y, a continuación, envía la información actualizada al servicio de OMS en la nube para su procesamiento. El servicio Microsoft Monitoring Agent está configurado como un servicio automático y debe estar ejecutándose siempre en el equipo de destino.

![Supervisión de actualizaciones](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Se aplica la lógica a los datos actualizados y el servicio en la nube registra los datos. Si se detectan actualizaciones que faltan, estas se muestran en el panel **Actualizaciones** . Puede usar el panel **Actualizaciones** para trabajar con actualizaciones que faltan y desarrollar un plan para aplicarlas a los servidores que las necesiten. Siga los pasos siguientes para obtener acceso al panel de **actualización** :

1. En el panel principal de **Microsoft Operations Management Suite**, haga clic en el icono **Seguridad y auditoría**.
2. En el panel de **Seguridad y auditoría**, haga clic en **Evaluación de actualización** en **Dominios de seguridad**. El panel de actualización aparecerá como se muestra a continuación:

![Evaluación de la actualización](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

En este panel puede realizar una evaluación de kas actualizaciones para comprender el estado actual de los equipos y solucionar las amenazas más importantes. Mediante el uso del icono **Actualizaciones críticas o de seguridad** , los administradores de TI podrán obtener acceso a información detallada sobre las actualizaciones que faltan tal como se muestra a continuación:

![resultado de la búsqueda](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este informe incluye información crítica que puede utilizarse para identificar el tipo de amenaza para el que es vulnerable este sistema, que incluye los artículos de Microsoft KB asociados con la actualización de seguridad y el boletín de MS que tenga más detalles sobre la vulnerabilidad.

### <a name="monitoring-identity-and-access"></a>Supervisión de la identidad y el acceso
Puesto que los usuarios trabajan desde cualquier lugar, usan diferentes dispositivos y obtienen acceso a una gran cantidad de aplicaciones locales y en la nube, es fundamental que se protejan sus credenciales. Los ataques de robo de credenciales son aquellos en los que un atacante inicialmente obtiene acceso a las credenciales de un usuario normal para tener acceso a un sistema dentro de la red. Muchas veces, este ataque inicial es solo una forma de obtener acceso a la red y el objetivo final es descubrir las cuentas con privilegios. 

Los atacantes permanecerán en la red haciendo uso de herramientas disponibles gratuitamente para extraer las credenciales de las sesiones de otras cuentas de inicio de sesión. Dependiendo de la configuración del sistema, se pueden extraer estas credenciales en forma de hash, vales o incluso contraseñas de texto sin formato.  

> [!NOTE]
> Las máquinas que están directamente expuestas a Internet sufrirán muchos intentos fallidos de iniciar sesión con todo tipo de nombres de usuario conocidos (por ejemplo, administrador). En la mayoría de los casos no hay ningún problema si no se utilizan nombres de usuario conocidos y si la contraseña es suficientemente segura.
> 
> 

Es posible identificar estos intrusos antes que pongan en peligro una cuenta con privilegios. Puede aprovechar la solución **Seguridad y auditoría** de OMS para supervisar la identidad y el acceso. Siga los pasos siguientes para obtener acceso al panel de **Identidad y acceso** :

1. En el panel principal de **Microsoft Operations Management Suite** , haga clic en el icono Seguridad y auditoría.
2. En el panel de **Seguridad y auditoría**, haga clic en **Identity and Access (Preview)** (Identidad y acceso (versión preliminar)) en **Dominios de seguridad**. El panel **Identity and Access (Preview)** (Identidad y acceso (versión preliminar)) aparece como se muestra a continuación:

![Identidad y acceso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Debe incluir la supervisión de la identidad como parte de su estrategia de supervisión regular. El administrador de TI debe comprobar si hay un nombre de usuario válido específico con el que se hayan realizado muchos intentos. Esto podría indicar que un atacante ha adquirido el nombre de usuario real e intenta obtener acceso mediante fuerza bruta o que hay una herramienta automática que usa una contraseña codificada de forma rígida que ha expirado.

Este panel permite a la TI identificar rápidamente amenazas posibles relacionadas con la identidad y el acceso a los recursos de la empresa. Es especialmente importante identificar también las tendencias potenciales, por ejemplo en el icono Inicios de sesión con el paso del tiempo, puede ver la cantidad de veces que se produjo un error al intentar iniciar sesión. En este caso, el equipo **FileServer** intentó iniciar sesión 35 veces. Puede obtener más información sobre este equipo haciendo clic en él. 

![más detalles](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

El informe generado para este equipo aporta información valiosa sobre este patrón. Tenga en cuenta que la columna **ACCOUNT** proporciona la cuenta de usuario que se usó para intentar obtener acceso al sistema, la columna **TIMEGENERATED** ofrece el intervalo de tiempo en el que se realizó el intento y la columna **LOGONTYPENAME** proporciona la ubicación en la que se ha realizado este intento. Si estos intentos se realizaron localmente en el sistema mediante un programa, la columna **PROCESS** podría mostrar el nombre del proceso. En escenarios donde el intento de inicio de sesión procede de un programa, ya dispone del nombre del proceso, por lo que puede realizar una investigación más en profundidad en el sistema de destino.

## <a name="see-also"></a>Consulte también
En este documento, aprendió a utilizar la solución Seguridad y auditoría de OMS para supervisar sus recursos. Para obtener más información sobre Seguridad de OMS, consulte los siguientes artículos:

* [Información general de Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Introducción a la solución Seguridad y auditoría de Operations Management Suite](oms-security-getting-started.md)
* [Supervisión de las alertas de seguridad y su respuesta en la solución Seguridad y auditoría de Operations Management Suite](oms-security-responding-alerts.md)

