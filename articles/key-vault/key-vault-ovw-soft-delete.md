---
ms.assetid: 
title: "Eliminación temporal de Azure Key Vault | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 2b73fb55384cdcc8db2736557e0efef97b34fc45
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---
# <a name="azure-key-vault-soft-delete-feature-overview"></a>Información general sobre la característica de eliminación temporal de Azure Key Vault

>[!NOTE]
>Para esta versión preliminar de Azure Key Vault, la característica de **eliminación temporal** es la única que se encuentra aún en versión preliminar. Azure Key Vault, como un todo, es un servicio de producción completo.

Para esta versión preliminar de Azure Key Vault describimos la eliminación recuperable de instancias y objetos de Key Vault, lo que se conoce como eliminación temporal. En concreto, se tratan los siguientes escenarios:

- Compatibilidad con la eliminación recuperable de instancias de Key Vault
- Compatibilidad con la eliminación recuperable de objetos de Key Vault (por ejemplo, claves, secretos y certificados)

## <a name="supporting-interfaces"></a>Interfaces admitidas

La característica de eliminación temporal está disponible inicialmente a través de las interfaces de REST, .NET/C# y PowerShell. Para obtener más información al respecto, consulte la [referencia sobre Key Vault](https://docs.microsoft.com/azure/key-vault/).

## <a name="scenarios"></a>Escenarios

Los almacenes Azure Key Vault son recursos controlados que se administran por medio de Azure Resource Manager. Azure Resource Manager especifica además un comportamiento bien definido para su eliminación, lo que conlleva que una operación DELETE ejecutada correctamente debería traducirse en que ese recurso deje de estar accesible. La característica de eliminación temporal aborda la recuperación del objeto eliminado, con independencia de que la eliminación haya sido voluntaria o involuntaria.

1. En un escenario típico, un usuario puede haber eliminado por accidente una instancia o un objeto de Key Vault; si esa instancia o ese objeto de Key Vault fueran recuperables durante un período predeterminado, el usuario podría deshacer la eliminación y recuperar sus datos.

2. En un escenario diferente, un usuario malintencionado podría intentar eliminar una instancia o un objeto de Key Vault, como una clave dentro de un almacén, para provocar una interrupción de la actividad empresarial. El hecho de que al eliminar la instancia o el objeto de Key Vault no se eliminen los datos subyacentes puede resultar útil como medida de seguridad ya que, por ejemplo, se pueden limitar los permisos sobre la eliminación de datos a un rol diferente que sea de confianza. Esto requiere un quórum efectivo para una operación que, en caso contrario, podría provocar una pérdida de datos inmediata.

### <a name="soft-delete-behavior"></a>Comportamiento de eliminación temporal

Con esta versión preliminar, la operación DELETE sobre un objeto o instancia de Key Vault es una operación temporal que retiene los recursos durante un período determinado durante el cual parece que el objeto se ha eliminado realmente. El servicio proporciona además un mecanismo para recuperar el objeto eliminado, básicamente deshaciendo la eliminación. 

La eliminación temporal es un comportamiento opcional de Key Vault y no **está habilitado de forma predeterminada** en esta versión. Para obtener más información acerca de cómo habilitar la eliminación de software para su instancia de Key Vault, consulte las instrucciones específicas relativas a la interfaz que esté utilizando en la [referencia de Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="key-vault-recovery"></a>Recuperación de Key Vault

Tras eliminar una instancia de Key Vault, el servicio creará un recurso de proxy en la suscripción, agregando metadatos suficientes para la recuperación. El recurso de proxy es un objeto almacenado, disponible en la misma ubicación que la instancia de Key Vault eliminada. 

### <a name="key-vault-object-recovery"></a>Recuperación de un objeto de Key Vault

Cuando se elimina un objeto de Key Vault, como una clave, el servicio pondrá el objeto en estado "eliminado", de manera que no será accesible para ninguna operación de recuperación. Mientras esté en ese estado, el objeto de Key Vault solo se puede colocar en una lista, recuperar o eliminar por la fuerza/permanentemente. 

Al mismo tiempo, Key Vault programará la eliminación de los datos subyacentes correspondientes al objeto o la instancia de Key Vault eliminados para su ejecución tras un intervalo de retención predeterminado. El registro DNS correspondiente al almacén también se conserva durante la duración del intervalo de retención.

### <a name="soft-delete-retention-period"></a>Período de retención de la eliminación temporal

Los recursos eliminados temporalmente se conservan durante un período de tiempo determinado: 90 días. 

>[!NOTE]
> Este período de retención para la eliminación temporal no es configurable para la versión preliminar del 10 de mayo de 2017. 

Durante el intervalo de retención de eliminación temporal, se dan las circunstancias siguientes:

- Puede confeccionar un listado con todos los objetos e instancias de Key Vault que se encuentran en estado de eliminación temporal en su suscripción, así como tener acceso a la información de eliminación y recuperación sobre ellos.
    - Solo los usuarios con permisos especiales pueden consultar los almacenes eliminados. Se recomienda que los usuarios creen un rol personalizado con estos permisos especiales para tratar los almacenes eliminados.
- No es posible crear una instancia de Key Vault con el mismo nombre en la misma ubicación; en consecuencia, no se puede crear un objeto de Key Vault en un almacén determinado si esa instancia de Key Vault contiene un objeto con el mismo nombre y está en estado eliminado. 
- Solo un usuario con privilegios determinados puede restaurar una instancia o un objeto de Key Vault mediante la emisión de un comando de recuperación en el recurso de proxy correspondiente.
    - El usuario, miembro del rol personalizado, que tiene privilegios para crear una instancia de Key Vault en el grupo de recursos puede restaurar el almacén.
- Solo un usuario con privilegios determinados puede eliminar por la fuerza una instancia o un objeto de Key Vault mediante la emisión de un comando de eliminación en el recurso de proxy correspondiente.

Una vez que finalice el intervalo de retención, el servicio llevará a cabo una purga de la instancia o el objeto de Key Vault eliminados temporalmente y de su contenido, a menos que se hayan recuperado. La eliminación de recursos no se puede volver a programar.

### <a name="permissioned-purge"></a>Purga autorizada

La purga permanente de una instancia de Key Vault es posible a través de una operación POST en el recurso de proxy y requiere privilegios especiales. Por lo general, el propietario de la suscripción es el único que puede purgar una instancia de Key Vault. La operación POST activará la eliminación inmediata y no recuperable de esa instancia. 

Se da una excepción a esta circunstancia cuando la suscripción de Azure se ha marcado como *no eliminable*. En este caso, solo el servicio puede realizar la eliminación real, y lo hará como un proceso programado. 




