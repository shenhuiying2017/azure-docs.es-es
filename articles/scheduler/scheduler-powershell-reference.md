<properties
 pageTitle="Referencia de cmdlets de PowerShell de Programador"
 description="Referencia de cmdlets de PowerShell de Programador"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Referencia de cmdlets de PowerShell de Programador

En la tabla siguiente aparece una descripción y vínculos a la página de referencia de cada uno de los cmdlets importantes de Programador de Azure.

Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

Para obtener más información sobre los [cmdlets de Azure PowerShell](https://msdn.microsoft.com/library/mt125356(v=azure.200).aspx), consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

|Cmdlet|Descripción del cmdlet|
|---|---|
[Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133(v=azure.200).aspx) |Deshabilita una colección de trabajos. 
[Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135(v=azure.200).aspx) |Habilita una colección de trabajos.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125(v=azure.200).aspx) |Obtiene trabajos de Scheduler.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132(v=azure.200).aspx) |Obtiene colecciones de trabajos.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126(v=azure.200).aspx) |Obtiene el historial de trabajos.
[New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136(v=azure.200).aspx) |Crea un trabajo HTTP.
[New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141(v=azure.200).aspx) |Crea una colección de trabajos.
[New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134(v=azure.200).aspx) |Crea un trabajo de cola de Service Bus.
[New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142(v=azure.200).aspx) |Crea un trabajo de tema de Service Bus.
[New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127(v=azure.200).aspx) |Crea un trabajo de cola de almacenamiento. 
[Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140(v=azure.200).aspx) |Elimina un trabajo de Scheduler.  
[Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131(v=azure.200).aspx) |Quita una colección de trabajos. 
[Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130(v=azure.200).aspx) |Modifica un trabajo HTTP de Scheduler.
[Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129(v=azure.200).aspx) |Modifica una colección de trabajos. 
[Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143(v=azure.200).aspx) |Modifica un trabajo de cola de Service Bus.  
[Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137(v=azure.200).aspx) |Modifica un trabajo de tema de Service Bus. 
[Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128(v=azure.200).aspx) |Modifica un trabajo de cola de almacenamiento.   

Para obtener información más detallada, puede ejecutar cualquiera de los siguientes cmdlets:

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## Otras referencias


 [¿Qué es Programador?](scheduler-intro.md)

 [Conceptos, terminología y jerarquía de entidades de Programador de Azure](scheduler-concepts-terms.md)

 [Introducción al Programador de Azure en el Portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en Programador de Azure](scheduler-plans-billing.md)

 [Referencia de API de REST de Programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Alta disponibilidad y confiabilidad de Programador de Azure](scheduler-high-availability-reliability.md)

 [Límites, valores predeterminados y códigos de error de Programador de Azure](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Programador de Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0914_2016-->