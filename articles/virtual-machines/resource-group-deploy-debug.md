<properties
   pageTitle="Solución de problemas de implementaciones de grupo de recursos en Azure"
	description="Describe los problemas habituales que se dan al implementar recursos en Azure, y muestra cómo usar el Portal de Azure, la interfaz de línea de comandos de Azure para Mac, Linux, Windows (CLI de Azure) y PowerShell para examinar las implementaciones y detectar problemas."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="command-line-interface"
	ms.workload="infrastructure"
	ms.date="08/26/2015"
	ms.author="rasquill"/>

# Solución de problemas de implementaciones de grupo de recursos en Azure

Las implementaciones pueden tener errores por varios motivos. Es mucho mejor evitar errores de implementación mediante la comprobación de antemano de un par de cosas. Este documento describe herramientas y operaciones para evitar errores sencillos, descargar archivos de plantilla y examinar registros de implementación. También describe los principales aspectos que hay que tener en cuenta al buscar errores en los registros de implementación.

## Herramientas útiles para interactuar con Azure
Cuando trabaje con los recursos de Azure desde la línea de comandos, recopilará herramientas que le ayudarán a realizar su trabajo. Las plantillas de grupo de recursos de Azure son documentos JSON, y la API de administración de recursos de Azure acepta y devuelve JSON; así pues, las herramientas de análisis de JSON son uno de los primeros utensilios que podrán ayudarle a navegar por la información sobre los recursos, así como diseñar o interactuar con plantillas y archivos de parámetros de plantilla.

### Herramientas de Windows, Linux y Mac
Si usa la interfaz de línea de comandos de Azure para Mac, Linux y Windows, probablemente esté familiarizado con las herramientas estándar de descarga como **[curl](http://curl.haxx.se/)** y **[wget](https://www.gnu.org/software/wget/)**, o **[Resty](https://github.com/beders/Resty)**, y las utilidades de JSON como **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** y las bibliotecas de lenguaje que sirven para administrar JSON correctamente. (Muchas de estas herramientas también tienen puertos para Windows, como [wget](http://gnuwin32.sourceforge.net/packages/wget.htm); de hecho, hay varias formas de conseguir que Linux y otras herramientas de software de código abierto se ejecuten también en Windows).

Este tema incluye algunos comandos de CLI de Azure que puede utilizar con **jq** para obtener exactamente la información que desee de forma más eficaz. Debe elegir el conjunto de herramientas que le resulte cómodo para comprender el uso de los recursos de Azure.

### Windows PowerShell

Windows PowerShell tiene varios comandos básicos para realizar las mismas acciones.

- Utilice el cmdlet **[Invoke-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)** para descargar archivos como plantillas de grupo de recursos o archivos JSON de parámetros.
- Utilice el cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)** para convertir una cadena JSON en un objeto personalizado ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)) que tiene una propiedad para cada campo de la cadena JSON.

## Prevención de errores en la CLI de Azure para Mac, Linux y Windows

La CLI de Azure tiene diversos comandos para ayudar a evitar errores y detectar comportamientos incorrectos cuando estos se producen.

- **azure location list**. Este comando obtiene las ubicaciones que admiten cada tipo de recurso, como el proveedor para las máquinas virtuales. Antes de especificar una ubicación para un recurso, use este comando para comprobar si la ubicación admite el tipo de recurso.

    Dado que la lista de ubicaciones puede ser larga y hay muchos proveedores, puede utilizar herramientas para examinar los proveedores y las ubicaciones antes de utilizar una ubicación que aún no esté disponible. El siguiente script usa **jq** para detectar las ubicaciones donde está disponible el proveedor de recursos para Máquinas virtuales de Azure. ()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate <resource group>**. Este comando valida sus plantillas y parámetros de plantilla antes de usarlos. Especifique una plantilla personalizada o de la galería y los valores de los parámetros de la plantilla que pretende usar.

    En el ejemplo siguiente se muestra cómo validar una plantilla y los parámetros necesarios. La CLI de Azure solicita valores de parámetros que son necesarios.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Obtención de información para solucionar problemas de implementación con la CLI de Azure

- **azure group log show <resource group>**: este comando obtiene las entradas en el registro para cada implementación del grupo de recursos. Si algo va mal, empiece por examinar los registros de implementación.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

Use la opción **--última implementación** para recuperar solo el registro para la implementación más reciente. El script siguiente usa la opción **--json** y **jq** para buscar el registro de errores de implementación.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **Opciones --verbose y -vv**: utilice la opción **--verbose** para establecer el modo en detallado a fin de mostrar los pasos que recorren las operaciones `stdout`. Para que el historial de solicitud completo incluya los pasos que habilita **--verbose**, use la opción **-vv**. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

- **Las credenciales de Azure no se han configurado o han caducado**: para actualizar las credenciales de la sesión de CLI de Azure, basta con escribir `azure login`. Para obtener ayuda con errores de autenticación, asegúrese de que ha [configurado correctamente la CLI de Azure](../xplat-cli-connect.md).

## Prevención de errores en Windows PowerShell

El módulo AzureResourceManager incluye cmdlets que le ayudan a prevenir errores.


- **Get-AzureLocation**: este cmdlet obtiene las ubicaciones que admiten cada tipo de recurso. Antes de especificar una ubicación para un recurso, use este cmdlet para comprobar si la ubicación admite el tipo de recurso.


- **Test-AzureResourceGroupTemplate**: pruebe la plantilla y el parámetro de plantilla antes de utilizarlos. Especifique una plantilla personalizada o de la galería y los valores de los parámetros de la plantilla que pretende usar. Este cmdlet prueba si la plantilla es coherente internamente y si el conjunto de valores de los parámetros coincide con la plantilla.

## Obtención de información para solucionar problemas de implementación en Windows PowerShell

- **Get-AzureResourceGroupLog**: este cmdlet obtiene las entradas del registro para cada implementación del grupo de recursos. Si algo va mal, empiece por examinar los registros de implementación.

- **Verbose y Debug**: los cmdlets del módulo AzureResourceManager llaman a las API de REST que hacen el trabajo real. Para ver los mensajes que las API devuelven, defina la variable $DebugPreference en "Continue" y use el parámetro común Verbose en los comandos. Los mensajes suelen ofrecer pistas fundamentales sobre la causa de cualquier error.

- **Las credenciales de Azure no se han configurado o han caducado**: para actualizar las credenciales de la sesión de Windows PowerShell, use el cmdlet **Add-AzureAccount**. Las credenciales de un archivo de configuración de publicación no son suficientes para los cmdlets del módulo AzureResourceManager.

## Problemas de autenticación, suscripción, rol y cuota

Puede haber uno o varios problemas que impiden la correcta implementación y que guardan relación con la autenticación y la autorización y Azure Active Directory. Independientemente de cómo administre los grupos de recursos de Azure, la identidad que se use para iniciar sesión en su cuenta debe estar formada por objetos de Azure Active Directory o entidades de servicio, lo que también se denomina cuentas profesionales o educativas, o bien identificadores organizativos.

Sin embargo, Azure Active Directory permite al usuario o al administrador controlar qué identidades pueden tener acceso a qué recursos con un alto grado de precisión. Si se producen errores en las implementaciones, examine las propias solicitudes en busca de indicios de problemas de autenticación o autorización, y examine también los registros de implementación para el grupo de recursos. Observará que, aunque tiene permisos para algunos recursos, no tiene permisos para otros. Con la CLI de Azure, puede examinar los inquilinos de Azure Active Directory y los usuarios que usen los comandos `azure ad`. (Para obtener una lista completa de los comandos de CLI de Azure, consulte [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](azure-cli-arm-commands.md)).

También puede tener problemas cuando una implementación llega a cuota predeterminada, que podría haberse establecido por grupo de recursos, suscripciones, cuentas, etc. Confirme que dispone de los recursos disponibles para implementar correctamente. Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).

Para examinar las cuotas de su propia suscripción para núcleos, debería usar el comando `azure vm list-usage` en la CLI de Azure y el cmdlet **Get-AzureVMUsage** de PowerShell. A continuación se muestra el comando en la CLI de Azure y la cuota de núcleos para una cuenta de evaluación gratuita es 4:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si tuviera que intenta implementar una plantilla que crea más de 4 núcleos en la región occidental de Estados Unidos en la suscripción anterior, obtendría un error de implementación que podría ser similar al siguiente (en el portal o investigando los registros de implementación):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

En estos casos, debe ir al portal y archivar un problema de soporte técnico para aumentar su cuota para la región en la que desea realizar la implementación.

> [AZURE.NOTE] Recuerde que para los grupos de recursos, la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en el oeste de Estados Unidos, debe pedir 30 núcleos de administrador de recursos en el oeste de Estados Unidos. Si necesita implementar 30 núcleos en cualquiera de las regiones para las que tiene acceso, debe pedir 30 núcleos de administrador de recursos en todas las regiones.
<!-- -->
Para ser específicos sobre núcleos, por ejemplo, puede comprobar las regiones para las que debe solicitar la cantidad adecuada de cuota mediante el comando siguiente, que se canaliza en **jq** para el análisis de json. El proveedor de Azure de
<!-- -->
        muestra Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}'
        {
          "name": "virtualMachines",
          "apiVersions": [
            "2015-05-01-preview",
            "2014-12-01-preview"
          ],
          "locations": [
            "Este de Estados Unidos",
            "Oeste de Estados Unidos",
            "Europa occidental",
            "Asia oriental", "Sudeste de Asia"
          ]
        }


## Problemas del modo de CLI de Azure y PowerShell

Puede que haya observado que los recursos implementados mediante la API de administración de servicio o el portal no son visibles al usar la API de administrador de recursos o el portal de Azure. Es importante administrar los recursos con la misma API de administrador o el mismo portal que se usó para crearlos. Si un recurso ha desaparecido, compruebe si está disponible mediante la otra API de administración o el otro portal.

## Problemas de registro del proveedor de recursos de Azure

Los recursos son administrados por los proveedores de recursos, y es posible que una cuenta o suscripción esté habilitada para utilizar un proveedor determinado. Si están habilitadas para utilizar un proveedor, también se deben registrar para su uso. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos.

Para ver si el proveedor está registrado para su uso con la CLI de Azure, use el comando `azure provider list` (lo siguiente es un ejemplo truncado del resultado).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Nuevamente, si desea obtener más información sobre los proveedores, incluida su disponibilidad regional, escriba `azure provider list --json`. Lo siguiente selecciona solo la primera de ellas en la lista que se va a ver:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


Si un proveedor requiere el registro, use el comando `azure provider register <namespace>`, donde el valor *namespace* proviene de la lista anterior.

## Descripción de una implementación correcta para las plantillas personalizadas

Si utiliza las plantillas que ha creado, es importante comprender que el sistema de Administrador de recursos de Azure informa de que una implementación se ha completado correctamente cuando todos los proveedores terminan la implementación correctamente. Esto significa que todos los elementos de plantilla se implementaron para su uso.

Sin embargo, tenga en cuenta que esto no significa necesariamente que el grupo de recursos esté "activo y listo para sus usuarios". Por ejemplo, la mayoría de las implementaciones solicitan a la implementación que descargue actualizaciones, que espere en otros recursos no son plantillas o que instale complejos scripts o alguna otra actividad ejecutable que Azure no conoce porque no es una actividad de la que un proveedor esté realizando seguimiento. En estos casos, puede trascurrir algo de tiempo antes de que los recursos estén disponibles para su utilización por parte de todos los usuarios. Por ello, debe esperar a cierto tiempo tras la confirmación del estado de implementación antes de que la implementación se pueda utilizar realmente.

Sin embargo, puede evitar que Azure informe de que la implementación se produjo correctamente mediante la creación de un script personalizado para su plantilla personalizada (usando por ejemplo [CustomScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)) que sepa cómo supervisar toda la implementación para la preparación de todo el sistema y se devuelva correctamente solo cuando los usuarios puedan interactuar con toda la implementación. Si desea asegurarse de que su extensión es la última en ejecutarse, utilice la propiedad **dependsOn** de la plantilla. Puede ver un ejemplo [aquí](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Combinación de las plantillas

En ocasiones puede que necesite combinar dos plantillas, o puede que necesite generar una plantilla secundaria a partir de una primaria. Esto se puede lograr mediante la utilización de un recurso de implementación dentro de la plantilla principal para implementar una plantilla secundaria.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Uso cruzado de grupos de recursos

Es habitual querer utilizar un recurso desde fuera del grupo de recursos actual donde se está implementando una plantilla. La situación de este tipo más común responde al uso de una cuenta de almacenamiento o una red virtual en un grupo de recursos alternativo. A menudo, esto es necesario para que la eliminación del grupo de recursos que contiene las máquinas virtuales no dé como resultado la eliminación de los blobs de un disco duro virtual o de una red virtual que usan varios grupos de recursos. En el ejemplo siguiente se muestra cómo un recurso de un grupo de recursos externos se puede usar:


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }

## Pasos siguientes

Para dominar la creación de plantillas, lea [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md) y visite el [repositorio de plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates) para obtener ejemplos implementables. Un ejemplo de la propiedad **dependsOn** es [Creación de una máquina virtual con varias NIC y con acceso a RDP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!----HONumber=September15_HO1-->