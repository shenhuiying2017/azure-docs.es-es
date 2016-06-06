<properties
   pageTitle="Solución de errores comunes de implementación de Azure | Microsoft Azure"
   description="Describe cómo resolver errores comunes durante la implementación con Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="tomfitz"/>

# Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager

En este tema se describe cómo resolver algunos de los errores comunes que puede encontrarse al implementar recursos en Azure. Se asume la aparición de un mensaje descriptivo del error. Si necesita conocer más detalles sobre el motivo del error de la implementación, consulte el artículo sobre [visualización de operaciones de implementación](resource-manager-troubleshoot-deployments-portal.md).

## Plantilla o recurso no válidos

Si recibe un error que indica que la plantilla o una propiedad de un recurso no es válida, puede que falte un carácter en la plantilla. Este error es fácil de cometer cuando utiliza expresiones de plantilla porque la expresión está incluida entre comillas, por lo que JSON lo valida y el editor puede no detectar el error. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si no proporciona toda la sintaxis de coincidencia, la plantilla generará un valor que es muy diferente del que esperaba.

En función de si el carácter que falta está ubicado en la plantilla, recibirá un error que indica que la plantilla o un recurso no son válidos. El error puede indicar también que el proceso de implementación no pudo procesar la expresión del lenguaje de plantilla. Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones.

## El nombre de recurso ya existe

Para algunos recursos, sobre todo cuentas de almacenamiento, servidores de base de datos y sitios web, debe proporcionar un nombre para el recurso que sea único en todo Azure. Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función [uniqueString](./resource-group-template-functions/#uniquestring).
 
    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 

## No se encuentra el recurso durante la implementación

Cuando es posible, Resource Manager optimiza la implementación mediante la creación de recursos en paralelo. Si un recurso se debe implementar después de otro recurso, debe usar el elemento **dependsOn** en la plantilla para crear una dependencia en el otro recurso. Por ejemplo, al implementar una aplicación web, debe existir el plan de Servicio de aplicaciones. Si no ha especificado que la aplicación web depende del plan de Servicio de aplicaciones, Resource Manager creará ambos recursos al mismo tiempo. Recibirá un error que indica que no se encuentra el recurso del plan de Servicio de aplicaciones, porque aún no existe cuando se intenta establecer una propiedad en la aplicación web. Este error se puede evitar estableciendo la dependencia en la aplicación web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

## Ubicación no disponible para el recurso

Al especificar la ubicación para un recurso, debe usar una de las ubicaciones que admiten el recurso. Antes de especificar una ubicación para un recurso, use uno de los comandos siguientes para comprobar si la ubicación admite el tipo de recurso.

### PowerShell

Utilice **Get-AzureRmResourceProvider** si desea obtener ubicaciones y tipos admitidos para un proveedor de recursos determinado.

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

Obtendrá una lista de los tipos de recursos para ese proveedor de recursos.

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Puede centrarse en las ubicaciones para un tipo determinado de recurso con:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Que devuelve las ubicaciones admitidas:

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure CLI

Para CLI de Azure, puede usar **azure location list**. Dado que la lista de ubicaciones puede ser larga y hay muchos proveedores, puede utilizar herramientas para examinar los proveedores y las ubicaciones antes de utilizar una ubicación que aún no esté disponible. El siguiente script usa **jq** para detectar las ubicaciones donde está disponible el proveedor de recursos para Máquinas virtuales de Azure.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    
Que devuelve las ubicaciones admitidas:
    
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### API de REST

Para la API de REST, consulte [Obtención de información sobre un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Cuota superada

Podría tener problemas cuando una implementación supera una cuota, lo que podría suceder por grupo de recursos, suscripciones, cuentas y otros ámbitos. Por ejemplo, la suscripción puede configurarse para limitar el número de núcleos para una región. Si intenta implementar una máquina virtual con más núcleos que la cantidad permitida, recibirá un error que indica que se ha superado la cuota. Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits.md).

Para examinar las cuotas de su suscripción respecto a núcleos, puede usar el comando `azure vm list-usage` en la CLI de Azure. En el siguiente ejemplo se muestra que la cuota de núcleos para una cuenta de evaluación gratuita es 4:

    azure vm list-usage
    
Que devuelve:
    
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

O bien, en PowerShell, puede emplear el cmdlet **Get-AzureRmVMUsage**.

    Get-AzureRmVMUsage
    
Que devuelve:

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

En estos casos, debe ir al portal y archivar un problema de soporte técnico para aumentar su cuota para la región en la que desea realizar la implementación.

> [AZURE.NOTE] Recuerde que para los grupos de recursos, la cuota para cada región individual, no para toda la suscripción. Si necesita implementar 30 núcleos en el oeste de Estados Unidos, debe pedir 30 núcleos de administrador de recursos en el oeste de Estados Unidos. Si necesita implementar 30 núcleos en cualquiera de las regiones para las que tiene acceso, debe pedir 30 núcleos de administrador de recursos en todas las regiones.


## Error de autorización

Puede recibir un error durante la implementación porque la cuenta o la entidad de servicio que intenta implementar los recursos no tiene acceso para realizar esas acciones. Azure Active Directory permite al usuario o al administrador controlar qué identidades pueden acceder a qué recursos con un alto grado de precisión. Por ejemplo, si su cuenta se asigna al rol Lector, no podrá crear nuevos recursos. En ese caso, debería ver un mensaje de error que indica que hubo un error de autorización.

Para obtener más información sobre el control de acceso basado en roles, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de Azure Active Directory](./active-directory/role-based-access-control-configure.md).

Además del control de acceso basado en roles, las acciones de implementación pueden estar limitadas por las directivas de la suscripción. A través de directivas, el administrador puede exigir convenciones en todos los recursos implementados en la suscripción. Por ejemplo, un administrador puede requerir que se proporcione un valor de etiqueta específico para un tipo de recurso. Si no ha cumplido los requisitos de la directiva, recibirá un error durante la implementación. Para obtener más información sobre las directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

## Comprobación del registro del proveedor de recursos

Los proveedores de recursos administran recursos y se debe registrar una cuenta o suscripción para utilizar un proveedor determinado. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos.

### PowerShell

Para ver su estado de registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Que devuelve todos los proveedores de recursos disponibles y su estado de registro:

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Para registrar un proveedor, use **Register-AzureRmResourceProvider** y proporcione el nombre del proveedor de recursos que desea registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Se le pedirá que confirme el registro y después se devolverá un estado.

    Confirm
    Are you sure you want to register the provider 'Microsoft.Cdn'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

    ProviderNamespace RegistrationState ResourceTypes
    ----------------- ----------------- -------------
    Microsoft.Cdn     Registering       {profiles, profiles/endpoints,...

### Azure CLI

Para ver si el proveedor está registrado para su uso con la CLI de Azure, use el comando `azure provider list` (lo siguiente es un ejemplo truncado del resultado).

    azure provider list
        
Que devuelve todos los proveedores de recursos disponibles y su estado de registro:
        
    info:    Executing command provider list
    + Getting ARM registered providers
    data:    Namespace                        Registered
    data:    -------------------------------  -------------
    data:    Microsoft.Compute                Registered
    data:    Microsoft.Network                Registered  
    data:    Microsoft.Storage                Registered
    data:    microsoft.visualstudio           Registered
    ...
    info:    provider list command OK

Para registrar un proveedor de recursos, use el comando `azure provider register` y especifique el *espacio de nombres* que desea registrar.

    azure provider register Microsoft.Cdn

### API de REST

Para obtener el estado de registro, consulte [Obtención de información sobre un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Para registrar un proveedor, consulte [Registro de una suscripción con un proveedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx).

## Errores de extensión de script personalizado

Si se produce un error con una extensión de script personalizado al implementar una máquina virtual, consulte [Solución de problemas de la extensión de máquina virtual de Microsoft Azure](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md) o [Solución de problemas de la extensión de máquina virtual de Linux Azure](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md).

## Errores de aprovisionamiento y asignación de máquinas virtuales

Si intenta implementar una máquina virtual y se ha producido un error de aprovisionamiento de la imagen del sistema operativo o de asignación, consulte [Solución de problemas de implementación de Resource Manager con la creación de una máquina virtual de Windows en Azure](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md) y [Solución de problemas de errores de asignación al crear, reiniciar o cambiar el tamaño de las VM de Windows en Azure](./virtual-machines/virtual-machines-windows-allocation-failure.md).

## Comprensión de cuándo una implementación está lista 

Azure Resource Manager notifica que una implementación se realizó correctamente cuando todos los proveedores regresan de la implementación correctamente. Sin embargo, tenga en cuenta que esto no significa necesariamente que el grupo de recursos esté "activo y listo para sus usuarios". Por ejemplo, una implementación puede que necesite descargar actualizaciones, esperar en otros recursos que no son plantillas o instalar complejos scripts o alguna otra actividad ejecutable que Azure no conoce porque no es una actividad de la que un proveedor esté realizando seguimiento. En estos casos, puede trascurrir algo de tiempo antes de que los recursos estén disponibles para su utilización por parte de todos los usuarios. Por ello, debe esperar a cierto tiempo tras la confirmación del estado de implementación antes de que la implementación se pueda utilizar realmente.

Sin embargo, puede evitar que Azure informe de que la implementación se produjo correctamente mediante la creación de un script personalizado para su plantilla personalizada (usando por ejemplo [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)) que sepa cómo supervisar toda la implementación para la preparación de todo el sistema y se devuelva correctamente solo cuando los usuarios puedan interactuar con toda la implementación. Si desea asegurarse de que su extensión es la última en ejecutarse, utilice la propiedad **dependsOn** de la plantilla.

## Pasos siguientes

- Para obtener más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Resolución de errores comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-troubleshoot-deployments-portal.md).
- Para solucionar errores de Protocolo de escritorio remoto en su máquina virtual de Windows, consulte [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md).
- Si desea solucionar errores de Secure Shell en su máquina virtual de Linux, consulte [conexiones de escritorio remoto solucionar](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

<!---HONumber=AcomDC_0525_2016-->