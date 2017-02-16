---
title: "Integración de las alertas de Azure Security Center con la integración de registro de Azure (versión preliminar) | Microsoft Docs"
description: "Este artículo lo ayuda a comenzar a integrar las alertas de Security Center con la integración de registro de Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: 32d26fa811f1b5b34981e40d20e6c941d5f08477


---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration-preview"></a>Integración de las alertas de Azure Security Center con la integración de registro de Azure (versión preliminar)
Muchos equipos de respuesta a incidentes y operaciones de seguridad confían en una solución de administración de eventos (SIEM) e información de seguridad como punto de partida para clasificar e investigar alertas de seguridad. Gracias a la integración de registro de Azure, los clientes pueden sincronizar alertas de Azure Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM casi en tiempo real.

La integración de registro de Azure es compatible con HP ArcSight, Splunk, IBM QRadar y otras soluciones.

## <a name="what-logs-can-i-integrate"></a>¿Qué registros se pueden integrar?
Azure genera gran cantidad de registros para cada servicio. Estos registros se categorizan de la siguiente manera:

* **Registros de control/administración**, que ofrecen visibilidad sobre las operaciones CREATE, UPDATE y DELETE de Azure Resource Manager.
* **Registros del plano de los datos** , que ofrecen visibilidad sobre los eventos que surgen cuando se usa un recurso de Azure. Un ejemplo de esto es el Registro de eventos de Windows, que son los registros de seguridad y aplicación en una máquina virtual.

La integración de registro de Azure actualmente admite la integración de lo siguiente:

* Registros de VM de Azure
* Registros de auditoría de Azure
* Alertas de Azure Security Center

## <a name="install-azure-log-integration"></a>Instalación de la integración de registro de Azure
Descargue la [integración de registro de Azure](https://www.microsoft.com/download/details.aspx?id=53324).

El servicio de integración de registro de Azure recolecta datos de telemetría desde la máquina donde está instalado.  Los datos de telemetría recopilados son:

* Excepciones que se producen durante la ejecución de la integración de registro de Azure.
* Métricas sobre la cantidad de consultas y eventos que se procesan.
* Estadísticas sobre cuáles son las opciones de la línea de comandos de Azlog.exe que se usan.

> [!NOTE]
> Puede desactivar la recopilación de datos de telemetría si quita la marca de esta opción.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integración de los registros de auditoría de Azure y las alertas de Security Center
1. Abra el símbolo del sistema y ejecute**cd** en **c:\Program Files\Microsoft Azure Log Integration**.
2. Ejecute el comando **azlog createazureid** para crear una [entidad de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md) en los inquilinos de Azure Active Directory (AD) que hospedan las suscripciones de Azure.

    Se le pedirá los datos de inicio de sesión de Azure.

   > [!NOTE]
   > Debe ser el propietario o un coadministrador de la suscripción.
   >
   >

    La autenticación en Azure se hace con Azure AD.  La creación de una entidad de servicio para la integración de registro de Azure creará la identidad de Azure AD a la que se otorgará acceso de lectura de las suscripciones de Azure.
3. Ejecute el comando **azlog authorize <SubscriptionID>** para asignar acceso de lectura sobre la suscripción a la entidad de servicio que se creó en el paso 2. Si no especifica un **identificador de suscripción**, la entidad de servicio recibirá el rol de lector de todas las suscripciones a las que se tiene acceso.

   > [!NOTE]
   > Puede que vea advertencias si ejecuta el comando **authorize** inmediatamente después del comando **createazureid**. Se produce cierta latencia entre el momento en que se crea la cuenta de Azure AD y el momento en que la cuenta está disponible para su uso. Si espera unos 10 segundos después de ejecutar el comando **createazureid** para ejecutar el comando **authorize**, no debería ver estas advertencias.
   >
   >
4. Compruebe las carpetas siguientes para confirmar que contienen los archivos JSON del registro de auditoría:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Compruebe las carpetas siguientes para confirmar que contienen las alertas de Security Center:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Dirija el conector del reenviador de archivos SIEM estándar a la carpeta adecuada para canalizar los datos a la instancia SIEM. Consulte las [configuraciones de SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) en la configuración de SIEM.

Si tiene dudas sobre la integración del registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los registros de auditoría de Azure y las definiciones de propiedad, consulte:

* [Operaciones de auditoría con el Administrador de recursos](../azure-resource-manager/resource-group-audit.md)
* [Lista de los eventos de administración de una suscripción](https://msdn.microsoft.com/library/azure/dn931934.aspx) , para recuperar los eventos de registro de auditoría.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.



<!--HONumber=Feb17_HO3-->


