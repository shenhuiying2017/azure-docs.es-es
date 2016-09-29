<properties 
	pageTitle="Data Management Gateway para Data Factory | Microsoft Azure"
	description="Configuración de una puerta de enlace para mover datos entre una infraestructura local y la nube. Uso de Data Management Gateway en Data Factory de Azure para mover los datos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="spelluru"/>

# Data Management Gateway
Data Management Gateway es un agente cliente que debe instalar en el entorno local para permitir que se copien datos entre almacenes de datos en la nube y locales. Los almacenes de datos locales compatibles con Data Factory se muestran en la sección de [orígenes de datos compatibles](data-factory-data-movement-activities.md##supported-data-stores).

Este artículo complementa al tutorial de [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md). El objetivo es crear una canalización que utilice la puerta de enlace para mover los datos de una base de datos de SQL Server local a un blob de Azure. En este artículo se proporciona información detallada sobre el uso de Data Management Gateway.

## Información general

### Funciones de Data Management Gateway
Data Management Gateway proporciona las siguientes funciones:

- Modelo de orígenes de datos local y orígenes de datos en la nube en la misma factoría de datos y movimiento de datos.
- Tiene un solo panel de vidrio para la supervisión y administración con visibilidad del estado de la puerta de enlace desde la hoja de Data Factory.
- Administrar el acceso a orígenes de datos locales de forma segura.
	- No es necesario ningún cambio en el firewall corporativo. La puerta de enlace solo hace conexiones salientes basadas en HTTP para abrir Internet.
	- Cifrar las credenciales de los almacenes de datos locales con su certificado.
- Mover datos de manera eficaz: los datos se transfieren en paralelo, con resistencia a problemas de red intermitentes mediante una lógica automática de reintentos.

### Flujo de comandos y flujo de datos
Cuando se utiliza una actividad de copia con la que copiar datos entre un entorno local y la nube, se usa una puerta de enlace para transferir información desde el origen de datos local hasta la nube, y viceversa.

A continuación se muestra el flujo de datos de alto nivel y el resumen de los pasos para copiar con una puerta de enlace de datos: ![Flujo de datos mediante la puerta de enlace](./media/data-factory-data-management-gateway/data-flow-using-gateway.png).

1.	El desarrollador de datos crea una puerta de enlace para una instancia de Data Factory de Azure mediante el [Portal de Azure](https://portal.azure.com) o un [cmdlet de PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2.	El desarrollador de datos crea un servicio vinculado para un almacén de datos local especificando la puerta de enlace. Como parte de la configuración del servicio vinculado, el desarrollador de datos usa la aplicación Setting Credentials (Establecer credenciales) para especificar las credenciales y los tipos de autenticación. El cuadro de diálogo de la aplicación Setting Credentials (Establecer credenciales) se comunicará con el almacén de datos para probar la conexión y la puerta de enlace para guardar las credenciales.
3. Gateway cifra las credenciales con el certificado asociado a la puerta de enlace (suministrado por el desarrollador de datos) antes de guardar las credenciales en la nube.
4. El servicio de Data Factory se comunica con la puerta de enlace para la programación y administración de trabajos a través de un canal de control que usa una cola de bus de servicio de Azure compartida. Cuando es necesario iniciar un trabajo de actividad de copia, Data Factory pone en cola la solicitud junto con la información de credenciales. La puerta de enlace inicia el trabajo después de sondear la cola.
5.	La puerta de enlace descifra las credenciales con el mismo certificado y, después, se conecta al almacén de datos local con las credenciales y el tipo de autenticación adecuados.
6.	La puerta de enlace copia datos de un almacén local a un almacenamiento en nube, o viceversa, en función de cómo esté configurada la actividad de copia en la canalización de datos. En este paso, la puerta de enlace se comunica directamente con servicios de almacenamiento basados en la nube, como Almacenamiento de blobs de Azure, a través de un canal seguro (HTTPS).

### Consideraciones a la hora de usar la puerta de enlace
- Se puede utilizar una sola instancia de Data Management Gateway para varios orígenes de datos locales. Sin embargo, **estará asociada a una única instancia de Data Factory de Azure** y no podrá compartirse con otra factoría de datos.
- **Solo puede haber una instancia de Data Management Gateway** instalada en un solo equipo. Supongamos que tiene dos factorías de datos que necesitan acceder a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales; es decir, cada puerta de enlace estará asociada a una factoría de datos concreta.
- La **puerta de enlace no tiene que estar en la misma máquina que el origen de datos**. Sin embargo, si se coloca cerca del origen de datos, se reducirá el tiempo que necesita la puerta de enlace para conectarse a este. Le recomendamos que instale la puerta de enlace en una máquina diferente de la que hospeda el origen de datos local. Cuando la puerta de enlace y el origen de datos estén en máquinas distintas, no emplearán los mismos recursos.
- Puede tener **varias puertas de enlace en diferentes equipos conectados al mismo origen de datos local**. Por ejemplo, puede que tenga dos puertas de enlace que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos.
- Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI**, instale una **puerta de enlace independiente para Data Factory de Azure** en otra máquina.
- Debe usar la puerta de enlace, incluso cuando utilice **ExpressRoute**.
- Considere el origen de datos como uno de tipo local (que está detrás de un firewall), aunque utilice **ExpressRoute**. Use la puerta de enlace para establecer la conectividad entre el servicio y el origen de datos.
- Debe **usar la puerta de enlace** incluso cuando el almacén de datos esté en la nube, en una **VM de IaaS de Azure**.

## Instalación

### Requisitos previos
- Las versiones de **sistema operativo** compatibles son Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. En estos momentos, no se admite la instalación de Data Management Gateway en un controlador de dominio.
- Es necesario .NET Framework 4.5.1 o posterior. Si está instalando la puerta de enlace en una máquina con Windows 7, instale .NET Framework 4.5 o posterior. Consulte [Requisitos de sistema de .NET Framework](https://msdn.microsoft.com/library/8z6watww.aspx) para más información.
- La **configuración** recomendada de la máquina de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
- Si la máquina host está en hibernación, la puerta de enlace no responde a las solicitudes de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. Si la máquina está configurada para hibernar, se mostrará un mensaje durante la instalación de la puerta de enlace.
- Debe ser administrador del equipo para instalar y configurar correctamente Data Management Gateway. Puede agregar más usuarios al grupo local de Windows **Usuarios de Data Management Gateway**. Los miembros de este grupo pueden usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.

Puesto que las ejecuciones de la actividad de copia suceden con una frecuencia determinada, el uso de recursos (CPU, memoria) en la máquina también sigue el mismo patrón, con horas pico y tiempos de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando hay varios trabajos de copia en curso, puede ver que el uso de los recursos aumenta durante las horas pico.

### Opciones de instalación
Data Management Gateway se puede instalar de las siguientes maneras:

- Descargando un paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). El MSI también puede usarse para actualizar el Data Management Gateway existente a la versión más reciente, con toda la configuración conservada.
- Haciendo clic en el vínculo **Descargar e instalar la puerta de enlace de datos** en INSTALACIÓN MANUAL o en la opción **Instalar directamente en este equipo** en CONFIGURACIÓN RÁPIDA. Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo usar la configuración rápida. El paso manual le lleva al centro de descarga. Las instrucciones para descargar e instalar la puerta de enlace desde el centro de descarga se encuentran en la sección siguiente.

### Procedimientos recomendados de instalación:
1.	Configurar el plan de energía en el equipo host para la puerta de enlace, de forma que el equipo no hiberne. Si la máquina host está en hibernación, la puerta de enlace no responde a las solicitudes de datos.
2.	Se debe hacer una copia de seguridad del certificado asociado a la puerta de enlace.

### Instalación de la puerta de enlace desde el Centro de descarga
1. Vaya a la [página de descarga de Microsoft Data Management Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Haga clic en **Descargar**, seleccione la versión apropiada (**32 bits** o **64 bits**) y haga clic en **Siguiente**.
3. Ejecute el **MSI** directamente o guárdelo en el disco duro para ejecutarlo más adelante.
4. En la página **principal**, seleccione un **idioma** y haga clic en **Siguiente**.
5. **Acepte** el contrato de licencia para el usuario final y haga clic en **Siguiente**.
6. Seleccione la **carpeta** donde quiera instalar la puerta de enlace y haga clic en **Siguiente**.
7. En la página **Preparado para instalar...**, haga clic en **Instalar**.
8. Haga clic en **Finalizar** para completar la instalación.
9. Obtenga la clave del Portal de Azure. Vaya a la siguiente sección para obtener instrucciones detalladas al respecto.
10. En la página **Registrar puerta de enlace** del **Administrador de configuración de Data Management Gateway** que se está ejecutando en la máquina, siga estos pasos:
	1. Pegue la clave en el texto.
	2. Si lo desea, haga clic en **Mostrar la clave de la puerta de enlace** para ver el texto de la clave.
	3. Haga clic en **Registrar**.

### Registro de la puerta de enlace mediante una clave

#### Si aún no ha creado una puerta de enlace lógica en el Portal
Para crear una puerta de enlace en el portal y obtener la clave en la hoja **Configurar**, siga los pasos del tutorial del artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md).

#### Si ya ha creado una puerta de enlace lógica en el Portal
1. En el Portal de Azure, vaya a la hoja **Data Factory** y haga clic en el icono **Servicios vinculados**.

	![Hoja de la Factoría de datos](media/data-factory-data-management-gateway/data-factory-blade.png)
2. En la hoja **Servicios vinculados**, seleccione la **puerta de enlace** lógica que ha creado en el portal.

	![puerta de enlace lógica](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. En la hoja **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.

	![Vínculo de descarga en el Portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. En la hoja **Configurar**, haga clic en **Volver a crear clave**. Haga clic en Sí en el mensaje de advertencia después de leerlo detenidamente.

	![Volver a crear clave](media/data-factory-data-management-gateway/recreate-key-button.png)
4. Haga clic en el botón Copiar, que se encuentra al lado de la clave. La clave se copia en el Portapapeles.
	
	![Copiar clave](media/data-factory-data-management-gateway/copy-gateway-key.png)

### Notificaciones/iconos de la bandeja del sistema
En la siguiente imagen, se muestran algunos de los iconos de la bandeja que verá.

![Iconos de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Si mueve el cursor sobre el mensaje de notificación o el icono en la bandeja del sistema, verá detalles sobre el estado de la puerta de enlace o la operación de actualización en una ventana emergente.

### Puertos y firewall
Existen dos firewalls que tiene que tener en cuenta: el **firewall corporativo**, que se ejecuta en el enrutador central de la organización, y **Firewall de Windows**, configurado como demonio en la máquina local en la que está instalada la puerta de enlace.

![Firewalls](./media/data-factory-data-management-gateway/firewalls.png)

En el firewall corporativo, debe configurar los siguientes dominios y puertos de salida:

| Nombres de dominio | Puertos | Descripción |
| ------ | --------- | ------------ |
| *.servicebus.windows.net | 443, 80 | Agentes de escucha de retransmisión de Bus de servicio sobre TCP (requiere 443 para la adquisición del token de Control de acceso) | 
| *.servicebus.windows.net | 9350-9354, 5671 | Retransmisión de bus de servicio opcional sobre TCP | 
| *.core.windows.net | 443 | HTTPS | 
| *. clouddatahub.net | 443 | HTTPS | 
| graph.windows.net | 443 | HTTPS |
| login.windows.net | 443 | HTTPS | 

En el nivel de Firewall de Windows, normalmente se habilitan estos puertos de salida. De lo contrario, puede configurar los puertos y dominios según corresponda en el equipo de la puerta de enlace.

#### Copia de datos de un almacén de datos de origen a uno de tipo receptor

Asegúrese de que las reglas del firewall estén habilitadas correctamente en el firewall corporativo, en el Firewall de Windows de la máquina de la puerta de enlace y en el propio almacén de datos. De este modo, la puerta de enlace podrá conectarse al origen y al receptor correctamente. Habilite las reglas de cada almacén de datos que participe en la operación de copia.

Por ejemplo, para copiar información desde **un almacén de datos local a un receptor de Base de datos SQL de Azure o uno de Almacenamiento de datos SQL de Azure**, debe hacer lo siguiente:

- Permita la comunicación **TCP** saliente en el puerto **1433** para el Firewall de Windows y el corporativo.
- Establezca la configuración de firewall del servidor SQL de Azure para agregar la dirección IP de la máquina de la puerta de enlace a la lista de IP permitidas.


### Consideraciones acerca del servidor proxy
Si en su entorno de red corporativo se usa un servidor proxy para acceder a Internet, configure Data Management Gateway para utilizar la configuración de proxy adecuada. Puede establecer el proxy durante la fase de registro inicial.

![Configuración del proxy durante el registro](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

La puerta de enlace utiliza el servidor proxy para conectarse al servicio en la nube. Haga clic en el vínculo **Cambiar** durante la configuración inicial. Verá el cuadro de diálogo de **configuración de proxy**.

![Configuración del proxy mediante el Administrador de configuración](media/data-factory-data-management-gateway/SetProxySettings.png)

Hay tres opciones de configuración:

- **No utilizar proxy**: la puerta de enlace no usa expresamente ningún proxy para conectarse a servicios en la nube.
- **Usar proxy del sistema**: la puerta de enlace utiliza la configuración de proxy que esté definida en diahost.exe.config. Si no hay ningún proxy configurado en diahost.exe.config, se conectará al servicio en la nube directamente sin pasar por el proxy.
- **Usar proxy personalizado**: establezca la configuración del proxy HTTP que se utilizará para la puerta de enlace, en lugar de usar las configuraciones de diahost.exe.config. La dirección y el puerto son obligatorios. El nombre de usuario y la contraseña son opcionales según la configuración de autenticación del proxy. Todas las configuraciones se cifran con el certificado de credencial de la puerta de enlace y se almacenan de forma local en el equipo host de la puerta de enlace.

El servicio host Data Management Gateway se reiniciará automáticamente después de guardar la configuración de proxy actualizada.

Cuando la puerta de enlace se haya registrado correctamente, si quiere ver o actualizar la configuración de proxy, utilice el Administrador de configuración de Data Management Gateway.

1. Inicie el Administrador de configuración de Data Management Gateway.
2. Cambie a la pestaña **Configuración**.
3. Haga clic en el vínculo **Cambiar** de la sección **Proxy HTTP** para iniciar el cuadro de diálogo **Establecer proxy HTTP**.
4. Tras hacer clic en el botón **Siguiente**, verá un cuadro de diálogo de advertencia que solicita permiso para guardar la configuración de proxy y reiniciar el servicio host Data Management Gateway.

Puede ver y actualizar el proxy HTTP mediante la herramienta Administrador de configuración.

![Configuración del proxy mediante el Administrador de configuración](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [AZURE.NOTE] Si configura un servidor proxy con la autenticación NTLM, el servicio host Data Management Gateway se ejecutará con la cuenta de dominio. Si más adelante cambia la contraseña de dicha cuenta de dominio, no olvide actualizar la configuración del servicio y reiniciarlo en consecuencia. Debido a este requisito, se recomienda usar una cuenta de dominio específica para acceder al servidor proxy, que no requiere actualizar la contraseña con frecuencia.

### Configuración del servidor proxy en diahost.exe.config
Si selecciona la opción de configuración **Usar proxy del sistema** para el proxy HTTP, la puerta de enlace utilizará la configuración de proxy de diahost.exe.config. Si no se especifica ningún proxy en diahost.exe.config, se conectará al servicio en la nube directamente sin pasar por el proxy. En el procedimiento siguiente se proporcionan instrucciones para actualizar el archivo de configuración.

1.	En el Explorador de archivos, cree una copia segura de C:\\Archivos de programa\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config para conservar una copia de seguridad del archivo original.
2.	Inicie Notepad.exe como administrador y abra el archivo de texto C:\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config. Encontrará la etiqueta predeterminada para system.net de la forma siguiente:

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

Además de estos puntos anteriores, también tiene que asegurarse de que Microsoft Azure se encuentra en la lista blanca de su compañía. Se puede descargar una lista de direcciones IP válidas de Microsoft Azure en el [ Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### Posibles síntomas de problemas relacionados con el firewall y el servidor proxy
Si se producen errores como los siguientes, es probable que se deban a una configuración incorrecta del servidor proxy o del firewall, que impide que la puerta de enlace se conecte a Data Factory para autenticarse. Consulte la sección anterior para garantizar que el firewall y el servidor proxy están configurados correctamente.

1.	Al intentar registrar la puerta de enlace, recibirá el siguiente error: "Error al registrar la clave de la puerta de enlace. Antes de volver a intentar registrar la clave de la puerta de enlace, confirme que Data Management Gateway está en estado conectado y el servicio host de Data Management Gateway se ha iniciado."
2.	Al abrir el Administrador de configuración, verá el estado Desconectado o Conectando. Cuando se consultan los registros de eventos de Windows, en Visor de eventos > Registros de aplicaciones y servicios > Data Management Gateway aparecen mensajes de error como el siguiente: `Unable to connect to the remote server` `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`.

### Apertura del puerto 8050 para el cifrado de credenciales 
En la aplicación **Setting Credentials** (Establecer credenciales) se usa el puerto de entrada **8050** para retransmitir las credenciales a la puerta de enlace al configurar un servicio vinculado local en el Portal de Azure. Durante la instalación de la puerta de enlace, la instalación de Data Management Gateway lo abre de forma predeterminada en el equipo de la puerta de enlace.
 
Si usa un firewall de terceros, puede abrir manualmente el puerto 8050. Si se presenta un problema de firewall durante la instalación de la puerta de enlace, puede probar a usar el comando siguiente para instalarla sin configurar el firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si decide no abrir el puerto 8050 en la máquina de puerta de enlace, use otros mecanismos que no sean la aplicación de **configuración de credenciales** para configurar las credenciales del almacén de datos. Por ejemplo, puede usar el cmdlet de PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Consulte la sección [Configuración de credenciales y seguridad](#set-credentials-and-securityy) para más información sobre cómo configurar las credenciales del almacén de datos.

## Actualizar 
De forma predeterminada, Data Management Gateway se actualiza automáticamente cuando hay disponible una versión más reciente de la puerta de enlace. La puerta de enlace no se actualiza hasta que se completan todas las tareas programadas y deja de procesar tareas hasta que se complete la operación de actualización. Si se produce un error en la actualización, la puerta de enlace se revierte a la versión anterior.

El tiempo de actualización programado se consulta en los lugares siguientes:

- La hoja de propiedades de la puerta de enlace del Portal de Azure
- La página principal del Administrador de configuración de Data Management Gateway
- En un mensaje de notificación de la bandeja del sistema

La pestaña Inicio del Administrador de configuración de Data Management Gateway muestra la programación de actualización, así como la última vez que se instaló o actualizó la puerta de enlace.

![Programar actualizaciones](media/data-factory-data-management-gateway/UpdateSection.png)

Puede instalar la actualización inmediatamente o esperar a que la puerta de enlace se actualice automáticamente a la hora programada. Por ejemplo, en la siguiente imagen se muestra el mensaje de notificación que aparece en el Administrador de configuración de Data Management Gateway junto con el botón de actualización en el que se hace clic para instalarla inmediatamente.

![Actualización en el Administrador de configuración de Data Management Gateway](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

El mensaje de notificación en la bandeja del sistema tiene el siguiente aspecto:

![Mensaje de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Verá el estado de la operación de actualización (manual o automática) en la bandeja del sistema. La próxima vez que inicie el Administrador de configuración de Data Management Gateway verá un mensaje en la barra de notificación que indica que se ha actualizado la puerta de enlace, junto con un vínculo al [tema con las novedades](data-factory-gateway-release-notes.md).

### Para habilitar o deshabilitar la característica de actualización automática
Se puede habilitar o deshabilitar la característica de actualización automática de la forma siguiente:

1. Inicie Windows PowerShell en el equipo de la puerta de enlace.
2. Cambie a la carpeta C:\\Archivos de programa\\Microsoft Data Management Gateway\\2.0\\PowerShellScript.
3. Ejecute el siguiente comando para desactivar (deshabilitar) la característica de actualización automática.

		.\GatewayAutoUpdateToggle.ps1  -off

4. Para volver a activarla:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Administrador de configuración 
Una vez instalada la puerta de enlace, puede iniciar el Administrador de configuración de Data Management Gateway de una de las siguientes maneras:

- En la ventana **Búsqueda**, escriba **Data Management Gateway** para tener acceso a esta utilidad.
- Ejecute el archivo ejecutable **ConfigManager.exe** en la carpeta **C:\\Archivos de programa\\Microsoft Data Management Gateway\\2.0\\Shared**.
 
### Página de inicio
En la página principal puede hacer lo siguiente:

- Ver el estado de la puerta de enlace (conectada al servicio en la nube, etc.).
- **Registrar** con una clave desde el Portal.
- **Detener** e iniciar el **servicio host de Data Management Gateway** en el equipo de puerta de enlace.
- **Programar actualizaciones** en determinados momentos del día.
- Ver la fecha en que la puerta de enlace se **actualizó por última vez**.

### Página Configuración
En la página Configuración puede hacer lo siguiente:

- Ver, modificar y exportar el **certificado** usado por la puerta de enlace. Este certificado se utiliza para cifrar las credenciales de orígenes de datos.
- Cambiar el **puerto HTTPS** del punto de conexión. La puerta de enlace abre un puerto para establecer las credenciales del origen de datos.
- Ver el **estado** del punto de conexión
- El **certificado SSL** de visualización se utiliza para establecer la comunicación SSL entre el portal y la puerta de enlace con el fin de establecer credenciales para los orígenes de datos.

### Página Diagnóstico
En la página Diagnóstico puede hacer lo siguiente:

- Habilitar un **registro** detallado, ver los registros en el visor de eventos y enviar registros a Microsoft en caso de error.
- **Probar la conexión** a un origen de datos.

### Página de ayuda
En la página Ayuda se muestra la siguiente información:

- Breve descripción de la puerta de enlace
- Número de la versión
- Vínculos a la ayuda en línea, la declaración de privacidad y el contrato de licencia.

## Solución de problemas

- Puede obtener más información en los registros de la puerta de enlace de los registros de eventos de Windows. Puede encontrarlos utilizando el **Visor de eventos** de Windows, que se encuentra en **Registros de aplicaciones y servicios** > **Data Management Gateway**. Al solucionar problemas relacionados con la puerta de enlace, busque eventos de error en el Visor de eventos.
- Si la puerta de enlace deja de funcionar después de **haber cambiado el certificado**, reinicie el **Servicio Data Management Gateway** con la herramienta Administrador de configuración de Microsoft Data Management Gateway o el applet Servicios del Panel de control. Si todavía ve un error, puede que tenga que dar permisos explícitos para el usuario del servicio de Data Management Gateway para que tenga acceso al certificado en el Administrador de certificados (certmgr.msc). La cuenta de usuario predeterminada para el servicio es **NT Service\\DIAHostService**.
- Si la aplicación **Administrador de credenciales** **no cifra** las credenciales al hacer clic en el botón Cifrar del Editor de Data Factory, compruebe que está ejecutando esta aplicación en la **máquina de la puerta de enlace**. Si no es así, ejecute la aplicación en la máquina de la puerta de enlace e intente cifrar las credenciales.
- Si ve errores relacionados con los controladores o la conexión al almacén de datos, siga estos pasos:
	- Inicie el **Administrador de configuración de Data Management Gateway** en la máquina de la puerta de enlace.
	- Cambie a la pestaña **Diagnósticos**.
	- Seleccione o escriba los valores adecuados para los campos del grupo **Probar la conexión a un origen de datos local con esta puerta de enlace**.
	- Haga clic en **Probar conexión** para ver si puede conectarse al origen de datos local desde la máquina de la puerta de enlace utilizando las credenciales y la información de conexión. Si la conexión de prueba sigue sin funcionar después de instalar un controlador, reinicie la puerta de enlace para recoger los cambios más recientes.

	![Probar conexión](./media/data-factory-data-management-gateway/TestConnection.png)

### Enviar registros de puerta de enlace a Microsoft
Cuando se ponga en contacto con el equipo de soporte técnico de Microsoft para solucionar problemas de la puerta de enlace, es posible que se le pida que comparta los registros de la puerta de enlace. La versión de la puerta de enlace permite compartir fácilmente los registros de puerta de enlace necesarios con dos clics de botón en el Administrador de configuración de Data Management Gateway.

1. Cambie a la pestaña **Diagnóstico** del Administrador de configuración de Data Management Gateway.
 
	![Data Management Gateway: pestaña Diagnóstico](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. Haga clic en el vínculo **Enviar registros** para ver el siguiente cuadro de diálogo.

	![Data Management Gateway: Enviar registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (opcional) Haga clic en **Ver registros** para revisar los registros en el Visor de eventos.
4. (opcional) Haga clic en **Privacidad** para revisar la declaración de privacidad de Microsoft Online Services.
3. Una vez que esté satisfecho con lo que va a cargar, haga clic en **Enviar registros** para enviar realmente los registros de los últimos siete días a Microsoft para solucionar problemas. Debería ver el estado de la operación de envío de registros tal y como aparece en la siguiente imagen.

	![Data Management Gateway: Estado del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. Cuando finalice la operación, verá un cuadro de diálogo como el que se muestra en la siguiente imagen.
	
	![Data Management Gateway: Estado del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. Anote el **identificador de informe** y envíelo al equipo de soporte técnico de Microsoft. El identificador de informe sirve para encontrar los registros de la puerta de enlace que ha cargado para solucionar problemas. Este identificador del informe también se guarda en el Visor de eventos para tenerlo como referencia. Lo encontrará si examina el identificador de evento “25” y comprueba la fecha y la hora.
	
	![Data Management Gateway: Identificador de informe del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### Archivar registros de puerta de enlace en el equipo host de puerta de enlace
Hay veces en las que existen problemas con la puerta de enlace y no es posible compartir los registros de puerta de enlace de forma directa:

- Cuando se instala y registra manualmente la puerta de enlace
- Cuando se trata de registrar la puerta de enlace con una clave regenerada en el Administrador de configuración
- Cuando se trata de enviar registros, pero el servicio host Data Management Gateway no se puede conectar

En tales casos, puede guardar los registros de puerta de enlace como un archivo ZIP y compartirlo más adelante, cuando pueda ponerse en contacto con el equipo de soporte técnico de Microsoft. Por ejemplo, si recibe el siguiente error al registrar la puerta de enlace tal y como se muestra en la siguiente imagen:

![Data Management Gateway: Error de registro](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

Haga clic en el vínculo **Archivar registros de puerta de enlace** para archivar y guardar los registros y, luego, comparta el archivo ZIP con el equipo de soporte técnico de Microsoft.

![Data Management Gateway: Archivar registros](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

### La puerta de enlace está en línea con funcionalidad limitada 
Verá el estado de la puerta de enlace como **en línea con funcionalidad limitada** por una de las siguientes razones.

- La puerta de enlace no se puede conectar al servicio en la nube mediante el bus de servicio.
- El servicio en la nube no se puede conectar a la puerta de enlace mediante el bus de servicio.

Cuando la puerta de enlace está en línea con funcionalidad limitada, no podrá usar el Asistente para copia de Data Factory para crear canalizaciones de datos entre almacenes de datos locales.

La solución de este problema (en línea con funcionalidad limitada) se basa en si la puerta de enlace no puede conectarse al servicio en la nube o viceversa. En las secciones siguientes se proporcionan estas soluciones.

#### La puerta de enlace no se puede conectar al servicio en la nube mediante el bus de servicio.
Siga estos pasos para poner en línea la puerta de enlace:

1. Habilite los puertos de salida 9350-9354 en el firewall de Windows en la máquina de puerta de enlace y en el firewall corporativo. Consulte la sección [Puertos y firewall](#ports-and-firewall) para más información.
2. Configure los valores de proxy en la puerta de enlace. Consulte la sección [Consideraciones sobre el servidor proxy](#proxy-server-considerations) para más información.

Como alternativa, use Data Factory Editor en Azure Portal, Visual Studio o Azure PowerShell.

#### Error: el servicio en la nube no se puede conectar a la puerta de enlace mediante el bus de servicio.
Siga estos pasos para poner en línea la puerta de enlace:
 
1. Habilite los puertos de salida 5671 y 9350-9354 en el firewall de Windows de la máquina de puerta de enlace y en el firewall corporativo. Consulte la sección [Puertos y firewall](#ports-and-firewall) para más información.
2. Configure los valores de proxy en la puerta de enlace. Consulte la sección [Consideraciones sobre el servidor proxy](#proxy-server-considerations) para más información.
3. Quite la limitación de IP estática en el servidor proxy.

Como alternativa, puede usar Data Factory Editor en Azure Portal, Visual Studio o Azure PowerShell.
 
## Movimiento de la puerta de enlace de un equipo a otro
En esta sección se proporcionan pasos para mover el cliente de puerta de enlace de un equipo a otro equipo.

2. En el portal, vaya a la **página principal de Factoría de datos** y haga clic en el icono **Servicios vinculados**.

	![Vínculo de puertas de enlace de datos](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. Seleccione la puerta de enlace en la sección **PUERTAS DE ENLACE DE DATOS** de la hoja **Servicios vinculados**.
	
	![Hoja Servicios vinculados con puerta de enlace seleccionada](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. En la hoja **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.
	
	![Vínculo de puerta de enlace de descarga](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. En la hoja **Configurar**, haga clic en **Descargar e instalar la puerta de enlace de datos** y siga las instrucciones para instalarla en la máquina.

	![Hoja Configurar](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. Mantenga el **Administrador de configuración de Microsoft Data Management Gateway** abierto.
 
	![Administrador de configuración](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. En la hoja **Configurar** del portal, haga clic en **Volver a crear clave** en la barra de comandos y haga clic en **Sí** en el mensaje de advertencia. Haga clic en el **botón Copiar** que verá junto al texto de la clave para copiarla en el Portapapeles. La puerta de enlace en la máquina antigua deja de funcionar en cuanto se vuelve a crear la clave.
	
	![Volver a crear clave](./media/data-factory-data-management-gateway/RecreateKey.png)
	 
8. Pegue la **clave** en el cuadro de texto en la página **Registrar puerta de enlace** del **Administrador de configuración de Data Management Gateway** en la máquina. (Opcional) Haga clic en la casilla **Mostrar clave de puerta de enlace** para ver el texto de la clave.
 
	![Copiar clave y registrar](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
9. Haga clic en **Registrar** para registrar la puerta de enlace con el servicio en la nube.
10. En la pestaña **Configuración**, haga clic en **Cambiar** para seleccionar el mismo certificado que se ha usado con la puerta de enlace anterior, escriba la **contraseña** y haga clic en **Finalizar**.
 
	![Especificar certificado](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

	Puede exportar un certificado desde la puerta de enlace anterior haciendo lo siguiente: inicie el Administrador de configuración de Data Management Gateway en la máquina antigua, pase a la pestaña **Certificado**, haga clic en el botón **Exportar** y siga las instrucciones.
10. Después del registro correcto de la puerta de enlace, debe ver **Registro** establecido en **Registrado** y **Estado** establecido en **Iniciado** en la página principal del administrador de configuración de puertas de enlace.

## Cifrado de credenciales 
Para cifrar las credenciales en el Editor de Data Factory, siga estos pasos:

1. Inicie el explorador web en la **máquina de la puerta de enlace** y vaya al [Portal de Azure](http://portal.azure.com). Busque la factoría de datos si es necesario, ábrala en la hoja **DATA FACTORY** y haga clic en **Crear e implementar** para iniciar el Editor de Data Factory.
1. Haga clic en un **servicio vinculado** existente en la vista de árbol para ver su definición de JSON o cree uno que requiera Data Management Gateway (por ejemplo, SQL Server u Oracle).
2. En el editor de JSON, escriba el nombre de la puerta de enlace para la propiedad **gatewayName**.
3. Escriba el nombre del servidor para la propiedad **Data Source** en **connectionString**.
4. Escriba el nombre de la base de datos para la propiedad **Initial Catalog** en **connectionString**.
5. Haga clic en el botón **Cifrar** de la barra de comandos para iniciar la aplicación de un solo clic **Administrador de credenciales**. Se abrirá el cuadro de diálogo **Setting Credentials** (Establecer credenciales). ![Cuadro de diálogo Establecer credenciales](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
6. En el cuadro de diálogo **Establecer credenciales**, realice lo siguiente:
	1.	Seleccione la **autenticación** que desea que use el servicio Factoría de datos para conectarse a la base de datos.
	2.	Escriba el nombre del usuario que tiene acceso a la base de datos para el valor **NOMBRE DE USUARIO**.
	3.	Escriba la contraseña para el usuario para el valor **CONTRASEÑA**.
	4.	Haga clic en **Aceptar** para cifrar las credenciales y cerrar el cuadro de diálogo.
5.	Ahora verá una propiedad **encryptedCredential** en **connectionString**.
		
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

Si tiene acceso al portal desde un equipo diferente del equipo de la puerta de enlace, debe asegurarse de que la aplicación del Administrador de credenciales puede conectarse al equipo de la puerta de enlace. Si la aplicación no puede ponerse en contacto con la máquina de la puerta de enlace, no podrá establecer las credenciales del origen de datos ni probar la conexión a él.

Cuando se usa la aplicación **Setting Credentials** (Establecer credenciales), el portal las cifra con el certificado especificado en la pestaña **Certificado** del **Administrador de configuración de Data Management Gateway** de la máquina de la puerta de enlace.

Si quiere adoptar un enfoque basado en API para cifrar las credenciales, puede usar el cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de PowerShell para cifrar las credenciales. El cmdlet usa el certificado cuyo uso tiene configurado esa puerta de enlace para cifrar las credenciales. Se agregan credenciales cifradas al elemento **EncryptedCredential** de **connectionString** en el JSON, y se usa el JSON con el cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) o en Data Factory Editor.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Hay otro enfoque para establecer las credenciales mediante el Editor de la Factoría de datos. Si crea un servicio vinculado de SQL Server mediante el editor y escribe las credenciales en texto sin formato, se cifran mediante un certificado que posee el servicio Data Factory, NO usa el certificado que la puerta de enlace está configurada para usar. Aunque este enfoque puede resultar un poco más rápido, en algunos casos es menos seguro. Por lo tanto, se recomienda seguir este enfoque solo para fines de desarrollo y pruebas.


## Cmdlets de PowerShell 
En esta sección se explica cómo crear y registrar una puerta de enlace usando cmdlets de Azure PowerShell.

1. Inicie **Azure PowerShell** en modo de administrador.
2. Inicie sesión en su cuenta de Azure ejecutando el siguiente comando y escribiendo sus credenciales de Azure.

	Login-AzureRmAccount
2. Use el cmdlet **New-AzureRmDataFactoryGateway** para crear una puerta de enlace lógica como se indica a continuación:

		$MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Ejemplo de comando y salida**:


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

	
4. En Azure PowerShell, cambie a la carpeta **C:\\Archivos de programa\\Microsoft Data Management Gateway\\2.0\\PowerShellScript**. Ejecute el script **RegisterGateway.ps1** asociado a la variable local **$Key** tal y como se muestra en el siguiente comando. Este script registra al agente cliente instalado en la máquina con la puerta de enlace lógica que creó antes.

		PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
		
		Agent registration is successful!

	Puede registrar la puerta de enlace en un equipo remoto mediante el parámetro IsRegisterOnRemoteMachine. Ejemplo:
		
		.\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true

5. Use el cmdlet **Get-AzureRmDataFactoryGateway** para obtener la lista de puertas de enlace de su factoría de datos. Cuando el **Estado** es **En línea**, significa que la puerta de enlace está lista para usarla.

		Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

Puede quitar una puerta de enlace con el cmdlet **Remove-AzureRmDataFactoryGateway** y actualizar la descripción de una puerta de enlace con los cmdlets **Set-AzureRmDataFactoryGateway**. Para ver la sintaxis y otros detalles de estos cmdlets, consulte la documentación de referencia de los cmdlets de Factoría de datos.

### Enumeración de puertas de enlace con PowerShell

	Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup

### Eliminación de puerta de enlace con PowerShell
	
	Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force 


## Pasos siguientes
- Consulte el artículo sobre [Data Management Gateway](data-factory-data-management-gateway.md) para más información sobre la puerta de enlace.

<!---HONumber=AcomDC_0914_2016-->