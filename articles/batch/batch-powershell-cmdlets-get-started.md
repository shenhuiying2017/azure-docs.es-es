<properties
   pageTitle="Introducción a PowerShell para Lote de Azure | Microsoft Azure"
   description="Obtenga una rápida introducción a los cmdlets de Azure PowerShell que puede usar para administrar el servicio Lote de Azure"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="07/28/2016"
   ms.author="danlep"/>

# Introducción a los cmdlets de lotes PowerShell de Azure
Con los cmdlets de PowerShell de Lote de Azure puede realizar directamente y mediante scripts muchas de las mismas tareas que se llevan a cabo con las API de Lote, el Portal de Azure y la interfaz de la línea de comandos (CLI) de Azure. Esta es una breve introducción a los cmdlets que se pueden usar para administrar cuentas de Lote y trabajar con recursos de Lote tales como grupos, trabajos y tareas. Este artículo se basa en los cmdlets de Azure PowerShell versión 1.6.0.

Para obtener una lista completa de los cmdlets de Lote y la sintaxis detallada de los cmdlets, consulte la [referencia de los cmdlets de Lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Requisitos previos

* **Azure PowerShell**: consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones para descargar e instalar Azure PowerShell.
   
    * Como los cmdlets de Lote de Azure se incluyen en el módulo Azure Resource Manager, deberá ejecutar el cmdlet **Login-AzureRmAccount** para conectarse a su suscripción.
    
    * Le recomendamos que actualice su instancia de Azure PowerShell con frecuencia para aprovechar las mejoras y actualizaciones del servicio.
    
* **Registro en el espacio de nombres del proveedor de Lote (operación única)**: antes de trabajar con las cuentas de Lote, debe registrarse en el espacio de nombres del proveedor de Lote. Esta operación solo debe realizarse una vez por cada suscripción. Ejecute el siguiente cmdlet:

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## Administrar claves y cuentas por lotes

### Crear una cuenta de lote

**New-AzureRmBatchAccount** crea una nueva cuenta de Lote en un grupo de recursos especificado. Si no dispone de un grupo de recursos, cree uno mediante el cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), especificando una de las regiones de Azure en el parámetro **Location**, como, por ejemplo, "Centro de EE. UU.". Por ejemplo:


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


A continuación, cree una nueva cuenta de Lote en el grupo de recursos, especifique su nombre en <*account\_name*> y la ubicación y el nombre del grupo de recursos. La operación de crear la cuenta de Lote puede tardar un tiempo en completarse. Por ejemplo:


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] El nombre de la cuenta de Lote debe ser único para la región de Azure en el grupo de recursos, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.

### Obtener claves de acceso de cuenta
**Get-AzureBatchAccountKeys** muestra las claves de acceso asociadas a una cuenta de Lote de Azure. Por ejemplo, ejecute lo siguiente para obtener las claves principales y secundarias de la cuenta que creó.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Generar una nueva clave de acceso
**New-AzureBatchAccountKey** genera una nueva clave de cuenta principal o secundaria para una cuenta de Lote de Azure. Por ejemplo, para generar una nueva clave principal para la cuenta de proceso por lotes, escriba:


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] Para generar una nueva clave secundaria, especifique "Secondary" para el parámetro **KeyType**. Deberá volver a generar las claves principales y secundarias por separado.

### Eliminar una cuenta de lote
**Remove-AzureBatchAccount** elimina una cuenta de Lote. Por ejemplo:


    Remove-AzureRmBatchAccount -AccountName <account_name>

Cuando se le pida, confirme que desea quitar la cuenta. La eliminación de cuenta puede tardar unos minutos en completarse.

## Creación de un objeto BatchAccountContext

Para realizar la autenticación mediante los cmdlets de PowerShell del servicio Lote al crear y administrar grupos, trabajos, tareas y otros recursos de este servicio, primero debe crear un objeto BatchAccountContext para almacenar el nombre y las claves de la cuenta:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Pase el objeto BatchAccountContext a los cmdlets que usan el parámetro **BatchContext**.

> [AZURE.NOTE] De forma predeterminada, se utiliza la clave principal de la cuenta para la autenticación, pero se puede seleccionar explícitamente la clave para utilizar cambiando el objeto la propiedad **KeyInUse** del objeto BatchAccountContext: `$context.KeyInUse = "Secondary"`.



## Creación y modificación de recursos de Lote
Use cmdlets como **New-AzureBatchPool**, **New-AzureBatchJob** y **New-AzureBatchTask** para crear recursos en una cuenta de Lote. Hay cmdlets **Get-** y **Set-** correspondientes para actualizar las propiedades de los recursos existentes, y cmdlets **Remove-** para quitar recursos en una cuenta de Lote.

Cuando se utilizan muchos de estos cmdlets, además de pasar un objeto BatchContext, es preciso crear o pasar objetos que contienen la configuración detallada de los recursos, como se muestra en el ejemplo siguiente. Para obtener ejemplos adicionales, consulte la ayuda detallada de cada cmdlet.

### Crear un grupo de Lote

Al crear o actualizar un grupo de Lote, seleccione una configuración de servicio en la nube o una configuración de máquina virtual para el sistema operativo en los nodos de proceso (consulte [Información general de las características de Lote para desarrolladores](batch-api-basics.md#pool)). La elección determina si se crean imágenes de los nodos de proceso con una de las [versiones de SO invitado de Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) o con una de las imágenes de máquina virtual Linux o Windows compatibles de Azure Marketplace.

Al ejecutar **New-AzureBatchPool**, pase la configuración del sistema operativo en un objeto PSCloudServiceConfiguration o PSVirtualMachineConfiguration. Por ejemplo, el siguiente cmdlet crea un nuevo grupo de Lote con nodos de proceso de tamaño pequeño en la configuración del servicio en la nube, con una imagen de la última versión de sistema operativo de familia 3 (Windows Server 2012). En este caso, el parámetro **CloudServiceConfiguration** especifica la variable *$configuration* como objeto PSCloudServiceConfiguration. El parámetro **BatchContext** especifica una variable definida anteriormente *$context* como objeto BatchAccountContext.


    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")
    
    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

El número destino de nodos de proceso en el nuevo grupo se determina mediante una fórmula de autoescalado. En este caso, la fórmula es simplemente **$TargetDedicated=4**, que indica que el número máximo de nodos de proceso en el grupo es 4.

## Consulta de grupos, trabajos, tareas y otros detalles

Use cmdlets como **Get-AzureBatchPool**, **Get-AzureBatchJob** y **Get-AzureBatchTask** para consultar las entidades creadas en una cuenta de Lote.


### Consulta de datos

Por ejemplo, use **Get-AzureBatchPools** para encontrar sus grupos. De forma predeterminada, esto consulta todos los grupos de su cuenta, siempre que ya haya almacenado el objeto BatchAccountContext en *$context*:


    Get-AzureBatchPool -BatchContext $context

### Uso de un filtro OData

Puede proporcionar un filtro de OData mediante el parámetro **Filter** para buscar solo los objetos que le interesan. Por ejemplo, puede encontrar todos los grupos cuyos id. empiecen por "myPool":


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Este método no es tan flexible como "Where-Object" en una canalización local. Sin embargo, la consulta se envía al servicio de lote directamente para que todos los filtros se apliquen en el servidor, lo cual ahorra ancho de banda de Internet.

### Uso del parámetro Id

Una alternativa a un filtro OData es el parámetro **Id**. Para consultar un grupo específico con el Id. "myPool":


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


El parámetro **Id** solo admite la búsqueda de id. completo, no con caracteres comodín ni filtros al estilo de OData.



### Uso del parámetro MaxCount

De forma predeterminada, cada cmdlet devuelve un máximo de 1000 objetos. Si se alcanza este límite, puede refinar el filtro para que devuelva menos objetos, o establecer explícitamente un máximo mediante el parámetro **MaxCount**. Por ejemplo:


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Para quitar el límite superior, establezca **MaxCount** en 0 o menos.

### Usar la canalización

Los cmdlets de lote pueden aprovechar la canalización para enviar datos entre los cmdlets de PowerShell. Esto tiene el mismo efecto que si se especifica un parámetro, pero hace enumerar varias entidades de forma más fácil. Por ejemplo, lo siguiente busca todas las tareas en su cuenta:


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Pasos siguientes
* Para conocer la sintaxis detallada de cmdlets y ejemplos de los mismos, consulte [Azure Batch Cmdlets](https://msdn.microsoft.com/library/azure/mt125957.aspx) (Cmdlets de Lote de Azure).

* Para más información acerca de cómo reducir el número de elementos y el tipo de información que se devuelve en las consultas a Lote, consulte [Consulta eficaz del servicio Lote de Azure](batch-efficient-list-queries.md).

<!---HONumber=AcomDC_0803_2016-->