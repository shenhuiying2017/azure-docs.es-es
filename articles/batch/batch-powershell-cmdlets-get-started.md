<properties
   pageTitle="Introducción a los cmdlets de Lote de Azure PowerShell | Microsoft Azure"
   description="Presenta los cmdlets de Azure PowerShell usados para administrar el servicio de lotes de Azure"
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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Introducción a los cmdlets de lotes PowerShell de Azure
Este artículo es una breve introducción a los cmdlets de PowerShell de Azure que puede usar para administrar sus cuentas por lotes y obtener información acerca de los elementos de trabajo, los trabajos y las tareas por lotes.

Para conocer la sintaxis detallada de cmdlet, escriba `get-help <Cmdlet_name>` o consulte la [referencia de cmdlets de Lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Requisitos previos

* **Vista previa del lote**: suscríbase a la [Vista previa del lote](https://account.windowsazure.com/PreviewFeatures), si no lo ha hecho aún, para trabajar con el servicio.
* **Azure PowerShell**: vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener los requisitos previos y descargar las instrucciones de instalación. Los cmdlets de lotes se introdujeron en la versión 0.8.10 y posteriores.

## Usar los cmdlets de lote

Utilice los procedimientos estándar para iniciar Azure PowerShell y [conectarse a las suscripciones de Azure](../powershell-install-configure.md#Connect). Además:

* **Seleccione la suscripción de Azure**: si tiene más de una suscripción, seleccione la suscripción a la que agregó la característica de vista previa del lote:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Cambie al modo de AzureResourceManage**: los cmdlets por lotes envían el módulo de administrador de recursos de Azure. Vea [Uso de Windows PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md) para obtener más detalles. Para utilizar este módulo, ejecute el cmdlet [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx):

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Administrar claves y cuentas por lotes

Puede usar los cmdlets de PowerShell de Azure para crear y administrar claves y cuentas por lotes.

### Crear una cuenta de lote

**AzureBatchAccount nuevo** crea una nueva cuenta de proceso por lotes en un grupo de recursos especificado. Si no dispone de un grupo de recursos, cree uno mediante el cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), especificando una de las regiones de Azure en el parámetro **Location**. (Encontrará las regiones disponibles para los distintos recursos de Azure ejecutando [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx)). Por ejemplo:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

A continuación, cree una nueva cuenta de proceso por lotes en el grupo de recursos, especificando también un nombre de cuenta para <*account_name*> y la ubicación en la que está disponible el servicio de proceso por lotes. La creación de la cuenta puede tardar varios minutos en completarse. Por ejemplo:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]El nombre de la cuenta de proceso debe ser único para Azure, contener entre 3 y 24 caracteres y usar solo letras minúsculas y números.

### Obtener claves de acceso de cuenta
**Get-AzureBatchAccountKeys** muestra las claves de acceso asociadas a una cuenta de proceso de Azure. Por ejemplo, ejecute lo siguiente para obtener las claves principales y secundarias de la cuenta que creó.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Generar una nueva clave de acceso
**New-AzureBatchAccountKey** genera una nueva clave de cuenta principal o secundaria para una cuenta de lotes de Azure. Por ejemplo, para generar una nueva clave principal para la cuenta de proceso por lotes, escriba:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Para generar una nueva clave secundaria, especifique "Secondary" para el parámetro **KeyType**. Deberá volver a generar las claves principales y secundarias por separado.

### Eliminar una cuenta de lote
**Remove-AzureBatchAccount** elimina una cuenta de lote. Por ejemplo:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Cuando se le pida, confirme que desea quitar la cuenta. La eliminación de cuenta puede tardar unos minutos en completarse.

## Consulta de elementos de trabajo, trabajos y tareas

Use cmdlets como **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** y **Get-AzureBatchPool** para consultar las entidades creadas en una cuenta de lote.

Para usar estos cmdlets, primero deberá crear un objeto AzureBatchContext para almacenar el nombre de cuenta y las claves:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Aplique este contexto en los cmdlets que interactúan con el servicio de proceso por lotes mediante el parámetro **BatchContext**.

> [AZURE.NOTE]De forma predeterminada, se utiliza la clave principal de la cuenta para la autenticación, pero se puede seleccionar explícitamente la clave para utilizar cambiando el objeto la propiedad **KeyInUse** del objeto BatchAccountContext: ```$context.KeyInUse = "Secondary"```.


### Consulta de datos

Por ejemplo, utilice **Get-AzureBatchWorkItem** para buscar los elementos de trabajo. De forma predeterminada, esto consulta todos los elementos de trabajo en su cuenta, suponiendo que ya almacena el objeto BatchAccountContext en *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

Puede hacer lo mismo con otras entidades, como grupos:

```
Get-AzureBatchPool -BatchContext $context
```
### Uso de un filtro OData

Puede proporcionar un filtro de OData mediante el parámetro **Filter** para buscar solo los objetos que le interesan. Por ejemplo, puede encontrar todos los elementos de trabajo cuyos nombres empiecen por "myWork":

```
$filter = "startswith(name,'myWork') and state eq 'active'"
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
```

Este método no es tan flexible como "Where-Object" en una canalización local. Sin embargo, la consulta se envía al servicio de lote directamente para que todos los filtros se apliquen en el servidor, lo cual ahorra ancho de banda de Internet.

### Uso del parámetro Name

Una alternativa al filtro OData es el parámetro **Name**. Para consultar un elemento de trabajo específico denominado "myWorkItem":

```
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context

```
El parámetro **Name** admite solo búsqueda de nombre completo, no de caracteres comodín o filtros al estilo de OData.

### Uso de la canalización

Los cmdlets de lote pueden aprovechar la canalización para enviar datos entre los cmdlets de PowerShell. Esto tiene el mismo efecto que si se especifica un parámetro, pero hace enumerar varias entidades de forma más fácil. Por ejemplo, puede encontrar todas las tareas en su cuenta:

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Uso del parámetro MaxCount

De forma predeterminada, cada cmdlet devuelve un máximo de 1000 objetos. Si se alcanza este límite, puede perfeccionar el filtro para que devuelva menos objetos, o establecer explícitamente un máximo mediante el parámetro **MaxCount**. Por ejemplo:

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context

```

Para quitar el límite superior, establezca **MaxCount** en 0 o menos.

## Temas relacionados
* [Información técnica de los lotes](batch-technical-overview.md)
* [Descarga de Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Referencia de cmdlets de Lote de Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Consultas de lista eficaces](batch-efficient-list-queries.md)

<!---HONumber=58_postMigration-->