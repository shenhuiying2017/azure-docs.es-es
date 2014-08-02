<properties linkid="manage-services-hdinsight-debug-error-messages" urlDisplayName="Debug HDInsight Errors" pageTitle="Debug HDInsight: Error messages | Azure" metaKeywords="dinsight, hdinsight service, hdinsight azure, debug, error messages, errors" description="Learn about the error messages you might receive when administering HDInsight using PowerShell, and steps you can take to recover." services="hdinsight" title="Debug HDInsight: Error messages" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="bradsev" />

Depuración de HDInsight: mensajes de error
==========================================

Introducción
------------

Con los mensajes de error incluidos en este tema pretendemos ayudar a los usuarios de Azure HDInsight a comprender algunas situaciones de error con las que se pueden encontrar al administrar el servicio utilizando Azure PowerShell, así como asesorarlos sobre los pasos que pueden seguir para recuperarse del error.

Algunos de estos mensajes de error también podrían aparecer en el portal de Azure al utilizarlo para administrar clústeres de HDInsight. Sin embargo, no es posible presentar de forma tan pormenorizada otros mensajes de error que pueden aparecer allí debido a las restricciones que afectan a las acciones de subsanación posibles en este contexto. Otros mensajes de error se asocian a los contextos en que la mitigación resulta obvia. Si se infringen las restricciones de los parámetros (por ejemplo, el mensaje emerge en el lado derecho del cuadro donde se escribió el valor), se trata de un caso de solicitud de demasiados nodos de datos. La solución es reducir el número hasta un valor permitido, es decir, 22 o menos.

![HDI.Debugging.ErrorMessages.Portal](./media/hdinsight-debug-jobs/hdi-debug-errormessages-portal.png)

En la sección [Errores de HDInsight](#hdinsight-error-messages) se enumeran los errores que un usuario puede encontrarse en Azure PowerShell o en el portal de Azure, ordenados por orden alfabético según el nombre y vinculados a una entrada de la sección [Descripción y mitigación de los errores](#discription-mitigation-errors), donde se facilita la siguiente información sobre los mismos:

-   **Descripción**: El mensaje de error que ven los usuarios
-   **Mitigación**: Pasos que se pueden seguir para recuperarse del error

### Errores de HDInsight

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

Descripción y mitigación de los errores
---------------------------------------

### AtleastOneSqlMetastoreMustBeProvided

-   **Descripción**: Proporcione los datos de la base de datos SQL de Azure de al menos un componente a fin de utilizar la configuración personalizada para las tiendas de metadatos de Hive y Oozie.
-   **Mitigación**: El usuario debe facilitar una tienda de metadatos de Azure SQL válida y volver a enviar la solicitud.

### AzureRegionNotSupported

-   **Descripción**: No se pudo crear un clúster en la región *nombredelaregión*. Utilice una región de HDInsight válida y vuelva a enviar la solicitud.
-   **Mitigación**: El cliente debe crear el clúster en una región que actualmente lo admita: norte de Europa, este de EE. UU. u oeste de EE. UU.

### ClusterContainerRecordNotFound

-   **Descripción**: El servidor no pudo encontrar el registro de clúster solicitado.
-   **Mitigación**: Vuelva a intentarlo.

### ClusterDnsNameInvalidReservedWord

-   **Descripción**: El nombre DNS del clúster *nombreDNS* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.
-   **Mitigación**: Asegúrese de que utilizó un nombre DNS válido para el clúster que empiece y acabe con un carácter alfanumérico y que no contenga ningún carácter especial aparte del guión (-), y, a continuación, vuelva a intentarlo.

### ClusterNameUnavailable

-   **Descripción**: El nombre de clúster *nombredeclúster* no está disponible. Elija otro nombre.
-   **Mitigación**: El usuario debe especificar un nombre de clúster que sea único y que no esté ya en uso, y, a continuación, volver a intentarlo. Si el usuario está utilizando el portal, la IU le informará durante los pasos de creación si el nombre de clúster ya está en uso.

### ClusterPasswordInvalid

-   **Descripción**: La contraseña del clúster no es válida. La contraseña debe cumplir estos requisitos: tener al menos 10 caracteres, incluir como mínimo un número, una letra mayúscula, una letra minúscula y un carácter especial, no contener espacios, y no estar formada a partir del nombre de usuario.
-   **Mitigación**: Proporcione una contraseña de clúster válida y vuelva a intentarlo.

### ClusterUserNameInvalid

-   **Descripción**: El nombre de usuario del clúster no es válido. Asegúrese de que no contenga caracteres especiales ni espacios.
-   **Mitigación**: Proporcione un nombre de usuario de clúster válido y vuelva a intentarlo.

### ClusterUserNameInvalidReservedWord

-   **Descripción**: El nombre DNS del clúster *nombreDNSdelclúster* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.
-   **Mitigación**: Proporcione un nombre de usuario de clúster DNS válido y vuelva a intentarlo.

### ContainerNameMisMatchWithDnsName

-   **Descripción**: El nombre de contenedor del URI *URIdecontenedor* y el nombre DNS *nombreDNS* del cuerpo de la solicitud deben coincidir.
-   **Mitigación**: Asegúrese de que el nombre del contenedor y el nombre DNS coincidan y vuelva a intentarlo.

### DataNodeDefinitionNotFound

-   **Descripción**: Configuración de clúster no válida. No se puede encontrar ninguna definición de nodo de datos en el tamaño de nodo.
-   **Mitigación**: Vuelva a intentarlo.

### DeploymentDeletionFailure

-   **Descripción**: Error de eliminación de la implementación del clúster.
-   **Mitigación**: Vuelva a intentar la eliminación.

### DnsMappingNotFound

-   **Descripción**: Error de configuración del servicio. Información de asignación de DNS requerida no encontrada.
-   **Mitigación**: Elimine el clúster y cree uno nuevo.

### DuplicateClusterContainerRequest

-   **Descripción**: Intento de duplicación de un contenedor de clúster. Existe un registro con el nombre *nombredelcontenedor*, pero las propiedades Etag no coinciden.
-   **Mitigación**: Proporcione un nombre exclusivo para el contenedor y vuelva a intentarlo.

### DuplicateClusterInHostedService

-   **Descripción**: El servicio hospedado *nombredelserviciohospedado* ya incluye un clúster. Los servicios hospedados no pueden contener varios clústeres.
-   **Mitigación**: Hospede el clúster en otro servicio hospedado.

### FailureToUpdateDeploymentStatus

-   **Descripción**: El servidor no pudo actualizar el estado de la implementación del clúster.
-   **Mitigación**: Vuelva a intentarlo. Si esto ocurre varias veces, póngase en contacto con CSS.

### HdiRestoreClusterAltered

-   **Descripción**: El clúster *nombredelclúster* se eliminó durante el mantenimiento. Vuelva a crearlo.
-   **Mitigación**: Vuelva a crear el clúster.

### HeadNodeConfigNotFound

-   **Descripción**: Configuración de clúster no válida. Configuración de nodo principal requerida no encontrada en los tamaños de nodo.
-   **Mitigación**: Vuelva a intentarlo.

### HostedServiceCreationFailure

-   **Descripción**: No se puede crear el servicio hospedado *nombredelserviciohospedado*. Vuelva a intentarlo.
-   **Mitigación**: Vuelva a intentarlo.

### HostedServiceHasProductionDeployment

-   **Descripción**: El servicio hospedado *nombredelserviciohospedado* ya incluye una implementación de producción. Los servicios hospedados no pueden contener varias implementaciones de producción. Vuelva a intentarlo con un nombre de clúster diferente.
-   **Mitigación**: Utilice un nombre de clúster diferente y vuelva a intentarlo.

### HostedServiceNotFound

-   **Descripción**: No se encontró el servicio hospedado *nombredelserviciohospedado* del clúster.
-   **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### HostedServiceWithNoDeployment

-   **Descripción**: El servicio hospedado *nombredelserviciohospedado* no tiene asociada ninguna implementación.
-   **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### InsufficientResourcesCores

-   **Descripción**: Al identificador de suscripción *identificadordelasuscripción* no le quedan recursos principales para crear el clúster *nombredelclúster*. Requeridos: *recursosrequeridos*, disponibles: *recursosdisponibles*.
-   **Mitigación**: Libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### InsufficientResourcesHostedServices

-   **Descripción**: El identificador de suscripción *identificadordelasuscripción* no tiene cuota para un nuevo servicio hospedado y, por tanto, no puede crear el clúster *nombredelclúster*.
-   **Mitigación**: Libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### InternalErrorRetryRequest

-   **Descripción**: Se produjo un error interno en el servidor. Vuelva a intentarlo.
-   **Mitigación**: Vuelva a intentarlo.

### InvalidAzureStorageLocation

-   **Descripción**: La ubicación de almacenamiento de Azure *nombrederegióndedatos* no es válida. Asegúrese de que la región sea correcta y vuelva a intentarlo.
-   **Mitigación**: Seleccione una ubicación de almacenamiento compatible con HDInsight, compruebe que el clúster esté colocalizado y vuelva a intentarlo.

### InvalidNodeSizeForDataNode

-   **Descripción**: Tamaño de máquina virtual no válido para los nodos de datos. Únicamente el tamaño de máquina virtual grande es compatible con todos los nodos de datos.
-   **Mitigación**: Especifique el tamaño de nodo admitido para el nodo de datos y vuelva a intentarlo.

### InvalidNodeSizeForHeadNode

-   **Descripción**: Tamaño de máquina virtual no válido para un nodo principal. Únicamente el tamaño de máquina virtual extragrande es compatible con los nodos principales.
-   **Mitigación**: Especifique el tamaño de nodo admitido para el nodo principal y vuelva a intentarlo.

### InvalidRightsForDeploymentDeletion

-   **Descripción**: El identificador de suscripción *identificadordelasuscripción* que se está utilizando no dispone de suficientes permisos para ejecutar la operación de eliminación del clúster *nombredelclúster*.
-   **Mitigación**: Si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### InvalidStorageAccountBlobContainerName

-   **Descripción**: El nombre del contenedor de blobs de la cuenta de almacenamiento externa *nombredelcontenedor* no es válido. Asegúrese de que el nombre comience con una letra y solo contenga minúsculas, números y guiones.
-   **Mitigación**: Especifique un nombre de contenedor de blobs de cuenta de almacenamiento válido y vuelva a intentarlo.

### InvalidStorageAccountConfigurationSecretKey

-   **Descripción**: Para configurar la cuenta de almacenamiento externa *nombredelacuentadealmacenamiento* es necesario establecer primero los datos de la clave secreta.
-   **Mitigación**: Especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentarlo.

### InvalidVersionHeaderFormat

-   **Descripción**: El encabezado de la versión *encabezadodelaversión* no presenta el formato válido aaaa-mm-dd.
-   **Mitigación**: Especifique un formato válido para el encabezado de la versión y vuelva a intentarlo.

### MoreThanOneHeadNode

-   **Descripción**: Configuración de clúster no válida. Se encontró más de una configuración de nodo principal.
-   **Mitigación**: Edite la configuración para que solo se especifique un nodo principal.

### OperationTimedOutRetryRequest

-   **Descripción**: La operación no se pudo completar en el tiempo permitido, o bien se alcanzó el número máximo de intentos. Vuelva a intentarlo.
-   **Mitigación**: Vuelva a intentarlo.

### ParameterNullOrEmpty

-   **Descripción**: El parámetro *nombredelparámetro* no puede ser inexistente o estar vacío.
-   **Mitigación**: Especifique un valor válido para el parámetro.

### PreClusterCreationValidationFailure

-   **Descripción**: Al menos uno de los datos de la solicitud de creación de clúster no es válido. Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.
-   **Mitigación**: Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.

### RegionCapabilityNotAvailable

-   **Descripción**: La funcionalidad de región no está disponible para la región *nombredelaregión* y el identificador de suscripción *identificadordelasuscripción*.
-   **Mitigación**: Especifique una región compatible con los clústeres de HDInsight. Las regiones admitidas públicamente son: este de EE. UU., oeste de EE. UU. y norte de Europa.

### StorageAccountNotColocated

-   **Descripción**: La cuenta de almacenamiento *nombredelacuentadealmacenamiento* se encuentra en la región *nombredelaregiónactual*. Debería coincidir con la región del clúster *nombredelaregióndelclúster*.
-   **Mitigación**: Especifique una cuenta de almacenamiento situada en la misma región que el clúster, o bien, si la cuenta de almacenamiento ya contiene datos, cree un nuevo clúster en la misma región que la cuenta de almacenamiento existente. Si está utilizando el portal, la IU le informará de este problema con antelación.

### SubscriptionIdNotActive

-   **Descripción**: El identificador de suscripción proporcionado *identificadordelasuscripción* no está activo.
-   **Mitigación**: Vuelva a activar la suscripción u obtenga una nueva suscripción válida.

### SubscriptionIdNotFound

-   **Descripción**: No se pudo encontrar el identificador de suscripción *identificadordelasuscripción*.
-   **Mitigación**: Compruebe que el identificador de la suscripción sea válido y vuelva a intentarlo.

### UnableToResolveDNS

-   **Descripción**: No se puede resolver el DNS *URLdelDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.
-   **Mitigación**: Proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida, entre otras cosas debe empezar por *http://* y acabar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure.

### UnableToVerifyLocationOfResource

-   **Descripción**: No se puede comprobar la ubicación del recurso *yourDnsUrl*. Asegúrese de facilitar la dirección URL completa del extremo del blob.
-   **Mitigación**: Proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida, entre otras cosas debe empezar por *http://* y acabar en *.com*. La dirección URL completa suele encontrarse en la pestaña de almacenamiento del Portal de administración de Azure.

### VersionCapabilityNotAvailable

-   **Descripción**: La funcionalidad de versión no está disponible para la versión *versiónespecificada* y el identificador de suscripción *identificadordelasuscripción*.
-   **Mitigación**: Elija una versión disponible y vuelva a intentarlo.

### VersionNotSupported

-   **Descripción**: La versión *versiónespecificada* no es compatible.
-   **Mitigación**: Elija una versión compatible y vuelva a intentarlo.

### VersionNotSupportedInRegion

-   **Descripción**: La versión *versiónespecificada* no está disponible en la región de Azure *regiónespecificada*.
-   **Mitigación**: Elija una versión admitida en la región y vuelva a intentarlo.

### WasbAccountConfigNotFound

-   **Descripción**: Configuración de clúster no válida. Configuración de cuenta WASB requerida no encontrada en las cuentas externas.
-   **Mitigación**: Compruebe que la cuenta existe y que esté bien indicada en la configuración, y luego vuelva a intentarlo.

Recursos adicionales de depuración
----------------------------------

-   [Azure HDInsight SDK documentation](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

