---
title: "Introducción a integración de registro de Azure | Microsoft Docs"
description: "Aquí aprenderá cómo instalar el servicio de integración de registro de Azure y cómo integrar los registros de Almacenamiento de Azure, los registros de auditoría de Azure y las alertas de Azure Security Center."
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/20/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 4555216950811960ccb42241bcade5ec892a77ce
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration en registros de Diagnósticos de Azure y reenvío de eventos de Windows

Azure Log Integration (AzLog) ofrece a los clientes una alternativa en el caso de que un conector de [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) no esté disponible en su proveedor de SIEM. Azure Log Integration pone los registros de Azure a disposición de su SIEM y le permite crear un panel seguridad unificado para todos sus recursos.

>[!NOTE]
>Para más información acerca de Azure Monitor, puede revisar [Introducción a Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md), si lo que desea es información sobre el estado de un conector de Azure Monitor, póngase en contacto con su proveedor de SIEM.

>[!IMPORTANT]
>Si su interés principal está en la recopilación de registros de máquina virtual, la mayoría de los proveedores de SIEM incluyen este servicio en su solución. El conector del proveedor de SIEM debe usarse siempre como alternativa preferida.

Este artículo le ayuda a empezar con Azure Log Integration centrándose en la instalación del servicio AzLog e integrando el servicio con Azure Diagnostics. Después, Azure Log Integration podrá recopilar información de registro de eventos de Windows desde el canal de eventos de seguridad de Windows de máquinas virtuales implementadas en IaaS de Azure. Se trata de un proceso similar al del "Reenvío de eventos" que puede haber utilizado en un entorno local.

>[!NOTE]
>El propio servicio SIEM ofrece la posibilidad de obtener la salida de Azure Log Integration en integración con SIEM. Para más información consulte el artículo sobre [Integración de Azure Log Integration con el entorno local de SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

Para ser exactos, el servicio de Azure Log Integration se ejecuta en un equipo físico o virtual que usa el sistema operativo Windows Server 2008 R2 o una versión posterior (se prefieren Windows Server 2012 R2 o Windows Server 2016).

El equipo físico se puede ejecutar de forma local (o en un sitio de hospedaje). Si decide ejecutar el servicio de Azure Log Integration en una máquina virtual, esta puede ubicarse localmente o en una nube pública, como Microsoft Azure.

El equipo físico o la máquina virtual en que se ejecuta el servicio de Azure Log Integration precisa de conectividad de red a la nube pública de Azure. En los pasos de este artículo se proporcionan detalles sobre la configuración.

## <a name="prerequisites"></a>requisitos previos

Como mínimo, la instalación de AzLog requiere los siguientes elementos:

* Una **suscripción de Azure**. Si no tiene, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una **cuenta de almacenamiento** que se pueda utilizar para el registro de diagnóstico de Microsoft Azure (puede usar una cuenta de almacenamiento configurada previamente o crear una. Se va a realizar una demostración de cómo configurar la cuenta de almacenamiento más adelante en este artículo).

  >[!NOTE]
  Puede que no sea necesaria una cuenta de almacenamiento en función del escenario. Para el escenario de diagnóstico de Azure descrito en este artículo se necesita una.

* **Dos sistemas**: un equipo que ejecutará el servicio Azure Log Integration y una máquina que se va a supervisar y cuya información de registro se envía a la máquina del servicio AzLog.
   * Un equipo que desea supervisar: se trata de una máquina virtual que se ejecuta como una [máquina virtual de Azure](../virtual-machines/virtual-machines-windows-overview.md).
   * Una máquina que ejecuta el servicio Azure Log Integration, que recopila toda la información de registro que se importará más adelante en el entorno de SIEM.
    * Este sistema puede encontrarse en un entorno local o en Microsoft Azure.  
    * Debe ejecutar una versión de x64 de Windows Server 2008 R2 SP1 o posterior y tener instalado .NET 4.5.1. Puede determinar la versión de .NET instalada según se indica en el artículo titulado [Cómo: Determinar qué versiones de .NET Framework están instaladas](https://msdn.microsoft.com/library/hh925568).  
    Debe tener conectividad a la cuenta de Azure Storage que se usa para el registro de diagnóstico de Azure. Se proporcionarán instrucciones más adelante en este artículo sobre cómo puede confirmar esta conectividad

* Una **cuenta de almacenamiento** que se puede utilizar para el registro de diagnóstico de Microsoft Azure. Puede usar una cuenta de almacenamiento preconfigurada o crear una nueva. Más adelante en este artículo configurará la cuenta de almacenamiento.
  >[!NOTE]
  Puede que no sea necesaria una cuenta de almacenamiento en función del escenario. Para el escenario de diagnóstico de Azure descrito en este artículo se necesita una.
* **Dos sistemas**: un equipo que ejecuta el servicio Azure Log Integration y una máquina que se va a supervisar y cuya información de registro se envía a la máquina del servicio AzLog.
   * Un equipo que desea supervisar: se trata de una máquina virtual que se ejecuta como una [máquina virtual de Azure](../virtual-machines/virtual-machines-windows-overview.md).
   * Una máquina que ejecuta el servicio Azure Log Integration, que recopila toda la información de registro que se importará más adelante en el entorno de SIEM.
    * Este sistema puede encontrarse en un entorno local o en Microsoft Azure.  
    * Debe ejecutar una versión de x64 de Windows Server 2008 R2 SP1 o posterior y tener instalado .NET 4.5.1. Puede determinar la versión de .NET instalada según se indica en el artículo titulado [Cómo: Determinar qué versiones de .NET Framework están instaladas](https://msdn.microsoft.com/library/hh925568).  
    Debe tener conectividad a la cuenta de Azure Storage que se usa para el registro de diagnóstico de Azure. Más adelante en este artículo se describe cómo confirmar esta conectividad.

Para ver una demostración rápida del proceso de creación de una máquina virtual mediante Azure Portal, vea el siguiente vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Consideraciones de la implementación

Durante la etapa de prueba, puede usar cualquier sistema que cumpla los requisitos mínimos de sistema operativo. En un entorno de producción, es posible que sea necesario planear el escalado horizontal o vertical de la carga.

Puede ejecutar varias instancias del servicio Azure Log Integration. Esto se limita a una instancia por máquina física o virtual. Además, puede equilibrar la carga de las cuentas de almacenamiento de Diagnósticos de Azure para Windows (WAD) y el número de suscripciones que necesita proporcionar a las instancias debe basarse en su capacidad.

>[!NOTE]
>En este momento no hay recomendaciones específicas sobre cuándo escalar horizontalmente las instancias de las máquinas de Azure Log Integration (es decir, las máquinas que ejecutan el servicio Azure Log Integration) o para cuentas de almacenamiento o suscripciones. Las decisiones de escalado deben basarse en las observaciones del rendimiento en cada una de estas áreas.

Si el volumen de eventos es alto, puede ejecutar varias instancias del servicio de Azure Log Integration (una instancia por máquina física o virtual). Además, puede equilibrar la carga de las cuentas de almacenamiento de Azure Diagnostics para Windows (WAD).

También tiene la opción de escalar verticalmente el servicio de Azure Log Integration para ayudar a mejorar el rendimiento. Las siguientes métricas de rendimiento pueden ayudarlo a ajustar el tamaño de las máquinas que elija para ejecutar el servicio de Azure Log Integration:

* En una máquina con 8 procesadores (núcleos), una instancia única de AzLog Integrator puede procesar aproximadamente 24 millones de eventos por día (~1M/hora).
* En una máquina con 4 procesadores (núcleos), una instancia única de AzLog Integrator puede procesar aproximadamente 1,5 millones de eventos por día (~62 500/hora).

## <a name="install-azure-log-integration"></a>Instalación de la integración de registro de Azure

Para instalar Azure Log Integration, necesita descargar el archivo de instalación de [Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324). Ejecute toda la rutina de configuración y decida si desea proporcionar información de telemetría a Microsoft.  

![Pantalla de instalación con la casilla de telemetría activada](./media/security-azure-log-integration-get-started/telemetry.png)

> [!NOTE]
> Se recomienda que permita que Microsoft recopile datos de telemetría. Puede desactivar la recopilación de datos de telemetría si quita la marca de esta opción.
>


El servicio de Azure Log Integration recolecta datos de telemetría desde la máquina donde está instalado.  

Los datos de telemetría recopilados son:

* Excepciones que se producen durante la ejecución de la integración de registro de Azure.
* Métricas sobre la cantidad de consultas y eventos que se procesan.
* Estadísticas sobre cuáles son las opciones de la línea de comandos de Azlog.exe que se usan.

En el vídeo siguiente se explica el proceso de instalación.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>Pasos posteriores a la instalación y validación

Después de completar la rutina de configuración básica, está listo para realizar los pasos posteriores a la instalación y validación:

1. Abra una ventana de PowerShell con privilegios elevados y vaya a **c:\Archivos de programa\Microsoft Azure Log Integration**.
2. El primer paso que debe realizar es obtener los cmdlets de AzLog importados. Puede hacerlo mediante la ejecución del script **LoadAzlogModule.ps1** (tenga en cuenta el ".\" en el siguiente comando). Escriba **.\LoadAzlogModule.ps1** y presione **ENTRAR**. Debería ver algo parecido a lo que se muestra en la siguiente ilustración. </br></br>
![Pantalla de instalación con la casilla de telemetría activada](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. Ahora debe configurar AzLog para usar un entorno de Azure específico. Un "entorno de Azure" es el "tipo" de centro de datos de la nube de Azure en que desea trabajar. Aunque hay varios entornos de Azure en este momento, las opciones pertinentes actualmente son **AzureCloud** o **AzureUSGovernment**.   En el entorno de PowerShell con privilegios elevados, asegúrese de que se encuentra en **C:\Archivos de programa\Microsoft Azure Log Integration\**. </br></br>
    Una vez ahí, ejecute el comando: </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (para anuncios de Azure)

      >[!NOTE]
      >Si el comando se ejecuta correctamente, no recibirá ningún informe.  Si desea usar la nube de Azure US Government, debe usar **AzureUSGovernment** en la variable de nombre para la nube del gobierno estadounidense. No se admiten otras nubes de Azure en este momento.  
4. Para poder supervisar un sistema, necesitará el nombre de la cuenta de almacenamiento que usa para Azure Diagnostics.  En Azure Portal, vaya a **Máquinas virtuales** y busque la máquina virtual que a supervisar. En la sección **Propiedades**, elija **Configuración de diagnóstico**.  Haga clic en **Agente** y anote el nombre de cuenta de almacenamiento especificado. Necesitará este nombre de cuenta para un paso posterior.

    ![Configuración de diagnóstico de Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

    ![Configuración de diagnóstico de Azure](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

     >[!NOTE]
     >Si no se ha habilitado la supervisión durante la creación de la máquina virtual, se le dará la opción de habilitarla como se ha mostrado anteriormente.

5. Ahora se va a centrar la atención de nuevo en la máquina de Azure Log Integration. Es necesario verificar que tiene conectividad a la cuenta de almacenamiento desde el sistema en que está instalado Azure Log Integration. El equipo en el que se ejecuta el servicio Azure Log Integration necesita acceder a la cuenta de almacenamiento para recuperar información registrada por Azure Diagnostics como se ha configurado en cada uno de los sistemas supervisados.  
    a. Puede descargar el explorador de Azure Storage [aquí](http://storageexplorer.com/).
   b. Ejecute toda la rutina de configuración c. Una vez finalizada la instalación, haga clic en **Siguiente** y deje marcada la casilla **Launch Microsoft Azure Storage Explorer** (Iniciar el Explorador de Microsoft Azure Storage).  
    d. Inicie sesión en Azure.
   e. Verifique que puede ver la cuenta de almacenamiento configurada para Diagnósticos de Azure. 
        ![Cuentas de almacenamiento](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
6. Observe que hay unas cuantas opciones en las cuentas de almacenamiento. Una de ellas es **Tablas**. En **Tablas** debería ver una denominada **WADWindowsEventLogsTable**. </br></br>

 Si no se ha habilitado la supervisión durante la creación de la máquina virtual, se le da la opción de habilitarla como se ha mostrado anteriormente.
7. Ahora se va a centrar la atención de nuevo en la máquina de Azure Log Integration. Es necesario verificar que tiene conectividad a la cuenta de almacenamiento desde el sistema en que está instalado Azure Log Integration. El equipo físico o la máquina virtual en que se ejecuta el servicio de Azure Log Integration necesita acceder a la cuenta de almacenamiento para recuperar información registrada por Diagnósticos de Azure como se ha configurado en cada uno de los sistemas supervisados.  
  1. Puede descargar el explorador de Azure Storage [aquí](http://storageexplorer.com/).
  2. Ejecución de toda la rutina de configuración
  3. Una vez finalizada la instalación, haga clic en **Siguiente** y deje marcada la casilla **Launch Microsoft Azure Storage Explorer** (Iniciar el Explorador de Microsoft Azure Storage).  
  4. Inicie sesión en Azure.
  5. Verifique que puede ver la cuenta de almacenamiento configurada para Diagnósticos de Azure.  
  6. Observe que hay unas cuantas opciones en las cuentas de almacenamiento. Una de ellas es **Tablas**. En **Tablas** debería ver una denominada **WADWindowsEventLogsTable**. </br></br>
   ![Cuentas de almacenamiento](./media/security-azure-log-integration-get-started/storage-explorer.png) 

## <a name="integrate-azure-diagnostic-logging"></a>Integración de registros de Diagnósticos de Azure

En este paso, configurará la máquina que ejecuta el servicio de Azure Log Integration para conectarse a la cuenta de almacenamiento que contiene los archivos de registro.
Antes de realizar este paso, se necesitan otros elementos previamente.  
* **FriendlyNameForSource:** se trata de un nombre descriptivo que se puede aplicar a la cuenta de almacenamiento configurada para que la máquina virtual almacene en ella información de Diagnósticos de Azure.
* **StorageAccountName:** se trata del nombre de la cuenta de almacenamiento especificada al configurar Azure Diagnostics.  
* **StorageKey:** se trata de la clave de almacenamiento de la cuenta de almacenamiento en la que se almacena información de Diagnósticos de Azure para esta máquina virtual.  

Realice los pasos siguientes para obtener la clave de almacenamiento:
 1. Vaya a [Azure Portal](http://portal.azure.com).
 2. En el panel de navegación de la consola de Azure, haga clic en **Todos los servicios.**
  3. Escriba **Almacenamiento** en el cuadro de texto **Filtrar**. Haga clic en **Cuentas de almacenamiento**.

       ![Captura de pantalla que muestra las cuentas de almacenamiento en todos los servicios](./media/security-azure-log-integration-get-started/filter.png)
 4. Aparecerá una lista de cuentas de almacenamiento; haga doble clic en la cuenta asignada al almacenamiento de registros.

       ![lista de cuentas de almacenamiento](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. Haga clic en **Claves de acceso** en la sección **Configuración**.

      ![Claves de acceso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. Copie **key1** y colóquela en una ubicación segura a la que pueda acceder para el siguiente paso.

       ![dos claves de acceso](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. En el servidor en que se ha instalado Azure Log Integration, abra un símbolo del sistema con privilegios elevados (tenga en cuenta que aquí se usa una venta del símbolo del sistema con privilegios elevados, no una consola de PowerShell).
 8. Vaya a **C:\Archivos de programa\Microsoft Azure Log Integration**
 9. Ejecute ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` </br> Por ejemplo ``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`` Si desea que el identificador de suscripción aparezca en el XML de eventos, anexe el identificador de suscripción al nombre descriptivo: ``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`` o, por ejemplo, ``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]
>Espere hasta 60 minutos y después vea los eventos que se extrajeron de la cuenta de almacenamiento. Para verlos, abra **Visor de eventos > Registros de Windows > Eventos reenviados** en AzLog Integrator.

Aquí puede ver un vídeo que explica los pasos descritos anteriormente.

>[!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>¿Qué ocurre si los datos no aparecen en la carpeta Eventos reenviados?
Si después de una hora los datos no aparecen en la carpeta **Eventos reenviados** , haga lo siguiente:

1. Compruebe la máquina en que se ejecuta el servicio de Azure Log Integration y confirme que puede acceder a Azure. Para probar la conectividad, intente abrir el [Portal de Azure](http://portal.azure.com) desde el explorador.
2. Asegúrese de que la cuenta de usuario **AzLog** tiene permiso de escritura en la carpeta **users\Azlog**.
  <ol type="a">
   <li>Abra el **Explorador de Windows** .</li>
  <li> Vaya a **c:\users** .</li>
  <li> Haga clic con el botón derecho en **c:\users\Azlog** </li>
  <li> Haga clic en **Seguridad**  .</li>
  <li> Haga clic en **Servicio NT\Azlog** y compruebe los servicios de la cuenta. Si la cuenta no está en esta pestaña o si no aparecen actualmente los permisos apropiados, en esta pestaña puede conceder los derechos de cuentas.</li>
  </ol>
3. Asegúrese de que la cuenta de almacenamiento agregada en el comando **Azlog source add** aparece al ejecutar el comando **Azlog source list**.
4. Vaya a **Visor de eventos > Registros de Windows > Aplicación** y vea si la integración de registro de Azure ha notificado algún error.


Si experimenta problemas durante la instalación y la configuración, abra una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md) y seleccione **Integración de registros** como el servicio para el que está solicitando soporte técnico.

Otra opción de soporte técnico es el [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). Aquí la comunidad ofrece ayuda común en relación con preguntas, respuestas, consejos y sugerencias sobre cómo sacar el máximo partido de Azure Log Integration. Además, el equipo de Azure Log Integration supervisa este foro y le ayudará siempre que sea posible.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre Azure Log Integration, vea los siguientes documentos:

* [Microsoft Azure Log Integration for Azure logs](https://www.microsoft.com/download/details.aspx?id=53324) (Integración de registros de Microsoft Azure para registros de Azure): Centro de descarga para obtener información, los requisitos del sistema y las instrucciones de instalación de la integración de registros de Azure.
* [Introduction to Azure log integration](security-azure-log-integration-overview.md) (Introducción a la integración de registro de Azure): este documento es una introducción del servicio de integración de registro de Azure, las principales funcionalidades y cómo funciona.
* [Partner configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Pasos de configuración de soluciones de asociados): esta entrada de blog muestra cómo configurar la integración de registro de Azure para trabajar con soluciones de asociados como Splunk, HP ArcSight e IBM QRadar. Esta es la guía actual sobre cómo configurar los componentes de SIEM. Primero póngase en contacto con el proveedor de SIEM para obtener más detalles.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Este artículo de preguntas más frecuentes responde a preguntas sobre la integración de registro de Azure.
* [Integración de las alertas de Azure Security Center con la integración de registro de Azure](../security-center/security-center-integrating-alerts-with-log-integration.md): este documento muestra cómo sincronizar alertas de Security Center, además de eventos de seguridad de máquina virtual recopilados por Azure Diagnostics y Registros de actividad de Azure, con la solución Log Analytics o de SIEM.
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Diagnósticos de Azure y registros de auditoría de Azure): esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.
