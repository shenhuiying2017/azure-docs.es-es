
<properties
	pageTitle="Administración de recursos con la CLI de Azure | Microsoft Azure"
	description="Utilice la interfaz de línea de comandos (CLI) de Azure para administrar recursos y grupos de Azure"
	editor=""
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="danlep"/>

# Uso de la CLI de Azure para administrar los recursos y grupos de recursos de Azure


> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [API DE REST](resource-manager-rest-api.md)


La interfaz de línea de comandos de Azure (CLI de Azure) es una de las diversas herramientas que puede usar para implementar y administrar recursos con Resource Manager. En este artículo se describen formas habituales de administrar los recursos y los grupos de recursos de Azure con la CLI de Azure en el modo Resource Manager. Para más información sobre el uso de CLI para implementar recursos, consulte [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](resource-group-template-deploy-cli.md). Para más información sobre el Administrador de recursos de Azure, consulte [Información general Azure Resource Manager](resource-group-overview.md).

>[AZURE.NOTE] Para administrar recursos de Azure con la CLI de Azure, necesitará [instalar la CLI de Azure](xplat-cli-install.md) e [iniciar sesión en Azure](xplat-cli-connect.md) mediante el comando `azure login`. Asegúrese de que la CLI está en modo de Resource Manager (ejecute `azure config mode arm`). Si ha realizado estas acciones, ya está preparado para comenzar.



## Obtención de recursos y grupos de recursos

### Grupos de recursos

Para obtener una lista de todos los grupos de recursos de la suscripción y sus ubicaciones, ejecute este comando.

    azure group list
    

### Recursos
 Para ver todos los recursos de un grupo, como el denominado *testRG*, utilice el comando siguiente.

	azure resource list testRG

Para ver un recurso individual dentro del grupo, como la máquina virtual denominada *MyUbuntuVM*, use un comando similar al siguiente.

	azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"
    
Observe el parámetro **Microsoft.Compute/virtualMachines**. Este parámetro indica el tipo del recurso sobre el que solicita información.
    
>[AZURE.NOTE]Cuando utilice los comandos **azure resource** distintos del comando **list**, debe especificar la versión de API del recursos con el parámetro **-o**. Si no está seguro sobre la versión de API, consulte el archivo de plantilla y busque el campo apiVersion correspondiente al recurso. Para más información sobre las versiones de API de Resource Manager, consulte [Proveedores, regiones, versiones de API y esquemas de Resource Manager](resource-manager-supported-services.md).

Cuando se consultan los detalles de un recurso, generalmente resulta útil usar el parámetro `--json`. Este parámetro hace que el resultado sea más legible, ya que algunos valores son estructuras anidadas o colecciones. El siguiente ejemplo demuestra los resultados obtenidos con el comando **show** como un documento JSON.

	azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json

>[AZURE.NOTE] Si lo desea, guarde los datos JSON en un archivo con el carácter &gt; para dirigir la salida a un archivo. Por ejemplo:
>
> `azure resource show testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15" --json > myfile.json`

### Etiquetas

Para organizar mejor sus recursos, agregue [etiquetas](resource-group-using-tags.md) a los recursos y grupos de recursos. Para ver qué etiquetas ya están aplicadas, solo debe obtener un grupo de recursos y sus recursos con **azure group show**.

    azure group show -n tag-demo-group
    
Este comando devuelve metadatos sobre el grupo de recursos, incluidas las etiquetas aplicadas al mismo.
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Para obtener las etiquetas solo para el grupo de recursos, use una utilidad de JSON como [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    
Este comando devuelve las etiquetas de ese grupo de recursos.
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Vea las etiquetas de un recurso específico con **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
Este comando devuelve lo siguiente.
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
Recupere todos los recursos con una etiqueta determinada mediante un comando similar al siguiente.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
Este comando devuelve los nombres de los recursos con esa etiqueta.
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Las etiquetas se actualizan como un todo, por lo que si agrega una etiqueta a un recurso que ya se ha etiquetado, tiene que recupere las etiquetas existentes que desee conservar. Para establecer los valores de las etiquetas de un grupo de recursos, use **azure group set** y proporcione todas las etiquetas del grupo de recursos.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
Se devuelve un resumen del grupo de recursos con las nuevas etiquetas.
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Para enumerar las etiquetas existentes en una suscripción, use **azure tag list**, mientras que para agregar una etiqueta nueva, use **azure tag create**. Para quitar una etiqueta de la taxonomía de una suscripción, quítela primero de los recursos con los que se usa y, luego, quítela con **azure tag delete**.

## Administración de recursos


Para agregar un recurso como una cuenta de almacenamiento a un grupo de recursos, ejecute un comando similar al siguiente:

    azure resource create testRG MyStorageAccount "Microsoft.Storage/storageAccounts" "westus" -o "2015-06-15" -p "{"accountType": "Standard_LRS"}"
    
Además de especificar la versión de API del recurso con el parámetro **-o**, use el parámetro **-p** que pasa una cadena con formato JSON con cualquier propiedad necesaria o adicional.
    
    
Para eliminar un recurso existente, como un recurso de máquina virtual, use un comando como el siguiente.

	azure resource delete testRG MyUbuntuVM Microsoft.Compute/virtualMachines -o "2015-06-15"

Para mover recursos existentes a otro grupo de recursos o a otra suscripción, use el comando **azure resource move**. En el siguiente ejemplo se muestra cómo trasladar una Caché de Redis a un nuevo grupo de recursos. En el parámetro **-i**, ofrezca una lista separada por comas del id. de recurso que se va a mover.


    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"

## Control de acceso a los recursos

Puede utilizar la CLI de Azure para crear y administrar directivas para controlar el acceso a los recursos de Azure. Para más información acerca de las definiciones de directivas y de la asignación de directivas a los recursos, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

Por ejemplo, defina la siguiente directiva para denegar todas las solicitudes cuya ubicación no sea oeste de Estados Unidos o centro-norte de Estados Unidos y guardarla en el archivo de definición de directivas policy.json:

    {
    "if" : {
        "not" : {
        "field" : "location",
        "in" : ["westus" ,  "northcentralus"]
        }
    },
    "then" : {
        "effect" : "deny"
    }
    }

A continuación, ejecute el comando **policy definition create**:

    azure policy definition create MyPolicy -p c:\temp\policy.json
    
Este comando muestra una salida similar a la siguiente.

    + Creating policy definition MyPolicy
    data:    PolicyName:             MyPolicy
    data:    PolicyDefinitionId:     /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy

    data:    PolicyType:             Custom
    data:    DisplayName:            undefined
    data:    Description:            undefined
    data:    PolicyRule:             field=location, in=[westus, northcentralus], effect=deny

 Para asignar una directiva en el ámbito que desee, utilice el atributo **PolicyDefinitionId** devuelto por el comando anterior. En el ejemplo siguiente, este ámbito es la suscripción, pero puede restringir el ámbito a recursos individuales o a grupos de recursos:

    azure policy assignment create MyPolicyAssignment -p /subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/MyPolicy -s /subscriptions/########-####-####-####-############/

Puede obtener, cambiar o quitar definiciones de directivas mediante los comandos **policy definition show**, **policy definition set** y **policy definition delete**.

De igual forma, puede obtener, cambiar o quitar asignaciones de directivas mediante los comandos **policy assignment show**, **policy assignment set** y **policy assignment delete**.


## Exportación de un grupo de recursos como una plantilla

Para un grupo de recursos existente, puede ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.

2. Para familiarizarse con la sintaxis de la plantilla, consulte el JSON que representa la solución.

Con la CLI de Azure, puede exportar una plantilla que representa el estado actual de su grupo de recursos o descargar la plantilla que se usó para una implementación determinada.

* **Exportar la plantilla para un grupo de recursos**: resulta útil cuando se han realizado cambios en un grupo de recursos y necesita recuperar la representación JSON del estado actual. Sin embargo, la plantilla generada contiene solo un número mínimo de parámetros y ninguna variable. La mayoría de los valores de la plantilla está codificados. Antes de implementar la plantilla generada, quizás desee convertir más valores en parámetros para poder personalizar la implementación para distintos entornos.

    Para exportar la plantilla para un grupo de recursos en un directorio local, ejecute el comando `azure group export` como se muestra en el ejemplo siguiente. (Sustituya un directorio local adecuado para su entorno de sistema operativo).

        azure group export testRG ~/azure/templates/

* **Descargar la plantilla para una implementación concreta**: resulta útil si necesita ver la plantilla real que se usó para implementar recursos. La plantilla incluye todos los parámetros y las variables definidas para la implementación original. Sin embargo, si alguien de su organización realiza cambios en el grupo de recursos fuera de lo que se define en la plantilla, esta plantilla no representa el estado actual del grupo de recursos.

    Para descargar la plantilla usada para una implementación concreta en un directorio local, ejecute el comando `azure group deployment template download`. Por ejemplo:

        azure group deployment template download TestRG testRGDeploy ~/azure/templates/downloads/
 
>[AZURE.NOTE] La exportación de plantillas es una versión preliminar y no todos los tipos de recursos admiten actualmente la exportación de una plantilla. Al intentar exportar una plantilla, verá un error que indica que algunos recursos no se han exportado. Si es necesario, defina manualmente estos recursos en la plantilla después de descargarla.



## Pasos siguientes

* Para obtener detalles de las operaciones de implementación y solucionar los errores de implementación con la CLI de Azure, consulte [Visualización de operaciones de implementación con la CLI de Azure](resource-manager-troubleshoot-deployments-cli.md).
* Si desea utilizar la CLI para configurar una aplicación o un script con objeto de obtener acceso a los recursos, consulte [Uso de la CLI de Azure para crear a una entidad de servicio para acceder a recursos](resource-group-authenticate-service-principal-cli.md).

<!---HONumber=AcomDC_0914_2016-->