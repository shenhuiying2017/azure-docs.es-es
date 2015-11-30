
<properties
   pageTitle="Seguridad de los clústeres de Service Fabric: roles del cliente | Microsoft Azure"
   description="En este artículo, se describen los dos roles del cliente y los permisos que otorga cada uno de ellos." 
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="subramar"/>



# Control de acceso basado en roles (para clientes de Service Fabric)

Service Fabrics admite dos tipos distintos de control de acceso para los clientes que están conectados a un clúster de Service Fabric: administrador y usuario. El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster. Los administradores tienen acceso completo a las capacidades de administración (incluidas las de lectura y escritura) y los usuarios, de forma predeterminada, tienen acceso de solo lectura a la administración (por ejemplo, pueden hacer consultas) y a la capacidad para resolver las aplicaciones y los servicios.

Los dos roles (administrador o cliente) se especifican en el momento de la creación del clúster al proporcionar certificados independientes para cada uno. Consulte la información sobre [seguridad del clúster de Service Fabric](service-fabric-cluster-security.md) para obtener más información acerca de cómo configurar un clúster seguro de Service Fabric.


## Configuración del control de acceso predeterminado


El tipo de control de acceso del administrador permite acceder a todas las API de FabricClient. Se puede realizar cualquier operación de lectura y escritura en el clúster de Service Fabric, incluidos los siguientes aspectos:

### Operaciones en servicios y aplicaciones 
* **CreateService**: creación de servicios 							
* **CreateServiceFromTemplate**: creación de servicios a partir de plantillas 							
* **UpdateService**: actualizaciones de servicios 							
* **DeleteService**: eliminación de servicios 							
* **ProvisionApplicationType**: aprovisionamiento del tipo de aplicación 							
* **CreateApplication**: creación de aplicaciones 							
* **DeleteApplication**: eliminación de aplicaciones 							
* **UpgradeApplication**: inicio o interrupción del proceso de actualización de las aplicaciones 							
* **UnprovisionApplicationType**: desaprovisionamiento de tipos de aplicaciones 							
* **MoveNextUpgradeDomain**: reanudación de las actualizaciones de aplicaciones con un dominio de actualización explícito 							
* **ReportUpgradeHealth**: reanudación de las actualizaciones de aplicaciones con el progreso de actualización actual 							
* **ReportHealth**: generación de informes sobre el estado 							
* **PredeployPackageToNode**: API de preimplementación 							
* **CodePackageControl**: reinicio de paquetes de código 							
* **RecoverPartition**: recuperación de una partición 							
* **RecoverPartitions**: recuperación de particiones 							
* **RecoverServicePartitions**: recuperación de particiones de servicio 							
* **RecoverSystemPartitions**: recuperación de particiones de servicio de sistema 							


### Operaciones del clúster
* **ProvisionFabric**: aprovisionamiento de MSI o manifiesto de clúster 							
* **UpgradeFabric**: inicio de actualizaciones del clúster 							
* **UnprovisionFabric**: desaprovisionamiento de MSI o manifiesto de clúster 							
* **MoveNextFabricUpgradeDomain**: reanudación de las actualizaciones del clúster con un dominio de actualización explícito 							
* **ReportFabricUpgradeHealth**: reanudación de las actualizaciones del clúster con el progreso de actualización actual 							
* **StartInfrastructureTask**: inicio de tareas de infraestructura 							
* **FinishInfrastructureTask**: finalización de tareas de infraestructura 							
* **InvokeInfrastructureCommand**: comandos de administración de tareas de infraestructura 							
* **ActivateNode**: activación de un nodo 							
* **DeactivateNode**: desactivación de un nodo 							
* **DeactivateNodeBatch**: desactivación de varios nodos 							
* **RemoveNodeDeactivations**: revertir la desactivación en varios nodos 							
* **GetNodeDeactivationStatus**: comprobación del estado de desactivación 							
* **NodeStateRemoved**: generación de informes sobre el estado del nodo eliminado 							
* **ReportFault**: generación de informes sobre errores 							
* **FileContent**: transferencia de archivos del cliente al almacén de imágenes (externo al clúster) 							
* **FileDownload**: inicio de la descarga de archivos del cliente al almacén de imágenes (externo al clúster) 							
* **InternalList**: operación de enumeración en listas de los archivos del cliente en el almacén de imágenes (interno) 							
* **Delete**: operación de eliminación del cliente del almacén de imágenes 							
* **Upload**: operación de carga del cliente en el almacén de imágenes 							
* **NodeControl**: inicio, detención y reinicio de los nodos 							
* **MoveReplicaControl**: movimiento de réplicas de un nodo a otro 							

### Otras operaciones
* **Ping**: acción de hacer ping al cliente 							
* **Query**: todas las consultas permitidas
* **NameExists**: comprobaciones de la existencia del identificador URI de la nomenclatura 							



El tipo de control de acceso del usuario está limitado a las siguientes operaciones de manera predeterminada. El control de acceso del administrador también permite acceder a estas operaciones:

* **EnumerateSubnames**: enumeración del identificador URI de la nomenclatura 							
* **EnumerateProperties**: enumeración de las propiedades de la nomenclatura 							
* **PropertyReadBatch**: operaciones de lectura de las propiedades de la nomenclatura 							
* ****GetServiceDescription: notificaciones del servicio de sondeo largo y descripciones del servicio de lectura
* **ResolveService**: resolución del servicio basado en reclamaciones 							
* **ResolveNameOwner**: resolución del propietario del identificador URI de la nomenclatura 							
* **ResolvePartition**: resolución de los servicios del sistema 							
* **ServiceNotifications**: notificaciones del servicio basadas en eventos 							
* **GetUpgradeStatus**: sondeo sobre el estado de actualización de la aplicación 							
* **GetFabricUpgradeStatus**: sondeo sobre el estado de actualización del clúster 							
* **InvokeInfrastructureQuery**: consulta sobre las tareas de infraestructura 							
* **List**: operación de enumeración en listas de los archivos del cliente en el almacén de imágenes 							
* **ResetPartitionLoad**: restablecimiento de la carga para failoverUnit 							
* ****ToggleVerboseServicePlacementHealthReporting: cambio al sistema de informes detallados sobre el estado de la ubicación de los servicios

## Cambio de la configuración predeterminada para los roles de cliente

En el archivo de manifiesto de clúster, se pueden proporcionar capacidades de administración al cliente si es necesario. Se pueden cambiar los valores predeterminados mediante la opción **Fabric Settings* durante la [creación del clúster](service-fabric-cluster-creation-via-portal.md) y especificar la configuración con la nomenclatura especificada anteriormente en el campo del **nombre** y **admin, user** en el campo del valor.

## Pasos siguientes

[Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)

[Creación de clústeres de Service Fabric](service-fabric-cluster-creation-via-portal.md)

<!---HONumber=Nov15_HO4-->