---
ms.assetid: 
title: "Eliminación temporal de Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 09/25/2017
ms.openlocfilehash: 01357e4fdb9b6f27e9baf5f5c8e4c7d6b582ad35
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-soft-delete-overview"></a>Información general sobre la eliminación temporal de Azure Key Vault

La característica de eliminación temporal de Key Vault permite recuperar almacenes y objetos de almacenes eliminados, lo que se conoce como eliminación temporal. En concreto, se tratan los siguientes escenarios:

- Compatibilidad con la eliminación recuperable de una instancia de Key Vault
- Compatibilidad con la eliminación recuperable de objetos de Key Vault (por ejemplo, claves, secretos y certificados)

## <a name="supporting-interfaces"></a>Interfaces admitidas

La característica de eliminación temporal está disponible inicialmente a través de las interfaces de REST, .NET/C#, PowerShell y la CLI.

Para obtener información general, consulte la [referencia sobre Key Vault](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Escenarios

Los almacenes Azure Key Vault son recursos controlados que se administran por medio de Azure Resource Manager. Azure Resource Manager especifica además un comportamiento bien definido para su eliminación, lo que conlleva que una operación DELETE ejecutada correctamente debería traducirse en que ese recurso deje de estar accesible. La característica de eliminación temporal aborda la recuperación del objeto eliminado, con independencia de que la eliminación haya sido voluntaria o involuntaria.

1. En un escenario típico, un usuario puede haber eliminado por accidente una instancia o un objeto de Key Vault; si esa instancia o ese objeto de Key Vault fueran recuperables durante un período predeterminado, el usuario podría deshacer la eliminación y recuperar sus datos.

2. En un escenario diferente, un usuario malintencionado podría intentar eliminar una instancia o un objeto de Key Vault, como una clave dentro de un almacén, para provocar una interrupción de la actividad empresarial. El hecho de que al eliminar la instancia o el objeto de Key Vault no se eliminen los datos subyacentes puede resultar útil como medida de seguridad ya que, por ejemplo, se pueden limitar los permisos sobre la eliminación de datos a un rol diferente que sea de confianza. Este enfoque requiere un cuórum efectivo para una operación que, en caso contrario, podría provocar una pérdida de datos inmediata.

### <a name="soft-delete-behavior"></a>Comportamiento de eliminación temporal

Con esta característica, la operación DELETE sobre un objeto o instancia de Key Vault es una operación temporal que retiene los recursos durante un período determinado durante el cual parece que el objeto se ha eliminado realmente. El servicio proporciona además un mecanismo para recuperar el objeto eliminado, básicamente deshaciendo la eliminación. 

La eliminación temporal es un comportamiento opcional de Key Vault y no **está habilitado de forma predeterminada** en esta versión. 

### <a name="key-vault-recovery"></a>Recuperación de Key Vault

Tras eliminar una instancia de Key Vault, el servicio crea un recurso de proxy en la suscripción, agregando metadatos suficientes para la recuperación. El recurso de proxy es un objeto almacenado, disponible en la misma ubicación que la instancia de Key Vault eliminada. 

### <a name="key-vault-object-recovery"></a>Recuperación de un objeto de Key Vault

Cuando se elimina un objeto de Key Vault, como una clave, el servicio pondrá el objeto en estado "eliminado", de manera que no será accesible para ninguna operación de recuperación. Mientras esté en ese estado, el objeto de Key Vault solo se puede colocar en una lista, recuperar o eliminar por la fuerza/permanentemente. 

Al mismo tiempo, Key Vault programará la eliminación de los datos subyacentes correspondientes al objeto o la instancia de Key Vault eliminados para su ejecución tras un intervalo de retención predeterminado. El registro DNS correspondiente al almacén también se conserva durante la duración del intervalo de retención.

### <a name="soft-delete-retention-period"></a>Período de retención de la eliminación temporal

Los recursos eliminados temporalmente se conservan durante un período de tiempo determinado: 90 días. Durante el intervalo de retención de eliminación temporal, se dan las circunstancias siguientes:

- Puede confeccionar un listado con todos los objetos e instancias de Key Vault que se encuentran en estado de eliminación temporal en su suscripción, así como tener acceso a la información de eliminación y recuperación sobre ellos.
    - Solo los usuarios con permisos especiales pueden consultar los almacenes eliminados. Se recomienda que los usuarios creen un rol personalizado con estos permisos especiales para tratar los almacenes eliminados.
- No es posible crear una instancia de Key Vault con el mismo nombre en la misma ubicación; en consecuencia, no se puede crear un objeto de Key Vault en un almacén determinado si esa instancia de Key Vault contiene un objeto con el mismo nombre y está en estado eliminado. 
- Solo un usuario con privilegios determinados puede restaurar una instancia o un objeto de Key Vault mediante la emisión de un comando de recuperación en el recurso de proxy correspondiente.
    - El usuario, miembro del rol personalizado, que tiene privilegios para crear una instancia de Key Vault en el grupo de recursos puede restaurar el almacén.
- Solo un usuario con privilegios determinados puede eliminar por la fuerza una instancia o un objeto de Key Vault mediante la emisión de un comando de eliminación en el recurso de proxy correspondiente.

Una vez que finalice el intervalo de retención, el servicio lleva a cabo una purga de la instancia o el objeto de Key Vault eliminados temporalmente y de su contenido, a menos que se hayan recuperado. La eliminación de recursos no se puede volver a programar.

### <a name="permitted-purge"></a>Purga permitida

La purga permanente de una instancia de Key Vault es posible a través de una operación POST en el recurso de proxy y requiere privilegios especiales. Por lo general, el propietario de la suscripción es el único que puede purgar una instancia de Key Vault. La operación POST activa la eliminación inmediata y no recuperable de esa instancia. 

Se da una excepción a esta circunstancia cuando la suscripción de Azure se ha marcado como *no eliminable*. En este caso, solo el servicio puede realizar la eliminación real, y lo hace como un proceso programado. 

## <a name="next-steps"></a>pasos siguientes

Las dos guías siguientes ofrecen los escenarios de uso principal para uso de la eliminación temporal.

- [Uso de la eliminación temporal de Key Vault con PowerShell](key-vault-soft-delete-powershell.md) 
- [Uso de la eliminación temporal de Key Vault con la CLI](key-vault-soft-delete-cli.md)

