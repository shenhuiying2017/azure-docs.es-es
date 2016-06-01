<properties
	pageTitle="Instalación y administración fáciles de aplicaciones en Lote de Azure | Microsoft Azure"
	description="Utilice la característica paquetes de aplicación de Lote de Azure para administrar fácilmente varias aplicaciones y versiones para la instalación en nodos de proceso de Lote."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="05/12/2016"
	ms.author="marsma" />

# Implementación de aplicaciones con paquetes de aplicación de Lote de Azure

La característica paquetes de aplicación de Lote de Azure permite administrar e implementar fácilmente aplicaciones en los nodos de proceso de un grupo. Con los paquetes de aplicación se pueden cargar y administrar varias versiones de las aplicaciones que ejecutan las tareas, incluyendo los archivos binarios y auxiliares, implemente automáticamente una o varias de estas aplicaciones en los nodos de proceso del grupo.

En este artículo, aprenderá a cargar y administrar paquetes de aplicación mediante el Portal de Azure y luego a instalarlos en los nodos de proceso de un grupo mediante la biblioteca [.NET de Lote][api_net].

> [AZURE.NOTE] La característica paquetes de aplicación que se describe aquí reemplaza a la característica "aplicaciones de Lote" disponible en las versiones anteriores del servicio.

## Requisitos de paquetes de aplicación

La característica paquetes de aplicación que se explica en este artículo *solo* será compatible con los grupos de Lote después del 10 de marzo de 2016. Los paquetes de aplicación no se implementarán en los nodos de proceso de los grupos creados antes de esta fecha.

Esta característica se introdujo en [API de REST de Lote][api_rest], versión 2015-12-01.2.2, y la correspondiente biblioteca [.NET de lote][api_net], versión 3.1.0. Si se trabaja con Lote, se recomienda utilizar siempre la versión más reciente de la API.

## Acerca de las aplicaciones y los paquetes de aplicación

En Lote de Azure, una **aplicación** hace referencia a un conjunto de archivos binarios con versiones que se pueden descargar automáticamente en los nodos de proceso del grupo. Un **paquete de aplicación** hace referencia a un *conjunto específico* de dichos archivos binarios y representa una *versión* determinada de la aplicación.

![Diagrama de alto nivel de aplicaciones y paquetes de aplicación][1]

### Aplicaciones

Una aplicación de Lote contiene uno o varios paquetes de aplicación. Especifica las opciones de configuración de la aplicación como la versión predeterminada del paquete de aplicación que se instala en los nodos de proceso y si sus paquetes se pueden actualizar o eliminar.

### Paquetes de aplicación

Un paquete de aplicación es un archivo ZIP que contiene tanto los archivos binarios de una aplicación como los archivos auxiliares que se requieren para la ejecución por parte de las tareas. Cada paquete de aplicación representa una versión específica de la aplicación. Al crear un grupo en el servicio de Lote, se pueden especificar una o varias aplicaciones y (opcionalmente) una versión, y los paquetes de aplicación se descargarán automáticamente y se extraerán en cada uno de los nodos a medida que se unan al grupo.

> [AZURE.IMPORTANT] Hay restricciones en el número de aplicaciones y paquetes de aplicación que puede haber en una cuenta de Lote, así como en el tamaño máximo del paquete de aplicación. Para más información sobre estos límites, consulte [Cuotas y límites del servicio de Lote de Azure](batch-quota-limit.md).

### Ventajas de los paquetes de aplicación

Los paquetes de aplicación pueden simplificar el código de una solución de Lote, así como reducir la sobrecarga requerida en la administración de las aplicaciones que ejecutan las tareas.

Con los paquetes de aplicaciones no es preciso que la tarea de inicio del grupo especifique una larga lista de archivos de recursos individuales que se deben instalar en los nodos. No es preciso administrar manualmente varias versiones de estos archivos en Almacenamiento de Azure ni en los nodos. Y tampoco es preciso preocuparse de generar [direcciones URL de SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) para proporcionar acceso a los archivos de su cuenta de Almacenamiento de Azure.

Lote controla los detalles del trabajo con Almacenamiento de Azure en segundo plano para almacenar e implementar paquetes de aplicación en nodos de proceso, por lo que tanto el código como la sobrecarga de administración se pueden simplificar.

## Carga y administración de aplicaciones

Mediante el Portal de Azure es posible agregar, actualizar y eliminar paquetes de aplicación, así como configurar versiones predeterminadas para cada aplicación.

En las próximas secciones, se tratará en primer lugar la asociación de una cuenta de Almacenamiento con su cuenta de Lote y luego se revisarán las características de administración de paquetes disponibles en el Portal de Azure. Después, aprenderá a implementar dichos paquetes en nodos de proceso mediante la biblioteca [.NET de Lote][api_net].

### Vínculo a una cuenta de Almacenamiento

Para utilizar paquetes de aplicación, primero se debe vincular una cuenta de Almacenamiento de Azure a su cuenta de Lote. Si aún no ha configurado ninguna cuenta de Almacenamiento para su cuenta de Lote, el Portal de Azure mostrará una advertencia la primera vez que haga clic en el icono *Aplicaciones* en la hoja Cuenta de Lote.

> [AZURE.IMPORTANT] Actualmente, Lote *solo* admite el tipo de cuenta de almacenamiento **Uso general**, como se describe en el paso 5 de la sección [Crear una cuenta de almacenamiento](../storage/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). Cuando vincula una cuenta de Almacenamiento de Azure a su cuenta de Lote, *solo* se vincula una cuenta de almacenamiento **de uso general**.

![Advertencia de que no hay cuentas de almacenamiento almacenadas en el Portal de Azure][9]

El servicio de Lote utiliza la cuenta de Almacenamiento asociada para el almacenamiento y la recuperación de paquetes de aplicación. Una vez que haya vinculado las dos cuentas, Lote puede implementar automáticamente los paquetes almacenados en la cuenta de Almacenamiento vinculada en los nodos de proceso. Haga clic en **Configuración de cuenta de almacenamiento** en la hoja *Advertencia* y luego en **Cuenta de almacenamiento** en la hoja *Cuenta de almacenamiento* para vincular una cuenta de almacenamiento a su cuenta de Lote.

![Hoja Elegir de cuenta de almacenamiento en el Portal de Azure][10]

Se recomienda que cree una cuenta de almacenamiento *específicamente* para su uso con la cuenta de Lote y que la seleccione aquí. Para obtener información sobre cómo crear una cuenta de almacenamiento, consulte "Crear una cuenta de almacenamiento" en [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). Una vez que haya creado una cuenta de Almacenamiento, puede vincularla a su cuenta de Lote mediante la hoja *Cuenta de almacenamiento*.

> [AZURE.WARNING] Dado que Lote almacena los paquetes de aplicación con Almacenamiento de Azure, [se aplican cargos normales][storage_pricing] a los datos de blob en bloques. Asegúrese de considerar el tamaño y número de los paquetes de aplicación y elimine periódicamente los paquetes en desuso para minimizar el costo.

### Visualización de las aplicaciones actuales

Para ver las aplicaciones en la cuenta de Lote, haga clic en el icono **Aplicaciones** de la hoja Cuenta de Lote.

![Icono Aplicaciones][2]

Se abrirá la hoja *Aplicaciones*:

![Enumerar aplicaciones][3]

La hoja *Aplicaciones* muestra el identificador de cada aplicación de su cuenta, así como las siguientes propiedades:

* **Paquetes**: el número de versiones asociadas a la aplicación.
* **Versión predeterminada**: si no especifica una versión al configurar la aplicación para un grupo, se instalará esta versión. Esta configuración es opcional.
* **Permitir actualizaciones**: si se establece en *No*, las actualizaciones y eliminaciones de paquetes se deshabilitan en la aplicación (solo se pueden agregar versiones nuevas del paquete de aplicación). El valor predeterminado es *Sí*.

### Visualización de los detalles de una aplicación

Al hacer clic en una aplicación en la hoja *Aplicaciones*, se muestra su hoja de detalles.

![Detalles de aplicación][4]

En la hoja de detalles de la aplicación, puede configurar los siguientes valores de la aplicación.

* **Permitir actualizaciones**: especifique si sus paquetes de aplicación se pueden actualizar o eliminar (consulte "Actualización o eliminación de un paquete de aplicación" a continuación).
* **Versión predeterminada**: especifique el paquete de aplicación predeterminado que se implementa en los nodos de proceso.
* **Nombre para mostrar**: es un nombre "descriptivo" que la solución de Lote puede usar al mostrar información de la aplicación, como en la interfaz de usuario de un servicio que proporciona a los clientes a través de Lote.

### Adición de una nueva aplicación

Para crear una nueva aplicación, agregue un paquete de aplicación mediante un identificador de aplicación nuevo y exclusivo. El primer paquete de aplicación que agregue mediante el nuevo identificador de aplicación también creará la nueva aplicación.

Haga clic en **Agregar** en la hoja *Aplicaciones* para abrir la hoja *Nueva aplicación*.

![Hoja Nueva aplicación en el Portal de Azure][5]

La hoja *Nueva aplicación* proporciona los siguientes campos para especificar la configuración de la nueva aplicación y del paquete de aplicación.

**Id. de la aplicación**

Especifica el identificador de la nueva aplicación, que está sujeto a las reglas de validación estándar de identificadores de Lote de Azure:

* Pueden contener cualquier combinación de caracteres alfanuméricos, incluidos los guiones y caracteres de subrayado.
* No pueden contener más de 64 caracteres.
* Deben ser único en la cuenta de Lote.
* Conservación de mayúsculas y minúsculas y no distingue mayúsculas de minúsculas.

**Versión**

Especifica la versión del paquete de aplicación que se carga. Las cadenas de la versión están sujetas a las siguientes reglas de validación:

* Pueden contener cualquier combinación de caracteres alfanuméricos, incluidos guiones, caracteres de subrayado y puntos.
* No pueden contener más de 64 caracteres.
* Deben ser únicas en la aplicación.
* Conservación de mayúsculas y minúsculas y no distingue mayúsculas de minúsculas.

**Paquete de aplicación**

Especifica el archivo ZIP que contiene tanto los archivos binarios de la aplicación como los archivos auxiliares que se requieren para la ejecución de la aplicación. Haga clic en el cuadro de texto **Seleccionar un archivo** o en el icono de la carpeta a la que desee desplazarse y seleccione un archivo ZIP que contenga los archivos de la aplicación.

Una vez que haya seleccionado un archivo, haga clic en **Aceptar** para iniciar la carga en Almacenamiento de Azure. Cuando se complete la operación de carga, recibirá una notificación y se cerrará la hoja. Tenga en cuenta que, en función del tamaño del archivo que se va a cargar y de la velocidad de la conexión de red, esta operación puede tardar un tiempo.

> [AZURE.WARNING] No cierre la hoja *Nueva aplicación* antes de que se complete la operación de carga. Si lo hace, se anulará el proceso de carga.

### Adición de un nuevo paquete de aplicación

Para agregar una nueva versión del paquete de aplicación para una aplicación existente, seleccione una aplicación en la hoja *Aplicaciones*, haga clic en **Paquetes** y, luego, haga clic en **Agregar** para mostrar la hoja *Agregar paquete*.

![Hoja Agregar paquete de aplicación en el Portal de Azure][8]

Como puede ver, los campos coinciden con los de la hoja *Nueva aplicación*, excepto el cuadro de texto "Id. de la aplicación", que está deshabilitado. Como antes, especifique la **Versión** del paquete nuevo, vaya al archivo ZIP de su **Paquete de aplicación** y haga clic en **Aceptar** para cargar el paquete.

### Actualización o eliminación de un paquete de aplicación

Para actualizar o eliminar un paquete de aplicación existente, abra la hoja de detalles de la aplicación, haga clic en **Paquetes** para mostrar la hoja *Paquetes*, haga clic en los **puntos suspensivos** de la fila del paquete de aplicación que desee modificar y seleccione la acción que desee realizar.

![Actualizar o eliminar paquete en el Portal de Azure][7]

**Actualizar**

Al hacer clic en **Actualizar**, se muestra la hoja *Actualizar paquete*. Esta hoja es similar a la hoja *Nuevo paquete de aplicación*. Sin embargo, solo está habilitado el campo de selección de paquete, lo que permite especificar un nuevo archivo ZIP para cargarlo.

![Hoja Actualizar paquete en el Portal de Azure][11]

**Eliminar**

Al hacer clic en **Eliminar**, se le pedirá que confirme la eliminación de la versión del paquete y Lote eliminará el paquete del Almacenamiento de Azure. Si elimina la versión predeterminada de una aplicación, la configuración de la versión predeterminada se quita de la aplicación.

![Eliminar aplicación][12]

## Instalación de aplicaciones en nodos de proceso

Una vez que se ha explicado cómo cargar y administrar paquetes de aplicación mediante el Portal de Azure, ya se puede explicar cómo implementarlos en los nodos de proceso y su ejecución con las tareas de Lote.

Para instalar un paquete de aplicación en los nodos de proceso de un grupo, especifique una o varias *referencias* de paquetes de aplicación para el grupo. En el entorno .NET de Lote, se puede hacer agregando una o varias propiedades [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] al crear el grupo o a un grupo existente.

La clase [ApplicationPackageReference][net_pkgref] especifica un identificador de la aplicación y la versión que se va a instalar en los nodos de proceso de un grupo.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

## Ejecución de las aplicaciones instaladas

Cuando cada nodo de proceso se une a un grupo (o se reinicia o su imagen inicial se restablece), los paquetes que ha especificado se descargan y se extraen a un directorio con nombre dentro de `AZ_BATCH_ROOT_DIR` en el nodo. Lote también crea una variable de entorno para que las líneas de comando de la tarea la utilicen al llamar a los archivos binarios de aplicación; esta variable se ajusta al esquema de nomenclatura siguiente:

`AZ_BATCH_APP_PACKAGE_appid#version`

Por ejemplo, si especifica que se instale la versión 2.7 de la aplicación *blender*, las tareas podrán acceder a sus archivos binarios haciendo referencia a la siguiente variable de entorno en sus líneas de comandos:

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si la aplicación especifica una versión predeterminada, puede hacer referencia a la variable de entorno sin el sufijo de la cadena de la versión. Por ejemplo, si había especificado la versión 2.7 de forma predeterminada de la aplicación *blender* en el Portal de Azure, las tareas pueden hacer referencia a la siguiente variable de entorno:

`AZ_BATCH_APP_PACKAGE_BLENDER`

El siguiente fragmento de código muestra cómo se puede configurar una tarea cuando se ha especificado una versión predeterminada para la aplicación *blender*.

```csharp
string taskId = "blendertask01";
string commandLine = @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -my -command -args";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Para más información sobre la configuración del entorno del nodo de proceso, consulte el apartado "Configuración del entorno para las tareas" de [Información general de las características de Lote](batch-api-basics.md).

## Actualización de los paquetes de aplicación de un grupo

Si un grupo existente ya se ha configurado con un paquete de aplicación, se puede especificar un paquete nuevo para el grupo. Todos los nodos nuevos que se unen al grupo instalarán el paquete recién especificado, al igual que cualquier nodo existente que se reinicie o cuya imagen inicial se restablezca. Los nodos de proceso que ya estén en el grupo cuando se actualicen las referencias del paquete no instalan automáticamente el paquete de aplicación nuevo.

En este ejemplo, el grupo existente tiene la versión 2.7 de la aplicación *blender* configurada como una de sus propiedades [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]. Para actualizar los nodos del grupo con la versión 2.76b, especifique una nueva clase [ApplicationPackageReference][net_pkgref] con la nueva versión y confirme el cambio.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Ahora que se ha configurado la nueva versión, todos los nodos *nuevos* que se unan al grupo tendrán implementada la versión 2.76b. Para instalar la versión 2.76b en los nodos que *ya* están en el grupo, reinícielos (o restablezca su imagen inicial). Tenga en cuenta que los nodos reiniciados conservarán los archivos de las anteriores implementaciones del paquete.

## Enumeración de las aplicaciones en una cuenta de Lote

Puede enumerar las aplicaciones y sus paquetes en una cuenta de Lote mediante el método [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries].

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## Conclusión

Con los paquetes de aplicaciones le resultará más fácil proporcionar a los clientes la capacidad de seleccionar las aplicaciones para sus trabajos y especificar la versión exacta que deben usar al procesar los trabajos con su servicio con Lote habilitado. También puede proporcionar a los clientes la capacidad de cargar y hacer un seguimiento de sus propias aplicaciones en su servicio.

## Pasos siguientes

* La [API de REST de Lote][api_rest] también proporciona compatibilidad para trabajar con paquetes de aplicación. Por ejemplo, consulte el elemento [applicationPackageReferences][rest_add_pool_with_packages] en [Adición de un grupo a una cuenta][rest_add_pool] para especificar los paquetes que se instalan con la API de REST. Para más información acerca de cómo obtener información de las aplicaciones mediante la API de REST de Lote, consulte [Aplicaciones][rest_applications].

* Aprenda a [administrar mediante programación cuentas y cuotas de Lote de Azure con la biblioteca .NET de Administración de Lote](batch-management-dotnet.md). La biblioteca [.NET de Administración de Lote][api_net_mgmt] puede habilitar las características de creación y eliminación de cuentas de una aplicación o servicio de Lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nivel de paquetes de aplicación"
[2]: ./media/batch-application-packages/app_pkg_02.png "Icono Aplicaciones en el Portal de Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Hoja Aplicaciones en el Portal de Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Hoja Detalles de aplicación en el Portal de Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Hoja Nueva aplicación en el Portal de Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Lista desplegable Actualizar o eliminar paquetes en el Portal de Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Hoja Paquete de aplicación nuevo en el Portal de Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Alerta de cuenta de almacenamiento vinculada"
[10]: ./media/batch-application-packages/app_pkg_10.png "Hoja Elegir de cuenta de almacenamiento en el Portal de Azure"
[11]: ./media/batch-application-packages/app_pkg_11.png "Hoja Actualizar paquete en el Portal de Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Cuadro de diálogo de confirmación de eliminación de paquetes en el Portal de Azure"

<!---HONumber=AcomDC_0518_2016-->