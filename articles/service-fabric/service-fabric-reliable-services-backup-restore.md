<properties
   pageTitle="Copia de seguridad y restauración de Service Fabric | Microsoft Azure"
   description="Documentación conceptual para la copia de seguridad y la restauración de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/28/2016"
   ms.author="mcoskun"/>

# Copia de seguridad y restauración de Reliable Services y Reliable Actors

Azure Service Fabric es una plataforma de alta disponibilidad que replica el estado entre varios nodos para mantener esta disponibilidad alta. Por lo tanto, incluso si se produce un error en un nodo del clúster, los servicios siguen estando disponibles. Aunque esta redundancia integrada proporcionada por la plataforma puede ser suficiente para algunos casos, en otros es conveniente que el servicio haga una copia de seguridad de los datos (en un almacén externo).

>[AZURE.NOTE] Es fundamental hacer una copia de seguridad de los datos y restaurarlos (y comprobar que funcionan según lo esperado) para que se pueda recuperar de escenarios de pérdida de datos.

Por ejemplo, un servicio podría realizar copias de seguridad en los escenarios siguientes:

* En el caso de la pérdida permanente de todo un clúster de Service Fabric o de todos los nodos que ejecutan una partición determinada.

* Errores administrativos por los cuales el estado se elimina o daña accidentalmente. Por ejemplo, esto puede ocurrir si un administrador con suficientes privilegios elimina el servicio por error.

* Errores en el servicio que causan daños en los datos. Por ejemplo, esto puede suceder cuando una actualización de código de servicio empieza a escribir datos defectuosos en una colección confiable. En tal caso, es posible que el código y los datos se tengan que revertir a un estado anterior.

* Procesamiento de datos sin conexión. Podría ser conveniente procesar los datos para inteligencia empresarial sin conexión de forma independiente del servicio que los genera.

La característica de copia de seguridad y restauración permite que los servicios basados en la API de Reliable Services creen y restauren copias de seguridad. Las API de copia de seguridad proporcionadas por la plataforma permiten hacer copias de seguridad del estado de una partición de servicio sin bloquear las operaciones de lectura o de escritura. Las API de restauración permiten restaurar el estado de una partición de servicio a partir de una copia de seguridad elegida.


## Copias de seguridad de Reliable Services

El autor del servicio tiene control total sobre cuándo se realizan las copias de seguridad y dónde se almacenan.

Para iniciar una copia de seguridad, el servicio debe invocar la función de miembro heredada **BackupAsync**. Las copias de seguridad solo se pueden realizar desde réplicas principales y necesitan que se conceda el estado de escritura.

Como se muestra a continuación, **BackupAsync** asimila un objeto **BackupDescription**, donde puede especificarse una copia de seguridad completa o incremental, así como una función de devolución de llamada, **Func << BackupInfo  bool >>** que se invoca cuando la carpeta de copia de seguridad se ha creado localmente y está lista para moverse fuera a algún almacenamiento externo.

```C#

BackupDescription myBackupDescription = new BackupDescription(backupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

**BackupInfo** proporciona información sobre la copia de seguridad, incluida la ubicación de la carpeta donde runtime la guardó (**BackupInfo.Directory**). La función de devolución de llamada puede mover **BackupInfo.Directory** a un almacén externo u otra ubicación. Además, esta función devuelve un valor booleano que indica si se pudo mover correctamente la carpeta de copia de seguridad a su ubicación de destino.

El código siguiente muestra cómo se puede usar el método **BackupCallbackAsync** para cargar la copia de seguridad a Almacenamiento de Azure:

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

En el ejemplo anterior, **ExternalBackupStore** es la clase de ejemplo que se usa para interactuar con el almacenamiento de blobs de Azure y **UploadBackupFolderAsync** es el método que comprime la carpeta y la coloca en el almacén de blobs de Azure.

Observe lo siguiente:

- Solo puede haber una operación de copia de seguridad en proceso por réplica en cualquier momento dado. Más de una llamada a **BackupAsync** a la vez producirá una excepción **FabricBackupInProgressException** para limitar las copias de seguridad en proceso a una.

- Si una réplica conmuta por error mientras se realiza una copia de seguridad, es posible que esta no se complete. Por lo tanto, una vez finalizada la conmutación por error, es responsabilidad del servicio reiniciar la copia de seguridad mediante la invocación de **BackupAsync** según sea necesario.

## Restauración de Reliable Services

En general, los casos en los que necesite realizar una operación de restauración se dividen en estas categorías:

- La partición del servicio perdió datos. Por ejemplo, el disco de dos de las tres réplicas de una partición (incluida la réplica principal) sufre daños o se borra. Es posible que la nueva réplica principal necesite restaurar datos desde una copia de seguridad.

- Se perdió la totalidad del servicio. Por ejemplo, un administrador quita todo el servicio y, por tanto, se deben restaurar tanto el servicio como los datos.

- El servicio replicó datos dañados de la aplicación (por ejemplo, debido a un error de la aplicación). En este caso, el servicio se debe actualizar o revertir para quitar la causa de los daños y se deben restaurar los datos no dañados.

Aunque hay muchos enfoques posibles, ofrecemos algunos ejemplos de uso de **RestoreAsync** para recuperarse de los escenarios anteriores.

## Pérdida de datos de la partición en Reliable Services

En este caso, el tiempo de ejecución detectaría automáticamente la pérdida de datos e invocaría la API **OnDataLossAsync**.

El autor del servicio debe realizar lo siguiente para recuperar:

- Invalide el método de clase base virtual **OnDataLossAsync**.

- Busque la última copia de seguridad en la ubicación externa que contiene las copias de seguridad del servicio.

- Descargue la última copia de seguridad (y descomprímala en la carpeta de copia de seguridad si estaba comprimida).

- El método **OnDataLossAsync** proporciona **RestoreContext**. Llame a la API **RestoreAsync** en el **RestoreContext** proporcionado.

- Devuelve true si la restauración fue correcta.

A continuación se muestra una implementación de ejemplo del método **OnDataLossAsync**:

```C#

protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

>[AZURE.NOTE] RestorePolicy se establece en Seguro de forma predeterminada. Esto significa que la API **RestoreAsync** generará una excepción ArgumentException si detecta que la carpeta de copia de seguridad contiene un estado igual o más antiguo que el estado contenido en esta réplica. Se puede usar **RestorePolicy.Force** para omitir esta comprobación de seguridad. Esto se especifica como parte de **RestoreDescription**.

## Eliminación o pérdida del servicio

Si se quita un servicio, en primer lugar debe volver a crearlo para poder restaurar los datos. Es importante crear el servicio con la misma configuración, por ejemplo, con el mismo esquema de partición, para que los datos se restauren sin ningún problema. Una vez que el servicio está activo, se debe invocar la API para restaurar datos (**OnDataLossAsync** más arriba) en todas las particiones de este servicio. Una manera de conseguir esto es utilizando **FabricClient.ServiceManager.InvokeDataLossAsync** en cada partición.

Desde este punto, la implementación es igual que en el escenario anterior. Cada partición debe restaurar la última copia de seguridad pertinente desde el almacén externo. Una advertencia es que es posible que el identificador de partición sea diferente, ya que el tiempo de ejecución crea los identificadores de partición de forma dinámica. Por lo tanto, el servicio necesita almacenar el nombre de servicio y la información de partición adecuada para identificar la copia de seguridad correcta más reciente desde la que restaurar para cada partición.


## Replicación de datos de aplicación dañados

Si hay un error en la actualización de la aplicación recién implementada, puede que produzca daños en los datos. Por ejemplo, una actualización de la aplicación puede empezar a actualizar todos los registros de número de teléfono en un diccionario confiable con un código de área no válido. En este caso, los números de teléfono no válidos se replicarán porque Service Fabric no conoce la naturaleza de los datos que se almacenan.

Lo primero que debe hacer después de detectar un error tan notorio que cause daños en los datos es inmovilizar el servicio en el nivel de aplicación y, si es posible, actualizar a la versión del código de aplicación que no contenga el error. Sin embargo, incluso una vez corregido el código del servicio, es posible que los datos sigan dañados y que por tanto se deban restaurar. En tales casos, puede que no baste con restaurar la última copia de seguridad, ya que las copias de seguridad más recientes también pueden estar dañadas. Por lo tanto, tiene que buscar la última copia de seguridad realizada antes de que los datos resultaran dañados.

Si no está seguro de qué copias de seguridad están dañadas, podría implementar un nuevo clúster de Service Fabric y restaurar las copias de seguridad de las particiones afectadas de la misma forma que en el escenario de "Eliminación o pérdida del servicio" anterior. Para cada partición, inicie la restauración de las copias de seguridad desde la más a la menos reciente. Una vez que encuentre una copia de seguridad sin los daños, mueva o elimine todas las copias de seguridad de esta partición que sean más recientes (que esa copia de seguridad). Repita este proceso para cada partición. Ahora, cuando se llama a **OnDataLossAsync** en la partición del clúster de producción, la última copia de seguridad que se encuentra en el almacén externo será la elegida por el proceso anterior.

Ahora se pueden usar los pasos de la sección "Eliminación o pérdida del servicio" para restaurar el estado del servicio al estado anterior a los daños producidos por el código con errores.

Observe lo siguiente:

- Con cada restauración, es posible que la copia de seguridad que se restaura sea anterior al estado de la partición antes de la pérdida de datos. Por este motivo, la restauración se debe utilizar solo como último recurso para recuperar tantos datos como sea posible.

- La cadena que representa la ruta de acceso de la carpeta de copia de seguridad, así como las rutas de acceso de los archivos dentro de la misma, puede tener más de 255 caracteres, según la ruta de acceso de FabricDataRoot y la longitud del nombre del tipo de aplicación. Esto puede hacer que algunos métodos .NET, como **Directory.Move**, inicien la excepción **PathTooLongException**. Una solución alternativa es llamar directamente a las API de kernel32, como **CopyFile**.

## Copia de seguridad y restauración de Reliable Actors

La copia de seguridad y restauración para Reliable Actors se basa en la funcionalidad de copia de seguridad y restauración que proporcionan los servicios de confianza. El propietario del servicio debe crear un servicio Actor personalizado que se deriva de **ActorService** (que es un servicio de confianza de Service Fabric que hospeda a actores) y después hacer la copia de seguridad y restauración de forma similar a Reliable Services tal como se describió en las secciones anteriores. Puesto que las copias de seguridad se realizan por partición, se llevará a cabo una copia de seguridad de los estados para todos los actores de esa partición concreta. La restauración es similar y se realizará por partición.

Observe lo siguiente:

(1) Al crear un servicio de actor personalizado, debe registrar también dicho servicio al registrar el actor. Consulte **ActorRuntime.RegistorActorAsync**. (2) **KvsActorStateProvider** actualmente solo admite la copia de seguridad completa. En versiones futuras se proporcionará compatibilidad con la copia de seguridad incremental. **KvsActorStateProvider** también pasa por alto la opción **RestorePolicy.Safe**.

## Prueba de la copia de seguridad y la restauración

Es importante asegurarse de que se hace una copia de seguridad de los datos y que se pueden restaurar desde dicha copia. Esto se puede hacer mediante la invocación del cmdlet **ServiceFabricPartitionDataLoss Invoke** de PowerShell que puede provocar la pérdida de datos en una partición determinada para comprobar si la funcionalidad de copia de seguridad y restauración de los datos para el servicio funciona según lo esperado. También es posible invocar la pérdida de datos mediante programación y restaurar a partir de ese evento también.

>[AZURE.NOTE] Puede encontrar una implementación de ejemplo de la funcionalidad de copia de seguridad y restauración en la aplicación de referencia web en Github. Consulte el servicio Inventory.Service para más detalles.

## Bajo el capó: Más detalles sobre la copia de seguridad y la restauración

A continuación se proporcionan más detalles sobre la copia de seguridad y la restauración.

### Copia de seguridad
El Administrador de estado fiable proporciona la capacidad de crear copias de seguridad coherentes sin bloquear ninguna operación de lectura ni escritura. Para ello, usa un mecanismo de persistencia de registro y punto de control. El Administrador de estado fiable instaura puntos de control aproximados (ligeros) en determinados puntos para aliviar la presión sobre el registro transaccional y mejorar los tiempos de recuperación. Cuando se llama a **BackupAsync**, el Administrador de estado confiable indica a todos los objetos confiables que copien los archivos de punto de control más recientes en una carpeta local de copia de seguridad. Después, el Administrador de estado confiable copia todas las entradas del registro desde el "puntero de inicio" hasta la entrada del registro más reciente en la carpeta de copia de seguridad. Puesto que todas las entradas del registro, de la primera a la última, se incluyen en la copia de seguridad y el Administrador de estado confiable conserva el registro de escritura previa, dicho administrador garantiza que todas las transacciones que se confirmen (aquellas en las que se devuelva **CommitAsync** correctamente) se incluyan en la copia de seguridad.

Cualquier transacción que se confirme después de la llamada a **BackupAsync** puede estar o no incluida en la copia de seguridad. Una vez que la plataforma rellene la carpeta de copia de seguridad (es decir, el tiempo de ejecución completó la copia de seguridad local), se invoca la devolución de llamada de la copia de seguridad del servicio. Esta devolución de llamada se encarga de mover la carpeta de copia de seguridad a una ubicación externa, como Almacenamiento de Azure.

### Restauración

El Administrador de estado confiable ofrece la posibilidad de restaurar desde una copia de seguridad mediante la API **RestoreAsync**. Solo se puede llamar al método **RestoreAsync** en **RestoreContext** dentro del método **OnDataLossAsync**. El valor booleano devuelto por **OnDataLossAsync** indica si el servicio restauró su estado a partir de un origen externo. Si el método **OnDataLossAsync** devuelve true, Service Fabric volverá a generar todas las demás réplicas a partir de esta principal. Service Fabric garantiza que las réplicas que van a recibir la llamada **OnDataLossAsync** realicen en primer lugar la transición al rol principal, pero no se les concede el estado de lectura o escritura. Esto supone que, para los implementadores de StatefulService, no se llamará a **RunAsync** hasta que **OnDataLossAsync** se complete correctamente. Después se invocará a **OnDataLossAsync** en la nueva réplica principal. Hasta que un servicio complete esta API correctamente (y devuelva true o false) y finalice la reconfiguración pertinente, se seguirá llamando a la API de uno en uno.


En primer lugar, **RestoreAsync** quita todo estado existente en la réplica principal en la que se llamó. Después, el Administrador de estado confiable crea todos los objetos confiables que existen en la carpeta de copia de seguridad. A continuación, se indica a los objetos confiables que restauren a partir de sus puntos de control en la carpeta de copia de seguridad. Por último, el Administrador de estado confiable recupera su propio estado de las entradas del registro en la carpeta de copia de seguridad y realiza la recuperación. Como parte del proceso de recuperación, se reproducen en los objetos confiables las operaciones a partir del "punto de partida" que tengan entradas de registro de confirmación en la carpeta de copia de seguridad. Este paso garantiza que el estado recuperado sea coherente.

<!---HONumber=AcomDC_0406_2016-->