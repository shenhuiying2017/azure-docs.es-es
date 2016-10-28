<properties
   pageTitle="Introducción a integración de registro de Azure | Microsoft Azure"
   description="Aquí aprenderá cómo instalar el servicio de integración de registro de Azure y cómo integrar los registros de Almacenamiento de Azure, los registros de auditoría de Azure y las alertas de Azure Security Center."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# Introducción a la integración de registro de Azure (versión preliminar)

Integración de registro de Azure le permite integrar los registros sin procesar de los recursos de Azure en los sistemas locales de administración de eventos e información de seguridad (SIEM). Esta integración proporciona un panel unificado de todos los recursos, locales o en la nube, para que pueda agregar, correlacionar, analizar y alertar de eventos de seguridad asociados a las aplicaciones.

Este tutorial le explica cómo instalar la integración de registro de Azure y cómo integrar los registros de Almacenamiento de Azure, los registros de auditoría de Azure y las alertas de Azure Security Center. El tiempo estimado para completar este tutorial es de una hora.

## Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

- Una máquina (local o en la nube) para instalar el servicio de integración de registro de Azure. Esta máquina debe ejecutar un sistema operativo Windows de 64 bits que tenga .Net 4.5.1 instalado. Esta máquina se denomina **Azlog Integrator**.
- Suscripción de Azure. Si no tiene, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
- Diagnósticos de Azure habilitado para las máquinas virtuales (VM) de Azure. Para habilitar los diagnósticos en los Servicios en la nube, consulte [Habilitación de Diagnósticos de Azure en Servicios en la nube de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Para habilitar los diagnósticos de una máquina virtual de Azure con Windows, consulte [Uso de PowerShell para habilitar Diagnósticos de Azure en una máquina virtual con Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Conectividad desde la máquina Azlog Integrator al Almacenamiento de Azure para autenticar y autorizar en la suscripción de Azure.
- Para los registros de la máquina virtual de Azure, el agente del SIEM (por ejemplo, Splunk Universal Forwarder, el agente recopilador de eventos de Windows de HP ArcSight o IBM QRadar WinCollect) debe instalarse en la máquina Azlog Integrator.

## Consideraciones de la implementación

Puede ejecutar varias instancias de la máquina Azlog Integrator si el volumen de eventos es grande. El equilibrio de carga de las cuentas de almacenamiento de Diagnósticos de Azure para Windows *(WAD)* y el número de suscripciones que necesita proporcionar a las instancias debe basarse en su capacidad.

En una máquina con 8 procesadores (núcleos), una instancia única de Azlog Integrator puede procesar aproximadamente 24 millones de eventos por día (~1M/hora).

En una máquina con 4 procesadores (núcleos), una instancia única de Azlog Integrator puede procesar aproximadamente 1,5 millones de eventos por día (~62 500/hora).

## Instalación de la integración de registro de Azure

Descargue la [integración de registro de Azure](https://www.microsoft.com/download/details.aspx?id=53324).

El servicio de integración de registro de Azure recolecta datos de telemetría desde la máquina donde está instalado. Los datos de telemetría recopilados son:

- Excepciones que se producen durante la ejecución de la integración de registro de Azure.
- Métricas sobre la cantidad de consultas y eventos que se procesan.
- Estadísticas sobre cuáles son las opciones de la línea de comandos de Azlog.exe que se usan.

> [AZURE.NOTE] Puede desactivar la recopilación de datos de telemetría si quita la marca de esta opción.

## Integración de registros de la máquina virtual de Azure desde las cuentas de almacenamiento de Diagnósticos de Azure

1. Compruebe los requisitos previos mencionados anteriormente para asegurarse de que la cuenta de almacenamiento WAD recopila los registros antes de continuar con la integración de registro de Azure. No realice los pasos siguientes si su cuenta de almacenamiento WAD no está recopilando registros.

2. Abra el símbolo del sistema y ejecute **cd** en **c:\\Program Files\\Microsoft Azure Log Integration**.

3. Ejecute el comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Reemplace StorageAccountName por el nombre de la cuenta de almacenamiento de Azure configurada para recibir los eventos de diagnóstico de la máquina virtual.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Si desea que el identificador de suscripción aparezca en el XML de eventos, anexe el identificador de suscripción al nombre descriptivo:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Espere de 30 a 60 minutos (podría tardar hasta una hora) y, a continuación, vea los eventos que se extrajeron de la cuenta de almacenamiento. Para verlos, abra **Visor de eventos > Registros de Windows > Eventos reenviados** en Azlog Integrator.

5. Asegúrese de que el conector del SIEM estándar instalado en la máquina está configurado para recopilar eventos de la carpeta **Eventos reenviados** y canalizarlos a la instancia del SIEM. Revise la configuración específica del SIEM para configurar y ver la integración de los registros.

## ¿Qué ocurre si los datos no aparecen en la carpeta Eventos reenviados?

Si después de una hora los datos no aparecen en la carpeta **Eventos reenviados**, haga lo siguiente:

1. Compruebe la máquina y confirme que puede acceder a Azure. Para probar la conectividad, intente abrir el [Portal de Azure](http://portal.azure.com) desde el explorador.

2. Asegúrese de que la cuenta de usuario **azlog** tiene permiso de escritura en la carpeta **users\\azlog**.

3. Asegúrese de que la cuenta de almacenamiento agregada en el comando **azlog source add** aparece al ejecutar el comando **azlog source list**.
4. Vaya a **Visor de eventos > Registros de Windows > Aplicación** y vea si la integración de registro de Azure ha notificado algún error.

Si sigue sin ver los eventos haga lo siguiente:

1. Descargue el [Explorador de almacenamiento de Microsoft Azure](http://storageexplorer.com/).

2. Conéctese a la cuenta de almacenamiento agregada en el comando **azlog source add**.

3. En el Explorador de almacenamiento de Microsoft Azure, vaya a la tabla **WADWindowsEventLogsTable** para ver si hay datos. Si no es así, los diagnósticos de la máquina virtual no se han configurado correctamente.

## Integración de los registros de auditoría de Azure y las alertas de Security Center

1. Abra el símbolo del sistema y ejecute **cd** en **c:\\Program Files\\Microsoft Azure Log Integration**.

2. Ejecute el comando

        azlog createazureid

      Este comando solicita el inicio de sesión de Azure. A continuación, el comando crea una [Entidad de servicio de Azure Active Directory](../active-directory/active-directory-application-objects.md) en los inquilinos de Azure AD que hospedan las suscripciones de Azure en las que el usuario inició sesión como administrador, coadministrador o propietario. El comando producirá un error si el usuario ha iniciado sesión solo como usuario invitado en el inquilino de Azure AD. La autenticación en Azure se hace con Azure Active Directory (AD). La creación de una entidad de servicio para la integración de registro de Azure creará la identidad de Azure AD a la que se otorgará acceso de lectura de las suscripciones de Azure.

3. Ejecute el comando

        azlog authorize <SubscriptionID>

      Este permite asignar acceso de lectura en la suscripción a la entidad de servicio que creó en el paso 2. Si no especifica un SubscriptionID, intentará asignar el rol de lector de la entidad de servicio a todas las suscripciones a las que tiene acceso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Puede que vea advertencias si ejecuta el comando **authorize** inmediatamente después del comando **createazureid**. Se produce cierta latencia entre el momento en que se crea la cuenta de Azure AD y el momento en que la cuenta está disponible para su uso. Si espera unos 10 segundos después de ejecutar el comando **createazureid** para ejecutar el comando **authorize**, no debería ver estas advertencias.

4. Compruebe las carpetas siguientes para confirmar que contienen los archivos JSON del registro de auditoría:

  - **c:\\Users\\azlog\\AzureResourceManagerJson**
  - **c:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. Compruebe las carpetas siguientes para confirmar que contienen las alertas de Security Center:

  - **c:\\Users\\azlog\\ AzureSecurityCenterJson**
  - **c:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. Dirija el conector del reenviador de archivos SIEM estándar a la carpeta adecuada para canalizar los datos a la instancia SIEM. Puede que necesite algunas asignaciones de campo según el producto de SIEM que utilice.

Si tiene dudas sobre la integración del registro de Azure, envíe un correo electrónico a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com).

## Pasos siguientes

En este tutorial, aprendió a instalar la integración de registro de Azure y a integrar los registros de Almacenamiento de Azure. Para obtener más información, consulte:

- [Microsoft Azure Log Integration for Azure logs (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – Download Center (Integración de registro de Microsoft Azure para registros de Azure (versión preliminar): Centro de descargas), para más información, los requisitos del sistema y las instrucciones de instalación de la integración de registro de Azure.
- [Introduction to Azure log integration](security-azure-log-integration-overview.md) (Introducción a la integración de registro de Azure): este documento es una introducción del servicio de integración de registro de Azure, las principales funcionalidades y cómo funciona.
- [Partner configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Pasos de configuración de soluciones de asociados): esta entrada de blog muestra cómo configurar la integración de registro de Azure para trabajar con soluciones de asociados como Splunk, HP ArcSight e IBM QRadar.
- [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas acerca de la integración de registro de Azure.
- [Integración de las alertas de Security Center con la integración de registro de Azure (versión preliminar)](../security-center/security-center-integrating-alerts-with-log-integration.md). Este documento le explica cómo sincronizar las alertas de Security Center, además de los eventos de seguridad de máquina virtual recopilados por Diagnósticos de Azure y los registros de auditoría de Azure, con sus análisis de registros o una solución SIEM.
- [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Diagnósticos de Azure y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.

<!---HONumber=AcomDC_0921_2016-->