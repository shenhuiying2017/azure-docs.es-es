---
title: Administración de cuentas de almacenamiento de Azure Stack | Microsoft Docs
description: Aprenda a buscar, administrar, recuperar y reclamar cuentas de almacenamiento de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076946"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Administración de cuentas de almacenamiento en Azure Stack
Aprenda a administrar cuentas de almacenamiento en Azure Stack para buscar, recuperar y reclamar capacidad de almacenamiento según las necesidades empresariales.

## <a name="find"></a>Búsqueda de una cuenta de almacenamiento
La lista de cuentas de almacenamiento de la región se puede ver en Azure Stack de las maneras siguientes:

1. En un explorador de Internet, vaya a https://adminportal.local.azurestack.external.
2. Inicie sesión en el portal de administración Azure Stack como un operador de nube (mediante las credenciales proporcionadas durante la implementación).
3. En el panel predeterminado, busque la lista **Administración de regiones** y seleccione la región que quiere explorar, por ejemplo, **(local)**.
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Seleccione **Almacenamiento** en la lista **Proveedores de recursos**.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Ahora, en el panel de administración del proveedor de recursos de almacenamiento, desplácese a la pestaña **Cuentas de almacenamiento** y selecciónela.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   La página resultante es la lista de cuentas de almacenamiento de esa región.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

De forma predeterminada, se muestran las 10 primeras cuentas. Puede hacer clic en el vínculo **Cargar más** de la parte inferior de la lista para capturar más.

OR

Si está interesado en una cuenta de almacenamiento determinada, puede **filtrar y capturar solo las cuentas correspondientes**.


**Para filtrar las cuentas:**

1. Seleccione **Filtrar** en la parte superior del panel.
2. En el panel Filtro, puede especificar el **nombre de cuenta**, el identificador de **suscripción o el **estado** para ajustar la lista de cuentas de almacenamiento que se mostrará. Use esta información de la manera adecuada.
3. Seleccione **Actualizar**. La lista se debe actualizar de forma correspondiente.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Para restablecer el filtro: seleccione **Filtro**, borre las selecciones y actualice.

El cuadro de texto de búsqueda (de la parte superior del panel de lista de cuentas de almacenamiento) permite resaltar el texto seleccionado en la lista de cuentas. Puede utilizar esto cuando el nombre completo o el identificador no están fácilmente disponibles.

Aquí puede usar texto sin formato para ayudar a encontrar la cuenta que le interese.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Examen de los detalles de la cuenta
Cuando haya encontrado las cuentas que le interese ver, puede seleccionar una en particular para ver determinados detalles. Se abre un nuevo panel con los detalles de la cuenta, como el tipo de cuenta, la hora de creación, la ubicación, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperación de una cuenta eliminada
Puede que se encuentre en una situación en que deba recuperar una cuenta eliminada.

En Azure Stack, existe una manera sencilla de hacerlo:

1. Vaya a la lista de cuentas de almacenamiento. Consulte [Búsqueda de una cuenta de almacenamiento](#find) en este tema para más información.
2. Busque esa cuenta en particular en la lista. Puede que deba filtrar.
3. Compruebe el *estado* de la cuenta. Debe indicar **Eliminada**.
4. Seleccione la cuenta para abrir el panel de detalles de la cuenta.
5. En la parte superior de este panel, busque el botón **Recuperar** y selecciónelo.
6. Seleccione **Sí** para confirmar la acción.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. La recuperación está ahora en *proceso...espere* para indicar que se ha realizado correctamente.
   También puede seleccionar el icono de "campana" en la parte superior del portal para ver las indicaciones de progreso.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Una vez que la cuenta recuperada se ha sincronizado correctamente, se puede volver a usar.

### <a name="some-gotchas"></a>Algunos gotchas
* La cuenta eliminada muestra un estado **fuera de retención**.
  
  Fuera de retención significa que la cuenta eliminada ha superado el período de retención y puede que no sea posible recuperarla.
* La cuenta eliminada no se muestra en la lista de cuentas.
  
  Su cuenta podría no aparecer en la lista de cuentas si la cuenta eliminada ya ha pasado la recolección de elementos no utilizados. En este caso no se puede recuperar. Consulte [Reclamación de capacidad](#reclaim) en este tema.

## <a name="set-the-retention-period"></a>Establecimiento del período de retención
La configuración del período de retención permite a un operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que cualquier cuenta eliminada se pueda recuperar. El período de retención predeterminado se establece en 0 días. Un valor configurado de "0" significa que una cuenta eliminada está inmediatamente fuera de retención y marcada para la recolección periódica de elementos no utilizados.

**Para cambiar el periodo de retención:**

1. En un explorador de Internet, vaya a https://adminportal.local.azurestack.external.
2. Inicie sesión en el portal de administración Azure Stack como un operador de nube (mediante las credenciales proporcionadas durante la implementación).
3. En el panel predeterminado, busque la lista **Administración de regiones** y seleccione la región que quiere explorar, por ejemplo, **(local)**.
4. Seleccione **Almacenamiento** en la lista **Proveedores de recursos**.
5. Seleccione **Configuración** en la parte superior para abrir el panel de configuración.
6. Seleccione **Configuración** y, luego, edite el valor del período de retención.

   Establezca el número de días y, a continuación, guárdelo.
   
   Este valor se hace efectivo inmediatamente y se aplica a toda la región.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Reclamación de capacidad
Uno de los efectos secundarios de tener un período de retención es que una cuenta eliminada sigue consumiendo su capacidad hasta que está fuera del período de retención. Como operador de nube, puede que necesite una manera de reclamar el espacio de la cuenta eliminada incluso aunque el periodo de retención no haya expirado aún.

Para ello, puede usar el portal o PowerShell.

**Para reclamar la capacidad mediante el portal:**
1. Vaya al panel de cuentas de almacenamiento. Consulte [Búsqueda de una cuenta de almacenamiento](#find).
2. Seleccione **Recuperar espacio** en la parte superior del panel.
3. Lea el mensaje y, a continuación, seleccione **Aceptar**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Espere a la notificación de operación realizada correctamente. Puede ver el icono de campana en el portal.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Actualice la página de cuentas de almacenamiento. Las cuentas eliminadas ya no se muestran en la lista porque se han purgado.

También puede usar PowerShell para reemplazar explícitamente el período de retención y reclamar inmediatamente la capacidad.

**Para reclamar la capacidad mediante PowerShell:**   

1. Confirme que ha instalado y configurado Azure PowerShell. Si no es así, use las siguientes instrucciones: 
   * Para instalar la versión más reciente de Azure PowerShell y asociarla a su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para más información sobre los cmdlets de Azure Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2. Ejecute los siguientes cmdlets:

> [!NOTE]
> Si ejecuta estos cmdlets, eliminará permanentemente la cuenta y su contenido. No se podrá volver a recuperar. Así que úselo con precaución.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Para más información, consulte la [documentación de PowerShell de Azure Stack](https://msdn.microsoft.com/library/mt637964.aspx).
 

## <a name="next-steps"></a>Pasos siguientes

 - Para obtener información sobre cómo administrar permisos, vea [Administrar el control de acceso basado en roles](azure-stack-manage-permissions.md).
 - Para obtener información sobre cómo administrar la capacidad de almacenamiento para Azure Stack, vea [Administración de la capacidad de almacenamiento para Azure Stack](azure-stack-manage-storage-shares.md).