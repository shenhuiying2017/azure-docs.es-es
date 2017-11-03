---
title: "Solución Office 365 de Operations Management Suite (OMS) | Microsoft Docs"
description: "Este artículo proporciona detalles sobre la configuración y el uso de la solución Office 365 en OMS.  Incluye una descripción detallada de los registros de Office 365 creados en Log Analytics."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 711071eaff7ab5e5199793663aa3cbb36a1e8d8a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Solución Office 365 de Operations Management Suite (OMS)

![Logotipo de Office 365](media/oms-solution-office-365/icon.png)

La solución Office 365 para Operations Management Suite (OMS) permite supervisar el entorno de Office 365 en Log Analytics.  

- Supervise las actividades de usuario en las cuentas de Office 365 para analizar patrones de uso e identificación de tendencias de comportamiento. Por ejemplo, puede extraer escenarios de uso específicos, como los archivos que se comparten fuera de la organización o los sitios de SharePoint más populares.
- Supervise las actividades del administrador para realizar el seguimiento de cambios de configuración u operaciones de privilegios elevados.
- Detecte e investigue comportamientos de usuario no deseados, que puede personalizar para las necesidades de la organización.
- Demuestre el cumplimiento de las normas y las auditorías. Por ejemplo, puede supervisar las operaciones de acceso a archivos en los archivos confidenciales, lo que pueden ayudarle con el proceso de cumplimiento y auditoría.
- Solucione problemas operativos mediante la búsqueda de OMS en los datos de actividad de Office 365 de su organización.

## <a name="prerequisites"></a>Requisitos previos
Se requiere lo siguiente antes de la instalación y configuración de esta solución.

- Suscripción organizativa de Office 365.
- Credenciales de una cuenta de usuario que sea un administrador global.
- Para recibir datos de auditoría, primero debe [configurar la auditoría](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) en su suscripción de Office 365.  Tenga en cuenta que la [auditoría de los buzones](https://technet.microsoft.com/library/dn879651.aspx) se configura por separado.  Puede instalar la solución y recopilar otros datos aunque no se configure la auditoría.
 


## <a name="management-packs"></a>Módulos de administración
Esta solución no instala ningún módulo de administración en grupos de administración conectados.
  

## <a name="configuration"></a>Configuración
Una vez realizada la [agregación de la solución de Office 365 a su suscripción](../log-analytics/log-analytics-add-solutions.md), debe conectarla a su suscripción de Office 365.

1. Agregue la solución Administración de alertas al área de trabajo de OMS mediante el proceso descrito en [Incorporación de soluciones](../log-analytics/log-analytics-add-solutions.md).
2. En el portal de OMS, vaya a **Configuración**.
3. En **Orígenes conectados**, seleccione **Office 365**.
4. Haga clic en **Conectar Office 365**.<br>![Conexión de Office 365](media/oms-solution-office-365/configure.png)
5. Inicie sesión en Office 365 con una cuenta que sea un administrador global de la suscripción. 
6. La suscripción aparecerá enumerada con las cargas de trabajo que la solución supervisará.<br>![Conexión de Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>Colección de datos
### <a name="supported-agents"></a>Agentes admitidos
La solución de Office 365 no recupera los datos desde ninguno de los [agentes de OMS](../log-analytics/log-analytics-data-sources.md).  Recupera los datos directamente desde Office 365.

### <a name="collection-frequency"></a>Frecuencia de recopilación
Office 365 envía una [notificación de webhook](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) con datos detallados a Log Analytics cada vez que se crea un registro.

## <a name="using-the-solution"></a>Uso de la solución
Al agregar la solución Office 365 al área de trabajo de OMS, se agrega el icono **Office 365** al panel de OMS. Este icono muestra un recuento y una representación gráfica del número de equipos en el entorno y del cumplimiento de las actualizaciones.<br><br>
![Icono de resumen de Office 365](media/oms-solution-office-365/tile.png)  

Haga clic en el icono de **Office 365** para abrir el panel de **Office 365**.

![Panel de Office 365](media/oms-solution-office-365/dashboard.png)  

El panel incluye las columnas de la tabla siguiente. Cada columna muestra las diez principales alertas por recuento que coinciden con los criterios de esa columna para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que proporcione toda la lista haciendo clic en Ver todo en la parte inferior de la columna o haciendo clic en el encabezado de columna.

| Columna | Descripción |
|:--|:--|
| Operaciones | Proporciona información acerca de los usuarios activos de todas las suscripciones de Office 365 supervisadas. También podrá ver el número de actividades que se producen con el tiempo.
| Exchange | Muestra el desglose de las actividades del servidor de Exchange, como Add-Mailbox Permission o Set-Mailbox. |
| SharePoint | Muestra las actividades principales que realizan los usuarios en documentos de SharePoint. Cuando obtiene los detalles de este icono, la página de búsqueda muestra los detalles de estas actividades, como el documento de destino y la ubicación de esta actividad. Por ejemplo, para un evento de acceso a archivos, podrá ver el documento al que se tiene acceso, el nombre de la cuenta asociada y la dirección IP. |
| Azure Active Directory | Incluye las actividades principales de los usuarios, como restablecer la contraseña de usuario y los intentos de inicio de sesión. Cuando se profundiza, podrá ver los detalles de estas actividades, como el estado del resultado. Esto es especialmente útil si desea supervisar las actividades sospechosas en Azure Active Directory. |




## <a name="log-analytics-records"></a>Registros de Log Analytics

El valor de **Tipo** es **OfficeActivity** para todos los registros creados en el área de trabajo de Log Analytics por la solución de Office 365.  La propiedad **OfficeWorkload** determina a qué servicio de Office 365 hace referencia el registro: Exchange, AzureActiveDirectory, SharePoint o OneDrive.  La propiedad **RecordType** especifica el tipo de operación.  Las propiedades varían para cada tipo de operación y se muestran en las tablas siguientes.

### <a name="common-properties"></a>Propiedades comunes
Las siguientes propiedades son comunes a todos los registros de Office 365.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | La dirección IP del dispositivo que se usó cuando se registró la actividad. La dirección IP se muestra en formato de dirección IPv4 o IPv6. |
| OfficeWorkload | Servicio de Office 365 al que hace referencia el registro.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operación | El nombre de la actividad de usuario o administrador.  |
| OrganizationId | El identificador único GUID del inquilino de Office 365 de su organización. Este valor siempre será el mismo para su organización, con independencia del servicio de Office 365 en el que se produce. |
| RecordType | Tipo de operación realizada. |
| ResultStatus | Indica si la acción (especificada en la propiedad Operation) se realizó correctamente o no. Los valores posibles son Succeeded (correcta), PartiallySucceded (parcialmente correcta) o Failed (con errores). Para la actividad de administración de Exchange, el valor es True o False. |
| UserId | El UPN (nombre principal de usuario) del usuario que realizó la acción que generó el registro, por ejemplo, my_name@my_domain_name. Tenga en cuenta que también se incluyen los registros de actividad realizada por cuentas del sistema (como SHAREPOINT\system o NTAUTHORITY\SYSTEM). | 
| UserKey | Un identificador alternativo para el usuario identificado en la propiedad UserId.  Por ejemplo, esta propiedad se rellena con el identificador único de Passport (PUID) para los eventos producidos por los usuarios de SharePoint, OneDrive para la empresa y Exchange. Esta propiedad también puede especificar el mismo valor que la propiedad UserID para los eventos que se producen en otros servicios y los eventos producidos por las cuentas del sistema|
| UserType | El tipo de usuario que realizó la operación.<br><br>Administrador<br>Application<br>DcAdmin<br>Regular <br>Reserved<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Azure Active Directory
Las siguientes propiedades son comunes a todos los registros de Azure Active Directory.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | El tipo de evento de Azure AD. |
| ExtendedProperties | Las propiedades extendidas del evento de Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Inicio de sesión de cuenta de Azure Active Directory
Estos registros se crean cuando un usuario de Active Directory intenta iniciar sesión.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Application | La aplicación que desencadena el evento de inicio de sesión en la cuenta, como Office 15. |
| Cliente | Detalles sobre dispositivo, sistema operativo del dispositivo y explorador del dispositivo que usó el cliente para el evento de inicio de sesión en la cuenta. |
| LoginStatus | Esta propiedad viene directamente de OrgIdLogon.LoginStatus. Los algoritmos de alertas podrían realizar la asignación de distintos errores de inicio de sesión interesantes. |
| UserDomain | La información de identidad del inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Estos registros se crean cuando se realizan cambios o adiciones en objetos de Active Directory de Azure.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | El usuario sobre el que se realizó la acción (identificada por la propiedad Operation). |
| Actor | El usuario o la entidad de servicio que realizó la acción. |
| ActorContextId | El identificador GUID de la organización al que pertenece el actor. |
| ActorIpAddress | Dirección IP del actor en formato de dirección IPV4 o IPV6. |
| InterSystemsId | El identificador GUID que realiza el seguimiento de las acciones en los componentes del servicio Office 365. |
| IntraSystemId |   El identificador GUID generado por Azure Active Directory para realizar el seguimiento de la acción. |
| SupportTicketId | El identificador del vale de soporte técnico para la acción en situaciones de "actuar en nombre de". |
| TargetContextId | El identificador GUID de la organización a la que pertenece el usuario de destino. |


### <a name="data-center-security"></a>Data Center Security
Estos registros se crean a partir de los datos de auditoría de Data Center Security.  

| Propiedad | Descripción |
|:--- |:--- |
| EffectiveOrganization | El nombre del inquilino al que estaba destinada la elevación o el cmdlet. |
| ElevationApprovedTime | La marca de tiempo en el momento de la aprobación de la elevación. |
| ElevationApprover | El nombre de un administrador de Microsoft. |
| ElevationDuration | La duración en activo de la elevación. |
| ElevationRequestId |  Un identificador único para la solicitud de elevación. |
| ElevationRole | El rol para el que se solicitó la elevación. |
| ElevationTime | La hora de inicio de la elevación. |
| Start_Time | La hora de inicio de la ejecución del cmdlet. |


### <a name="exchange-admin"></a>Administración de Exchange
Estos registros se crean cuando se realizan cambios en la configuración de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Especifica si el cmdlet se ha ejecutado por un usuario de la organización, por personal del centro de datos de Microsoft o una cuenta de servicio del centro de datos o por un administrador delegado. El valor False indica que el cmdlet se ejecutó por alguien de su organización. El valor True indica que el cmdlet lo ejecutó personal del centro de datos, una cuenta de servicio del centro de datos o un administrador delegado. |
| ModifiedObjectResolvedName |  Este es el nombre descriptivo del objeto que ha sido modificado por el cmdlet. Esto se registra únicamente si el cmdlet modifica el objeto. |
| OrganizationName | El nombre del inquilino. |
| OriginatingServer | El nombre del servidor desde el que se ejecutó el cmdlet. |
| parameters | El nombre y valor de todos los parámetros que se utilizaron con el cmdlet que se identifica en la propiedad Operations. |


### <a name="exchange-mailbox"></a>Buzón de Exchange
Estos registros se crean cuando se realizan cambios o agregaciones en los buzones de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Información sobre el cliente de correo electrónico que se usó para realizar la operación, como la versión del explorador, la versión de Outlook y la información del dispositivo móvil. |
| Client_IPAddress | La dirección IP del dispositivo que se usó cuando se registró la operación. La dirección IP se muestra en formato de dirección IPv4 o IPv6. |
| ClientMachineName | El nombre del equipo que hospeda al cliente de Outlook. |
| ClientProcessName | El cliente de correo electrónico que se usó para tener acceso al buzón. |
| ClientVersion | La versión del cliente de correo electrónico. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica el tipo de usuario que tuvo acceso al buzón de correo y realizó la operación que se ha registrado. |
| LogonUserDisplayName |    El nombre descriptivo del usuario que realizó la operación. |
| LogonUserSid | El identificador SID del usuario que realizó la operación. |
| MailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso. |
| MailboxOwnerMasterAccountSid | Identificador SID de la cuenta maestra de la cuenta del propietario del buzón de correo. |
| MailboxOwnerSid | El identificador SID del propietario del buzón. |
| MailboxOwnerUPN | La dirección de correo electrónico de la persona que posee el buzón al que se obtuvo acceso. |


### <a name="exchange-mailbox-audit"></a>Auditoría de buzón de Exchange
Estos registros se crean cuando se crea una entrada de auditoría de buzones de correo.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Elemento | Representa el elemento en el que se realizó la operación | 
| SendAsUserMailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso para enviar correo. |
| SendAsUserSmtp | Dirección SMTP del usuario que se está suplantando. |
| SendonBehalfOfUserMailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso para enviar correo en su nombre. |
| SendOnBehalfOfUserSmtp | Dirección SMTP del usuario en cuyo nombre se envió el correo electrónico. |


### <a name="exchange-mailbox-audit-group"></a>Auditoría de grupos buzones de Exchange
Estos registros se crean cuando se realizan cambios o agregaciones en los grupos de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Información sobre cada elemento del grupo. |
| CrossMailboxOperations | Indica si estuvo implicado más de un buzón de correo en la operación. |
| DestMailboxId | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador GUID del buzón de destino. |
| DestMailboxOwnerMasterAccountSid | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador SID de la cuenta maestra del propietario del buzón de destino. |
| DestMailboxOwnerSid | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador SID del buzón de correo de destino. |
| DestMailboxOwnerUPN | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el UPN del propietario del buzón de correo de destino. |
| DestFolder | La carpeta de destino, para operaciones como Move (mover). |
| Carpeta | La carpeta donde se encuentra un grupo de elementos. |
| Carpetas |     Información sobre las carpetas de origen implicadas en una operación; por ejemplo, si las carpetas son seleccionadas y, a continuación, se eliminan. |


### <a name="sharepoint-base"></a>SharePoint
Estas propiedades son comunes a todos los registros de SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica que se ha producido un evento en SharePoint. Los valores posibles son SharePoint y ObjectModel. |
| ItemType | El tipo de objeto al que se ha accedido o modificado. Consulte la tabla ItemType para obtener detalles sobre los tipos de objetos. |
| MachineDomainInfo | Información sobre las operaciones de sincronización del dispositivo. Esta información se notifica solo si está presente en la solicitud. |
| MachineId |   Información sobre las operaciones de sincronización del dispositivo. Esta información se notifica solo si está presente en la solicitud. |
| Site_ | El identificador GUID del sitio donde se encuentra el archivo o carpeta al que tuvo acceso el usuario. |
| Source_Name | La entidad que desencadenó la operación auditada. Los valores posibles son SharePoint y ObjectModel. |
| UserAgent | Información sobre el cliente o explorador del usuario. Esta información la proporciona el cliente o el explorador. |


### <a name="sharepoint-schema"></a>Esquema de SharePoint
Estos registros se crean cuando se realizan cambios en la configuración de SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadena opcional para eventos personalizados. |
| Event_Data |  Carga opcional para eventos personalizados. |
| ModifiedProperties | La propiedad se incluye para los eventos de administración, como agregar a un usuario como miembro de un sitio o un grupo de administración de la colección de sitios. La propiedad incluye el nombre de la propiedad que se ha modificado (por ejemplo, el grupo de administración del sitio), el nuevo valor de la propiedad modificada (como el usuario que se ha agregado como un administrador del sitio) y el valor anterior del objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operaciones de archivos de SharePoint
Estos registros se crean en respuesta a las operaciones de archivos en SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | La extensión de archivo de un archivo que se ha copiado o movido. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| DestinationFileName | El nombre del archivo que se ha copiado o movido. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| DestinationRelativeUrl | La dirección URL de la carpeta de destino donde se ha copiado o movido un archivo. La combinación de los valores de los parámetros SiteURL, DestinationRelativeURL y DestinationFileName es el valor de la propiedad ObjectID, que es el nombre de la ruta de acceso completa del archivo que se copió. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| SharingType | El tipo de permisos que se asignaron al usuario con el que se ha compartido el recurso. Este usuario se identifica mediante el parámetro UserSharedWith. |
| Site_Url | La dirección URL del sitio donde se encuentra el archivo o carpeta al que tuvo acceso el usuario. |
| SourceFileExtension | La extensión de archivo del archivo al que tuvo acceso el usuario. Esta propiedad está en blanco si el objeto al que se tuvo acceso es una carpeta. |
| SourceFileName |  El nombre del archivo o carpeta al que tuvo acceso el usuario. |
| SourceRelativeUrl | La dirección URL de la carpeta que contiene el archivo al que tuvo acceso el usuario. La combinación de los valores de los parámetros SiteURL, SourceRelativeURL y SourceFileName es el valor de la propiedad ObjectID, que es el nombre de la ruta de acceso completa del archivo al que tuvo acceso el usuario. |
| UserSharedWith |  El usuario con el que se compartió un recurso. |




## <a name="sample-log-searches"></a>Búsquedas de registros de ejemplo
En la tabla siguiente se proporcionan ejemplos de búsquedas de registros para los registros de actualización recopilados por esta solución.

| Consultar | Descripción |
| --- | --- |
|Recuento de todas las operaciones de la suscripción a Office 365 |Type = OfficeActivity &#124; measure count() by Operation |
|Uso de sitios de SharePoint|Type=OfficeActivity OfficeWorkload=sharepoint &#124; measure count() as Count by SiteUrl &#124; sort Count asc|
|Operaciones de acceso a archivos por tipo de usuario|Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed &#124; measure count() by UserType|
|Búsqueda por una palabra clave específica|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Supervisión de acciones externas en Exchange|Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true|



## <a name="troubleshooting"></a>Solución de problemas

Si la solución de Office 365 no está recopilando datos según lo previsto, compruebe su estado en el portal de OMS en **Configuración** -> **Orígenes conectados** -> **Office 365**. En la tabla siguiente se describe cada estado.

| Estado | Descripción |
|:--|:--|
| Active | La suscripción a Office 365 está activa y la carga de trabajo se ha conectado correctamente al área de trabajo de OMS. |
| Pending | La suscripción a Office 365 está activa pero la carga de trabajo aún no se ha conectado correctamente al área de trabajo de OMS. La primera vez que se conecta a la suscripción de Office 365, todas las cargas de trabajo estarán en este estado hasta que estén conectadas correctamente. Espere 24 horas para que las cargas de trabajo cambien a Activo. |
| Inactivo | La suscripción a Office 365 está en estado inactivo. Compruebe la página de administración de Office 365 para obtener los detalles. Después de activar la suscripción a Office 365, desvincúlela del área de trabajo de OMS y vincúlela de nuevo para empezar a recibir datos. |



## <a name="next-steps"></a>Pasos siguientes
* Usar Búsquedas de registros en [Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver datos detallados sobre la actualización.
* [Cree sus propios paneles](../log-analytics/log-analytics-dashboards.md) para mostrar las consultas de búsqueda favoritas de Office 365.
* [Cree alertas](../log-analytics/log-analytics-alerts.md) para recibir notificaciones proactivas de actividades importantes de Office 365.  
