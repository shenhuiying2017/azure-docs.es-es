<properties urlDisplayName="Debug HDInsight Hadoop Errors" pageTitle="Depuración de Hadoop en HDInsight: mensajes de error | Azure" metaKeywords="hdinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug Hadoop in HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Depuración de Hadoop en HDInsight: mensajes de error

##Introducción
Con los mensajes de error incluidos en este tema pretendemos ayudar a los usuarios de Hadoop en HDInsight de Azure a comprender algunas situaciones de error con las que se pueden encontrar al administrar el servicio con Azure PowerShell, así como asesorarlos sobre los pasos que pueden seguir para recuperarse del error. 

Algunos de estos mensajes de error también podrían aparecer en el portal de Azure cuando se utiliza para administrar clústeres de HDInsight. Sin embargo, no es posible presentar de forma tan pormenorizada otros mensajes de error que pueden aparecer allí debido a las restricciones que afectan a las acciones de subsanación posibles en este contexto. Otros mensajes de error se asocian a los contextos en que la mitigación resulta obvia. Por ejemplo, si se infringen las restricciones de los parámetros, el mensaje emerge en el lado derecho del cuadro donde se escribió el valor. Se trata de un caso de solicitud de demasiados nodos de datos. La solución es reducir el número hasta un valor permitido, es decir, 22 o menos.

![HDI.Debugging.ErrorMessages.Portal][image-hdi-debugging-error-messages-portal]

En la sección [Errores de HDInsight](#hdinsight-error-messages) se enumeran los errores que un usuario puede encontrarse en Azure PowerShell o en el portal de Azure, ordenados por orden alfabético según el nombre y vinculados a una entrada de la sección [Descripción y mitigación de los errores](#discription-mitigation-errors), donde se facilita la siguiente información sobre los mismos:
 	
- **Descripción**: El mensaje de error que ven los usuarios	
- **Mitigación**: Pasos que se pueden seguir para recuperarse del error 

###Errores de HDInsight

[AtleastOneSqlMetastoreMustBeProvided](#AtleastOneSqlMetastoreMustBeProvided)
[AzureRegionNotSupported](#AzureRegionNotSupported)		
[ClusterContainerRecordNotFound](#ClusterContainerRecordNotFound)	 
[ClusterDnsNameInvalidReservedWord](#ClusterDnsNameInvalidReservedWord)		
[ClusterNameUnavailable](#ClusterNameUnavailable)	
[ClusterUserNameInvalid](#ClusterUserNameInvalid)	
[ClusterUserNameInvalidReservedWord](#ClusterUserNameInvalidReservedWord)	
[ContainerNameMisMatchWithDnsName](#ContainerNameMisMatchWithDnsName)	
[DataNodeDefinitionNotFound](#DataNodeDefinitionNotFound)	
[DeploymentDeletionFailure](#DeploymentDeletionFailure)	
[DnsMappingNotFound](#DnsMappingNotFound)	
[DuplicateClusterContainerRequest](#DuplicateClusterContainerRequest)	
[DuplicateClusterInHostedService](#DuplicateClusterInHostedService)		
[FailureToUpdateDeploymentStatus](#FailureToUpdateDeploymentStatus)		
[HdiRestoreClusterAltered](#HdiRestoreClusterAltered)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	
[HeadNodeConfigNotFound](#HeadNodeConfigNotFound)	 
[HostedServiceCreationFailure](#HostedServiceCreationFailure)	
[HostedServiceHasProductionDeployment](#HostedServiceHasProductionDeployment)	
[HostedServiceNotFound](#HostedServiceNotFound)		
[HostedServiceWithNoDeployment](#HostedServiceWithNoDeployment)		
[InsufficientResourcesCores](#InsufficientResourcesCores)	
[InsufficientResourcesHostedServices](#InsufficientResourcesHostedServices)		
[InternalErrorRetryRequest](#InternalErrorRetryRequest)		
[InvalidAzureStorageLocation](#InvalidAzureStorageLocation)		
[InvalidNodeSizeForDataNode](#InvalidNodeSizeForDataNode)	
[InvalidNodeSizeForHeadNode](#InvalidNodeSizeForHeadNode)	
[InvalidRightsForDeploymentDeletion](#InvalidRightsForDeploymentDeletion)	
[InvalidStorageAccountBlobContainerName](#InvalidStorageAccountBlobContainerName)	
[InvalidStorageAccountConfigurationSecretKey](#InvalidStorageAccountConfigurationSecretKey)	
[InvalidVersionHeaderFormat](#InvalidVersionHeaderFormat)	
[MoreThanOneHeadNode](#MoreThanOneHeadNode)	
[OperationTimedOutRetryRequest](#OperationTimedOutRetryRequest)	
[ParameterNullOrEmpty](#ParameterNullOrEmpty)	
[PreClusterCreationValidationFailure](#PreClusterCreationValidationFailure)	
[RegionCapabilityNotAvailable](#RegionCapabilityNotAvailable)	
[StorageAccountNotColocated](#StorageAccountNotColocated)	
[SubscriptionIdNotActive](#SubscriptionIdNotActive)	
[SubscriptionIdNotFound](#SubscriptionIdNotFound)	
[UnableToResolveDNS](#UnableToResolveDNS)	
[UnableToVerifyLocationOfResource](#UnableToVerifyLocationOfResource)	
[VersionCapabilityNotAvailable](#VersionCapabilityNotAvailable)	
[VersionNotSupported](#VersionNotSupported)	
[VersionNotSupportedInRegion](#VersionNotSupportedInRegion)	
[WasbAccountConfigNotFound](#WasbAccountConfigNotFound)	



<h2><a id="discription-mitigation-errors"></a>Descripción y mitigación de los errores</h2> 


<h3><a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided</h3>
- **Descripción**: Proporcione los datos de la base de datos SQL de Azure de al menos un componente a fin de utilizar la configuración personalizada para las tiendas de metadatos de Hive y Oozie.   
- **Mitigación**: El usuario debe facilitar una tienda de metadatos de Azure SQL válida y volver a enviar la solicitud.  

<h3><a id="AzureRegionNotSupported"></a>AzureRegionNotSupported</h3>
- **Descripción**: No se pudo crear un clúster en la región *nombredelaregión*. Utilice una región de HDInsight válida y vuelva a enviar la solicitud.   
- **Mitigación**: El cliente debe crear el clúster en una región que actualmente lo admita: Sudeste asiático, Europa occidental, norte de Europa, este de Estados Unidos u oeste de Estados Unidos.  

<h3><a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound</h3>
- **Descripción**: El servidor no pudo encontrar el registro de clúster solicitado.  
- **Mitigación**: Vuelva a intentarlo. 

<h3><a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord</h3>
- **Descripción**: El nombre DNS del clúster *nombreDNS* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de '-'.
- **Mitigación**: Asegúrese de que utilizó un nombre DNS válido para el clúster que empiece y acabe con un carácter alfanumérico y que no contenga ningún carácter especial aparte del guión (-), y, a continuación, vuelva a intentarlo.

<h3><a id="ClusterNameUnavailable"></a>ClusterNameUnavailable</h3>
- **Descripción**: El nombre de clúster *nombredeclúster* no está disponible. Elija otro nombre.  
- **Mitigación**: El usuario debe especificar un nombre de clúster que sea único y que no esté ya en uso, y, a continuación, volver a intentarlo. Si el usuario está utilizando el portal, la IU le informará durante los pasos de creación si el nombre de clúster ya está en uso. 
 

<h3><a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid</h3>
- **Descripción**: La contraseña del clúster no es válida. La contraseña debe cumplir estos requisitos: tener al menos 10 caracteres, incluir como mínimo un número, una letra mayúscula, una letra minúscula y un carácter especial, no contener espacios, y no estar formada a partir del nombre de usuario.  
- **Mitigación**: Proporcione una contraseña de clúster válida y vuelva a intentarlo. 

<h3><a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid</h3>
- **Descripción**: El nombre de usuario del clúster no es válido. Asegúrese de que no contenga caracteres especiales ni espacios.  
- **Mitigación**: Proporcione un nombre de usuario de clúster válido y vuelva a intentarlo.

<h3><a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord</h3>
- **Descripción**: El nombre DNS del clúster *nombreDNSdelclúster* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de '-'.
- **Mitigación**: Proporcione un nombre de usuario de clúster DNS válido y vuelva a intentarlo.

<h3><a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName</h3>
- **Descripción**: El nombre de contenedor del URI *URIdecontenedor* y el nombre DNS *nombreDNS* del cuerpo de la solicitud deben coincidir.  
- **Mitigación**: Asegúrese de que el nombre del contenedor y el nombre DNS coincidan y vuelva a intentarlo.

<h3><a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound</h3>
- **Descripción**: Configuración de clúster no válida. No se puede encontrar ninguna definición de nodo de datos en el tamaño de nodo.  
- **Mitigación**: Vuelva a intentarlo.

<h3><a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure</h3>	
- **Descripción**: Error al eliminar la implementación del clúster.
- **Mitigación**: Vuelva a intentar la eliminación.

<h3><a id="DnsMappingNotFound"></a>DnsMappingNotFound</h3>	
- **Descripción**: Error de configuración del servicio. Información de asignación de DNS requerida no encontrada.  
- **Mitigación**: Elimine el clúster y cree uno nuevo.

<h3><a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest</h3>
- **Descripción**: Intento de duplicación de un contenedor de clúster. Existe un registro con el nombre *nombredelcontenedor*, pero las propiedades Etag no coinciden.   
- **Mitigación**: Proporcione un nombre exclusivo para el contenedor y vuelva a intentarlo. 

<h3><a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService</h3>
- **Descripción**: El servicio hospedado *nombredelserviciohospedado* ya incluye un clúster. Los servicios hospedados no pueden contener varios clústeres.
- **Mitigación**: Hospede el clúster en otro servicio hospedado. 

<h3><a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus</h3>
- **Descripción**: El servidor no pudo actualizar el estado de la implementación del clúster.  
- **Mitigación**: Vuelva a intentarlo. Si esto ocurre varias veces, póngase en contacto con CSS. 

<h3><a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered</h3>
- **Descripción**: El clúster *nombredelclúster* se eliminó durante el mantenimiento. Vuelva a crearlo.     
- **Mitigación**: Vuelva a crear el clúster.

<h3><a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound</h3>
- **Descripción**: Configuración de clúster no válida. Configuración de nodo principal requerida no encontrada en los tamaños de nodo.
- **Mitigación**: Vuelva a intentarlo.

<h3><a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure</h3>
- **Descripción**: No se puede crear el servicio hospedado *nombredelserviciohospedado*. Vuelva a intentarlo.  
- **Mitigación**: Vuelva a intentarlo.

<h3><a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment</h3>
- **Descripción**: El servicio hospedado *nombredelserviciohospedado* ya incluye una implementación de producción. Los servicios hospedados no pueden contener varias implementaciones de producción. Vuelva a intentarlo con un nombre de clúster diferente.   
- **Mitigación**: Utilice un nombre de clúster diferente y vuelva a intentarlo.

<h3><a id="HostedServiceNotFound"></a>HostedServiceNotFound</h3>
- **Descripción**: No se encontró el servicio hospedado *nombredelserviciohospedado* del clúster.  
- **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo. 

<h3><a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment</h3>
- **Descripción**: El servicio hospedado *nombredelserviciohospedado* no tiene asociada ninguna implementación.  
- **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo. 

<h3><a id="InsufficientResourcesCores"></a>InsufficientResourcesCores</h3>
- **Descripción**: Al identificador de suscripción *identificadordelasuscripción* no le quedan recursos principales para crear el clúster *nombredelclúster*. Requeridos: *resourcesRequired*, Disponibles: *recursosdisponibles*.  
- **Mitigación**: Libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

<h3><a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices</h3>
- **Descripción**: El identificador de suscripción *identificadordelasuscripción* no tiene cuota para un nuevo servicio hospedado y, por tanto, no puede crear el clúster *nombredelclúster*.  
- **Mitigación**: Libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

<h3><a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest</h3>
- **Descripción**: Se produjo un error interno en el servidor. Vuelva a intentarlo.  
- **Mitigación**: Vuelva a intentarlo. 

<h3><a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation</h3>
- **Descripción**: La ubicación de almacenamiento de Azure *nombrederegióndedatos* no es válida. Asegúrese de que la región sea correcta y vuelva a intentarlo.   
- **Mitigación**: Seleccione una ubicación de almacenamiento compatible con HDInsight, compruebe que el clúster esté colocalizado y vuelva a intentarlo. 

<h3><a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode</h3>
- **Descripción**: Tamaño de máquina virtual no válido para los nodos de datos. Únicamente el tamaño de máquina virtual grande es compatible con todos los nodos de datos.  
- **Mitigación**: Especifique el tamaño de nodo admitido para el nodo de datos y vuelva a intentarlo. 

<h3><a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode</h3>
- **Descripción**: Tamaño de máquina virtual no válido para un nodo principal. Únicamente el tamaño de máquina virtual extragrande es compatible con los nodos principales.  
- **Mitigación**: Especifique el tamaño de nodo admitido para el nodo principal y vuelva a intentarlo.

<h3><a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion</h3>
- **Descripción**: El identificador de suscripción *identificadordelasuscripción* que se está utilizando no dispone de suficientes permisos para ejecutar la operación de eliminación del clúster *nombredelclúster*.  
- **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.  

<h3><a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName</h3>
- **Descripción**: El nombre del contenedor de blobs de la cuenta de almacenamiento externa *nombredelcontenedor* no es válido. Asegúrese de que el nombre comience con una letra y solo contenga minúsculas, números y guiones.  
- **Mitigación**: Especifique un nombre de contenedor de blobs de cuenta de almacenamiento válido y vuelva a intentarlo.

<h3><a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey</h3>
- **Descripción**: Para configurar la cuenta de almacenamiento externa *nombredelacuentadealmacenamiento* es necesario establecer primero los datos de la clave secreta.  
- **Mitigación**: Especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentarlo.

<h3><a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat</h3>
- **Descripción**: El encabezado de la versión *encabezadodelaversión* no presenta el formato válido aaaa-mm-dd.  
- **Mitigación**: Especifique un formato válido para el encabezado de la versión y vuelva a intentarlo. 

<h3><a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode</h3>
- **Descripción**: Configuración de clúster no válida. Se encontró más de una configuración de nodo principal.  
- **Mitigación**: Edite la configuración para que solo se especifique un nodo principal. 

<h3><a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest</h3>
- **Descripción**: La operación no se pudo completar en el tiempo permitido, o bien se alcanzó el número máximo de intentos. Vuelva a intentarlo.  
- **Mitigación**: Vuelva a intentarlo. 

<h3><a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty</h3>
- **Descripción**: El parámetro *nombredelparámetro* no puede ser inexistente o estar vacío.  
- **Mitigación**: Especifique un valor válido para el parámetro. 

<h3><a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure</h3>
- **Descripción**: Al menos uno de los datos de la solicitud de creación de clúster no es válido. Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.  
- **Mitigación**: Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo. 

<h3><a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable</h3>
- **Descripción**: La funcionalidad de región no está disponible para la región *nombredelaregión* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: Especifique una región compatible con los clústeres de HDInsight. Las regiones admitidas públicamente son: Sudeste asiático, Europa occidental, norte de Europa, este de Estados Unidos u oeste de Estados Unidos. 

<h3><a id="StorageAccountNotColocated"></a>StorageAccountNotColocated</h3>
- **Descripción**: La cuenta de almacenamiento *nombredelacuentadealmacenamiento* se encuentra en la región *nombredelaregiónactual*. Debería coincidir con la región del clúster *nombredelaregióndelclúster*.  
- **Mitigación**: Especifique una cuenta de almacenamiento situada en la misma región que el clúster, o bien, si la cuenta de almacenamiento ya contiene datos, cree un nuevo clúster en la misma región que la cuenta de almacenamiento existente. Si está utilizando el portal, la IU le informará de este problema con antelación. 

<h3><a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive</h3>
- **Descripción**: El identificador de suscripción proporcionado *identificadordelasuscripción* no está activo.  
- **Mitigación**: Vuelva a activar la suscripción u obtenga una nueva suscripción válida.

<h3><a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound</h3>
- **Descripción**: No se pudo encontrar el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: Compruebe que el identificador de la suscripción sea válido y vuelva a intentarlo. 

<h3><a id="UnableToResolveDNS"></a>UnableToResolveDNS</h3>
- **Descripción**: No se puede resolver el DNS *URLdelDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: Proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida, entre otras cosas debe empezar por *http://* y acabar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure.  

<h3><a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource</h3>
- **Descripción**: No se puede comprobar la ubicación del recurso *yourDnsUrl*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
- **Mitigación**: Proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida, entre otras cosas debe empezar por *http://* y acabar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure. 

<h3><a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable</h3>
- **Descripción**: La funcionalidad de versión no está disponible para la versión *versiónespecificada* y el identificador de suscripción *identificadordelasuscripción*.  
- **Mitigación**: Elija una versión disponible y vuelva a intentarlo. 

<h3><a id="VersionNotSupported"></a>VersionNotSupported</h3>
- **Descripción**: La versión *versiónespecificada* no es compatible.   
- **Mitigación**: Elija una versión compatible y vuelva a intentarlo.

<h3><a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion</h3>
- **Descripción**: La versión *versiónespecificada* no está disponible en la región de Azure *regiónespecificada*.  
- **Mitigación**: Elija una versión admitida en la región y vuelva a intentarlo. 

<h3><a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound</h3>
- **Descripción**: Configuración de clúster no válida. Configuración de cuenta WASB requerida no encontrada en las cuentas externas.  
- **Mitigación**: Compruebe que la cuenta existe y que esté bien indicada en la configuración, y luego vuelva a intentarlo. 

<h2><a id="resources"></a>Recursos adicionales de depuración</h2> 

* [Documentación sobre el SDK de HDInsight de Azure][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx

[image-hdi-debugging-error-messages-portal]: ./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png






