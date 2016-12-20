---
title: "Solución de problemas de Data Management Gateway | Microsoft Docs"
description: Ofrece sugerencias para solucionar problemas relacionados con Data Management Gateway.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c6756c37-4e5a-4d1e-ab52-365f149b4128
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4619716238d77caf494c116266c60e7e0c86ca92


---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Solución de problemas con Data Management Gateway
En este artículo se ofrece información sobre la solución de problemas con Data Management Gateway. 

> [!NOTE]
> Para más información sobre la puerta de enlace, vea el artículo [Data Management Gateway](data-factory-data-management-gateway.md). En el artículo [Mover datos entre implementaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) puede ver un tutorial sobre cómo mover datos entre una base de datos de SQL Server local a Azure Blob Storage con la puerta de enlace. 
> 
> 

## <a name="failed-to-install-or-register-gateway"></a>Error al instalar o registrar la puerta de enlace
### <a name="1-problem"></a>1. Problema
Se muestra este mensaje de error al instalar o registrar una puerta de enlace (en concreto, al descargar el archivo de instalación de la puerta de enlace). 

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

### <a name="cause"></a>Causa
La máquina donde intenta instalar la puerta de enlace no puede descargar la versión más reciente del archivo de instalación de la puerta de enlace del Centro de descarga debido a un problema de red.

### <a name="resolution"></a>Resolución
Compruebe si la configuración del firewall o del servidor proxy bloquea la conexión de red desde la máquina al [Centro de descarga](https://download.microsoft.com/) y actualice la configuración según corresponda. 

Como alternativa, puede descargar el archivo de instalación de la puerta de enlace más reciente desde el [Centro de descarga](https://www.microsoft.com/en-US/download/details.aspx?id=39717) en otras máquinas que puedan acceder al Centro de descarga, copiar el archivo instalador en el equipo host de la puerta de enlace y, después, ejecutarlo de forma manual para instalar o actualizar la puerta de enlace.

### <a name="2-problem"></a>2. Problema
Se muestra este error al hacer clic en “Instalar directamente en este equipo” en Azure Portal para intentar instalar una puerta de enlace. 

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

### <a name="cause"></a>Causa
Ya hay instalada una puerta de enlace en la máquina. 

### <a name="resolution"></a>Resolución
Desinstale la puerta de enlace existente en la máquina y vuelva a hacer clic en el vínculo “Instalar directamente en este equipo”.

### <a name="3-problem"></a>3. Problema
Puede que vea este error al registrar una nueva puerta de enlace.

`Error: The gateway has encountered an error during registration.`

### <a name="cause"></a>Causa
Puede que vea este mensaje por uno de los motivos siguientes:

* El formato de la clave de la puerta de enlace no es válido.
* Se ha invalidado la clave de la puerta de enlace.
* Se ha vuelto a generar la clave de la puerta de enlace desde el portal.  

### <a name="resolution"></a>Resolución
Compruebe si usa la clave de la puerta de enlace correcta en Azure Portal. Si es necesario, vuelva a generar una clave y úsela para registrar la puerta de enlace.

### <a name="4-problem"></a>4. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace: 

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`

como se muestra en la captura de pantalla siguiente:

![el contenido o el formato de la clave no son válidos](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

### <a name="cause"></a>Causa
El contenido o el formato de la clave de la puerta de enlace de entrada no son correctos. Uno de los motivos puede ser que haya copiado solo una parte de la copia desde el portal o que haya usado una clave no válida.

### <a name="resolution"></a>Resolución
Genere una clave de la puerta de enlace en Azure Portal y use el botón Copiar para copiar la clave entera y, después, péguela en esta ventana para registrar la puerta de enlace.

### <a name="5-problem"></a>5. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace:

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![la clave de la puerta de enlace no es válida o está vacía](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

### <a name="cause"></a>Causa
La clave de la puerta de enlace se ha vuelto a generar o la puerta de enlace se ha eliminado en Azure Portal.

### <a name="resolution"></a>Resolución
Si aún existe una puerta de enlace, vuelva a generar una clave de la puerta de enlace en Azure Portal, use el botón Copiar para copiar la clave entera y, después, péguela en esta ventana para registrar la puerta de enlace. En caso contrario, vuelva a crear la puerta de enlace y empiece de nuevo.

### <a name="6-problem"></a>6. Problema
Puede que vea el siguiente mensaje de error al volver a registrar una puerta de enlace:

`Error: Gateway has been online for a while, then shows “Gateway is not registered” with the status “Gateway key is invalid”`

![la clave de la puerta de enlace no es válida o está vacía](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

### <a name="cause"></a>Causa
Este error puede producirse porque se ha eliminado la puerta de enlace o porque se ha vuelto generar la clave de la puerta de enlace asociada.

### <a name="resolution"></a>Resolución
Si se ha eliminado la puerta de enlace, vuelva a crear la puerta de enlace desde el portal, haga clic en Registrar, copie la clave desde el portal, péguela y, después, intente registrar la puerta de enlace.

Si la puerta de enlace aún existe, pero su clave se ha vuelto a generar, use la nueva clave para registrar la puerta de enlace. Si no tiene la clave, vuelva a generar la clave desde el portal. 

### <a name="7-problem"></a>7. Problema
Al registrar una puerta de enlace, es posible que tenga que especificar la ruta de acceso y la contraseña de un certificado. 

![especificar un certificado](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

### <a name="cause"></a>Causa
La puerta de enlace se ha registrado en otras máquinas anteriormente. Durante el registro inicial de una puerta de enlace se ha asociado un certificado de cifrado con la puerta de enlace. El certificado puede ser generado automáticamente por la puerta de enlace o puede proporcionarlo el usuario.  Este certificado se usa para cifrar las credenciales del almacén de datos (servicio vinculado).  

![exportar un certificado](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Al restaurar la puerta de enlace en un equipo host distinto, el asistente para registro pide este certificado para descifrar las credenciales que se han cifrado anteriormente con este certificado.  Sin este certificado, las credenciales no se pueden descifrar con la nueva puerta de enlace y las ejecuciones de actividades de copia posteriores asociadas con esta nueva puerta de enlace producirán errores.  

### <a name="resolution"></a>Resolución
Si ha exportado el certificado de credenciales desde la máquina de la puerta de enlace original con el botón Exportar de la pestaña **Configuración** de **Gateway Configuration Manager**, use el certificado aquí.

No puede omitir esta fase al recuperar una puerta de enlace. Si falta el certificado, necesitará eliminar la puerta de enlace del portal y volver a crear una puerta de enlace.  Además, es necesario actualizar todos los servicios vinculados relacionados con la puerta de enlace volviendo a escribir sus credenciales. 

### <a name="8-problem"></a>8. Problema
Puede ver el siguiente mensaje de error: 

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

### <a name="cause"></a>Causa
Este error se produce cuando la puerta de enlace se encuentra en un entorno que necesita un proxy HTTP para acceder a recursos de Internet, o bien cuando se cambia la contraseña de autenticación del proxy, pero no se actualiza en la puerta de enlace. 

### <a name="resolution"></a>Resolución
Siga las instrucciones de la sección [Consideraciones sobre el servidor proxy](#proxy-server-considerations) en este documento y configure el proxy con Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>La puerta de enlace está en línea con funcionalidad limitada
### <a name="1-problem"></a>1. Problema
El estado de la puerta de enlace es **en línea con funciones limitadas**. 

### <a name="cause"></a>Causa
El estado de la puerta de enlace es “en línea con funciones limitadas” por uno de los motivos siguientes:

* La puerta de enlace no se puede conectar al servicio en la nube mediante el bus de servicio.
* El servicio en la nube no se puede conectar a la puerta de enlace mediante el bus de servicio.

Cuando la puerta de enlace está en línea con funcionalidad limitada, no podrá usar el Asistente para copia de Data Factory para crear canalizaciones de datos entre almacenes de datos locales. Como alternativa, puede usar Data Factory Editor en Azure Portal, Visual Studio o Azure PowerShell.

### <a name="resolution"></a>Resolución
La solución a este problema (en línea con funciones limitadas) depende de si la puerta de enlace se puede conectar al servicio en la nube o viceversa. En las secciones siguientes se describen estas soluciones. 

### <a name="2-problem"></a>2. Problema
Verá este error: 

`Error: Gateway cannot connect to cloud service through service bus`

![La puerta de enlace no se puede conectar al servicio en la nube](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

### <a name="cause"></a>Causa
La puerta de enlace no se puede conectar al servicio en la nube mediante el bus de servicio.

### <a name="resolution"></a>Resolución
Siga estos pasos para poner en línea la puerta de enlace:

* Permita reglas de salida de direcciones IP en la máquina de la puerta de enlace y en el firewall corporativo. Encontrará las direcciones IP en el Registro de eventos de Windows (id. == 401): intento de acceso a un socket no permitido por sus permisos de acceso XX.XX.XX.XX:9350.
* Configure los valores de proxy en la puerta de enlace. Consulte la sección [Consideraciones sobre el servidor proxy](#proxy-server-considerations) para más información.
* Habilite los puertos de salida 5671 y 9350-9354 en el Firewall de Windows de la máquina de la puerta de enlace y en el firewall corporativo. Consulte la sección [Puertos y firewall](#ports-and-firewall) para más información. Este paso es opcional, pero se recomienda por motivos de rendimiento.

### <a name="3-problem"></a>3. Problema
Verá este error: 

`Error: Cloud service cannot connect to gateway through service bus.`

### <a name="cause"></a>Causa
Un error transitorio en la conectividad de red.

### <a name="resolution"></a>Resolución
Siga estos pasos para poner en línea la puerta de enlace:

* Espere un par de minutos; la conectividad se recuperará automáticamente cuando desaparezca el error.
* Si el error persiste, reinicie el servicio de puerta de enlace.

## <a name="failed-to-author-linked-service"></a>Error al crear el servicio vinculado
### <a name="problem"></a>Problema
Puede que vea este error al intentar usar el Administrador de credenciales en el portal para especificar las credenciales de un nuevo servicio vinculado o para actualizar las credenciales de un servicio vinculado existente.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Cuando vea este error, la página de configuración de Configuration Manager será similar a esta:

![No se puede establecer conexión con la base de datos](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

### <a name="cause"></a>Causa
Es posible que se haya perdido el certificado SSL en la máquina de la puerta de enlace. La puerta de enlace no puede cargar certificado usado actualmente para el cifrado SSL. Es posible que también vea un mensaje de error en el registro de eventos similar al siguiente: "No se puede obtener la configuración de la pasarela del servicio en la nube. Compruebe la clave de la pasarela y la conexión de red. (No se puede cargar certificado con huella digital)".

### <a name="resolution"></a>Resolución
Siga estos pasos para solucionar el problema:

1. Inicie el **Administrador de configuración de Data Management Gateway**.
2. Cambie a la pestaña **Configuración** .  
3. Haga clic en el botón **Cambiar** para cambiar el certificado SSL.
   
   ![botón Cambiar certificado](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png) 
4. Seleccione un nuevo certificado como Certificado SSL. (Puede usar cualquier certificado SSL que haya generado por su cuenta o que haya generado una organización).
   
   ![especificar un certificado](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Error en la actividad de copia
### <a name="1-problem"></a>1. Problema
Puede que vea el error "UserErrorFailedToConnectToSqlserver" después de configurar una canalización en Azure Portal. 

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

### <a name="cause"></a>Causa
Esto puede ocurrir por diferentes motivos y la mitigación varía según la causa. 

### <a name="resolution"></a>Resolución
Permita las conexiones TCP de salida a través del puerto TCP/1433 en el lado cliente de Data Management Gateway antes de conectar a una base de datos SQL. 

Si la base de datos de destino es una base de datos SQL de Azure, compruebe también la configuración del firewall de Azure SQL Server.

Vea en la sección siguiente los pasos necesarios para realizar una prueba de conexión en el almacén de datos local. 

## <a name="data-store-connection-or-driver-related-errors"></a>Errores relacionados con la conexión al almacén de datos o con los controladores
Si ve errores relacionados con los controladores o la conexión al almacén de datos, realice los siguientes pasos: 

1. Inicie el **Administrador de configuración de Data Management Gateway** en la máquina de la puerta de enlace.
2. Cambie a la pestaña **Diagnósticos** .
3. Seleccione o escriba los valores correspondientes en los campos de **Probar la conexión a un origen de datos local con esta puerta de enlace** con este grupo de puerta de enlace.
4. Haga clic en **Probar conexión** para ver si puede conectarse al origen de datos local desde la máquina de la puerta de enlace utilizando las credenciales y la información de conexión. Si la prueba de conexión sigue produciendo errores después de instalar un controlador, reinicie la puerta de enlace para aplicar los cambios más recientes.

![Prueba de conexión en la pestaña Diagnósticos](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Registros de puerta de enlace
### <a name="send-gateway-logs-to-microsoft"></a>Enviar registros de puerta de enlace a Microsoft
Cuando se ponga en contacto con el equipo de soporte técnico de Microsoft para solucionar problemas de la puerta de enlace, es posible que se le pida que comparta los registros de la puerta de enlace. La versión de la puerta de enlace permite compartir fácilmente los registros de puerta de enlace necesarios con dos clics de botón en el Administrador de configuración de Data Management Gateway.    

1. Cambie a la pestaña **Diagnóstico** del Administrador de configuración de Data Management Gateway.
   
    ![Data Management Gateway: pestaña Diagnóstico](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Haga clic en el vínculo **Enviar registros** para ver el siguiente cuadro de diálogo: 
   
    ![Data Management Gateway: Enviar registros](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. (opcional) Haga clic en **Ver registros** para revisar los registros en el Visor de eventos.
4. (opcional) Haga clic en **Privacidad** para revisar la declaración de privacidad de Microsoft Online Services. 
5. Una vez que esté satisfecho con lo que va a cargar, haga clic en **Enviar registros** para enviar realmente los registros de los últimos siete días a Microsoft para solucionar problemas. Debería ver el estado de la operación de envío de registros tal y como aparece en la siguiente imagen:
   
    ![Data Management Gateway: Estado del envío de registros](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Cuando finalice la operación, verá un cuadro de diálogo como el que se muestra en la siguiente imagen:
   
    ![Data Management Gateway: Estado del envío de registros](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Anote el **identificador de informe** y envíelo al equipo de soporte técnico de Microsoft. El identificador de informe sirve para encontrar los registros de la puerta de enlace que ha cargado para solucionar problemas.  Este identificador del informe también se guarda en el Visor de eventos para tenerlo como referencia.  Lo encontrará si examina el identificador de evento “25” y comprueba la fecha y la hora.
   
    ![Data Management Gateway: Identificador de informe del envío de registros](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Archivar registros de puerta de enlace en el equipo host de puerta de enlace
Hay veces en las que existen problemas con la puerta de enlace y no es posible compartir los registros de puerta de enlace de forma directa: 

* Cuando se instala y registra manualmente la puerta de enlace
* Cuando se trata de registrar la puerta de enlace con una clave regenerada en el Administrador de configuración 
* Cuando se trata de enviar registros, pero el servicio host Data Management Gateway no se puede conectar

En tales casos, puede guardar los registros de puerta de enlace como un archivo ZIP y compartirlo más adelante, cuando pueda ponerse en contacto con el equipo de soporte técnico de Microsoft. Por ejemplo, si recibe el siguiente error al registrar la puerta de enlace tal y como se muestra en la siguiente imagen:   

![Data Management Gateway: Error de registro](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Haga clic en el vínculo **Archivar registros de puerta de enlace** para archivar y guardar los registros y, luego, comparta el archivo ZIP con el equipo de soporte técnico de Microsoft. 

![Data Management Gateway: Archivar registros](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Buscar registros de puerta de enlace
Puede obtener más información en los registros de la puerta de enlace de los registros de eventos de Windows. Puede encontrarlos utilizando el **Visor de eventos** de Windows, que se encuentra en **Registros de aplicaciones y servicios** > **Data Management Gateway**. Al solucionar problemas relacionados con la puerta de enlace, busque eventos de error en el Visor de eventos.

![Data Management Gateway: Registros en el Visor de eventos](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)




<!--HONumber=Nov16_HO3-->


