---
title: Data Management Gateway para Data Factory | Microsoft Docs
description: "Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Azure Data Factory para mover los datos."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9e40eba285aeb1cce6b77311d1b69a6b96967a0b
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---
# <a name="data-management-gateway"></a>Data Management Gateway
Data Management Gateway es un agente de cliente que debe instalar en el entorno local para copiar datos entre almacenes de datos en la nube y locales. Los almacenes de datos locales compatibles con Data Factory se muestran en la sección de [orígenes de datos compatibles](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Este artículo complementa al tutorial de [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) . El objetivo es crear una canalización que utilice la puerta de enlace para mover los datos de una base de datos de SQL Server local a un blob de Azure. En este artículo se proporciona información detallada sobre el uso de Data Management Gateway. 

Puede escalar horizontalmente una puerta de enlace de administración de datos mediante la asociación de varias máquinas locales con la puerta de enlace. Puede escalar verticalmente aumentando el número de trabajos de movimiento de datos que pueden ejecutarse simultáneamente en un nodo. Esta característica también está disponible para una puerta de enlace lógica con un único nodo. Consulte el artículo [Escalado en Data Management Gateway en Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) para más información.

> [!NOTE]
> Actualmente, la puerta de enlace solo admite la actividad de copia y la actividad de procedimiento almacenado en Data Factory. No es posible usar la puerta de enlace de una actividad personalizada para acceder a orígenes de datos locales.      

## <a name="overview"></a>Información general
### <a name="capabilities-of-data-management-gateway"></a>Funcionalidades de Data Management Gateway
Data Management Gateway proporciona las siguientes funcionalidades:

* Modelo de orígenes de datos local y orígenes de datos en la nube en la misma factoría de datos y movimiento de datos.
* Tiene un panel único para la supervisión y administración con visibilidad del estado de la puerta de enlace desde la página de Data Factory.
* Administrar el acceso a orígenes de datos locales de forma segura.
  * No es necesario ningún cambio en el firewall corporativo. La puerta de enlace solo hace conexiones salientes basadas en HTTP para abrir Internet.
  * Cifrar las credenciales de los almacenes de datos locales con su certificado.
* Mover datos de manera eficaz: los datos se transfieren en paralelo, con resistencia a problemas de red intermitentes mediante una lógica automática de reintentos.

### <a name="command-flow-and-data-flow"></a>Flujo de comandos y flujo de datos
Cuando se utiliza una actividad de copia con la que copiar datos entre un entorno local y la nube, se usa una puerta de enlace para transferir información desde el origen de datos local hasta la nube, y viceversa.

A continuación se muestra el flujo de datos genérico y el resumen de los pasos necesarios para copiar con una puerta de enlace de datos: ![Flujo de datos mediante la puerta de enlace](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. El desarrollador de datos crea una puerta de enlace para una instancia de Azure Data Factory mediante [Azure Portal](https://portal.azure.com) o un [cmdlet de PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2. El desarrollador de datos crea un servicio vinculado para un almacén de datos local especificando la puerta de enlace. Como parte de la configuración del servicio vinculado, el desarrollador de datos usa la aplicación Setting Credentials (Establecer credenciales) para especificar las credenciales y los tipos de autenticación.  El cuadro de diálogo de la aplicación Setting Credentials (Establecer credenciales) se comunicará con el almacén de datos para probar la conexión y la puerta de enlace para guardar las credenciales.
3. Gateway cifra las credenciales con el certificado asociado a la puerta de enlace (suministrado por el desarrollador de datos) antes de guardar las credenciales en la nube.
4. El servicio Data Factory se comunica con la puerta de enlace para la programación y administración de trabajos a través de un canal de control que usa una cola de Azure Service Bus compartida. Cuando es necesario iniciar un trabajo de actividad de copia, Data Factory pone en cola la solicitud junto con la información de credenciales. La puerta de enlace inicia el trabajo después de sondear la cola.
5. La puerta de enlace descifra las credenciales con el mismo certificado y, después, se conecta al almacén de datos local con las credenciales y el tipo de autenticación adecuados.
6. La puerta de enlace copia datos de un almacén local a un almacenamiento en nube, o viceversa, en función de cómo esté configurada la actividad de copia en la canalización de datos. En este paso, la puerta de enlace se comunica directamente con servicios de almacenamiento basados en la nube, como Azure Blob Storage, a través de un canal seguro (HTTPS).

### <a name="considerations-for-using-gateway"></a>Consideraciones a la hora de usar la puerta de enlace
* Se puede utilizar una sola instancia de Data Management Gateway para varios orígenes de datos locales. Sin embargo, **estará asociada a una única instancia de Azure Data Factory** y no podrá compartirse con otra factoría de datos.
* **Solo puede haber una instancia de Data Management Gateway** instalada en un equipo. Supongamos que tiene dos factorías de datos que necesitan acceder a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales; es decir, cada puerta de enlace estará asociada a una factoría de datos concreta.
* La **puerta de enlace no tiene que estar en la misma máquina que el origen de datos**. Sin embargo, si se coloca cerca del origen de datos, se reducirá el tiempo que necesita la puerta de enlace para conectarse a este. Le recomendamos que instale la puerta de enlace en una máquina diferente de la que hospeda el origen de datos local. Cuando la puerta de enlace y el origen de datos estén en máquinas distintas, no emplearán los mismos recursos.
* Puede tener **varias puertas de enlace en diferentes equipos conectados al mismo origen de datos local**. Por ejemplo, puede que tenga dos puertas de enlace que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos.
* Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI**, instale una **puerta de enlace independiente para Azure Data Factory** en otra máquina.
* Debe usar la puerta de enlace, incluso cuando utilice **ExpressRoute**.
* Considere el origen de datos como uno de tipo local (que está detrás de un firewall), aunque utilice **ExpressRoute**. Use la puerta de enlace para establecer la conectividad entre el servicio y el origen de datos.
* Debe **usar la puerta de enlace** incluso cuando el almacén de datos esté en la nube, en una **VM de IaaS de Azure**.

## <a name="installation"></a>Instalación
### <a name="prerequisites"></a>Requisitos previos
* Las versiones de **sistema operativo** compatibles son Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. En estos momentos, no se admite la instalación de Data Management Gateway en un controlador de dominio.
* Es necesario .NET Framework 4.5.1 o posterior. Si está instalando la puerta de enlace en una máquina con Windows 7, instale .NET Framework 4.5 o posterior. Consulte [Requisitos de sistema de .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) para más información.
* La **configuración** recomendada de la máquina de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
* Si la máquina host está en hibernación, la puerta de enlace no responde a las solicitudes de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. Si la máquina está configurada para hibernar, se mostrará un mensaje durante la instalación de la puerta de enlace.
* Debe ser administrador del equipo para instalar y configurar correctamente Data Management Gateway. Puede agregar más usuarios al grupo local de Windows **Usuarios de Data Management Gateway**. Los miembros de este grupo pueden usar la herramienta **Configuration Manager de Data Management Gateway** para configurar la puerta de enlace.

Puesto que las ejecuciones de la actividad de copia suceden con una frecuencia determinada, el uso de recursos (CPU, memoria) en la máquina también sigue el mismo patrón, con horas pico y tiempos de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando hay varios trabajos de copia en curso, puede ver que el uso de los recursos aumenta durante las horas pico.

### <a name="installation-options"></a>Opciones de instalación
Data Management Gateway se puede instalar de las siguientes maneras:

* Descargando un paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).  El paquete MSI también puede usarse para actualizar la versión existente de Data Management Gateway a la versión más reciente, conservando toda la configuración.
* Haciendo clic en el vínculo **Descargar e instalar la puerta de enlace de datos** en INSTALACIÓN MANUAL o en la opción **Instalar directamente en este equipo** en CONFIGURACIÓN RÁPIDA. Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo usar la configuración rápida. El paso manual le lleva al centro de descarga.  Las instrucciones para descargar e instalar la puerta de enlace desde el centro de descarga se encuentran en la sección siguiente.

### <a name="installation-best-practices"></a>Procedimientos recomendados de instalación:
1. Configurar el plan de energía en el equipo host para la puerta de enlace, de forma que el equipo no hiberne. Si la máquina host está en hibernación, la puerta de enlace no responde a las solicitudes de datos.
2. Se debe hacer una copia de seguridad del certificado asociado a la puerta de enlace.

### <a name="install-the-gateway-from-download-center"></a>Instalación de la puerta de enlace desde el Centro de descarga
1. Vaya a la [página de descarga de Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Haga clic en **Descargar**, seleccione la versión apropiada (**32 bits** o **64 bits**) y haga clic en **Siguiente**.
3. Ejecute el **MSI** directamente o guárdelo en el disco duro para ejecutarlo más adelante.
4. En la página **principal**, seleccione un **idioma** y haga clic en **Siguiente**.
5. **Acepte** el contrato de licencia para el usuario final y haga clic en **Siguiente**.
6. Seleccione la **carpeta** donde quiera instalar la puerta de enlace y haga clic en **Siguiente**.
7. En la página **Preparado para instalar...**, haga clic en **Instalar**.
8. Haga clic en **Finalizar** para completar la instalación.
9. Obtenga la clave en Azure Portal. Vaya a la siguiente sección para obtener instrucciones detalladas al respecto.
10. En la página **Registrar puerta de enlace** del **Administrador de configuración de Data Management Gateway** que se está ejecutando en la máquina, siga los siguientes pasos:
    1. Pegue la clave en el texto.
    2. Si lo desea, haga clic en **Mostrar la clave de la puerta de enlace** para ver el texto de la clave.
    3. Haga clic en **Registrar**.

### <a name="register-gateway-using-key"></a>Registro de la puerta de enlace mediante una clave
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Si aún no ha creado una puerta de enlace lógica en el Portal
Para crear una puerta de enlace en el portal y obtener la clave en la página **Configurar**, siga los pasos del tutorial del artículo [Movimiento de datos entre orígenes locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md).    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Si ya ha creado una puerta de enlace lógica en el Portal
1. En Azure Portal, vaya a la página **Data Factory** y haga clic en el icono **Servicios vinculados**.

    ![Página Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. En la página **Servicios vinculados**, seleccione la **puerta de enlace** lógica que ha creado en el portal.

    ![puerta de enlace lógica](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. En la página **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.

    ![Vínculo de descarga en el Portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. En la página **Configurar**, haga clic en **Volver a crear clave**. Haga clic en Sí en el mensaje de advertencia después de leerlo detenidamente.

    ![Volver a crear clave](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Haga clic en el botón Copiar, que se encuentra al lado de la clave. La clave se copia en el Portapapeles.

    ![Copiar clave](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Notificaciones/iconos de la bandeja del sistema
En la siguiente imagen, se muestran algunos de los iconos de la bandeja que verá.

![Iconos de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Si mueve el cursor sobre el mensaje de notificación o el icono en la bandeja del sistema, verá detalles sobre el estado de la puerta de enlace o la operación de actualización en una ventana emergente.

### <a name="ports-and-firewall"></a>Puertos y firewall
Existen dos firewalls que tiene que tener en cuenta: el **firewall corporativo**, que se ejecuta en el enrutador central de la organización, y **Firewall de Windows**, configurado como demonio en la máquina local en la que está instalada la puerta de enlace.  

![Firewalls](./media/data-factory-data-management-gateway/firewalls2.png)

En el firewall corporativo, debe configurar los siguientes dominios y puertos de salida:

| Nombres de dominio | Puertos | Descripción |
| --- | --- | --- |
| *.servicebus.windows.net |443, 80 |Usado para la comunicación con el back-end del servicio de movimiento de datos |
| *.core.windows.net |443 |Usado para la copia de almacenamiento provisional que usa el blob de Azure (si está configurado)|
| *.frontend.clouddatahub.net |443 |Usado para la comunicación con el back-end del servicio de movimiento de datos |


En el nivel de Firewall de Windows, normalmente se habilitan estos puertos de salida. De lo contrario, puede configurar los puertos y dominios según corresponda en el equipo de la puerta de enlace.

> [!NOTE]
> 1. En función del origen y los receptores, quizá deba agregar a la lista de permitidos del firewall corporativo o de Windows más dominios y puertos de salida.
> 2. Para algunas bases de datos en la nube (por ejemplo: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings), [Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access), etc.), puede que necesite agregar a la lista de permitidos de la configuración del firewall la dirección IP de la máquina de la puerta de enlace.
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Copia de datos de un almacén de datos de origen a uno de tipo receptor
Asegúrese de que las reglas del firewall estén habilitadas correctamente en el firewall corporativo, en el Firewall de Windows de la máquina de la puerta de enlace y en el propio almacén de datos. De este modo, la puerta de enlace podrá conectarse al origen y al receptor correctamente. Habilite las reglas de cada almacén de datos que participe en la operación de copia.

Por ejemplo, para copiar información desde **un almacén de datos local a un receptor de Azure SQL Database o uno de Azure SQL Data Warehouse**, debe realizar los siguientes pasos:

* Permita la comunicación **TCP** saliente en el puerto **1433** para el Firewall de Windows y el corporativo.
* Establezca la configuración de firewall del servidor SQL de Azure para agregar la dirección IP de la máquina de la puerta de enlace a la lista de IP permitidas.

> [!NOTE]
> Si el firewall no permite el puerto de salida 1433, la puerta de enlace no podrá tener acceso directamente a Azure SQL. En este caso, puede usar la [copia de almacenamiento provisional](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy) en SQL Azure Database y SQL Azure DW. En este escenario, requeriría solo HTTPS (puerto 443) para el movimiento de datos.
>
>


### <a name="proxy-server-considerations"></a>Consideraciones acerca del servidor proxy
Si en su entorno de red corporativo se usa un servidor proxy para acceder a Internet, configure Data Management Gateway para utilizar la configuración de proxy adecuada. Puede establecer el proxy durante la fase de registro inicial.

![Configuración del proxy durante el registro](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

La puerta de enlace utiliza el servidor proxy para conectarse al servicio en la nube. Haga clic en el vínculo **Cambiar** durante la configuración inicial. Verá el cuadro de diálogo de **configuración de proxy** .

![Configuración del proxy mediante el Administrador de configuración](media/data-factory-data-management-gateway/SetProxySettings.png)

Hay tres opciones de configuración:

* **No utilizar proxy**: la puerta de enlace no usa expresamente ningún proxy para conectarse a servicios en la nube.
* **Usar proxy del sistema**: la puerta de enlace utiliza la configuración de proxy de diahost.exe.config y diawp.exe.config.  Si no hay ningún proxy configurado en diahost.exe.config y diawp.exe.config, la puerta de enlace se conecta al servicio en la nube directamente sin pasar por el proxy.
* **Usar proxy personalizado**: establezca la configuración del proxy HTTP que se utilizará para la puerta de enlace, en lugar de usar las configuraciones de diahost.exe.config y diawp.exe.config.  La dirección y el puerto son obligatorios.  El nombre de usuario y la contraseña son opcionales según la configuración de autenticación del proxy.  Todas las configuraciones se cifran con el certificado de credencial de la puerta de enlace y se almacenan de forma local en el equipo host de la puerta de enlace.

El servicio de host de Data Management Gateway se reinicia automáticamente después de guardar la configuración de proxy actualizada.

Cuando la puerta de enlace se haya registrado correctamente, si quiere ver o actualizar la configuración de proxy, utilice el Administrador de configuración de Data Management Gateway.

1. Inicie el **Administrador de configuración de Data Management Gateway**.
2. Cambie a la pestaña **Configuración** .
3. Haga clic en el vínculo **Cambiar** de la sección **Proxy HTTP** para iniciar el cuadro de diálogo **Establecer proxy HTTP**.  
4. Tras hacer clic en el botón **Siguiente** , verá un cuadro de diálogo de advertencia que solicita permiso para guardar la configuración de proxy y reiniciar el servicio host Data Management Gateway.

Puede ver y actualizar el proxy HTTP mediante la herramienta Administrador de configuración.

![Configuración del proxy mediante el Administrador de configuración](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> Si configura un servidor proxy con la autenticación NTLM, el servicio host Data Management Gateway se ejecutará con la cuenta de dominio. Si más adelante cambia la contraseña de dicha cuenta de dominio, no olvide actualizar la configuración del servicio y reiniciarlo en consecuencia. Debido a este requisito, se recomienda usar una cuenta de dominio específica para acceder al servidor proxy, que no requiere actualizar la contraseña con frecuencia.
>
>

### <a name="configure-proxy-server-settings"></a>Configuración de un servidor proxy
Si selecciona la opción **Usar proxy del sistema** para el proxy HTTP, la puerta de enlace utilizará la configuración de proxy de diahost.exe.config y diawp.exe.config.  Si no hay ningún proxy especificado en diahost.exe.config y diawp.exe.config, la puerta de enlace se conecta al servicio en la nube directamente sin pasar por el proxy. En el procedimiento siguiente se proporcionan instrucciones para actualizar el archivo diahost.exe.config.  

1. En el Explorador de archivos, cree una copia segura de C:\Archivos de programa\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config para conservar una copia de seguridad del archivo original.
2. Inicie Notepad.exe como administrador y abra el archivo de texto C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config. Encontrará la etiqueta predeterminada para system.net como se muestra en el siguiente código:

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   Después, puede agregar los detalles del servidor proxy tal y como se muestran en el ejemplo siguiente:

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   Se permiten propiedades adicionales dentro de la etiqueta proxy para especificar la configuración requerida, como scriptLocation. Consulte [proxy (Elemento, Configuración de red)](https://msdn.microsoft.com/library/sa91de1e.aspx) para ver la sintaxis.

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. Guarde el archivo de configuración en la ubicación original; después, reinicie el servicio host Data Management Gateway para aplicar los cambios. Para hacerlo, use el applet Servicios del Panel de control o, en el **Administrador de configuración de Data Management Gateway**, haga clic en el botón **Detener servicio** y, luego, en **Iniciar servicio**. Si el servicio no se inicia, probablemente sea debido a que se ha agregado una sintaxis de etiqueta XML incorrecta en el archivo de configuración de aplicación que se ha modificado.

> [!IMPORTANT]
> No olvide actualizar **ambos**, diahost.exe.config y diawp.exe.config.  


Además de estos puntos anteriores, también tiene que asegurarse de que Microsoft Azure se encuentra en la lista blanca de su compañía. Se puede descargar una lista de direcciones IP válidas de Microsoft Azure en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Posibles síntomas de problemas relacionados con el firewall y el servidor proxy
Si se producen errores como los siguientes, es probable que se deban a una configuración incorrecta del servidor proxy o del firewall, que impide que la puerta de enlace se conecte a Data Factory para autenticarse. Consulte la sección anterior para garantizar que el firewall y el servidor proxy están configurados correctamente.

1. Al intentar registrar la puerta de enlace, recibirá el siguiente error: "Error al registrar la clave de la puerta de enlace. Antes de volver a intentar registrar la clave de la puerta de enlace, confirme que Data Management Gateway está en estado conectado y el servicio de host de Data Management Gateway se ha iniciado."
2. Al abrir el Administrador de configuración, verá el estado Desconectado o Conectando. Cuando se consultan los registros de eventos de Windows, en “Visor de eventos” > “Registros de aplicaciones y servicios” > “Data Management Gateway” aparecen mensajes de error como el siguiente: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Apertura del puerto 8050 para el cifrado de credenciales
En la aplicación **Setting Credentials** (Establecer credenciales) se usa el puerto de entrada **8050** para retransmitir las credenciales a la puerta de enlace al configurar un servicio vinculado local en Azure Portal. Durante la instalación de la puerta de enlace, la instalación lo abre de forma predeterminada en el equipo de la puerta de enlace.

Si usa un firewall de terceros, puede abrir manualmente el puerto 8050. Si se presenta un problema de firewall durante la instalación de la puerta de enlace, puede probar a usar el comando siguiente para instalarla sin configurar el firewall.

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si decide no abrir el puerto 8050 en la máquina de puerta de enlace, use otros mecanismos que no sean la aplicación de **configuración de credenciales** para configurar las credenciales del almacén de datos. Por ejemplo, puede usar el cmdlet de PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) . Consulte la sección [Configuración de credenciales y seguridad](#set-credentials-and-securityy) para más información sobre cómo configurar las credenciales del almacén de datos.

## <a name="update"></a>Actualizar
De forma predeterminada, Data Management Gateway se actualiza automáticamente cuando hay disponible una versión más reciente de la puerta de enlace. La puerta de enlace no se actualiza hasta que se completan todas las tareas programadas y deja de procesar tareas hasta que se complete la operación de actualización. Si se produce un error en la actualización, la puerta de enlace se revierte a la versión anterior.

El tiempo de actualización programado se consulta en los lugares siguientes:

* Página de propiedades de la puerta de enlace en Azure Portal.
* La página principal del Administrador de configuración de Data Management Gateway
* En un mensaje de notificación de la bandeja del sistema

La pestaña Inicio del Administrador de configuración de Data Management Gateway muestra la programación de actualización, así como la última vez que se instaló o actualizó la puerta de enlace.

![Programar actualizaciones](media/data-factory-data-management-gateway/UpdateSection.png)

Puede instalar la actualización inmediatamente o esperar a que la puerta de enlace se actualice automáticamente a la hora programada. Por ejemplo, en la siguiente imagen se muestra el mensaje de notificación que aparece en el Administrador de configuración de Data Management Gateway junto con el botón de actualización en el que se hace clic para instalarla inmediatamente.

![Actualización en el Administrador de configuración de Data Management Gateway](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

El mensaje de notificación en la bandeja del sistema tiene el aspecto que presenta la siguiente imagen:

![Mensaje de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Verá el estado de la operación de actualización (manual o automática) en la bandeja del sistema. La próxima vez que inicie el Administrador de configuración de Data Management Gateway verá un mensaje en la barra de notificación que indica que se ha actualizado la puerta de enlace, junto con un vínculo al [tema con las novedades](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Para habilitar o deshabilitar la característica de actualización automática
Se puede habilitar o deshabilitar la función de actualización automática realizando los siguientes pasos:

[Para puerta de enlace de nodo único]
1. Inicie Windows PowerShell en el equipo de la puerta de enlace.
2. Cambie a la carpeta C:\Archivos de programa\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Ejecute el siguiente comando para desactivar (deshabilitar) la característica de actualización automática.   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. Para volver a activarla:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
[[Para puerta de enlace escalable y altamente disponible de varios nodos (versión preliminar)](data-factory-data-management-gateway-high-availability-scalability.md)]
1. Inicie Windows PowerShell en el equipo de la puerta de enlace.
2. Cambie a la carpeta C:\Archivos de programa\Microsoft Data Management Gateway\2.0\PowerShellScript.
3. Ejecute el siguiente comando para desactivar (deshabilitar) la característica de actualización automática.   

    Se requiere un parámetro AuthKey adicional para la característica de puerta de enlace con alta disponibilidad (versión preliminar).
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. Para volver a activarla:

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 


## Configuration Manager
Once you install the gateway, you can launch Data Management Gateway Configuration Manager in one of the following ways:

1. In the **Search** window, type **Data Management Gateway** to access this utility.
2. Run the executable **ConfigManager.exe** in the folder: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### Home page
The Home page allows you to do the following actions:

* View status of the gateway (connected to the cloud service etc.).
* **Register** using a key from the portal.
* **Stop** and start the **Data Management Gateway Host service** on the gateway machine.
* **Schedule updates** at a specific time of the days.
* View the date when the gateway was **last updated**.

### Settings page
The Settings page allows you to do the following actions:

* View, change, and export **certificate** used by the gateway. This certificate is used to encrypt data source credentials.
* Change **HTTPS port** for the endpoint. The gateway opens a port for setting the data source credentials.
* **Status** of the endpoint
* View **SSL certificate** is used for SSL communication between portal and the gateway to set credentials for data sources.  

### Diagnostics page
The Diagnostics page allows you to do the following actions:

* Enable verbose **logging**, view logs in event viewer, and send logs to Microsoft if there was a failure.
* **Test connection** to a data source.  

### Help page
The Help page displays the following information:  

* Brief description of the gateway
* Version number
* Links to online help, privacy statement, and license agreement.  

## Monitor gateway in the portal
In the Azure portal, you can view near-real time snapshot of resource utilization (CPU, memory, network(in/out), etc.) on a gateway machine.  

1. In Azure portal, navigate to the home page for your data factory, and click **Linked services** tile. 

    ![Data factory home page](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Select the **gateway** in the **Linked services** page.

    ![Linked services page](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. In the **Gateway** page, you can see the memory and CPU usage of the gateway.

    ![CPU and memory usage of gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Enable **Advanced settings** to see more details such as network usage.
    
    ![Advanced monitoring of gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

The following table provides descriptions of columns in the **Gateway Nodes** list:  

Monitoring Property | Description
:------------------ | :---------- 
Name | Name of the logical gateway and nodes associated with the gateway. Node is an on-premises Windows machine that has the gateway installed on it. For information on having more than one node (up to four nodes) in a single logical gateway, see [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Status of the logical gateway and the gateway nodes. Example: Online/Offline/Limited/etc. For information about these statuses, See [Gateway status](#gateway-status) section. 
Version | Shows the version of the logical gateway and each gateway node. The version of the logical gateway is determined based on version of majority of nodes in the group. If there are nodes with different versions in the logical gateway setup, only the nodes with the same version number as the logical gateway function properly. Others are in the limited mode and need to be manually updated (only in case auto-update fails). 
Available memory | Available memory on a gateway node. This value is a near real-time snapshot. 
CPU utilization | CPU utilization of a gateway node. This value is a near real-time snapshot. 
Networking (In/Out) | Network utilization of a gateway node. This value is a near real-time snapshot. 
Concurrent Jobs (Running/ Limit) | Number of jobs or tasks running on each node. This value is a near real-time snapshot. Limit signifies the maximum concurrent jobs for each node. This value is defined based on the machine size. You can increase the limit to scale up concurrent job execution in advanced scenarios, where CPU/memory/network is under-utilized, but activities are timing out. This capability is also available with a single-node gateway (even when the scalability and availability feature is not enabled).  
Role | There are two types of roles in a multi-node gateway – Dispatcher and worker. All nodes are workers, which means they can all be used to execute jobs. There is only one dispatcher node, which is used to pull tasks/jobs from cloud services and dispatch them to different worker nodes (including itself).

In this page, you see some settings that make more sense when there are two or more nodes (scale out scenario) in the gateway. See [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md) for details about setting up a multi-node gateway.

### Gateway status
The following table provides possible statuses of a **gateway node**: 

Status  | Comments/Scenarios
:------- | :------------------
Online | Node connected to Data Factory service.
Offline | Node is offline.
Upgrading | The node is being auto-updated.
Limited | Due to Connectivity issue. May be due to HTTP port 8050 issue, service bus connectivity issue, or credential sync issue. 
Inactive | Node is in a configuration different from the configuration of other majority nodes.<br/><br/> A node can be inactive when it cannot connect to other nodes. 


The following table provides possible statuses of a **logical gateway**. The gateway status depends on statuses of the gateway nodes. 

Status | Comments
:----- | :-------
Needs Registration | No node is yet registered to this logical gateway
Online | Gateway Nodes are online
Offline | No node in online status.
Limited | Not all nodes in this gateway are in healthy state. This status is a warning that some node might be down! <br/><br/>Could be due to credential sync issue on dispatcher/worker node. 

## Scale up gateway
You can configure the number of **concurrent data movement jobs** that can run on a node to scale up the capability of moving data between on-premises and cloud data stores. 

When the available memory and CPU are not utilized well, but the idle capacity is 0, you should scale up by increasing the number of concurrent jobs that can run on a node. You may also want to scale up when activities are timing out because the gateway is overloaded. In the advanced settings of a gateway node, you can increase the maximum capacity for a node. 
  

## Troubleshooting gateway issues
See [Troubleshooting gateway issues](data-factory-troubleshoot-gateway-issues.md) article for information/tips for troubleshooting issues with using the data management gateway.  

## Move gateway from one machine to another
This section provides steps for moving gateway client from one machine to another machine.

1. In the portal, navigate to the **Data Factory home page**, and click the **Linked Services** tile.

    ![Data Gateways Link](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Select your gateway in the **DATA GATEWAYS** section of the **Linked Services** page.

    ![Linked Services page with gateway selected](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. In the **Data gateway** page, click **Download and install data gateway**.

    ![Download gateway link](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. In the **Configure** page, click **Download and install data gateway**, and follow instructions to install the data gateway on the machine.

    ![Configure page](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Keep the **Microsoft Data Management Gateway Configuration Manager** open.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. In the **Configure** page in the portal, click **Recreate key** on the command bar, and click **Yes** for the warning message. Click **copy button** next to key text that copies the key to the clipboard. The gateway on the old machine stops functioning as soon you recreate the key.  

    ![Recreate key](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Paste the **key** into text box in the **Register Gateway** page of the **Data Management Gateway Configuration Manager** on your machine. (optional) Click **Show gateway key** check box to see the key text.

    ![Copy key and Register](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Click **Register** to register the gateway with the cloud service.
9. On the **Settings** tab, click **Change** to select the same certificate that was used with the old gateway, enter the **password**, and click **Finish**.

   ![Specify Certificate](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   You can export a certificate from the old gateway by doing the following steps: launch Data Management Gateway Configuration Manager on the old machine, switch to the **Certificate** tab, click **Export** button and follow the instructions.
10. After successful registration of the gateway, you should see the **Registration** set to **Registered** and **Status** set to **Started** on the Home page of the Gateway Configuration Manager.

## Encrypting credentials
To encrypt credentials in the Data Factory Editor, do the following steps:

1. Launch web browser on the **gateway machine**, navigate to [Azure portal](http://portal.azure.com). Search for your data factory if needed, open data factory in the **DATA FACTORY** page and then click **Author & Deploy** to launch Data Factory Editor.   
2. Click an existing **linked service** in the tree view to see its JSON definition or create a linked service that requires a data management gateway (for example: SQL Server or Oracle).
3. In the JSON editor, for the **gatewayName** property, enter the name of the gateway.
4. Enter server name for the **Data Source** property in the **connectionString**.
5. Enter database name for the **Initial Catalog** property in the **connectionString**.    
6. Click **Encrypt** button on the command bar that launches the click-once **Credential Manager** application. You should see the **Setting Credentials** dialog box.

    ![Setting credentials dialog](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. In the **Setting Credentials** dialog box, do the following steps:
   1. Select **authentication** that you want the Data Factory service to use to connect to the database.
   2. Enter name of the user who has access to the database for the **USERNAME** setting.
   3. Enter password for the user for the **PASSWORD** setting.  
   4. Click **OK** to encrypt credentials and close the dialog box.
8. You should see a **encryptedCredential** property in the **connectionString** now.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
Si tiene acceso al portal desde un equipo diferente del equipo de la puerta de enlace, debe asegurarse de que la aplicación del Administrador de credenciales puede conectarse al equipo de la puerta de enlace. Si la aplicación no puede ponerse en contacto con la máquina de la puerta de enlace, no podrá establecer las credenciales del origen de datos ni probar la conexión a él.  

Cuando se usa la aplicación **Setting Credentials** (Establecer credenciales), el portal las cifra con el certificado especificado en la pestaña **Certificado** del **Administrador de configuración de Data Management Gateway** de la máquina de la puerta de enlace.

Si quiere adoptar un enfoque basado en API para cifrar las credenciales, puede usar el cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de PowerShell para cifrar las credenciales. El cmdlet usa el certificado cuyo uso tiene configurado esa puerta de enlace para cifrar las credenciales. Se agregan credenciales cifradas al elemento **EncryptedCredential** de **connectionString** en el JSON, y se usa el JSON con el cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) o en Data Factory Editor.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Hay otro enfoque para establecer las credenciales mediante el Editor de la Factoría de datos. Si crea un servicio vinculado de SQL Server mediante el editor y escribe las credenciales en texto sin formato, se cifran mediante un certificado que posee el servicio Data Factory, NO usa el certificado que la puerta de enlace está configurada para usar. Aunque este enfoque puede resultar un poco más rápido, en algunos casos es menos seguro. Por lo tanto, se recomienda seguir este enfoque solo para fines de desarrollo y pruebas.

## <a name="powershell-cmdlets"></a>Cmdlets de PowerShell
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell.

1. Inicie **Azure PowerShell** en modo de administrador.
2. Inicie sesión en su cuenta de Azure ejecutando el siguiente comando y escribiendo sus credenciales de Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Use el cmdlet **New-AzureRmDataFactoryGateway** para crear una puerta de enlace lógica como se indica a continuación:

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Ejemplo de comando y salida**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. En Azure PowerShell, cambie a la carpeta **C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\**. Ejecute el script **RegisterGateway.ps1** asociado a la variable local **$Key** tal y como se muestra en el siguiente comando. Este script registra al agente cliente instalado en la máquina con la puerta de enlace lógica que creó antes.

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Puede registrar la puerta de enlace en un equipo remoto mediante el parámetro IsRegisterOnRemoteMachine. Ejemplo:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Use el cmdlet **Get-AzureRmDataFactoryGateway** para obtener la lista de puertas de enlace de su factoría de datos. Cuando el **Estado** es **En línea**, significa que la puerta de enlace está lista para usarla.

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
Puede quitar una puerta de enlace con el cmdlet **Remove-AzureRmDataFactoryGateway** y actualizar la descripción de una puerta de enlace con los cmdlets **Set-AzureRmDataFactoryGateway**. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.  

### <a name="list-gateways-using-powershell"></a>Enumeración de puertas de enlace con PowerShell

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Eliminación de puerta de enlace con PowerShell

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>Pasos siguientes
* Para más información, consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) . El objetivo es crear una canalización que utilice la puerta de enlace para mover los datos de una base de datos de SQL Server local a un blob de Azure.  

