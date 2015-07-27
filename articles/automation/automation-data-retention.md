<properties 
   pageTitle="Retención de datos de Automatización de Azure"
   description="Describe la política de retención de datos de Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Retención de datos de Automatización de Azure

Cuando elimina un recurso en Automatización de Azure, se conserva durante 90 días para fines de auditoría antes de quitarlo de manera permanente. Durante este tiempo, no es posible ver ni usar el recurso. Esta directiva también se aplica a recursos que pertenecen a una cuenta de automatización eliminada.

Automatización de Azure elimina automáticamente y quita de manera permanente los trabajos que tengan más de 90 días.

La tabla siguiente resume la directiva de retención para distintos recursos.

|Datos|Directiva|
|:---|:---|
|Cuentas|Se quitan de manera permanente 90 días después de que un usuario elimina la cuenta.|
|Recursos|Se quitan de manera permanente 90 días después de que un usuario elimina el recurso o 90 días después de que un usuario elimina la cuenta que contiene el recurso.|
|Módulos|Se quitan de manera permanente 90 días después de que un usuario elimina el módulo o 90 días después de que un usuario elimina la cuenta que contiene el módulo.|
|Runbooks|Se quitan de manera permanente 90 días después de que un usuario elimina el recurso o 90 días después de que un usuario elimina la cuenta que contiene el recurso.|
|Trabajos|Se eliminan y quitan de manera permanente 90 días después de la última modificación. Esto puede ser después de completar, detener o suspender el trabajo.|

La directiva de retención se aplica a todos los usuarios y, por el momento, no se puede personalizar.

## Artículos relacionados
- [Copia de seguridad de Automatización de Azure](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=July15_HO3-->