<properties
   pageTitle="Solución de errores comunes de implementación de Azure | Microsoft Azure"
   description="Describe cómo solucionar errores comunes al implementar recursos en Azure con Azure Resource Manager."
   services="azure-resource-manager"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   keywords="error de implementación, implementación de Azure, implementar en Azure"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="tomfitz"/>

# Solución de errores comunes de implementación de Azure con Azure Resource Manager

En este tema se describe cómo resolver algunos errores comunes con los que puede encontrarse al realizar una implementación de Azure. Si necesita más información sobre el motivo del error de implementación, consulte primero [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-troubleshoot-deployments-portal.md) y luego vuelva a este artículo con el fin de obtener ayuda para solucionar el error.

## Plantilla o recurso no válidos

Al implementar una plantilla, puede aparecer:

    Code=InvalidTemplate 
    Message=Deployment template validation failed

Si recibe un error que indica que la plantilla o una propiedad de un recurso no es válida, es posible que haya un error de sintaxis en la plantilla. Es fácil cometer este error porque las expresiones de plantilla pueden ser complejas. Por ejemplo, la siguiente asignación de nombre de una cuenta de almacenamiento contiene un conjunto de corchetes, tres funciones, tres conjuntos de paréntesis, un conjunto de comillas simples y una propiedad:

    "name": "[concat('storage', uniqueString(resourceGroup().id))]",

Si no proporciona toda la sintaxis de coincidencia, la plantilla generará un valor que es muy diferente del que esperaba.

Si recibe este tipo de error, revise cuidadosamente la sintaxis de las expresiones. Considere la posibilidad de utilizar un editor JSON como [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) o [Visual Studio Code](resource-manager-vs-code.md) que puede avisarle de los errores de sintaxis.

## Longitudes de segmentos incorrectas

Cuando el nombre del recurso no tiene el formato correcto, se produce otro error de plantilla no válida.

    Code=InvalidTemplate
    Message=Deployment template validation failed: 'The template resource {resource-name}' 
    for type {resource-type} has incorrect segment lengths.

Un recurso de nivel de raíz debe tener un segmento menos en el nombre que en el tipo de recurso. Cada segmento se distingue por una barra diagonal. En el ejemplo siguiente, el tipo tiene 2 segmentos y el nombre tiene 1 segmento, por lo que es un **nombre válido**.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "myHostingPlanName",

Pero el ejemplo siguiente **no es un nombre válido** porque el nombre tiene el mismo número de segmentos que el tipo.

    {
      "type": "Microsoft.Web/serverfarms",
      "name": "appPlan/myHostingPlanName",

Para los recursos secundarios, el tipo y el nombre deben tener el mismo número de segmentos. Esto tiene sentido ya que el nombre completo y el tipo para el elemento secundario incluyen el nombre del elemento primario y el tipo, por lo que el nombre completo sigue teniendo un segmento de menos que el tipo completo.

    "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "contosokeyvault",
            ...
            "resources": [
                {
                    "type": "secrets",
                    "name": "appPassword",

Obtener los segmentos correctos puede resultar especialmente complicado con los tipos de Resource Manager que se aplican en los proveedores de recursos. Por ejemplo, aplicar un bloqueo de recurso a un sitio web requiere un tipo con 4 segmentos. Por lo tanto, el nombre es 3 segmentos:

    {
        "type": "Microsoft.Web/sites/providers/locks",
        "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",

## El nombre del recurso ya existe o ya lo utiliza otro recurso

Para algunos recursos, sobre todo cuentas de almacenamiento, servidores de base de datos y sitios web, debe proporcionar un nombre para el recurso que sea único en todo Azure. Si no proporciona un nombre único, recibirá un error como:

    Code=StorageAccountAlreadyTaken 
    Message=The storage account named mystorage is already taken.

Puede crear un nombre único concatenando la convención de nomenclatura con el resultado de la función [uniqueString](resource-group-template-functions.md#uniquestring).

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

## No se pudo encontrar el miembro 'copy' en el objeto

Este error se produce cuando se ha aplicado el elemento **copy** a una parte de la plantilla que no lo admite. Solo se puede aplicar el elemento copy a un tipo de recurso. No se puede aplicar el elemento copy a una propiedad de un tipo de recurso. Por ejemplo, puede aplicar el elemento copy a una máquina virtual, pero no puede aplicarlo a los discos del sistema operativo de una máquina virtual. En algunos casos, puede convertir un recurso secundario en un recurso primario para crear un bucle del elemento copy. Para más información sobre cómo usar este elemento, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).

## SKU no disponible

Al implementar un recurso (normalmente una máquina virtual), puede recibir el siguiente código y mensaje de error:

    Code: SkuNotAvailable
    Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.

Recibirá este error si la SKU del recurso que ha seleccionado (como, por ejemplo, el tamaño de máquina virtual) no está disponible para la ubicación seleccionada. Tiene dos opciones para resolver este problema:

1.	Inicie sesión en el portal y agregue un nuevo recurso a través de la interfaz de usuario. A medida que establezca los valores, verá las SKU disponibles para ese recurso.

    ![sku disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

2.	Si no puede encontrar una SKU adecuada en esa región ni en ninguna región alternativa que satisfaga las necesidades de su negocio, póngase en contacto con el [soporte técnico de Azure](https://portal.azure.com/#create/Microsoft.Support).


## No se encontró el proveedor registrado

Al implementar recursos, puede recibir el siguiente código y mensaje de error:

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location '<location>' and API version '<api-version>' for type '<resource-type>'.

Recibirá este error por uno de estos tres motivos:

1. No se permite esta ubicación para el tipo de recurso
2. No se permite esta versión de API para el tipo de recurso
3. El proveedor de recursos no se ha registrado para la suscripción

El mensaje de error debería proporcionarle sugerencias con respecto a las versiones de API y a las ubicaciones admitidas. Puede cambiar la plantilla a uno de los valores sugeridos. La mayoría de los proveedores se registran automáticamente mediante el Portal de Azure o la interfaz de línea de comandos que esté utilizando; pero no ocurre con todos. Si no ha utilizado un proveedor de recursos determinado antes, debe registrar dicho proveedor. Encontrará más información acerca de los proveedores de recursos con los siguientes comandos.

### PowerShell

Para ver el estado de su registro, use **Get-AzureRmResourceProvider**.

    Get-AzureRmResourceProvider -ListAvailable

Para registrar un proveedor, use **Register-AzureRmResourceProvider** e indique el nombre del proveedor de recursos que desea registrar.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Para conocer las ubicaciones admitidas para un tipo determinado de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Para conocer las versiones de API admitidas para un tipo determinado de recurso, use:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

### Azure CLI

Para ver si el proveedor está registrado, utilice el comando `azure provider list`.

    azure provider list

Para registrar un proveedor de recursos, use el comando `azure provider register` y especifique el *espacio de nombres* que desea registrar.

    azure provider register Microsoft.Cdn

Para ver las ubicaciones y las versiones de API admitidas por un proveedor de recursos, use:

    azure provider show -n Microsoft.Compute --json > compute.json

## Cuota superada

Podría tener problemas cuando una implementación supera una cuota, lo que podría suceder por grupo de recursos, suscripciones, cuentas y otros ámbitos. Por ejemplo, la suscripción puede configurarse para limitar el número de núcleos para una región. Si intenta implementar una máquina virtual con más núcleos que la cantidad permitida, recibirá un error que indica que se ha superado la cuota. Para obtener información completa de las cuotas, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](azure-subscription-service-limits.md).

Para examinar las cuotas de núcleos de su suscripción, puede usar el comando `azure vm list-usage` en la CLI de Azure. En el siguiente ejemplo se muestra que la cuota de núcleos para una cuenta de evaluación gratuita es 4:

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

O bien en PowerShell, puede emplear el cmdlet **Get-AzureRmVMUsage**.

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

Para más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

Además del control de acceso basado en roles, las acciones de implementación pueden estar limitadas por las directivas de la suscripción. A través de directivas, el administrador puede exigir convenciones en todos los recursos implementados en la suscripción. Por ejemplo, un administrador puede requerir que se proporcione un valor de etiqueta específico para un tipo de recurso. Si no ha cumplido los requisitos de la directiva, recibirá un error durante la implementación. Para más información sobre las directivas, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

## Solución de problemas con máquinas virtuales

| Error | Artículos |
| -------- | ----------- |
| Errores de extensión de script personalizado | [Errores de extensión de máquina virtual Windows](./virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)<br />o<br />[Errores de extensión de máquina virtual Linux](./virtual-machines/virtual-machines-linux-extensions-troubleshoot.md) |
| Errores de aprovisionamiento de imágenes de sistema operativo | [Errores de la nueva máquina virtual Windows](./virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md)<br />o<br />[Errores de la nueva máquina virtual Linux](./virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md) |
| Errores de asignación | [Errores de asignación de máquina virtual Windows](./virtual-machines/virtual-machines-windows-allocation-failure.md)<br />o<br />[Errores de asignación de máquina virtual Linux](./virtual-machines/virtual-machines-linux-allocation-failure.md) |
| Errores de Secure Shell (SSH) al intentar conectarse | [Conexiones de Secure Shell a máquina virtual Linux](./virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md) |
| Errores de conexión a una aplicación que se ejecuta en una máquina virtual | [Aplicación que se ejecuta en una máquina virtual Windows](./virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md)<br />o<br />[Aplicación que se ejecuta en una máquina virtual Linux](./virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md) |
| Errores de conexión del Escritorio remoto | [Conexiones del Escritorio remoto a máquinas virtuales Windows](./virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md) |
| Errores de conexión resueltos mediante la repetición de la implementación | [Nueva implementación de la máquina virtual en un nuevo nodo de Azure](./virtual-machines/virtual-machines-windows-redeploy-to-new-node.md) |
| Errores de servicio en la nube | [Problemas de implementación de servicio en la nube](./cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

## Solución de problemas con otros servicios

La tabla siguiente no es una lista completa de temas de solución de problemas de Azure. En su lugar, se centra en los problemas relacionados con la implementación o la configuración de recursos. Si necesita ayuda para solucionar problemas de tiempo de ejecución con un recurso, consulte la documentación de ese servicio de Azure.

| Servicio | Artículo |
| -------- | -------- |
| Automatización | [Sugerencias para la solución de problemas para errores comunes de Automatización de Azure](./automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack | [Solución de problemas de Microsoft Azure Stack](./azure-stack/azure-stack-troubleshooting.md) |
| Azure Stack | [Aplicaciones Web y Azure Stack](./azure-stack/azure-stack-webapps-troubleshoot-known-issues.md) |
| Factoría de datos | [Solución de problemas de la factoría de datos](./data-factory/data-factory-troubleshoot.md) |
| Service Fabric | [Solución de problemas comunes al implementar servicios en Azure Service Fabric](./service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Recuperación de sitios | [Protección de supervisión y solución de problemas para las máquinas virtuales y los servidores físicos](./site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Almacenamiento | [Supervisión, diagnóstico y solución de problemas de Almacenamiento de Microsoft Azure](./storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple | [Solución de problemas de implementación de dispositivos de StorSimple](./storsimple/storsimple-troubleshoot-deployment.md) |
| Base de datos SQL | [Solución de problemas de conexión comunes relacionados con la base de datos SQL de Azure](./sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| Almacenamiento de datos SQL | [Solución de problemas de Almacenamiento de datos SQL de Azure](./sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## Comprensión de cuándo una implementación está lista

Azure Resource Manager notifica que una implementación se realizó correctamente cuando todos los proveedores regresan de la implementación correctamente. Sin embargo, tenga en cuenta que esto no significa necesariamente que el grupo de recursos esté "activo y listo para sus usuarios". Por ejemplo, una implementación puede que necesite descargar actualizaciones, esperar en otros recursos que no son plantillas o instalar complejos scripts o alguna otra actividad ejecutable que Azure no conoce porque no es una actividad de la que un proveedor esté realizando seguimiento. En estos casos, puede trascurrir algo de tiempo antes de que los recursos estén disponibles para su utilización por parte de todos los usuarios. Por ello, debe esperar a cierto tiempo tras la confirmación del estado de implementación antes de que la implementación se pueda utilizar realmente.

Sin embargo, puede evitar que Azure informe de que la implementación se produjo correctamente mediante la creación de un script personalizado para su plantilla personalizada (usando por ejemplo [CustomScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)) que sepa cómo supervisar toda la implementación para la preparación de todo el sistema y se devuelva correctamente solo cuando los usuarios puedan interactuar con toda la implementación. Si desea asegurarse de que su extensión es la última en ejecutarse, utilice la propiedad **dependsOn** de la plantilla.

## Pasos siguientes

- Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
- Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0720_2016-->