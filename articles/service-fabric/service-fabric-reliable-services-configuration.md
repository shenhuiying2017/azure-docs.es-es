<properties
   pageTitle="Configuración de los servicios fiables con estado"
   description="Obtenga información acerca de cómo configurar servicios fiables con estado en Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/24/2015"
   ms.author="sumukhs"/>

# Configuración de los servicios fiables con estado
La configuración predeterminada del servicio fiable con estado puede modificarse cambiando el archivo "settings.xml" generado en la raíz del paquete de Visual Studio en la carpeta "Config" para cada servicio de la aplicación.

El tiempo de ejecución de Service Fabric busca los nombres de sección predefinidos en el archivo de "settings.xml" y utiliza los valores de configuración mientras crea los componentes en tiempo de ejecución subyacentes.

> [AZURE.NOTE]**NO** elimine o modifique los nombres de sección de las siguientes configuraciones en el archivo de "settings.xml" que se genera en la solución de Visual Studio.

## Configuración de seguridad del replicador
Las configuraciones de seguridad del replicador se utilizan para proteger el canal de comunicación que se usa durante la replicación. Esto significa que los servicios no podrán ver el tráfico de replicación del otro, con lo que se garantiza que los datos que se ofrecen también están seguros. De forma predeterminada, una sección de configuración de seguridad vacía no permite la seguridad de replicación.

### Nombre de sección
ReplicatorSecurityConfig

### Nombres de configuración
Consulte [Seguridad de replicación](../service-fabric/service-fabric-replication-security.md)

## Configuración de replicador
Las configuraciones de replicador se usan para configurar el replicador que es responsable de hacer que el estado del servicio fiable con estado resulte altamente fiable replicando y conservando el estado localmente. La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el replicador.

### Nombre de sección
ReplicatorConfig

### Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Comentarios|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,05|Período de tiempo durante el que el replicador del secundario espera después de recibir una operación antes de enviar una confirmación al principal. El resto de confirmaciones que se enviarán para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.|
|ReplicatorEndpoint|N/D|N/D: RequiredParameter|Dirección IP y puerto que usará el replicador principal y secundario para comunicarse con otros replicadores del conjunto de réplicas. Esto debe hacer referencia a un extremo de recursos de TCP en el manifiesto del servicio. Consulte [Recursos del manifiesto del servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir recursos de extremo en el manifiesto del servicio. |
|RetryInterval|Segundos|5|Período de tiempo después del cual el replicador vuelve a transmitir un mensaje si no recibe una confirmación de una operación.|
|MaxReplicationMessageSize|Bytes|50 MB|Tamaño máximo de los datos de replicación que se puede transmitir en un único mensaje.|
|MaxPrimaryReplicationQueueSize|Número de operaciones|1024|Número máximo de operaciones de la cola principal. Se libera una operación después de que el replicador principal reciba una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|2048|Número máximo de operaciones de la cola secundaria. Se libera una operación después de que su estado esté altamente disponible a través de persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxStreamSizeInMB|MB|1024|Cantidad de espacio de archivo de registro que se ha reservado. El tamaño en bytes debe ser mayor que 16 veces MaxRecordSize en bytes.|
|MaxRecordSizeInKB|KB|1024|Tamaño de registro más grande que puede escribir el replicador en el registro. Este valor debe ser un múltiplo de 4 y superior a 16.|
|OptimizeForLocalSSD|boolean|false|Cuando es true, se configura el registro para que la información de estado se escriba directamente en el archivo de registro específico de la réplica. Esto ofrece el mejor rendimiento cuando los archivos de registro están en soportes de disco de estado sólido o cuando la velocidad de E/S de disco de máquina virtual está altamente limitada. Cuando es false, la información de estado se almacena en el archivo de registro compartido en primer lugar y, después, se descarga en el archivo de registro específico.|
|OptimizeLogForLowerDiskUsage|boolean|false|Cuando es true, se configura el registro para que se cree el archivo de registro específico de la réplica mediante un archivo disperso de NTFS. Esto reduce el uso del espacio en disco real del archivo. Cuando es false, el archivo se crea con asignaciones fijas que ofrecen el mejor rendimiento de escritura.|
|SharedLogId|guid|""|Especifica un guid único que debe usarse para identificar el archivo de registro compartido que se usa con esta réplica. Normalmente, los servicios no deberían usar esta opción; sin embargo, si se especifica SharedLogId, también debe especificarse SharedLogPath.|
|SharedLogPath|Nombre de ruta completo|""|Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deberían usar esta opción; sin embargo, si se especifica SharedLogPath, también debe especificarse SharedLogId.|

## Archivo de configuración de muestra

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## Comentarios
BatchAcknowledgementInterval controla la latencia de replicación. Un valor de "0" ofrecerá la menor latencia posible, a costa del rendimiento (como deben enviarse y procesarse más mensajes de confirmación, cada uno con menos confirmaciones). Cuanto mayor sea el valor de BatchAcknowledgementInterval, mayor será el rendimiento general de la replicación a costa de una mayor latencia de la operación. Esto se traduce directamente en la latencia de transacciones confirmadas.

El valor de MaxStreamSizeInMB determina la cantidad de espacio en disco que el replicador puede usar para almacenar información de estado en el archivo de registro específico de la réplica. Debido a que el estado de la información almacenada se usa para permitir que otra réplica coincida con el estado de una réplica principal, por lo general es mejor tener un archivo de registro más grande, ya que esto reducirá la cantidad de tiempo que tarda la otra réplica en coincidir con el estado de la réplica principal. Sin embargo, es posible que los archivos de registro más grandes consuman más espacio en disco y reduzcan así el número de réplicas que se pueden hospedar en un nodo determinado.

La configuración de OptimizeForLowerDiskUsage permite "sobreaprovisionar" espacio de archivo de registro para que las réplicas activas puedan almacenar información de estado adicional en sus archivos de registro mientras que réplicas inactivas usarían menos espacio en disco. Aunque esto permite hospedar más réplicas en un nodo que si hubiese falta de espacio en disco, al establecer OptimizeForLowerDiskUsage en false, la información de estado se escribe en los archivos de registro más rápidamente.

La configuración de OptimizeForLocalSSD se usa para deshabilitar la información de estado de escritura en el archivo de registro compartido antes de descargar en el archivo de registro específico. Normalmente se debe establecer cuando el almacenamiento en disco local del archivo de registro específico es un soporte de estado sólido debido a que minimizar el movimiento de cabezas de disco no es un problema. También puede usarse cuando la máquina virtual está realizando una gran cantidad de E/S de disco local y las tasas de E/S de disco local están limitadas significativamente por el host de máquina virtual.

El MaxRecordSizeInKB define el tamaño máximo de un registro que puede escribir el replicador en el archivo de registro. En casi todos los casos, el tamaño predeterminado del registro de 1024 KB es óptimo; sin embargo, si el servicio está causando que elementos de datos más grandes formen parte de la información de estado, es posible que se deba aumentar este valor. Hay pocas ventajas en cambiar MaxRecordSizeInKB para que tenga un tamaño inferior a 1024, ya que los registros más pequeños solamente usan el espacio necesario para el registro más pequeño. Se espera que deba cambiarse solamente en raras ocasiones.

La configuración de SharedLogId y SharedLogPath siempre se usa conjuntamente y permite que un servicio utilice un registro compartido independiente del registro compartido predeterminado del nodo. Para obtener una mayor eficacia, todos los servicios posibles deben especificar el mismo registro compartido. Los archivos de registro compartido deben colocarse en discos que se usen únicamente para el archivo de registro compartido, para reducir la contención del movimiento de los cabezales. Se espera que deba cambiarse solamente en raras ocasiones.
 

<!---HONumber=August15_HO6-->