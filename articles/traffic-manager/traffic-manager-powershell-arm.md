<properties
   pageTitle="Compatibilidad del Administrador de recursos de Azure para la vista previa del Administrador de tráfico | Microsoft Azure"
   description="Uso de PowerShell para el Administrador de tráfico con el Administrador de recursos de Azure (ARM) en vista previa"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="joaoma" />






# Compatibilidad del Administrador de recursos de Azure con la vista previa del Administrador de tráfico de Azure
El Administrador de recursos de Azure (ARM) es el nuevo marco de administración de servicios en Azure. Los perfiles del Administrador de tráfico de Azure ahora pueden administrarse mediante las herramientas y las API basadas en el Administrador de recursos de Azure. Para obtener más información sobre el Administrador de recursos de Azure, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-preview-portal-using-resource-groups.md).

>[AZURE.NOTE]La compatibilidad de ARM con el Administrador de tráfico se encuentra actualmente en vista previa, incluida la API de REST, Azure PowerShell, la CLI de Azure y el SDK de .NET.



## Modelo de recursos

El Administrador de tráfico de Azure se configura con una colección de valores denominada perfil del Administrador de tráfico. Contiene la configuración de DNS, de enrutamiento de tráfico, de supervisión de extremos y la lista de extremos de servicio a la que se enrutará el tráfico.

En ARM, cada perfil del Administrador de tráfico se representa mediante un recurso ARM, de tipo 'TrafficManagerProfiles', administrado por el proveedor de recursos 'Microsoft.Network'. En el nivel de la API de REST, el URI de cada perfil es el siguiente:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparación con la API de administración del servicio del Administrador de tráfico de Azure

El uso de la ARM para configurar perfiles del Administrador de tráfico proporciona acceso al mismo conjunto de características del Administrador de tráfico que la API de administración de servicio (no de ARM), excepto las limitaciones de vista previa que aparecen a continuación.

Sin embargo, mientras que las características siguen siendo las mismas, parte de la terminología ha cambiado:

- Se cambió el nombre del 'método de equilibrio de carga', que determina cómo elige el Administrador de tráfico a qué extremo debe enrutar el tráfico al responder a una solicitud concreta de DNS, por el de 'método de enrutamiento de tráfico'.

- Se cambió el nombre del tráfico de 'round robin' por el de 'ponderado'.

- Se cambió el nombre del tráfico de 'conmutación por error' por el de 'prioridad'.

## Limitaciones de vista previa
Como la compatibilidad del Administrador de recursos de Azure para el Administrador de tráfico es un servicio de vista previa, actualmente hay un número reducido de limitaciones:

- Los perfiles del Administrador de tráfico creados con la API de administración de servicio (no de ARM), las herramientas y el Portal existentes no están disponibles a través de ARM y viceversa. Actualmente no se admite la migración de perfiles desde API que no sean de ARM a API de ARM.

- 	La API de REST no admite las revisiones automáticas de perfiles del Administrador de tráfico. Para actualizar una propiedad del perfil, debe realizar un GET del perfil y un PUT del perfil modificado.
- 	Solo se admiten los extremos ‘externos’. Estos aún pueden utilizarse para usar el Administrador de tráfico con servicios basados en Azure y al hacerlo, esos extremos se facturarán a la tarifa del extremo interno. (El único impacto del uso de extremos externos es que no se deshabilitarán ni eliminarán automáticamente al deshabilitar o eliminar el servicio de Azure subyacente, en su lugar, hay que deshabilitar o eliminar manualmente el extremo).
-	El Administrador de tráfico de Azure no está aún disponible en el portal de Azure, solo en el portal clásico.

## Configuración de Azure PowerShell

Estas instrucciones utilizan Microsoft Azure PowerShell, que debe configurarse con los siguientes pasos.

Para los usuarios que no sean de PowerShell, también se pueden ejecutar las mismas operaciones a través de las otras interfaces.

### Paso 1
Instalación del PowerShell Azure más reciente, disponible en la página de descargas de Azure.
### Paso 2:
Cambio del modo de PowerShell para usar los cmdlets de ARM. Hay más información disponible en Uso de Windows PowerShell con el Administrador de recursos.

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### Paso 3
Inicio de sesión en la cuenta de Azure

	PS C:\> Add-AzureAccount

Se le pedirá autenticarse con sus credenciales.

### Paso 4
Elección de la suscripción de Azure que se va a usar.

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver una lista de suscripciones disponibles, use el cmdlet 'Get-AzureSubscription'.

### Paso 5

 El proveedor de recursos Microsoft.Network administra el servicio del Administrador de tráfico. Su suscripción de Azure debe estar registrada para utilizar este proveedor de recursos si desea poder usar el Administrador de tráfico a través de ARM. Se trata de una operación única para cada suscripción.

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### Paso 6
Creación de un grupo de recursos (omitir este paso si se utiliza un grupo de recursos existente)

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos del perfil del Administrador de tráfico son globales y no regionales, la elección de la ubicación del grupo de recursos no tiene efecto en el Administrador de tráfico de Azure.

## Creación de un perfil del Administrador de tráfico

Para crear un perfil del Administrador de tráfico, use el cmdlet New-AzureTrafficManagerProfile:

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Los parámetros son los siguientes:

- Name: el nombre del recurso de ARM para el recurso del perfil del Administrador de tráfico. Los perfiles del mismo grupo de recursos deben tener nombres únicos. Este nombre es independiente del nombre DNS que se utiliza para las consultas de DNS.

-	ResourceGroupName: el nombre del grupo de recursos de ARM que contiene el recurso del perfil.

-	TrafficRoutingMethod: especifica el método de enrutamiento de tráfico que se utiliza para determinar qué extremo es el que se devuelve en respuesta a las consultas de DNS entrantes. Los valores posibles son ‘Performance’, ‘Weighted’ o ‘Priority’.

-	RelativeDnsName: especifica el nombre DNS relativo proporcionado por este perfil del Administrador de tráfico. Este valor se combina con el nombre de dominio DNS utilizado por el Administrador de tráfico de Azure para formar el nombre de dominio completo (FQDN) del perfil. Por ejemplo, el valor ‘contoso’ proporcionará un perfil de Administrador de tráfico con el nombre completo 'contoso.trafficmanager.net'.

-	TTL: especifica el período de vida (TTL) de DNS, en segundos. Esto informa a las resoluciones DNS locales y a los clientes DNS cuánto tiempo se guardan en la memoria caché las respuestas DNS proporcionadas por este perfil del Administrador de tráfico.

-	MonitorProtocol: especifica el protocolo que se va a utilizar para supervisar el estado del extremo. Los valores posibles son ‘HTTP’ y ‘HTTPS’.

-	MonitorPort: especifica el puerto TCP utilizado para supervisar el estado del extremo.

-	MonitorPath: especifica la ruta de acceso en relación con el nombre de dominio de extremo utilizado para sondear el estado del extremo.

El cmdlet crea un perfil del Administrador de tráfico en el Administrador de tráfico de Azure y devuelve un objeto de perfil correspondiente. En este momento, el perfil no contiene ningún extremo, consulte [Actualización de un perfil del Administrador de tráfico](#update-a-traffic-manager-profile) para obtener más información sobre cómo agregar extremos a un perfil del Administrador de tráfico.

## Obtención de un perfil del Administrador de tráfico

Para recuperar un objeto del perfil del Administrador de tráfico existente, use el cmdlet Get-AzureTrafficManagerProfle:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

Este cmdlet devuelve un objeto del perfil del Administrador de tráfico.

## Actualización de un perfil del Administrador de tráfico [](#update-traffic-manager-profile)

Para modificar perfiles del Administrador de tráfico, por ejemplo, para agregar o quitar extremos o modificar la configuración del perfil, se sigue un proceso de tres pasos:

1.	Recuperar el perfil mediante Get-AzureTrafficManagerProfile (o utilizar el perfil devuelto por New-AzureTrafficManagerProfile).

2.	Modificar el perfil, agregando extremos, quitando extremos, cambiando parámetros de extremo o cambiando parámetros de perfil. Estos cambios se realizan sin conexión: solo se cambia el objeto local que representa el perfil.

3.	Confirmar los cambios mediante el cmdlet Set-AzureTrafficManagerProfile. Esto reemplaza el perfil existente en el Administrador de tráfico de Azure por el perfil proporcionado.

Esto se explica de forma más detallada en los siguientes ejemplos:

### Agregar extremos a un perfil

Se pueden agregar extremos a un perfil de Administrador de tráfico mediante el cmdlet 'Add-AzureTrafficManagerEndpointConfig':

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Los parámetros para AzureTrafficManagerEndpointConfig son los siguientes:

- EndpointName: el nombre del extremo. Los extremos del mismo perfil deben tener nombres distintos. Esto se utiliza para hacer referencia al extremo durante las operaciones de administración de servicio y no es el nombre DNS del extremo.

-	TrafficManagerProfile: El objeto de perfil del Administrador de tráfico al que se agregará el extremo.

-	Type: el tipo del extremo del Administrador de tráfico. Actualmente, solo se admite el tipo 'ExternalEndpoint' a través de la API de ARM (consulte [Limitaciones de vista previa](#preview-limitations)).

-	Target: el nombre DNS completo del extremo. El Administrador de tráfico devuelve este valor en las respuestas DNS para dirigir el tráfico a este extremo.

-	EndpointStatus: especifica el estado del extremo. Si el extremo está habilitado, se examina el estado del extremo y se incluye en el método de enrutamiento de tráfico. Los valores posibles son ‘Enabled’ o ‘Disabled’.

-	Weight: especifica el peso asignado al extremo. Solo se utiliza si el perfil del Administrador de tráfico está configurado para usar el método de enrutamiento de tráfico 'weighted'. Los valores posibles son de 1 a 1000:

-	Priority: especifica la prioridad de este extremo cuando se usa el método de enrutamiento de tráfico 'priority'. La prioridad debe encontrarse en el intervalo 1... 1000. Los valores más bajos representan una prioridad más alta.

-	EndpointLocation: especifica la ubicación del extremo externo para su uso con el método de enrutamiento de tráfico ‘Performance’. Para obtener una lista de las ubicaciones posibles, consulte Get-AzureLocation.

El estado, la ponderación y la prioridad del extremo son parámetros opcionales. Si se omiten, PowerShell no lo pasa y se aplican los valores predeterminados del servidor.

### Supresión de los extremos de un perfil

Para quitar un extremo de un perfil, utilice 'Remove-AzureTrafficmanagerEndpointConfig', especificando el nombre del extremo que se va a quitar:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

La secuencia de operaciones para agregar o quitar extremos puede también 'canalizarse', pasando el objeto de perfil a través de la canalización en lugar de como un parámetro. Por ejemplo:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### Cambio de configuración del perfil o extremo

Tanto los parámetros del perfil como del extremo se pueden cambiar sin conexión y los cambios se pueden confirmar mediante Set-AzureTrafficManagerProfile. La única excepción es que el RelativeDnsName del perfil no se puede cambiar una vez creado este último (para cambiar este valor, hay que eliminar el perfil y volverlo a crear). Por ejemplo, para cambiar el período de vida del perfil y el estado del primer extremo:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Eliminación de un perfil del Administrador de tráfico
Para eliminar un perfil del Administrador de tráfico, use el cmdlet Remove-AzureTrafficManagerProfile, especificando el nombre del perfil y el nombre del grupo de recursos:

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

Este cmdlet le pedirá confirmación. Se puede utilizar el conmutador '-Force' opcional para suprimir este mensaje. El perfil que se va a eliminar también se puede especificar con un objeto de perfil:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Se puede canalizar igualmente esta secuencia:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## Otras referencias

[Información general sobre el Administrador de tráfico](traffic-manager-overview.md)

[Introducción a los cmdlets de Azure](https://msdn.microsoft.com/library/jj554332.aspx)
 

<!---HONumber=July15_HO4-->