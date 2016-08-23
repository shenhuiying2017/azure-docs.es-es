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
	ms.date="08/05/2016" 
	ms.author="spelluru"/>

# Data Management Gateway
Data Management Gateway es un agente cliente que debe estar instalado en el entorno local para permitir el movimiento de datos entre almacenes de datos en la nube y locales [admitidos por la actividad de copia](data-factory-data-movement-activities.md##supported-data-stores).

Este artículo complementa el contenido del artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md), que tiene un tutorial para crear una canalización de Data Factory que usa la puerta de enlace para mover datos desde una base de datos de SQL Server local a un blob de Azure. En este artículo se proporciona información detallada sobre el uso de Data Management Gateway.

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
Al usar una actividad de copia en una canalización de datos para introducir datos locales en la nube para su posterior procesamiento, o bien exportar los datos de resultados en la nube de nuevo a un almacén de datos local, la actividad de copia usa internamente una puerta de enlace para transferir los datos de origen de un origen de datos local a la nube y viceversa.

A continuación se muestra el flujo de datos de alto nivel y el resumen de los pasos para copiar con una puerta de enlace de datos: ![Flujo de datos mediante la puerta de enlace](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1.	El desarrollador de datos crea una nueva puerta de enlace para una Factoría de datos de Azure mediante el [Portal de Azure](https://portal.azure.com) o un [cmdlet de PowerShell](https://msdn.microsoft.com/library/dn820234.aspx).
2.	El desarrollador de datos crea un servicio vinculado para un almacén de datos local especificando la puerta de enlace. Como parte de la configuración del servicio vinculado, el desarrollador de datos usa la aplicación Setting Credentials (Establecer credenciales) para especificar las credenciales y los tipos de autenticación. El cuadro de diálogo de la aplicación Setting Credentials (Establecer credenciales) se comunicará con el almacén de datos para probar la conexión y la puerta de enlace para guardar las credenciales.
3. Gateway cifra las credenciales con el certificado asociado a la puerta de enlace (suministrado por el desarrollador de datos) antes de guardar las credenciales en la nube.
4. El servicio de Data Factory se comunica con la puerta de enlace para la programación y administración de trabajos a través de un canal de control que usa una cola de bus de servicio de Azure compartida. Cuando es necesario iniciar un trabajo de actividad de copia, Data Factory pone en cola la solicitud junto con la información de credenciales. La puerta de enlace inicia el trabajo después de sondear la cola.
5.	La puerta de enlace descifra las credenciales con el mismo certificado y, después, se conecta al almacén de datos local con las credenciales y el tipo de autenticación adecuados.
6.	La puerta de enlace copia datos desde el almacén local a un almacenamiento en la nube o desde un almacenamiento en la nube a un almacén de datos local según cómo esté configurada la actividad de copia en la canalización de datos. Nota: Para este paso, la puerta de enlace se comunica directamente con el servicio de almacenamiento basado en la nube (Blob de Azure, SQL de Azure, etc.) a través del canal seguro (HTTPS).

### Consideraciones a la hora de usar la puerta de enlace
- Una sola instancia de Data Management Gateway se puede usar para varios orígenes de datos locales, pero tenga en cuenta que **una sola puerta de enlace está asociada a una sola Factoría de datos de Azure** y no puede compartirse con otra factoría de datos.
- **Solo puede haber una instancia de Data Management Gateway** instalada en un solo equipo. Supongamos que tiene dos factorías de datos que necesitan tener acceso a orígenes de datos locales. Debe instalar puertas de enlace en dos equipos locales donde cada puerta de enlace esté asociada a una factoría de datos diferente.
- La **puerta de enlace no necesita estar en el mismo equipo que el origen de datos** pero, si está cerca del origen de datos, reduce el tiempo de conexión de la puerta de enlace al origen de datos. Le recomendamos que instale la puerta de enlace en un equipo diferente del que hospeda el origen de datos local para que la puerta de enlace no compita por los recursos con el origen de datos.
- Puede tener **varias puertas de enlace en diferentes equipos conectados al mismo origen de datos local**. Por ejemplo, puede que tenga dos puertas de enlace que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos.
- Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI** , instale una **puerta de enlace independiente para Factoría de datos de Azure** en otro equipo.
- Debe **usar la puerta de enlace, incluso cuando use ExpressRoute**.
- Debe tratar el origen de datos como un origen de datos local (que está detrás de un firewall) aunque use **ExpressRoute** y **usar la puerta de enlace** para establecer la conectividad entre el servicio y el origen de datos.
- Debe **usar la puerta de enlace** incluso cuando el almacén de datos esté en la nube, en una **VM de IaaS de Azure**.

## Instalación

### Requisitos previos
- Las versiones de **sistema operativo** compatibles son Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. En estos momentos, no se admite la instalación de Data Management Gateway en un controlador de dominio.
- La **configuración** recomendada del equipo de la puerta de enlace es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
- Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos. Por tanto, configure un **plan de energía** adecuado en el equipo antes de instalar la puerta de enlace. La instalación de la puerta de enlace emite un mensaje si el equipo está configurado para la hibernación.
- Debe ser administrador del equipo para instalar y configurar correctamente Data Management Gateway. Puede agregar más usuarios al grupo local de Windows **Usuarios de Data Management Gateway**. Los miembros de este grupo podrán usar la herramienta Administrador de configuración de Data Management Gateway para configurar la puerta de enlace.

Puesto que las ejecuciones de la actividad de copia suceden con una frecuencia determinada, el uso de recursos (CPU, memoria) en el equipo también sigue el mismo patrón con las horas pico y los tiempos de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando hay varios trabajos de copia en curso, observará que el uso de los recursos aumenta durante las horas pico. Aunque arriba se muestra la configuración mínima, siempre es mejor tener una configuración con más recursos que la configuración mínima descrita, en función de la carga específica para el movimiento de datos.

### Opciones de instalación
Data Management Gateway se puede instalar de las siguientes maneras:

- Descargando un paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). El MSI también puede usarse para actualizar el Data Management Gateway existente a la versión más reciente, con toda la configuración conservada.
- Haciendo clic en el vínculo **Descargar e instalar la puerta de enlace de datos** en INSTALACIÓN MANUAL o en la opción **Instalar directamente en este equipo** en CONFIGURACIÓN RÁPIDA. Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo usar la configuración rápida. El paso manual le lleva al Centro de descarga. En la siguiente sección encontrará instrucciones para descargar e instalar Gateway desde el Centro de descarga.

### Procedimientos recomendados de instalación:
1.	Configurar el plan de energía en el equipo host para la puerta de enlace, de forma que el equipo no hiberne. Si el equipo host está en hibernación, la puerta de enlace no podrá responder a las solicitudes de datos.
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
10. En la página **Registrar puerta de enlace** del **Administrador de configuración de Data Management Gateway**, pegue la clave en el cuadro de texto (si quiere, haga clic en la casilla **Mostrar clave de puerta de enlace** para ver el texto de la clave) y, luego, haga clic en **Registrar**.

### Registro de la puerta de enlace mediante una clave

#### Si aún no ha creado una puerta de enlace lógica en el Portal
Siga los pasos del tutorial del artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para crear una puerta de enlace en el portal y obtener la clave desde la hoja **Configurar**.

#### Si ya ha creado una puerta de enlace lógica en el Portal
1. En el Portal de Azure, vaya a la hoja **Data Factory** y haga clic en el icono **Servicios vinculados**.

	![Hoja de la Factoría de datos](media/data-factory-data-management-gateway/data-factory-blade.png)
2. En la hoja **Servicios vinculados**, seleccione la **puerta de enlace** lógica que ha creado en el portal.

	![puerta de enlace lógica](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
2. En la hoja **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.

	![Vínculo de descarga en el Portal](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
3. En la hoja **Configurar**, haga clic en **Volver a crear clave**. Haga clic en Sí en el mensaje de advertencia después de leerlo detenidamente.

	![Volver a crear clave](media/data-factory-data-management-gateway/recreate-key-button.png)
4. Haga clic en el botón Copiar junto a la clave para copiarla en el Portapapeles.
	
	![Copiar clave](media/data-factory-data-management-gateway/copy-gateway-key.png)

### Notificaciones/iconos de la bandeja del sistema
En la siguiente imagen, se muestran algunos de los iconos de la bandeja que verá.

![Iconos de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Si mueve el cursor sobre el mensaje de notificación o el icono en la bandeja del sistema, verá detalles sobre el estado de la puerta de enlace o de la operación de actualización en una ventana emergente.

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

#### Para copiar datos de un almacén de datos de origen a un almacén de datos receptor

Debe asegurarse de que las reglas de firewall se habilitan correctamente en el firewall corporativo, en el Firewall de Windows en la máquina de puerta de enlace y en el mismo almacén de datos. Esto permite que la puerta de enlace se conecte tanto al origen como al receptor correctamente. Debe habilitar las reglas de cada almacén de datos que está implicado en la operación de copia.

Por ejemplo, para copiar de **un almacén de datos local a un receptor de Base de datos SQL de Azure o un receptor de Almacenamiento de datos SQL de Azure**, tiene que permitir la comunicación **TCP** de salida en el puerto **1433** tanto para Firewall de Windows como para el firewall corporativo, y necesita establecer la configuración del firewall del servidor SQL de Azure para agregar la dirección IP de la máquina de la puerta de enlace a la lista de direcciones IP permitidas.


### Configuración del servidor proxy
De forma predeterminada, Data Management Gateway aprovechará la configuración de proxy de Internet Explorer y usará credenciales predeterminadas para tener acceso a él. Si no se adapta a sus necesidades, puede establecer la **configuración del servidor proxy** tal como se muestra a continuación para asegurarse de que la puerta de enlace se pueda conectar a Data Factory de Azure:

1.	Después de instalar Data Management Gateway, en Explorador de archivos, haga una copia de “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config” para hacer una copia de seguridad del archivo original.
2.	Inicie Notepad.exe como administrador y abra el archivo de texto “C:\\Program Files\\Microsoft Data Management Gateway\\1.0\\Shared\\diahost.exe.config”. Encontrará la etiqueta predeterminada para system.net de la forma siguiente:

			<system.net>
				<defaultProxy useDefaultCredentials="true" />
			</system.net>	

	Después, puede agregar los detalles del servidor proxy, por ejemplo, la dirección de proxy dentro de esa etiqueta primaria, por ejemplo:

			<system.net>
			      <defaultProxy enabled="true">
			            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
			      </defaultProxy>
			</system.net>

	Se permiten propiedades adicionales dentro de la etiqueta proxy para especificar la configuración requerida, como scriptLocation. Consulte [proxy (Elemento, Configuración de red)](https://msdn.microsoft.com/library/sa91de1e.aspx) para ver la sintaxis.

			<proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>

3. Guarde el archivo de configuración en la ubicación original; a continuación, reinicie el servicio Data Management Gateway para aplicar los cambios. Para hacerlo, vaya a **Inicio** > **Services.msc** o, en el **Administrador de configuración de Data Management Gateway**, haga clic en el botón **Detener servicio** y, luego, en **Iniciar servicio**. Si el servicio no se inicia, probablemente sea debido a que se ha agregado una sintaxis de etiqueta XML incorrecta en el archivo de configuración de aplicación que se ha modificado.

Además de los puntos anteriores, también tiene que asegurarse de que Microsoft Azure se encuentra en la lista blanca de su empresa. Se puede descargar una lista de direcciones IP válidas de Microsoft Azure en el [ Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### Posibles síntomas de problemas relacionados con el firewall y el servidor proxy
Si se producen errores como los siguientes, es probable que sea debido a una configuración incorrecta del servidor proxy o del firewall, que impide que Data Management Gateway se conecte a Factoría de datos de Azure para autenticarse a sí mismo. Consulte la sección anterior para garantizar que el firewall y el servidor proxy están configurados correctamente.

1.	Al intentar registrar la puerta de enlace, recibirá el siguiente error: "Error al registrar la clave de la puerta de enlace. Antes de volver a intentar registrar la clave de la puerta de enlace, confirme que Data Management Gateway está en estado conectado y el servicio host de Data Management Gateway se ha iniciado."
2.	Al abrir el Administrador de configuración, verá el estado como "Desconectado" o "Conectando". Al ver los registros de eventos de Windows, bajo "Visor de eventos" > "Registros de aplicaciones y servicios" > "Data Management Gateway" aparecen mensajes de error como " No es posible conectar con el servidor remoto " o "Un componente de Data Management Gateway ha dejado de responder y se reiniciará automáticamente. Nombre del componente: puerta de enlace."

### Apertura del puerto 8050 para el cifrado de credenciales 
En la aplicación **Setting Credentials** (Establecer credenciales) se usará el puerto de entrada **8050** para retransmitir las credenciales a la puerta de enlace cuando configure un servicio vinculado local en el Portal de Azure (detalles más adelante en este artículo). Durante la instalación de la puerta de enlace, la instalación de Data Management Gateway lo abre de forma predeterminada en el equipo de la puerta de enlace.
 
Si se usa un firewall de terceros, puede abrir manualmente el puerto 8050. Si se presenta un problema de firewall durante la instalación de la puerta de enlace, puede intentar usar el comando siguiente para instalarla sin configurar el firewall.

	msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

Si decide no abrir el puerto 8050 en la máquina de la puerta de enlace, para configurar un servicio vinculado local tendrá que usar otros mecanismos distintos a la aplicación **Setting Credentials** (Establecer credenciales) para configurar las credenciales del almacén de datos. Por ejemplo, puede usar el cmdlet de PowerShell [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx). Consulte la sección [Configuración de credenciales y seguridad](#set-credentials-and-securityy) para más información sobre cómo configurar las credenciales del almacén de datos.

## Actualizar 
De forma predeterminada, Data Management Gateway se actualiza automáticamente cuando hay disponible una versión más reciente de la puerta de enlace. La puerta de enlace no se actualiza hasta que se completan todas las tareas programadas y deja de procesar tareas hasta que se complete la operación de actualización. Si se produce un error en la actualización, la puerta de enlace se revierte a la versión anterior.

Verá la hora de actualización programada en el portal en la hoja de propiedades de la puerta de enlace, en la página principal del Administrador de configuración de Data Management Gateway y en el mensaje de notificación de la bandeja del sistema.

La pestaña Inicio del Administrador de configuración de Data Management Gateway muestra la programación de actualización, así como la última vez que se instaló o actualizó la puerta de enlace.

![Programación de actualizaciones](media/data-factory-data-management-gateway/UpdateSection.png)

Tiene la opción de instalar la actualización inmediatamente o de esperar a que la puerta de enlace se actualice automáticamente a la hora programada. Por ejemplo, la captura de pantalla siguiente muestra el mensaje de notificación que aparece en el Administrador de configuración de Data Management Gateway junto con el botón de actualización en el que se hace clic para instalarla inmediatamente.

![Actualización en el Administrador de configuración de Data Management Gateway](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

El mensaje de notificación en la bandeja del sistema tiene el siguiente aspecto:

![Mensaje de la bandeja del sistema](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Verá el estado de la operación de actualización (manual o automática) en la bandeja del sistema. La próxima vez que abra el Administrador de configuración de Data Management Gateway verá un mensaje en la barra de notificación que indica que se ha actualizado la puerta de enlace, junto con un vínculo al [tema con las novedades](data-factory-gateway-release-notes.md).

### Para habilitar o deshabilitar la característica de actualización automática
Se puede habilitar o deshabilitar la característica de actualización automática de la forma siguiente:

1. Inicie Windows PowerShell en el equipo de la puerta de enlace.
2. Cambie a la carpeta C:\\Archivos de programa\\Microsoft Data Management Gateway\\1.0\\PowerShellScript.
3. Ejecute el siguiente comando para desactivar (deshabilitar) la característica de actualización automática.

		.\GatewayAutoUpdateToggle.ps1  -off

4. Para volver a activarla:
	
		.\GatewayAutoUpdateToggle.ps1  -on  

## Administrador de configuración 
Una vez instalada la puerta de enlace, puede iniciar el Administrador de configuración de Data Management Gateway de una de las siguientes maneras:

- En la ventana **Búsqueda**, escriba **Data Management Gateway** para tener acceso a esta utilidad.
- Ejecute el archivo ejecutable **ConfigManager.exe** en la carpeta **C:\\Archivos de programa\\Microsoft Data Management Gateway\\1.0\\Shared**.
 
### Página de inicio
En la página principal puede hacer lo siguiente:

- Ver el estado de la puerta de enlace; si la puerta de enlace está o no conectada al servicio en la nube.
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

- Habilitar un **registro** detallado, ver los registros en el Visor de eventos y enviar registros a Microsoft en caso de error.
- **Probar la conexión** a un origen de datos.

### Página de ayuda
En la página Ayuda se muestra la siguiente información:

- Breve descripción de la puerta de enlace
- Número de la versión
- Vínculos a la ayuda en línea, la declaración de privacidad y el contrato de licencia.

## Solución de problemas

- Puede obtener más información en los registros de la puerta de enlace de los registros de eventos de Windows. Puede encontrarlos mediante el **Visor de eventos** de Windows en **Registros de aplicaciones y servicios** > **Data Management Gateway**. Cuando soluciones problemas relacionados con la puerta de enlace consulte los eventos de error en el Visor de eventos.
- Si la puerta de enlace deja de funcionar después de **haber cambiado el certificado**, reinicie (detenga e inicie) el **Servicio Data Management Gateway** con la herramienta Administrador de configuración de Microsoft Data Management Gateway o el applet del panel de control Servicios. Si todavía ve un error, puede que tenga que dar permisos explícitos para el usuario del servicio de Data Management Gateway para que tenga acceso al certificado en el Administrador de certificados (certmgr.msc). La cuenta de usuario predeterminada para el servicio es **NT Service\\DIAHostService**.
- Si la aplicación **Administrador de credenciales** no **cifra** las credenciales al hacer clic en el botón Cifrar del Editor de Data Factory, compruebe que está ejecutando esta aplicación en la máquina en la que está instalada la puerta de enlace. Si no es así, ejecute la aplicación en la máquina de la puerta de enlace e intente cifrar las credenciales.
- Si ve errores relacionados con el controlador o la conexión del almacén de datos, inicie el **Administrador de configuración de Data Management Gateway** en la máquina de la puerta de enlace, cambie a la pestaña **Diagnósticos**, seleccione o especifique los valores adecuados en los campos del grupo **Probar la conexión a un origen de datos local con esta puerta de enlace** y haga clic en **Prueba de conexión** para ver si se puede conectar al origen de datos local desde la máquina de la puerta de enlace con la información de conexión y las credenciales. Si la conexión de prueba sigue sin funcionar después de instalar un controlador, reinicie la puerta de enlace para recoger los cambios más recientes.

	![Probar conexión](./media/data-factory-data-management-gateway/TestConnection.png)

### Enviar registros de puerta de enlace a Microsoft
En caso de que tenga problemas con la puerta de enlace y necesite ponerse en contacto con el equipo de soporte técnico de Microsoft, se le pedirá que comparta los registros de puerta de enlace. La versión de la puerta de enlace permite compartir fácilmente los registros de puerta de enlace necesarios con dos clics de botón en el Administrador de configuración de Data Management Gateway.

1. Cambie a la pestaña **Diagnóstico** del Administrador de configuración de Data Management Gateway.
 
	![Data Management Gateway: Pestaña Diagnóstico](media/data-factory-data-management-gateway/data-management-gateway-diagnostics-tab.png)
2. Haga clic en el vínculo **Enviar registros** para ver el siguiente cuadro de diálogo.

	![Data Management Gateway: Enviar registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-dialog.png)
3. (opcional) Haga clic en **Ver registros** para revisar los registros en el Visor de eventos.
4. (opcional) Haga clic en **Privacidad** para revisar la declaración de privacidad de Microsoft Online Services.
3. Una vez que esté satisfecho con lo que va a cargar, haga clic en **Enviar registros** para enviar de facto los registros de los últimos 7 días a Microsoft para solucionar problemas. El estado de la operación de envío de registros debe tener la siguiente apariencia.

	![Data Management Gateway: Estado del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-status.png)
4. Una vez completada la operación, verá un cuadro de diálogo como este.
	
	![Data Management Gateway: Estado del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-result.png)
5. Anote el **identificador de informe** y envíelo al equipo de soporte técnico de Microsoft. El identificador de informe sirve para encontrar los registros de la puerta de enlace que ha cargado para solucionar problemas. Este identificador del informe también se guarda en el Visor de eventos para tenerlo como referencia. Lo encontrará si examina el identificador de evento “25” y comprueba la fecha y la hora.
	
	![Data Management Gateway: Identificador de informe del envío de registros](media/data-factory-data-management-gateway/data-management-gateway-send-logs-report-id.png)

### Archivar registros de puerta de enlace en el equipo host de puerta de enlace
Hay veces en las que existen problemas con la puerta de enlace y no es posible compartir los registros de puerta de enlace de forma directa:

- Cuando los usuarios instalan y registran manualmente la puerta de enlace.
- Cuando los usuarios intentan registrar la puerta de enlace con una clave regenerada en Administrador de configuración.
- Cuando los usuarios intentan enviar registros pero el servicio de host de puerta de enlace no se puede conectar.

En tales casos, puede guardar los registros de puerta de enlace como un archivo ZIP y compartirlo más adelante, cuando pueda ponerse en contacto con el equipo de soporte técnico de Microsoft. Por ejemplo, si recibe el siguiente error al registrar la puerta de enlace:

![Data Management Gateway: Error de registro](media/data-factory-data-management-gateway/data-management-gateway-registration-error.png)

Haga clic en el vínculo **Archivar registros de puerta de enlace** para archivar y guardar los registros y, luego, comparta el archivo ZIP con el equipo de soporte técnico de Microsoft.

![Data Management Gateway: Archivar registros](media/data-factory-data-management-gateway/data-management-gateway-archive-logs.png)

 
## Movimiento de la puerta de enlace de un equipo a otro
En esta sección se proporcionan pasos para mover el cliente de puerta de enlace de un equipo a otro equipo.

2. En el portal, vaya a la **página principal de Factoría de datos** y haga clic en el icono **Servicios vinculados**.

	![Vínculo de puertas de enlace de datos](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
3. Seleccione la puerta de enlace en la sección **PUERTAS DE ENLACE DE DATOS** de la hoja **Servicios vinculados**.
	
	![Hoja Servicios vinculados con puerta de enlace seleccionada](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
4. En la hoja **Puerta de enlace de datos**, haga clic en **Descargar e instalar la puerta de enlace de datos**.
	
	![Vínculo de puerta de enlace de descarga](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
5. En la hoja **Configurar**, haga clic en **Descargar e instalar la puerta de enlace de datos** y siga las instrucciones para instalar la puerta de enlace de datos en la máquina.

	![Hoja Configurar](./media/data-factory-data-management-gateway/ConfigureBlade.png)
6. Mantenga el **Administrador de configuración de Microsoft Data Management Gateway** abierto.
 
	![Administrador de configuración](./media/data-factory-data-management-gateway/ConfigurationManager.png)
7. En la hoja **Configurar** del portal, haga clic en **Volver a crear clave** en la barra de comandos y haga clic en **Sí** para el mensaje de advertencia. Haga clic en el **botón Copiar** junto al texto de la clave para copiar la clave en el portapapeles. Tenga en cuenta que la puerta de enlace en la máquina antigua dejará de funcionar tan pronto como vuelva a crear la clave.
	
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

1. Inicie el explorador web en la **máquina de puerta de enlace**, vaya a [Portal de Azure](http://portal.azure.com), busque su factoría de datos si es necesario, ábrala en la hoja **DATA FACTORY** y, luego, haga clic en **Crear e implementar** para iniciar el Editor de Data Factory.
1. Haga clic en un **servicio vinculado** existente en la vista de árbol para ver su definición de JSON o cree uno que requiera Data Management Gateway (por ejemplo, SQL Server u Oracle).
2. En el editor de JSON, escriba el nombre de la puerta de enlace para la propiedad **gatewayName**.
3. Escriba el nombre del servidor para la propiedad **Data Source** en **connectionString**.
4. Escriba el nombre de la base de datos para la propiedad **Initial Catalog** en **connectionString**.
5. Haga clic en el botón **Cifrar** en la barra de comandos para iniciar la aplicación de un solo clic **Administrador de credenciales**. Se abrirá el cuadro de diálogo **Setting Credentials** (Establecer credenciales). ![Cuadro de diálogo Establecer credenciales](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
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

Si tiene acceso al portal desde un equipo diferente del equipo de la puerta de enlace, debe asegurarse de que la aplicación del Administrador de credenciales puede conectarse al equipo de la puerta de enlace. Si la aplicación no puede ponerse en contacto con el equipo de la puerta de enlace, no podrá establecer las credenciales del origen de datos ni probar la conexión al origen de datos.

Cuando usa la aplicación **Setting Credentials** (Establecer credenciales) desde el Portal de Azure con el objetivo de establecer credenciales para un origen de datos local, el Portal las cifra con el certificado especificado en la pestaña **Certificado** del **Administrador de configuración de Data Management Gateway** de la máquina de puerta de enlace.

Si desea un enfoque basado en API para cifrar las credenciales, puede usar el cmdlet [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de PowerShell para cifrar las credenciales. El cmdlet usa el certificado cuyo uso tiene configurado esa puerta de enlace para cifrar las credenciales. Puede usar las credenciales cifradas que devuelve este cmdlet y agregarlas al elemento **EncryptedCredential** de **connectionString** en el archivo JSON que se va a emplear con el cmdlet [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) o en el fragmento de código JSON del editor de Data Factory en el Portal.

	"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",

Hay otro enfoque para establecer las credenciales mediante el Editor de la Factoría de datos. Si crea un servicio vinculado de SQL Server mediante el editor y escribe las credenciales en texto sin formato, las credenciales se cifran mediante un certificado que posee el servicio Factoría de datos, NO el certificado que la puerta de enlace está configurada para usar. Aunque este enfoque puede resultar un poco más rápido, en algunos casos es menos seguro. Por lo tanto, se recomienda seguir este enfoque solo para fines de desarrollo y pruebas.


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

	
4. En Azure PowerShell, cambie a la carpeta **C:\\\Archivos de programa\\Microsoft Data Management Gateway\\1.0\\PowerShellScript** y ejecute el script **RegisterGateway.ps1** asociado a la variable local **$Key** como se muestra en el siguiente comando para registrar el agente cliente instalado en su máquina con la puerta de enlace lógica que ha creado antes.

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

<!---HONumber=AcomDC_0810_2016-->