
<properties
   pageTitle="Personalización de la configuración de un clúster de Service Fabric y una directiva de actualización de Fabric | Microsoft Azure"
   description="En este artículo se describe la configuración de Fabric y las directivas de actualización de Fabric que se pueden personalizar."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="chackdan"/>

# Personalización de la configuración de un clúster de Service Fabric y una directiva de actualización de Fabric

En este documento se explica cómo personalizar los diversos valores de configuración de Fabric y la directiva de actualización de Fabric para el clúster de Service Fabric. Puede personalizarlos en el portal o mediante una plantilla de Resource Manager.

## Configuración de Fabric que se puede personalizar


Esta es la configuración de Fabric que se puede personalizar:

### Nombre de sección: Seguridad

|**Parámetro**|**Valores permitidos**|**Orientación o breve descripción**|
|-----------------------|--------------------------|--------------------------|
|ClusterProtectionLevel|Ninguno o EncryptAndSign| Ninguno (valor predeterminado) para clústeres no seguros, EncryptAndSign para clústeres seguros. |

### Nombre de sección: Hospedaje

|**Parámetro**|**Valores permitidos**|**Orientación o breve descripción**|
|-----------------------|--------------------------|--------------------------|
|ServiceTypeRegistrationTimeout|Tiempo en segundos, el valor predeterminado es 300.| Tiempo máximo permitido para que se registre ServiceType con Fabric.|
|ServiceTypeDisableFailureThreshold|Número entero, el valor predeterminado es 1.| Este es el umbral del recuento de errores después del cual se notifica a FailoverManager (FM) que deshabilite el tipo de servicio en ese nodo y pruebe un nodo diferente para la ubicación.|
|ActivationRetryBackoffInterval|Tiempo en segundos, el valor predeterminado es 5.|Intervalo de retroceso en cada error de activación; con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de MaxActivationFailureCount. El intervalo de reintento en cada intento es un producto del error de activación continua y el intervalo de retroceso de activación.|
|ActivationMaxRetryInterval|Tiempo en segundos, el valor predeterminado es 300.| Con cada error de activación continuo, el sistema vuelve a intentar la activación hasta el valor de ActivationMaxFailureCount. ActivationMaxRetryInterval especifica el intervalo de tiempo de espera antes de un reintento después de cada error de activación. |
|ActivationMaxFailureCount|Número entero, el valor predeterminado es 10.| Número de veces que los reintentos del sistema no lograron la activación antes de desistir. |

### Nombre de sección: FailoverManager

|**Parámetro**|**Valores permitidos**|**Orientación o breve descripción**|
|-----------------------|--------------------------|--------------------------|
|PeriodicLoadPersistInterval|Tiempo en segundos, el valor predeterminado es 10| Determina la frecuencia con la que FM busca nuevos informes de carga.|

### Nombre de sección: Federación

|**Parámetro**|**Valores permitidos**|**Orientación o breve descripción**|
|-----------------------|--------------------------|--------------------------|
|LeaseDuration|Tiempo en segundos, el valor predeterminado es 30.|Duración de una concesión entre un nodo y sus vecinos.|
|LeaseDurationAcrossFaultDomain|Tiempo en segundos, el valor predeterminado es 30.|Duración de una concesión entre un nodo y sus vecinos entre dominios de error.|

### Nombre de sección: ClusterManager

|**Parámetro**|**Valores permitidos**|**Orientación o breve descripción**|
|-----------------------|--------------------------|--------------------------|
|UpgradeStatusPollInterval|Tiempo en segundos, el valor predeterminado es 60.|La frecuencia de sondeo del estado de actualización de la aplicación. Este valor determina la frecuencia de actualización de cualquier llamada a GetApplicationUpgradeProgress.|
|UpgradeHealthCheckInterval|Tiempo en segundos, el valor predeterminado es 60.|Se comprueba la frecuencia del estado de mantenimiento durante las actualizaciones de una aplicación supervisada.|
|FabricUpgradeStatusPollInterval|Tiempo en segundos, el valor predeterminado es 60.|La frecuencia de sondeo del estado de Service Fabric. Este valor determina la frecuencia de actualización de cualquier llamada a GetFabricUpgradeProgress. |
|FabricUpgradeHealthCheckInterval|Tiempo en segundos, el valor predeterminado es 60.|Se comprueba la frecuencia del estado de mantenimiento durante una actualización de Fabric supervisada.|



## Pasos siguientes

Lea estos artículos para más información sobre la administración de clúster:

[Agregar o quitar certificados del clúster de Azure ](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->