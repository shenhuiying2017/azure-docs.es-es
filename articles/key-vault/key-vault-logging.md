---
title: Registro de Azure Key Vault | Microsoft Docs
description: "Use este tutorial para tener ayuda para empezar a trabajar con el registro del Almacén de claves de Azure."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/16/2017
ms.author: barclayn
ms.openlocfilehash: 2faf45c7329f1c98a26bcf7ec5d569dfa16cbbda
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="azure-key-vault-logging"></a>Registro del Almacén de claves de Azure
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción
Después de haber creado uno o varios almacenes de claves, es probable que desee supervisar cómo y cuándo se accede a los almacenes de claves y quién lo hace. Puede hacerlo al habilitar el registro del Almacén de claves, que guarda la información en una cuenta de almacenamiento de Azure proporcionada por el usuario. Un nuevo contenedor llamado **insights-logs-auditevent** se crea automáticamente para su cuenta de almacenamiento especificada, y puede utilizar esta misma cuenta para recopilar registros de varios almacenes de claves.

Puede acceder a la información de registro como máximo, 10 minutos después de la operación del Almacén de claves. En la mayoría de los casos, será más rápido que esto.  Es decisión suya administrar los registros en la cuenta de almacenamiento:

* Utilice los métodos de control de acceso de Azure estándar para proteger los registros mediante la restricción de quién puede tener acceso a ellos.
* Elimine los registros que ya no desee mantener en la cuenta de almacenamiento.

Use este tutorial para tener ayuda para empezar a trabajar con el registro del Almacén de claves de Azure, para crear la cuenta de almacenamiento, habilitar el registro e interpretar la información de registro recopilada.  

> [!NOTE]
> Este tutorial no incluye instrucciones sobre cómo crear almacenes de claves, claves o secretos. Para obtener información, consulte [Introducción al Almacén de claves de Azure](key-vault-get-started.md). O bien, para obtener instrucciones de la interfaz de la línea de comandos entre plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
> Actualmente, no es posible configurar el Almacén de claves de Azure en el portal de Azure. En su lugar, siga estas instrucciones de Azure PowerShell.
>
>

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará lo siguiente:

* Un Almacén de claves existente que ha utilizado.  
* Azure PowerShell, **versión mínima: 1.0.1**. Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). Si ya instaló Azure PowerShell y no sabe la versión, en la consola de Azure PowerShell, escriba `(Get-Module azure -ListAvailable).Version`.  
* Suficiente almacenamiento en Azure para sus registros del Almacén de claves.

## <a id="connect"></a>Conexión a las suscripciones
Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:  

    Login-AzureRmAccount

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtendrá todas las suscripciones asociadas a esta cuenta y, de forma predeterminada, usará la primera.

Si tiene varias suscripciones, es posible que deba especificar la que se usó para crear su instancia de Azure Key Vault. Escriba lo siguiente para ver las suscripciones de su cuenta:

    Get-AzureRmSubscription

A continuación, para especificar la suscripción asociada al almacén de claves que registrará, escriba:

    Set-AzureRmContext -SubscriptionId <subscription ID>

> [!NOTE]
> Este es un paso importante y especialmente útil si tiene varias suscripciones asociadas a su cuenta. Si este paso se omite, puede que reciba un error al registrar Microsoft.Insights.
>   
>

Para obtener más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Creación de una cuenta de almacenamiento nueva para los registros
Aunque puede usar una cuenta de almacenamiento existente para sus registros, crearemos una cuenta de almacenamiento que se dedicará a los registros del Almacén de claves. Por comodidad para cuando tengamos que especificarlos más adelante, almacenaremos los detalles en una variable denominada **sa**.

Para una mayor facilidad de administración, también usaremos el grupo de recursos que contiene el Almacén de claves. Desde el [tutorial de introducción](key-vault-get-started.md), este grupo de recursos se denomina **ContosoResourceGroup** , y seguiremos usando la ubicación Asia Oriental. Sustituya estos valores para los suyos propios, según corresponda:

    $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'


> [!NOTE]
> Si decide usar una cuenta de almacenamiento existente, ésta debe usar tanto la misma suscripción que el almacén de claves como el modelo de implementación de Resource Manager, en lugar del modelo de implementación clásica.
>
>

## <a id="identify"></a>Identificación del Almacén de claves para los registros
En nuestro tutorial de introducción, el nombre de nuestro almacén de claves era **ContosoKeyVault**, por lo que seguiremos usando ese nombre y almacenaremos los detalles en una variable denominada **kv**:

    $kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Habilitación del registro
Para habilitar el registro del Almacén de claves, usaremos el cmdlet Set-AzureRmDiagnosticSetting, junto con las variables que hemos creado para nuestra nueva cuenta de almacenamiento y nuestro Almacén de claves. También estableceremos la marca **-Enabled** en **$true** y estableceremos la categoría en AuditEvent (la única categoría del registro de Key Vault):

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

El resultado de esto incluye:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0


Esto confirma que el registro está habilitado ahora para el Almacén de claves y guarda la información en su cuenta de almacenamiento.

Opcionalmente también se puede establecer una directiva de retención para los registros, de forma que los registros más antiguos se eliminen automáticamente. Por ejemplo, establezca la directiva de retención mediante la marca **-RetentionEnabled** en **$true** y establezca el parámetro **-RetentionInDays** en **90**, con el fin de que los registros que tengan más de 90 días se eliminen automáticamente.

    Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90

Qué se registra:

* Se registran todas las solicitudes de API de REST autenticadas, incluidas las solicitudes con error debido a permisos de acceso, errores del sistema o solicitudes incorrectas.
* Las operaciones en el Almacén de claves, incluidas la creación, la eliminación, la definición de políticas de acceso del Almacén de claves y la actualización de los atributos del Almacén de claves, como las etiquetas.
* Las operaciones en claves y secretos del Almacén de claves, incluidas la creación, la modificación o la eliminación de estas claves o secretos; operaciones como firmar, comprobar, cifrar, descifrar, encapsular y desencapsular claves, obtener secretos, generar listas de claves y secretos y sus versiones.
* Solicitudes no autenticadas que dan como resultado una respuesta 401. Por ejemplo, las solicitudes que no tienen un token de portador, cuyo formato es incorrecto o está caducado o tienen un token no válido.  

## <a id="access"></a>Acceso a los registros
Los registros de Key Vault se almacenan en el contenedor **insights-logs-auditevent** de la cuenta de almacenamiento proporcionada. Para mostrar una lista de todos los blobs de este contenedor, escriba:

En primer lugar, cree una variable para el nombre del contenedor. Se usará en el resto del tutorial.

    $container = 'insights-logs-auditevent'

Para mostrar una lista de todos los blobs de este contenedor, escriba:

    Get-AzureStorageBlob -Container $container -Context $sa.Context
El resultado será similar al siguiente.

**Identificador URI de contenedor: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Name**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****

Como puede ver en esta salida, los blobs siguen una convención de nomenclatura: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Los valores de fecha y hora usan UTC.

Puesto que la misma cuenta de almacenamiento puede usarse para recopilar registros de varios recursos, el identificador de recurso completo en el nombre del blob es muy útil para acceder o descargar solo los blobs que necesita. Pero antes de hacerlo, primero explicaremos cómo descargar todos los blobs.

En primer lugar, cree una carpeta para descargar los blobs. Por ejemplo:

    New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

A continuación, obtenga una lista de todos los blobs:  

    $blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Canalice esta lista mediante 'Get-AzureStorageBlobContent' para descargar los blobs en la carpeta de destino:

    $blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Al ejecutar este segundo comando, el delimitador **/** de los nombres de los blobs crea una estructura de carpeta completa en la carpeta de destino y dicha estructura se usará para descargar y almacenar los blobs como archivos.

Para descargar blobs de forma selectiva, utilice caracteres comodín. Por ejemplo:

* Si tiene varios almacenes de claves y desea descargar los registros solo para un Almacén de claves, denominado CONTOSOKEYVAULT3:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
* Si tiene varios grupos de recursos y desea descargar los registros solo de un grupo de recursos específico, use `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
* Si desea descargar todos los registros del mes de enero de 2016, use `-Blob '*/year=2016/m=01/*'`:

        Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Ahora está listo para comenzar a ver lo que está en los registros. Pero antes de pasar a eso, hay otros dos parámetros de Get-AzureRmDiagnosticSetting que puede ser necesario conocer:

* Para consultar el estado de la configuración del diagnóstico del recurso del almacén de claves: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Para deshabilitar el registro del recurso del almacén de claves: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Interpretación de los registros de Key Vault
Los blobs individuales se almacenan como texto, con formato de blob JSON. A continuación se muestra una entrada del registro de ejemplo cuando se ejecuta `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }


En la tabla siguiente se muestran los nombres y las descripciones de los campos.

| Nombre del campo | Description |
| --- | --- |
| Twitter en tiempo |Fecha y hora (UTC). |
| resourceId |Identificador de recursos del Administrador de recursos de Azure Para los registros de Key Vault, siempre es el udentificador de recurso correcto de Key Vault. |
| operationName |Nombre de la operación, como se documenta en la tabla siguiente. |
| operationVersion |Se trata de la versión de API de REST solicitada por el cliente. |
| categoría |Para los registros del Almacén de claves, AuditEvent es el único valor disponible. |
| resultType |Resultado de la solicitud de API de REST. |
| resultSignature |Estado de HTTP |
| resultDescription |Descripción adicional acerca del resultado, cuando esté disponible. |
| durationMs |Tiempo que tardó en atender la solicitud de API de REST, en milisegundos. Esto no incluye la latencia de red, por lo que el tiempo que se mide en el cliente podría no coincidir con este tiempo. |
| callerIpAddress |Dirección IP del cliente que realizó la solicitud. |
| correlationId |Un GUID opcional que el cliente puede pasar para correlacionar los registros del lado cliente con los registros del lado servicio (el Almacén de claves). |
| identidad |Identidad del token que se ha presentado al realizar la solicitud de API de REST. Suele ser un "usuario", una "entidad de servicio" o una combinación de "usuario + appId" como en el caso de una solicitud procedente de un cmdlet de Azure PowerShell. |
| propiedades |Este campo contendrá información diferente en función de la operación (operationName). En la mayoría de los casos, contiene información del cliente (la cadena useragent pasada por el cliente), el URI de la solicitud de API de REST exacta y el código de estado HTTP. Además, cuando se devuelve un objeto como resultado de una solicitud (por ejemplo, KeyCreate o VaultGet) también contiene el URI de la clave (como "id"), el URI del almacén o el URI del secreto. |

Los valores del campo **operationName** tienen el formato ObjectVerb. Por ejemplo:

* Todas las operaciones del almacén de claves tienen el formato 'Vault`<action>`', como `VaultGet` y `VaultCreate`.
* Todas las operaciones de las claves tienen el formato 'Key`<action>`', como `KeySign` y `KeyList`.
* Todas las operaciones del secreto tienen el formato 'Secret`<action>`', como `SecretGet` y `SecretListVersions`.

En la tabla siguiente se muestra el operationName y el comando de API de REST correspondiente.

| operationName | Comando de API de REST |
| --- | --- |
| Autenticación |Mediante un punto de conexión de Azure Active Directory |
| VaultGet |[Obtener información acerca de un almacén de claves](https://msdn.microsoft.com/en-us/library/azure/mt620026.aspx) |
| VaultPut |[Crear o actualizar un almacén de claves](https://msdn.microsoft.com/en-us/library/azure/mt620025.aspx) |
| VaultDelete |[Eliminar un almacén de claves](https://msdn.microsoft.com/en-us/library/azure/mt620022.aspx) |
| VaultPatch |[Crear o actualizar un almacén de claves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Lista de todos los almacenes de claves en un grupo de recursos](https://msdn.microsoft.com/en-us/library/azure/mt620027.aspx) |
| KeyCreate |[Crear una clave](https://msdn.microsoft.com/en-us/library/azure/dn903634.aspx) |
| KeyGet |[Obtener información sobre una clave](https://msdn.microsoft.com/en-us/library/azure/dn878080.aspx) |
| KeyImport |[Importar una clave a un almacén](https://msdn.microsoft.com/en-us/library/azure/dn903626.aspx) |
| KeyBackup |[Realizar una copia de seguridad de una clave](https://msdn.microsoft.com/en-us/library/azure/dn878058.aspx). |
| KeyDelete |[Eliminar una clave](https://msdn.microsoft.com/en-us/library/azure/dn903611.aspx) |
| KeyRestore |[Restaurar una clave](https://msdn.microsoft.com/en-us/library/azure/dn878106.aspx) |
| KeySign |[Firmar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878096.aspx) |
| KeyVerify |[Comprobar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878082.aspx) |
| KeyWrap |[Encapsular una clave](https://msdn.microsoft.com/en-us/library/azure/dn878066.aspx) |
| KeyUnwrap |[Desencapsular una clave](https://msdn.microsoft.com/en-us/library/azure/dn878079.aspx) |
| KeyEncrypt |[Cifrar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878060.aspx) |
| KeyDecrypt |[Descifrar con una clave](https://msdn.microsoft.com/en-us/library/azure/dn878097.aspx) |
| KeyUpdate |[Actualizar una clave](https://msdn.microsoft.com/en-us/library/azure/dn903616.aspx) |
| KeyList |[Enumerar las claves en un almacén](https://msdn.microsoft.com/en-us/library/azure/dn903629.aspx) |
| KeyListVersions |[Enumerar las versiones de una clave](https://msdn.microsoft.com/en-us/library/azure/dn986822.aspx) |
| SecretSet |[Crear un secreto](https://msdn.microsoft.com/en-us/library/azure/dn903618.aspx) |
| SecretGet |[Obtener un secreto](https://msdn.microsoft.com/en-us/library/azure/dn903633.aspx) |
| SecretUpdate |[Actualizar un secreto](https://msdn.microsoft.com/en-us/library/azure/dn986818.aspx) |
| SecretDelete |[Eliminar un secreto](https://msdn.microsoft.com/en-us/library/azure/dn903613.aspx) |
| SecretList |[Enumerar secretos en un almacén](https://msdn.microsoft.com/en-us/library/azure/dn903614.aspx) |
| SecretListVersions |[Enumerar versiones de un secreto](https://msdn.microsoft.com/en-us/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Uso de Log Analytics

Puede utilizar la solución Azure Key Vault en Log Analytics para revisar los registros AuditEvents de Azure Key Vault. Para más información, incluido cómo configurar esta opción, consulte [Solución Azure Key Vault en Log Analytics](../log-analytics/log-analytics-azure-key-vault.md). En este artículo también se incluyen instrucciones si necesita migrar desde la solución Key Vault anterior que se ofrecía durante la versión preliminar de Log Analytics, cuando enrutó por primera vez los registros a una cuenta de Azure Storage y configuró Log Analytics para que leyera desde ahí.

## <a id="next"></a>Pasos siguientes
Para ver un tutorial que use Azure Key Vault en una aplicación web, consulte [Uso de Azure Key Vault desde una aplicación web](key-vault-use-from-web-application.md).

Para conocer las referencias de programación, consulte la [Guía del desarrollador del Almacén de claves de Azure](key-vault-developers-guide.md).

Para obtener una lista de los cmdlets de Azure PowerShell 1.0 para Azure Key Vault, consulte [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault)(Cmdlets de Azure Key Vault).

Para ver un tutorial sobre la rotación de claves y la auditoría de registros con Azure Key Vault, consulte [Configuración de Key Vault con rotación de claves y auditoría integrales](key-vault-key-rotation-log-monitoring.md).
