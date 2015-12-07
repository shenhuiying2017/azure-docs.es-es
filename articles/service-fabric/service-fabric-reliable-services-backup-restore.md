<properties
   pageTitle="Copia de seguridad y restauración de Reliable Services | Microsoft Azure"
   description="Documentación conceptual para la copia de seguridad y la restauración de Reliable Services de Service Fabric"
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
   ms.date="08/18/2015"
   ms.author="mcoskun"/>

# Copia de seguridad y restauración de Reliable Services

Service Fabric es una plataforma de alta disponibilidad que replica el estado entre varios nodos para mantener esta disponibilidad alta. Por lo tanto, incluso si se produce un error en un nodo del clúster, los servicios siguen estando disponibles. Aunque esta redundancia integrada proporcionada por la plataforma puede ser suficiente para algunos casos, en otros es conveniente que el servicio haga una copia de seguridad de los datos (en un almacén externo).

Por ejemplo, un servicio podría realizar copias de seguridad en los escenarios siguientes:

* En el caso de la pérdida permanente de todo un clúster de Service Fabric o de todos los nodos que ejecutan una partición determinada. Esto puede ocurrir cuando, por ejemplo, no se cuenta con replicación geográfica, todo el clúster se encuentra en un solo centro de datos y este centro de datos entero deja de funcionar.

* Errores administrativos por los cuales el estado se elimina o daña accidentalmente. Por ejemplo, esto puede ocurrir si un administrador con suficientes privilegios elimina el servicio por error.

* Errores en el servicio que causan daños en los datos. Por ejemplo, esto puede suceder cuando una actualización de código de servicio empieza a escribir datos defectuosos en una colección fiable. En tal caso, es posible que el código y los datos se tengan que revertir a un estado anterior.

* Procesamiento de datos sin conexión. Podría ser conveniente procesar los datos para inteligencia empresarial sin conexión de forma independiente del servicio que los genera.

La característica de copia de seguridad y restauración permite que los servicios basados en la API de Reliable Services creen y restauren copias de seguridad. Las API de copia de seguridad proporcionadas por la plataforma permiten que se creen copias de seguridad del estado de una partición sin bloquear las operaciones de lectura ni de escritura. Las API de restauración permiten restaurar el estado de una partición desde una copia de seguridad elegida.


## Realización de copias de seguridad

El autor del servicio tiene control total sobre cuándo se realizan las copias de seguridad y dónde se almacenan.

Para iniciar una copia de seguridad, el servicio debe invocar **IReliableStateManager.BackupAsync**. Las copias de seguridad solo se pueden realizar desde réplicas principales y necesitan que se conceda el estado de escritura.

Tal como se muestra a continuación, la sobrecarga más simple de **BackupAsync** acepta Func << BackupInfo  bool >> llamado **backupCallback**.

        await this.StateManager.BackupAsync(this.BackupCallbackAsync);

**BackupInfo** proporciona información sobre la copia de seguridad, incluida la ubicación de la carpeta donde el tiempo de ejecución la guardó (BackupInfo.Directory). La función de devolución de llamada espera mover BackupInfo.Directory a un almacén externo u otra ubicación. Además, esta función devuelve un valor booleano que indica si se pudo mover correctamente la carpeta de copia de seguridad a su ubicación de destino.

El código siguiente muestra cómo se puede usar backupCallback para cargar la copia de seguridad a Almacenamiento de Azure:

```C#
        private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
        {
            var backupId = Guid.NewGuid();

            await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

            return true;
        }
```

En el ejemplo anterior, **ExternalBackupStore** es la clase de ejemplo que se usa para interactuar con el almacenamiento de blobs de Azure y **UploadBackupFolderAsync** es el método que comprime la carpeta y la coloca en el almacén de blobs de Azure.

>[AZURE.NOTE]Solo puede haber una **BackupAsync** por réplica en proceso en cualquier momento dado. Más de una llamada a **BackupAsync** a la vez producirá una excepción **FabricBackupInProgressException** para limitar las copias de seguridad en proceso a una.[AZURE.NOTE]Si una réplica conmuta por error mientras se realiza una copia de seguridad, es posible que esta no se complete. Por lo tanto, una vez finalizada la conmutación por error, es responsabilidad del servicio reiniciar la copia de seguridad mediante la invocación de **BackupAsync** de ser necesario.

## Restauración de los datos

Se pueden clasificar los escenarios de restauración en que el servicio en ejecución tiene que restaurar datos desde el almacén de copia de seguridad en los siguientes:

1. La partición del servicio perdió datos. Por ejemplo, el disco de dos de las tres réplicas de una partición (incluida la réplica principal) sufre daños o se borra. Es posible que la nueva réplica principal necesite restaurar datos desde una copia de seguridad.

2. Se perdió la totalidad del servicio. Por ejemplo, un administrador quita todo el servicio y, por tanto, se deben restaurar tanto el servicio como los datos.

3. El servicio replicó datos dañados de la aplicación (por ejemplo, debido a un error de la aplicación). En este caso, el servicio se debe actualizar o revertir para eliminar la causa de los daños y se deben restaurar los datos no dañados.

Aunque hay muchos enfoques posibles, ofrecemos algunos ejemplos de uso de RestoreAsync para recuperarse de los escenarios anteriores.

## Pérdida de datos de la partición

En este caso, el tiempo de ejecución detectaría automáticamente la pérdida de datos e invocaría la API **OnDataLossAsync**.

El autor del servicio debe realizar lo siguiente para recuperar: –Invalide **IReliableStateManager** para devolver un nuevo ReliableStateManager y proporcione una función de devolución de llamada a la que llamar de producirse un evento de pérdida de datos. –Busque la última copia de seguridad en la ubicación externa que contiene las copias de seguridad del servicio. –Si el estado de la copia de seguridad más reciente es anterior a la nueva réplica principal, devuelva false. Esto garantizará que la nueva réplica principal no se sobrescriba con datos más antiguos. -Descargue la última copia de seguridad (y descomprímala en la carpeta de copia de seguridad si estaba comprimida). –Llame a **IReliableStateManager.RestoreAsync** con la ruta de acceso a la carpeta de copias de seguridad. –Devuelva true si la restauración se realizó correctamente.

El siguiente es un ejemplo de implementación del método **OnDataLossAsync** junto con la invalidación de **IReliableStateManager**.

```C#
        protected override IReliableStateManager CreateReliableStateManager()
        {
            return new ReliableStateManager(new ReliableStateManagerConfiguration(
                    onDataLossEvent: this.OnDataLossAsync));
        }

        protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
        {
            var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

            await this.StateManager.RestoreAsync(backupFolder);

            return true;
        }
```

>[AZURE.NOTE]RestorePolicy se establece en Seguro de forma predeterminada. Esto significa que la API RestoreAsync generará una ArgumentException si detecta que la carpeta de copia de seguridad contiene un estado igual o más antiguo que el estado contenido en esta réplica. Se puede usar RestorePolicy.Force para pasar por alto esta comprobación de seguridad.

## Eliminación o pérdida del servicio

Si se quita un servicio, en primer lugar se debe volver a crear para poder restaurar los datos. Es importante crear el servicio con la misma configuración, por ejemplo, el esquema de partición, para que los datos se restauren sin problemas. Una vez que el servicio está activo, se debe invocar la API para restaurar datos (**OnDataLossAsync** más arriba) en todas las particiones de este servicio. Una manera de conseguir esto es usando **FabricClient.ServiceManager.InvokeDataLossAsync** en cada partición.

Desde este punto, la implementación es igual que en el escenario anterior. Cada partición debe restaurar la última copia de seguridad pertinente desde el almacén externo. Una advertencia es que es posible que el identificador de partición sea diferente, ya que el tiempo de ejecución crea los identificadores de partición de forma dinámica. Por lo tanto, el servicio necesita almacenar el nombre de servicio y la información de partición adecuada para identificar la copia de seguridad correcta más reciente desde la que restaurar para cada partición.


## Replicación de datos de aplicación dañados

Si hay un error en la actualización de aplicación recién implementada, puede que produzca daños en los datos; por ejemplo, una actualización de la aplicación puede empezar a actualizar todos los registros de número de teléfono en un diccionario fiable con un código de área no válido. En este caso, los números de teléfono no válidos se replicarán porque Service Fabric no conoce la naturaleza de los datos que se almacenan.

Lo primero que debe hacer después de detectar un error tan notorio que cause daños en los datos es inmovilizar el servicio en el nivel de aplicación y, si es posible, actualizar a la versión del código de aplicación que no contenga el error. Sin embargo, incluso una vez corregido el código del servicio, es posible que los datos sigan dañados y que por tanto se deban restaurar. En tales casos, puede que no baste con restaurar la última copia de seguridad, ya que las copias de seguridad más recientes también pueden estar dañadas. Por lo tanto, se tiene que buscar la última copia de seguridad realizada antes de que los datos resultaran dañados.

Si no sabe qué copias de seguridad están dañadas o no, podría implementar un nuevo clúster de Service Fabric y restaurar las copias de seguridad de las particiones afectadas de la misma forma que en el escenario de "Eliminación o pérdida del servicio" anterior. Para cada partición, inicie la restauración de las copias de seguridad desde la más a la menos reciente. Una vez que encuentre una copia de seguridad sin los daños, mueva o elimine todas las copias de seguridad de esta partición que sean más recientes (que esa copia de seguridad). Repita este proceso para cada partición. Ahora, cuando se llama a **OnDataLossAsync** en la partición del clúster de producción, la última copia de seguridad que se encuentra en el almacén externo será la elegida por el proceso anterior.

Ahora se pueden usar los pasos en "Eliminación o pérdida del servicio" para restaurar el estado de la copia de seguridad de servicio al anterior a los daños producidos por el código con errores.


>[AZURE.NOTE]Con cada restauración, es posible que la copia de seguridad que se restaura sea anterior al estado de la partición antes de la pérdida de datos. Por este motivo, la restauración se debe usar solo como último recurso para recuperar tantos datos como sea posible.

>[AZURE.NOTE]La cadena que representa la ruta de acceso de la carpeta de copia de seguridad, así como las rutas de acceso de los archivos dentro de la carpeta de copia de seguridad, puede tener más de 255 caracteres, según la ruta de acceso de FabricDataRoot y la longitud del nombre del tipo de aplicación. Esto puede hacer que algunos métodos .NET, como **Directory.Move**, produzcan una excepción **PathTooLongException**. Como solución alternativa, se puede llamar directamente a las API de kernel32 como **CopyFile**.


## Bajo el capó: Más detalles sobre la copia de seguridad y la restauración

### Copia de seguridad
El Administrador de estado fiable proporciona la capacidad de crear copias de seguridad coherentes sin bloquear ninguna operación de lectura ni escritura. Para ello, usa un mecanismo de persistencia de registro y punto de control. El Administrador de estado fiable instaura puntos de control aproximados (ligeros) en determinados puntos para aliviar la presión sobre el registro transaccional y mejorar los tiempos de recuperación. Cuando se llama a IReliableStateManager.**BackupAsync**, el Administrador de estado fiable indica a todos los objetos fiables que copien los archivos de punto de control más recientes en una carpeta local de copia de seguridad. Después, el Administrador de estado fiable copia todas las entradas de registro desde el "puntero de inicio" hasta la entrada de registro más reciente en la carpeta de copia de seguridad. Puesto que todas las entradas del registro hasta la última se incluyen en la copia de seguridad y el Administrador de estado fiable conserva el registro de escritura previa, el Administrador de estado fiable garantiza que todas las transacciones que se confirmen (en que se devolviera CommitAsync correctamente) se incluyan en la copia de seguridad.

Cualquier transacción que se confirme después de la llamada a **BackupAsync** puede estar o no incluida en la copia de seguridad. Una vez que la plataforma rellene la carpeta de copia de seguridad (es decir, el tiempo de ejecución completó la copia de seguridad local), se invoca la devolución de llamada de la copia de seguridad del servicio. Esta devolución de llamada se encarga de mover la carpeta de copia de seguridad a una ubicación externa, como Almacenamiento de Azure.

### Restauración

El Administrador de estado fiable proporciona la capacidad de restaurar desde una copia de seguridad mediante la API IReliableStateManager.RestoreAsync. Solo se puede llamar al método RestoreAsync dentro del método **OnDataLossAsync**. El valor booleano devuelto por **OnDataLossAsync** indica si el servicio restauró su estado a partir de un origen externo. Si **OnDataLossAsync** devuelve true, Service Fabric volverá a generar todas las demás réplicas a partir de esta principal. Service Fabric garantiza que las réplicas que van a recibir **OnDataLossAsync** realicen en primer lugar la transición al rol principal, pero no reciban el estado de lectura ni escritura. Esto supone que, para los implementadores de StatefulService, no se llamará a RunAsync hasta que **OnDataLossAsync** se complete correctamente. Después se invocará **OnDataLossAsync** en la nueva réplica principal. Hasta que un servicio complete esta API correctamente (y devuelva true o false) y finalice la reconfiguración pertinente, se seguirá llamando a la API de uno en uno.


En primer lugar, RestoreAsync quita todo estado existente en la réplica principal en la que se lo llamó. Después, el Administrador de estado fiable crea todos los objetos fiables que existen en la carpeta de copia de seguridad. A continuación, se indica a los objetos fiables que restauren a partir de sus puntos de control en la carpeta de copia de seguridad. Por último, el Administrador de estado fiable recupera su propio estado de las entradas del registro en la carpeta de copia de seguridad y realiza la recuperación. Como parte del proceso de recuperación, se reproducen en los objetos fiables las operaciones a partir del "punto de partida" que tengan entradas de registro de confirmación en la carpeta de copia de seguridad. Este paso garantiza que el estado recuperado sea coherente.

<!---HONumber=AcomDC_1125_2015-->