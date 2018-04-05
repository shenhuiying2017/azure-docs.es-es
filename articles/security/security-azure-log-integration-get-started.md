---
title: Introducción a Azure Log Integration | Microsoft Docs
description: Aquí aprenderá cómo instalar el servicio Azure Log Integration y cómo integrar los registros de Azure Storage, los registros de auditoría de Azure y las alertas de Azure Security Center.
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
ms.openlocfilehash: 3e229c4db44fc3c8d16aa2bd0a014fb1acc64a5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration en registros de Azure Diagnostics y reenvío de eventos de Windows

Azure Log Integration ofrece a los clientes una alternativa en el caso de que un conector de [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) no esté disponible en su proveedor de Administración de eventos e información de seguridad (SIEM). Azure Log Integration pone los registros de Azure a disposición de su SIEM para que pueda crear un panel seguridad unificado para todos sus recursos.

> [!NOTE]
> Para más información acerca de Azure Monitor, consulte [Introducción a Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md). Para más información sobre el estado de un conector de Azure Monitor, póngase en contacto con su proveedor de SIEM.

> [!IMPORTANT]
> Si su interés principal está en la recopilación de registros de máquina virtual, la mayoría de los proveedores de SIEM incluyen esta opción en su solución. El conector del proveedor de SIEM se usa siempre como alternativa preferida.

Este artículo le ayuda a empezar a trabajar con Azure Log Integration. Se centra en la instalación del servicio Azure Log Integration y la integración de este con Azure Diagnostics. El servicio Azure Log Integration recopila información del Registro de eventos de Windows del canal de eventos de seguridad de Windows a partir de las máquinas virtuales implementadas en una infraestructura de Azure como servicio. Esto se parece al *reenvío de eventos* que se puede usar en un sistema local.

> [!NOTE]
> La integración de la salida de Azure Log Integration con un proveedor de SIEM la realiza este último. Para más información, consulte [Integrate Azure Log Integration with your on-premises SIEM](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Integración de Azure Log Integration con el proveedor de SIEM local).

El servicio Azure Log Integration se puede ejecutar en un equipo físico o en uno virtual que ejecute Windows Server 2008 R2 u otra versión posterior (preferentemente Windows Server 2016 o Windows Server 2012 R2).

Un equipo físico se puede ejecutar de forma local (o en un sitio de hospedaje). Si decide ejecutar el servicio Azure Log Integration en una máquina virtual, esta puede ubicarse localmente o en una nube pública, como Microsoft Azure.

El equipo físico o la máquina virtual en que se ejecuta el servicio de Azure Log Integration precisa de conectividad de red a la nube pública de Azure. Este artículo proporciona detalles sobre la configuración que se necesita.

## <a name="prerequisites"></a>requisitos previos

Como mínimo, la instalación de Azure Log Integration requiere los elementos siguientes:

* Una **suscripción de Azure**. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una **cuenta de almacenamiento** que se puede utilizar para el registro de Windows Azure Diagnostics (WAD). Puede usar una cuenta de almacenamiento preconfigurada o crear una nueva. Más adelante en este artículo se indica cómo configurar la cuenta de almacenamiento.

  > [!NOTE]
  > Puede que no sea necesaria una cuenta de almacenamiento en función del escenario. En el escenario de Azure Diagnostics descrito en este artículo, se necesita una cuenta de almacenamiento.

* **Dos sistemas**: 
  * Una máquina que ejecute el servicio Azure Log Integration. Esta máquina recopila toda la información de registro que, posteriormente, se importará a su SIEM. Este sistema:
    * Puede encontrarse en un entorno local u hospedado en Microsoft Azure.  
    * Debe ejecutar una versión de x64 de Windows Server 2008 R2 SP1 o posterior y tener instalado Microsoft .NET 4.5.1. Para determinar la versión de .NET instalada, consulte [Determinación de las versiones instaladas de .NET Framework](https://msdn.microsoft.com/library/hh925568).  
    * Debe tener conectividad a la cuenta de Azure Storage que se usa para el registro de Azure Diagnostics. Más adelante en este artículo se indica cómo confirmar la conectividad.
  * Una máquina que desee supervisar. Se trata de una máquina virtual que se ejecuta como una [máquina virtual de Azure](../virtual-machines/virtual-machines-windows-overview.md). La información de registro de esta máquina se envía a la máquina del servicio Azure Log Integration.

Para ver una demostración rápida del proceso de creación de una máquina virtual mediante Azure Portal, vea el siguiente vídeo:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Consideraciones de la implementación

Durante la etapa de prueba, puede usar cualquier sistema que cumpla los requisitos mínimos de sistema operativo. En un entorno de producción, es posible que sea necesario planear el escalado horizontal o vertical de la carga.

Puede ejecutar varias instancias del servicio Azure Log Integration. Sin embargo, puede ejecutar solo una instancia del servicio por cada máquina física o virtual. Además, puede equilibrar la carga de las cuentas de almacenamiento de Azure Diagnostics para WAD. El número de suscripciones que debe proporcionar a las instancias se basará en su capacidad.

> [!NOTE]
> En este momento no hay recomendaciones específicas sobre cuándo escalar horizontalmente las instancias de las máquinas de Azure Log Integration (es decir, las máquinas que ejecutan el servicio Azure Log Integration) o para cuentas de almacenamiento o suscripciones. Las decisiones de escalado se deben basar en las observaciones del rendimiento en cada una de estas áreas.

También tiene la opción de escalar verticalmente el servicio de Azure Log Integration para ayudar a mejorar el rendimiento. Las siguientes métricas de rendimiento pueden ayudarlo a ajustar el tamaño de las máquinas que elija para ejecutar el servicio de Azure Log Integration:

* En una máquina con 8 procesadores (núcleos), una instancia única de Azure Log Integration puede procesar aproximadamente 24 millones de eventos por día (aproximadamente 1 millón de eventos por hora).
* En una máquina con 4 procesadores (núcleos), una instancia única de Azure Log Integration puede procesar aproximadamente 1,5 millones de eventos por día (aproximadamente 62 500 eventos por hora).

## <a name="install-azure-log-integration"></a>Instalación de Azure Log Integration

Para instalar Azure Log Integration, descargue el archivo de instalación de [Azure Log Integration](https://www.microsoft.com/download/details.aspx?id=53324). Termine el proceso de instalación. Decida si desea proporcionar información de telemetría a Microsoft.

El servicio Azure Log Integration recopila datos de telemetría desde la máquina donde está instalado.  

Entre los datos de telemetría que se recopilan se incluyen los siguientes:

* Excepciones que se producen durante la ejecución de Azure Log Integration.
* Métricas sobre la cantidad de consultas y eventos que se procesan.
* Estadísticas sobre cuáles son las opciones de la línea de comandos de Azlog.exe que se usan. 

> [!NOTE]
> Se recomienda que permita que Microsoft recopile datos de telemetría. Puede desactivar la recopilación de datos de telemetría anulando la selección de la casilla **Allow Microsoft to collect telemetry data** (Permitir a Microsoft recopilar datos de telemetría).
>

![Captura de pantalla del panel de instalación, con la casilla de telemetría seleccionada](./media/security-azure-log-integration-get-started/telemetry.png)


En el vídeo siguiente se explica el proceso de instalación:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Pasos posteriores a la instalación y validación

Después de completar la configuración básica, está listo para realizar los pasos posteriores a la instalación y validación:

1. Abra PowerShell como administrador. Vaya a C:\Archivos de programa\Microsoft Azure Log Integration.
2. Importe los cmdlets de Azure Log Integration. Para importar los cmdlets, ejecute el script `LoadAzlogModule.ps1`. Especifique `.\LoadAzlogModule.ps1`, y, a continuación, pulse Entrar (tenga en cuenta el uso de **.\\** en este comando). Debería ver algo parecido a lo que se muestra en la siguiente ilustración:

  ![Captura de pantalla de la salida del comando LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. A continuación, configure Azure Log Integration para usar un entorno de Azure específico. Un *entorno de Azure* es el tipo de centro de datos de la nube de Azure en que desea trabajar. Aunque hay varios entornos de Azure, las opciones pertinentes actualmente son **AzureCloud** o **AzureUSGovernment**. Ejecutando PowerShell como administrador, asegúrese de que se encuentra en C:\Archivos de programa\Microsoft Azure Log Integration\. Después, ejecute este comando:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (para **AzureCloud**)
  
  Si desea usar la nube de Azure US Government, use **AzureUSGovernment** para la variable **-Name**. Actualmente, no se admiten otras nubes de Azure.  

  > [!NOTE]
  > No se reciben comentarios cuando el comando se ejecuta correctamente. 

4. Para poder supervisar un sistema, necesitará el nombre de la cuenta de almacenamiento que usa para Azure Diagnostics. En Azure Portal, vaya a **Máquinas virtuales**. Busque la máquina virtual que desea supervisar. En la sección **Propiedades**, seleccione **Configuración de diagnóstico**.  A continuación, seleccione **Agente**. Anote el nombre de la cuenta de almacenamiento especificada. Necesitará este nombre de cuenta para un paso posterior.

  ![Captura de pantalla del panel de configuración de Azure Diagnostics](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Captura de pantalla del botón Habilitar supervisión a nivel de invitado](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Si no se ha habilitado la supervisión cuando se creó la máquina virtual, puede habilitarla como se muestra en la imagen anterior.

5. Ahora, vuelva a la máquina de Azure Log Integration. Compruebe que tiene conectividad a la cuenta de almacenamiento desde el sistema en que está instalado Azure Log Integration. El equipo en el que se ejecuta el servicio Azure Log Integration necesita acceder a la cuenta de almacenamiento para recuperar la información que registra Azure Diagnostics en cada uno de los sistemas supervisados. Para comprobar la conectividad: 
  1. [Descargue el Explorador de Azure Storage](http://storageexplorer.com/).
  2. Complete la instalación.
  3. Cuando finalice la instalación, seleccione **Siguiente**. Deje la casilla **Iniciar Explorador de Microsoft Azure Storage** activada.  
  4. Inicie sesión en Azure.
  5. Compruebe que puede ver la cuenta de almacenamiento configurada para Azure Diagnostics: 

    ![Captura de pantalla de las cuentas de almacenamiento del Explorador de Storage](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Aparecen varias opciones en las cuentas de almacenamiento. En **Tablas** debería ver una denominada **WADWindowsEventLogsTable**.

  Si no se ha habilitado la supervisión cuando se creó la máquina virtual, puede habilitarla como se ha descrito anteriormente.


## <a name="integrate-azure-diagnostics-logging"></a>Integración de registros de Azure Diagnostics

En este paso, configurará la máquina que ejecuta el servicio Azure Log Integration para conectarse a la cuenta de almacenamiento que contiene los archivos de registro.

Antes de realizar este paso, se necesitan varios elementos:  
* **FriendlyNameForSource:** se trata de un nombre descriptivo que se puede aplicar a la cuenta de almacenamiento configurada para que la máquina virtual almacene en ella información de Azure Diagnostics.
* **StorageAccountName:** se trata del nombre de la cuenta de almacenamiento especificada al configurar Azure Diagnostics.  
* **StorageKey:** se trata de la clave de almacenamiento de la cuenta de almacenamiento en la que se almacena la información de Azure Diagnostics para esta máquina virtual.  

Realice los pasos siguientes para obtener la clave de almacenamiento:
1. Vaya a [Azure Portal](http://portal.azure.com).
2. Seleccione **Todos los servicios** en el panel de navegación.
3. En el cuadro de texto **Filtrar**, escriba **Almacenamiento**. A continuación, seleccione **Cuentas de almacenamiento**.

  ![Captura de pantalla que muestra las cuentas de almacenamiento en todos los servicios](./media/security-azure-log-integration-get-started/filter.png)

4. Aparece una lista de cuentas de almacenamiento. Haga doble clic en la cuenta que asignó para almacenar los registros.

  ![Captura de pantalla que muestra una lista de cuentas de almacenamiento](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. En **Configuración**, seleccione **Claves de acceso**.

  ![Captura de pantalla que muestra la opción Claves de acceso en el menú](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Copie **key1** y guárdela en una ubicación segura a la que pueda acceder en el siguiente paso.
7. En el servidor donde instaló Azure Log Integration, abra una ventana del símbolo del sistema como administrador. (Asegúrese de que abre una ventana del símbolo del sistema como administrador, y no PowerShell).
8. Vaya a C:\Archivos de programa\Microsoft Azure Log Integration.
9. Ejecute este comando: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Ejemplo:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Si desea que el identificador de suscripción aparezca en el XML de eventos, anexe el identificador de suscripción al nombre descriptivo:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Ejemplo: 
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Espere hasta 60 minutos y después vea los eventos que se extrajeron de la cuenta de almacenamiento. Para ver los eventos, en Azure Log Integration, seleccione **Visor de eventos** > **Registros de Windows** > **Eventos reenviados**.

En el siguiente vídeo se tratan los pasos anteriores:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>¿Qué ocurre si los datos no aparecen en la carpeta Eventos reenviados?
Si después de una hora los datos no aparecen en la carpeta Eventos reenviados, realice los pasos siguientes:

1. Compruebe la máquina que ejecuta el servicio Azure Log Integration. Confirme que puede acceder a Azure. Para probar la conectividad en un explorador, intente acceder a [Azure Portal](http://portal.azure.com).
2. Asegúrese de que la cuenta de usuario AzLog tiene permiso de escritura en la carpeta users\Azlog.
  1. Abra el Explorador de archivos.
  2. Vaya a C:\users.
  3. Haga clic con el botón derecho en C:\users\Azlog.
  4. Seleccione **Seguridad**.
  5. Seleccione **NT Service\Azlog**. Compruebe los permisos de la cuenta. Si la cuenta no está en esta pestaña o si no aparecen actualmente los permisos apropiados, en esta pestaña puede conceder los permisos de cuentas.
3. Cuando ejecute el comando `Azlog source list`, asegúrese de que la cuenta de almacenamiento que se agregó en el comando `Azlog source add` aparece en la salida.
4. Para ver si el servicio Azure Log Integration ha notificado algún error, vaya a **Visor de eventos** > **Registros de Windows** > **Aplicación**.

Si tiene algún problema durante la instalación y configuración, puede crear una [solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md). Como servicio, seleccione **Log Integration**.

Otra opción de soporte técnico es el [Foro MSDN de Azure Log Integration](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). En él, la comunidad puede proporcionar soporte técnico respondiendo a preguntas y compartiendo sugerencias y trucos sobre cómo sacar el máximo partido a Azure Log Integration. El equipo de Azure Log Integration también supervisa este foro. Ayudan siempre que pueden.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre Azure Log Integration, consulte los siguientes artículos:

* [Azure Log Integration para registros de Azure](https://www.microsoft.com/download/details.aspx?id=53324). El Centro de descarga proporciona información, requisitos del sistema e instrucciones de instalación de Azure Log Integration.
* [Introducción a Azure Log Integration](security-azure-log-integration-overview.md). Este artículo es una introducción a Azure Log Integration, sus principales funcionalidades y cómo funciona.
* [Pasos de configuración de asociados](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Esta entrada de blog muestra cómo configurar la integración de registro de Azure para trabajar con soluciones de asociados como Splunk, HP ArcSight e IBM QRadar. Esta entrada describe la guía actual sobre cómo configurar los componentes de SIEM. Póngase en contacto con el proveedor de SIEM para obtener más detalles.
* [Preguntas más frecuentes sobre la integración de registro de Azure (P+F)](security-azure-log-integration-faq.md). Estas preguntas frecuentes dan respuesta a las preguntas sobre Azure Log Integration.
* [Integración de las alertas de Azure Security Center con Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). En este artículo se muestra cómo sincronizar las alertas de Security Center y los eventos de seguridad de las máquinas virtuales que recopilan Azure Diagnostics y los registros de actividades de Azure. Puede sincronizar los registros mediante Azure Log Analytics o una solución de SIEM.
* [New features for Azure Diagnostics and Azure audit logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) (Nuevas características de Azure Diagnostics y registros de auditoría de Azure). Esta entrada de blog es una introducción a los registros de auditoría de Azure y a otras características que le ayudarán a obtener información sobre las operaciones de los recursos de Azure.
