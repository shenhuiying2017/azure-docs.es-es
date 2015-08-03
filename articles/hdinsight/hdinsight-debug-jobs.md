<properties 
	pageTitle="Depuración de Hadoop en HDInsight: mensajes de error | Microsoft Azure" 
	description="Conozca los mensajes de error que puede recibir cuando administre HDInsight con PowerShell, así como los pasos que debe seguir para la recuperación." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="jgao"/>

# Depuración de Hadoop en HDInsight: interpretación de mensajes de error

Con los mensajes de error incluidos en este tema pretendemos ayudar a los usuarios de Hadoop en HDInsight de Azure a comprender algunas situaciones de error con las que se pueden encontrar al administrar el servicio con Azure PowerShell, así como asesorarlos sobre los pasos que pueden seguir para recuperarse del error.

Algunos de estos mensajes de error también podrían aparecer en el portal de Azure cuando se utiliza para administrar clústeres de HDInsight. Sin embargo, no es posible presentar de forma tan pormenorizada otros mensajes de error que pueden aparecer allí debido a las restricciones que afectan a las acciones de subsanación posibles en este contexto. Otros mensajes de error se asocian a los contextos en que la mitigación resulta obvia. Por ejemplo, si se infringen las restricciones de los parámetros, el mensaje emerge en el lado derecho del cuadro donde se escribió el valor. Se trata de un caso de solicitud de demasiados nodos de datos. La solución es reducir el número hasta un valor permitido, es decir, 22 o menos.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

En la sección [Errores de HDInsight](#hdinsight-error-messages) se enumeran los errores que un usuario puede encontrarse en Azure PowerShell o en el portal de Azure, ordenados alfabéticamente según el nombre y vinculados a una entrada de la sección [Descripción y mitigación de los errores](#discription-mitigation-errors), donde se facilita la siguiente información sobre dichos errores:
 	
- **Descripción**: el mensaje de error que ven los usuarios.	
- **Mitigación**: pasos que se pueden seguir para recuperarse del error. 

###Códigos de error de HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided) [AzureRegionNotSupported](#AzureRegionNotSupported) [ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound) [ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord) [ClusterNameUnavailable](#ClusterNameUnavailable) [ClusterUserNameInvalid](#ClusterUserNameInvalid) [ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord) [ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName) [DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound) [DeploymentDeletionFailure](#DeploymentDeletionFailure) [DnsMappingNotFound](#DnsMappingNotFound) [DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest) [DuplicateClusterInHostedService](#DuplicateClusterInHostedService) [FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus) [HdiRestoreClusterAltered](#HdiRestoreClusterAltered) [HeadNodeConfigNotFound](#HeadNodeConfigNotFound) [HeadNodeConfigNotFound](#HeadNodeConfigNotFound) [HostedServiceCreationFailure](#HostedServiceCreationFailure) [HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment) [HostedServiceNotFound](#HostedServiceNotFound) [HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment) [InsufficientResourcesCores](#InsufficientResourcesCores) [InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices) [InternalErrorRetryRequest](#InternalErrorRetryRequest) [InvalidAzureStorageLocation](#InvalidAzureStorageLocation) [InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode) [InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode) [InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion) [InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName) [InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey) [InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat) [MoreThanOneHeadNode](#MoreThanOneHeadNode) [OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest) [ParameterNullOrEmpty](#ParameterNullOrEmpty) [PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure) [RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable) [StorageAccountNotColocated](#StorageAccountNotColocated) [SubscriptionIdNotActive](#SubscriptionIdNotActive) [SubscriptionIdNotFound](#SubscriptionIdNotFound) [UnableToResolveDNS](#UnableToResolveDNS) [UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource) [VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable) [VersionNotSupported](#VersionNotSupported) [VersionNotSupportedInRegion](#VersionNotSupportedInRegion) [WasbAccountConfigNotFound](#WasbAccountConfigNotFound)



## <a id="discription-mitigation-errors"></a>Descripción y mitigación de los errores 


### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
- **Descripción**: proporcione los datos de la base de datos SQL de Azure de al menos un componente a fin de utilizar la configuración personalizada para las tiendas de metadatos de Hive y Oozie.   
- **Mitigación**: el usuario debe facilitar una tienda de metadatos SQL de Azure válida y volver a enviar la solicitud.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
- **Descripción**: no se pudo crear un clúster en la región *nombredelaregión*. Utilice una región de HDInsight válida y vuelva a enviar la solicitud.   
- **Mitigación**: el cliente debe crear el clúster en una región que actualmente lo admita: sudeste asiático, Europa occidental, norte de Europa y este u oeste de Estados Unidos.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
- **Descripción**: el servidor no encuentra el registro de clúster solicitado.  
- **Mitigación**: vuelva a intentarlo. 

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
- **Descripción**: el nombre DNS del clúster *nombreDNS* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
- **Mitigación**: asegúrese de que se haya utilizado un nombre DNS válido para el clúster que empiece y acabe con un carácter alfanumérico y que no contenga ningún carácter especial, aparte del guión (-), y, a continuación, vuelva a intentarlo.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
- **Descripción**: el nombre de clúster *nombredeclúster* no está disponible. Elija otro nombre.  
- **Mitigación**: el usuario debe especificar un nombre de clúster que sea único y que no esté ya en uso, y, a continuación, volver a intentarlo. Si el usuario está utilizando el portal, la IU le informará durante los pasos de creación si el nombre de clúster ya está en uso. 
 

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
- **Descripción**: la contraseña del clúster no es válida. La contraseña debe cumplir estos requisitos: tener al menos 10 caracteres, incluir como mínimo un número, una letra mayúscula, una letra minúscula y un carácter especial, no contener espacios, y no estar formada a partir del nombre de usuario.  
- **Mitigación**: proporcione una contraseña de clúster válida y vuelva a intentarlo. 

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
- **Descripción**: el nombre de usuario del clúster no es válido. Asegúrese de que no contenga caracteres especiales ni espacios.  
- **Mitigación**: proporcione un nombre de usuario de clúster válido y vuelva a intentarlo.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
- **Descripción**: el nombre DNS del clúster *nombreDNSdelclúster* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
- **Mitigación**: proporcione un nombre de usuario de clúster DNS válido y vuelva a intentarlo.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
- **Descripción**: el nombre de contenedor del URI *URIdecontenedor* y el nombre DNS *nombreDNS* del cuerpo de la solicitud deben coincidir.  
- **Mitigación**: asegúrese de que el nombre del contenedor y el nombre DNS coincidan y vuelva a intentarlo.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
- **Descripción**: la configuración de clúster no es válida. No se encuentra ninguna definición de nodo de datos en el tamaño de nodo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure 	
- **Descripción**: error al eliminar la implementación del clúster.  
- **Mitigación**: vuelva a intentar la eliminación.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
- **Descripción**: error de configuración del servicio. No se encuentra la información de asignación de DNS requerida.  
- **Mitigación**: elimine el clúster y cree uno nuevo.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
- **Descripción**: intento de duplicación de un contenedor de clúster. Existe un registro con el nombre *nombredelcontenedor*, pero las propiedades Etag no coinciden.   
- **Mitigación**: proporcione un nombre exclusivo para el contenedor y vuelva a intentarlo. 

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* ya incluye un clúster. Los servicios hospedados no pueden contener varios clústeres.  
- **Mitigación**: hospede el clúster en otro servicio hospedado. 

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
- **Descripción**: el servidor no pudo actualizar el estado de la implementación del clúster.  
- **Mitigación**: vuelva a intentarlo. Si esto ocurre varias veces, póngase en contacto con CSS. 

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
- **Descripción**: el clúster *nombredelclúster* se eliminó durante el mantenimiento. Vuelva a crearlo.     
- **Mitigación**: vuelva a crear el clúster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
- **Descripción**: configuración de clúster no válida. Configuración de nodo principal requerida no encontrada en los tamaños de nodo.
- **Mitigación**: vuelva a intentarlo.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
- **Descripción**: no se puede crear el servicio hospedado *nombredelserviciohospedado*. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* ya incluye una implementación de producción. Los servicios hospedados no pueden contener varias implementaciones de producción. Vuelva a intentarlo con un nombre de clúster diferente.   
- **Mitigación**: utilice un nombre de clúster diferente y vuelva a intentarlo.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
- **Descripción**: no se encuentra el servicio hospedado *nombredelserviciohospedado* del clúster.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo. 

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
- **Descripción**: el servicio hospedado *nombredelserviciohospedado* no tiene asociada ninguna implementación.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo. 

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
- **Descripción**: al identificador de suscripción *identificadordelasuscripción* no le quedan recursos principales para crear el clúster *nombredelclúster*. Necesario: *recursosrequeridos*, Disponible: *recursosdisponibles*.  
- **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
- **Descripción**: el identificador de suscripción *identificadordelasuscripción* no tiene cuota para un nuevo servicio hospedado y, por tanto, no puede crear el clúster *nombredelclúster*.  
- **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
- **Descripción**: se produjo un error interno en el servidor. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo. 

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
- **Descripción**: la ubicación de almacenamiento de Azure *nombrederegióndedatos* no es válida. Asegúrese de que la región sea correcta y vuelva a intentarlo.   
- **Mitigación**: elija una ubicación de almacenamiento compatible con HDInsight, compruebe que el clúster esté colocalizado y vuelva a intentarlo. 

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
- **Descripción**: tamaño de máquina virtual no válido para los nodos de datos. Únicamente el tamaño de máquina virtual grande es compatible con todos los nodos de datos.  
- **Mitigación**: especifique el tamaño de nodo admitido para el nodo de datos y vuelva a intentarlo. 

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
- **Descripción**: tamaño de máquina virtual no válido para un nodo principal. Únicamente el tamaño de máquina virtual extragrande es compatible con el nodo principal.  
- **Mitigación**: especifique el tamaño de nodo admitido para el nodo principal y vuelva a intentarlo.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
- **Descripción**: el identificador de suscripción *identificadordelasuscripción* que se está utilizando no dispone de suficientes permisos para ejecutar la operación de eliminación del clúster *nombredelclúster*.  
- **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
- **Descripción**: el nombre del contenedor de blobs de la cuenta de almacenamiento externa *nombredelcontenedor* no es válido. Asegúrese de que el nombre comience con una letra y solo contenga minúsculas, números y guiones.  
- **Mitigación**: especifique un nombre de contenedor de blobs de cuenta de almacenamiento válido y vuelva a intentarlo.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
- **Descripción**: para configurar la cuenta de almacenamiento externa *nombredelacuentadealmacenamiento* es necesario establecer primero los datos de la clave secreta.  
- **Mitigación**: especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentarlo.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
- **Descripción**: el encabezado de la versión *encabezadodelaversión* no tiene el formato válido de aaaa-mm-dd.  
- **Mitigación**: especifique un formato válido para el encabezado de la versión y vuelva a intentarlo. 

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
- **Descripción**: configuración de clúster no válida. Se encontró más de una configuración de nodo principal.  
- **Mitigación**: edite la configuración para que solo se especifique un nodo principal. 

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
- **Descripción**: la operación no se pudo completar en el tiempo permitido, o bien se alcanzó el número máximo de intentos. Vuelva a intentarlo.  
- **Mitigación**: vuelva a intentarlo. 

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
- **Descripción**: el parámetro *nombredelparámetro* no puede ser nulo ni estar vacío.  
- **Mitigación**: especifique un valor válido para el parámetro. 

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
- **Descripción**: al menos uno de los datos de la solicitud de creación de clúster no es válido. Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.  
- **Mitigación**: asegúrese de que los valores escritos sean correctos y vuelva a intentarlo. 

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
- **Descripción**: la funcionalidad de región no está disponible para la región *nombredelaregión* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: especifique una región compatible con los clústeres de HDInsight. Las regiones admitidas son: sudeste asiático, Europa occidental, norte y oeste de Europa y este u oeste de Estados Unidos. 

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
- **Descripción**: la cuenta de almacenamiento *nombredelacuentadealmacenamiento* se encuentra en la región *nombredelaregiónactual*. Debería coincidir con la región del clúster *nombredelaregióndelclúster*.  
- **Mitigación**: especifique una cuenta de almacenamiento situada en la misma región que el clúster, o bien, si la cuenta de almacenamiento ya contiene datos, cree un nuevo clúster en la misma región que la cuenta de almacenamiento existente. Si está utilizando el portal, la IU le informará de este problema con antelación. 

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
- **Descripción**: el identificador de suscripción proporcionado *identificadordelasuscripción* no está activo.  
- **Mitigación**: vuelva a activar la suscripción u obtenga una nueva suscripción válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
- **Descripción**: no se encuentra el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: compruebe que el identificador de la suscripción sea válido y vuelva a intentarlo. 

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
- **Descripción**: no se puede resolver el DNS *URLdelDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: proporcione una URL de blob válida. La dirección URL debe ser totalmente válida, entre otras cosas debe empezar por *http://* y terminar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure. 
### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
- **Descripción**: no se puede comprobar la ubicación del recurso *URLdeDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: proporcione una URL de blob válida. La dirección URL debe ser totalmente válida, entre otras cosas debe empezar por *http://* y terminar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure. 
### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
- **Descripción**: la funcionalidad de versión no está disponible para la versión *versiónespecificada* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: elija una versión disponible y vuelva a intentarlo. 

### <a id="VersionNotSupported"></a>VersionNotSupported
- **Descripción**: la versión *versiónespecificada* no es compatible.   
- **Mitigación**: elija una versión compatible y vuelva a intentarlo.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
- **Descripción**: la versión *versiónespecificada* no está disponible en la región de Azure *regiónespecificada*.  
- **Mitigación**: elija una versión admitida en la región y vuelva a intentarlo. 

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
- **Descripción**: configuración de clúster no válida. No se encuentra la configuración de cuenta WASB requerida en las cuentas externas.  
- **Mitigación**: compruebe que la cuenta exista y que esté bien especificada en la configuración, y luego, vuelva a intentarlo. 

## <a id="resources"></a>Additional Debugging Resources

* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






 

<!---HONumber=July15_HO4-->