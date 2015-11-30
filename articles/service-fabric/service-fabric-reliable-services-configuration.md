<properties
   pageTitle="Descripción general de la configuración de Reliable Services de Azure Service Fabric | Microsoft Azure"
   description="Obtenga información sobre cómo configurar Reliable Services con estado en Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2015"
   ms.author="sumukhs"/>

# Configuración de los servicios fiables con estado
La configuración predeterminada del servicio fiable con estado puede modificarse cambiando el archivo "settings.xml" generado en la raíz del paquete de Visual Studio en la carpeta "Config" para cada servicio de la aplicación.

El tiempo de ejecución de Service Fabric busca los nombres de sección predefinidos en el archivo de "settings.xml" y utiliza los valores de configuración mientras crea los componentes en tiempo de ejecución subyacentes.

> [AZURE.NOTE]**NO** elimine o modifique los nombres de sección de las siguientes configuraciones en el archivo de "settings.xml" que se genera en la solución de Visual Studio.

## Configuración de seguridad del replicador
Las configuraciones de seguridad del replicador se utilizan para proteger el canal de comunicación que se usa durante la replicación. Esto significa que los servicios no podrán ver el tráfico de replicación del otro, con lo que se garantiza que los datos que se ofrecen también están seguros. De forma predeterminada, una sección de configuración de seguridad vacía no permite la seguridad de replicación.

### Nombre de sección
ReplicatorSecurityConfig

## Configuración de replicador
Las configuraciones de replicador se usan para configurar el replicador que es responsable de hacer que el estado del servicio fiable con estado resulte altamente fiable replicando y conservando el estado localmente. La configuración predeterminada es generada por la plantilla de Visual Studio y debe ser suficiente. En esta sección se habla sobre las configuraciones adicionales que están disponibles para optimizar el replicador.

### Nombre de sección
ReplicatorConfig

### Nombres de configuración

|Nombre|Unidad|Valor predeterminado|Comentarios|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Segundos|0,05|Período de tiempo durante el que el replicador del secundario espera después de recibir una operación antes de enviar una confirmación al principal. El resto de confirmaciones que se enviarán para las operaciones que se procesan dentro de este intervalo se envían como una respuesta.|
|ReplicatorEndpoint|N/D|N/D: RequiredParameter|Dirección IP y puerto que usará el replicador principal y secundario para comunicarse con otros replicadores del conjunto de réplicas. Esto debe hacer referencia a un extremo de recursos de TCP en el manifiesto del servicio. Consulte [Recursos del manifiesto del servicio](service-fabric-service-manifest-resources.md) para obtener más información sobre cómo definir recursos de extremo en el manifiesto del servicio. |
|MaxPrimaryReplicationQueueSize|Número de operaciones|8192|Número máximo de operaciones de la cola principal. Se libera una operación después de que el replicador principal reciba una confirmación de todos los replicadores secundarios. Este valor debe ser mayor que 64 y una potencia de 2.|
|MaxSecondaryReplicationQueueSize|Número de operaciones|16384|Número máximo de operaciones de la cola secundaria. Se libera una operación después de que su estado esté altamente disponible a través de persistencia. Este valor debe ser mayor que 64 y una potencia de 2.|
|CheckpointThresholdInMB|MB|200|Cantidad del espacio del archivo de registro después de que se compruebe el estado.|
|MaxRecordSizeInKB|KB|1024|Tamaño de registro más grande que puede escribir el replicador en el registro. Este valor debe ser un múltiplo de 4 y superior a 16.|
|OptimizeLogForLowerDiskUsage|boolean|true|Cuando es true, se configura el registro para que se cree el archivo de registro específico de la réplica mediante un archivo disperso de NTFS. Esto reduce el uso del espacio en disco real del archivo. Cuando es false, el archivo se crea con asignaciones fijas que ofrecen el mejor rendimiento de escritura.|
|MaxRecordSizeInKB|KB|1024|Tamaño de registro más grande que puede escribir el replicador en el registro. Este valor debe ser un múltiplo de 4 y superior a 16.|
|SharedLogId|guid|""|Especifica un guid único que debe usarse para identificar el archivo de registro compartido que se usa con esta réplica. Normalmente, los servicios no deberían usar esta opción; sin embargo, si se especifica SharedLogId, también debe especificarse SharedLogPath.|
|SharedLogPath|Nombre de ruta completo|""|Especifica la ruta de acceso completa donde se creará el archivo de registro compartido para esta réplica. Normalmente, los servicios no deberían usar esta opción; sin embargo, si se especifica SharedLogPath, también debe especificarse SharedLogId.|

## Archivo de configuración de muestra

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
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

El valor de CheckpointThresholdInMB controla la cantidad de espacio en disco que el replicador puede usar para almacenar información de estado en el archivo de registro específico de la réplica. Aumentarlo a un valor mayor que el predeterminado podría dar lugar a tiempos de reconfiguración más rápidos cuando se agrega una nueva réplica al conjunto debido a la transferencia de estado parcial que tiene lugar debido a la disponibilidad de más historial de operaciones en el registro, a la vez que se aumenta potencialmente aumenta el tiempo de recuperación de una réplica después de un bloqueo.

La configuración de OptimizeForLowerDiskUsage permite "sobreaprovisionar" espacio de archivo de registro para que las réplicas activas puedan almacenar información de estado adicional en sus archivos de registro mientras que réplicas inactivas usarían menos espacio en disco. Aunque esto permite hospedar más réplicas en un nodo que si hubiese falta de espacio en disco, al establecer OptimizeForLowerDiskUsage en false, la información de estado se escribe en los archivos de registro más rápidamente.

El MaxRecordSizeInKB define el tamaño máximo de un registro que puede escribir el replicador en el archivo de registro. En casi todos los casos, el tamaño predeterminado del registro de 1024 KB es óptimo; sin embargo, si el servicio está causando que elementos de datos más grandes formen parte de la información de estado, es posible que se deba aumentar este valor. Hay pocas ventajas en cambiar MaxRecordSizeInKB para que tenga un tamaño inferior a 1024, ya que los registros más pequeños solamente usan el espacio necesario para el registro más pequeño. Se espera que deba cambiarse solamente en raras ocasiones.

La configuración de SharedLogId y SharedLogPath siempre se usa conjuntamente y permite que un servicio utilice un registro compartido independiente del registro compartido predeterminado del nodo. Para obtener una mayor eficacia, todos los servicios posibles deben especificar el mismo registro compartido. Los archivos de registro compartido deben colocarse en discos que se usen únicamente para el archivo de registro compartido, para reducir la contención del movimiento de los cabezales. Se espera que deba cambiarse solamente en raras ocasiones.

<!---HONumber=Nov15_HO4-->