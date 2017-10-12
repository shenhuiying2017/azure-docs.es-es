---
title: "Administración de cuentas de almacenamiento de Azure Stack | Microsoft Docs"
description: Aprenda a buscar, administrar, recuperar y reclamar cuentas de almacenamiento de Azure Stack.
services: azure-stack
documentationcenter: 
author: AniAnirudh
manager: darmour
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: anirudha
ms.openlocfilehash: 6e14bd6312135b45984a82099e68a934ec2a4a70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Administración de cuentas de almacenamiento en Azure Stack
Aprenda a administrar cuentas de almacenamiento en Azure Stack para buscar, recuperar y reclamar capacidad de almacenamiento según las necesidades empresariales.

## <a name="find"></a>Búsqueda de una cuenta de almacenamiento
La lista de cuentas de almacenamiento de la región se puede ver en Azure Stack de las maneras siguientes:

1. En un explorador de Internet, vaya a https://adminportal.local.azurestack.external.
2. Inicie sesión en el portal de administración Azure Stack como un operador de nube (mediante las credenciales proporcionadas durante la implementación).
3. En el panel predeterminado, busque la lista de **administración de regiones** y haga clic en la región que quiere explorar. Por ejemplo, **(local**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Seleccione **Almacenamiento** en la lista **Proveedores de recursos**.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Ahora, en la hoja de administración del proveedor de recursos de almacenamiento, desplácese a la pestaña **Cuentas de almacenamiento** y haga clic en ella.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   La página resultante es la lista de cuentas de almacenamiento de esa región.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

De forma predeterminada, se muestran las 10 primeras cuentas. Puede hacer clic en el vínculo **Cargar más** de la parte inferior de la lista para capturar más.

OR

Si está interesado en una cuenta de almacenamiento determinada, puede **filtrar y capturar solo las cuentas correspondientes**.


**Para filtrar las cuentas:**

1. Haga clic en **Filtrar** en la parte superior de la hoja.
2. En la hoja de filtro, puede especificar el **nombre de cuenta**, el **identificador de suscripción** o el **estado** para ajustar la lista de cuentas de almacenamiento que se mostrará. Use esta información de la manera adecuada.
3. Haga clic en **Update**(Actualizar). La lista se debe actualizar de forma correspondiente.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Para restablecer el filtro: haga clic en **Filtro**, borre las selecciones y actualice.

El cuadro de texto de búsqueda (de la parte superior de la hoja de lista de cuentas de almacenamiento) permite resaltar el texto seleccionado en la lista de cuentas. Esto resulta de mucha utilidad cuando el nombre completo o el identificador no están fácilmente disponibles.

Aquí puede usar texto sin formato para ayudar a encontrar la cuenta que le interese.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Examen de los detalles de la cuenta
Cuando haya encontrado las cuentas que le interese ver, puede hacer clic en una en particular para ver determinados detalles. Se abre una nueva hoja con los detalles de la cuenta, como el tipo de cuenta, la hora de creación, la ubicación, etc.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Recuperación de una cuenta eliminada
Puede que se encuentre en una situación en que deba recuperar una cuenta eliminada.

En Azure Stack, existe una manera muy sencilla de hacerlo:

1. Vaya a la lista de cuentas de almacenamiento. Consulte [Búsqueda de una cuenta de almacenamiento](#find) en este tema para más información.
2. Busque esa cuenta en particular en la lista. Puede que deba filtrar.
3. Compruebe el *estado* de la cuenta. Debe indicar **Eliminada**.
4. Haga clic en la cuenta que abre la hoja de detalles de cuenta.
5. En esta hoja, busque el botón **Recuperar** y haga clic en él.
6. Haga clic en **Sí** para continuar.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. La recuperación está ahora en *proceso...espere* para indicar que se ha realizado correctamente.
   También puede hacer clic en el icono de "campana" en la parte superior del portal para ver las indicaciones de progreso.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   Una vez que la cuenta recuperada se ha sincronizado correctamente, se puede volver a usar.

### <a name="some-gotchas"></a>Algunos gotchas
* La cuenta eliminada muestra un estado **fuera de retención**.
  
  Esto significa que la cuenta eliminada ha superado el período de retención y puede que no sea posible recuperarla.
* La cuenta eliminada no se muestra en la lista de cuentas.
  
  Esto podría significar que la cuenta eliminada ya se ha recolectado como elemento no utilizado. En este caso no se puede recuperar. Consulte [Reclamación de capacidad](#reclaim) en este tema.

## <a name="set-the-retention-period"></a>Establecimiento del período de retención
La configuración del período de retención permite a un operador de nube especificar un período de tiempo en días (entre 0 y 9999 días) durante el cual existe la posibilidad de que cualquier cuenta eliminada se pueda recuperar. El período de retención predeterminado se establece en 15 días. Un valor configurado de "0" significa que una cuenta eliminada está inmediatamente fuera de retención y marcada para la recolección periódica de elementos no utilizados.

**Para cambiar el periodo de retención:**

1. En un explorador de Internet, vaya a https://adminportal.local.azurestack.external.
2. Inicie sesión en el portal de administración Azure Stack como un operador de nube (mediante las credenciales proporcionadas durante la implementación).
3. En el panel predeterminado, busque la lista de **administración de regiones** y haga clic en la región que quiere explorar, por ejemplo, **(local**).
4. Seleccione **Almacenamiento** en la lista **Proveedores de recursos**.
5. Haga clic en **Configuración** en la parte superior para abrir la hoja de configuración.
6. Haga clic en **Configuración** y, luego, edite el valor del período de retención.

   Establezca el número de días y, a continuación, guárdelo.
   
   Este valor se hace efectivo inmediatamente y se aplica a toda la región.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Reclamación de capacidad
Uno de los efectos secundarios de tener un período de retención es que una cuenta eliminada sigue consumiendo su capacidad hasta que está fuera del período de retención. Como operador de nube, puede que necesite una manera de reclamar el espacio de la cuenta eliminada incluso aunque el periodo de retención no haya expirado aún.

Para ello, puede usar el portal o PowerShell.

**Para reclamar la capacidad mediante el portal:**
1. Vaya a la hoja de cuentas de almacenamiento. Consulte [Búsqueda de una cuenta de almacenamiento](#find).
2. Haga clic en **Reclaim space** (Reclamar espacio) en la parte superior de la hoja.
3. Lea el mensaje y, a continuación, haga clic en **Aceptar**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Espere a la notificación de operación realizada correctamente. Puede ver el icono de campana en el portal.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Actualice la página de cuentas de almacenamiento. Las cuentas eliminadas ya no se muestran en la lista porque se han purgado.

También puede usar PowerShell para reemplazar explícitamente el período de retención y reclamar inmediatamente la capacidad.

**Para reclamar la capacidad mediante PowerShell:**   

1. Confirme que ha instalado y configurado Azure PowerShell. Si no es así, use las siguientes instrucciones: 
   * Para instalar la versión más reciente de Azure PowerShell y asociarla a su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Para más información sobre los cmdlets de Azure Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2. Ejecute el siguiente cmdlet:

> [!NOTE]
> Si ejecuta este cmdlet, eliminará permanentemente la cuenta y su contenido. No se podrá volver a recuperar. Así que úselo con precaución.


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


Para más información, consulte la [documentación de PowerShell de Azure Stack.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="migrate-a-container"></a>Migración de un contenedor
Debido al uso de almacenamiento desigual por parte de los inquilinos, un operador de nube puede encontrarse con que uno o varios recursos compartidos de inquilino subyacentes usan más espacio que otros. Si esto sucede, el operador puede intentar liberar espacio en el recurso compartido sobrecargado mediante la migración manual de algunos contenedores de blobs a otro recurso compartido. 

Para migrar los contenedores, debe usar PowerShell.
> [!NOTE]
>La migración de contenedores de blobs no admite la migración en vivo y actualmente es una operación sin conexión. Durante la migración, y hasta que finaliza, los blobs subyacentes en ese contenedor no se pueden usar y están "sin conexión". 

**Para migrar contenedores mediante PowerShell:**

1. Confirme que ha instalado y configurado Azure PowerShell. Si no es así, use las siguientes instrucciones:
    * Para instalar la versión más reciente de Azure PowerShell y asociarla a su suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para más información sobre los cmdlets de Azure Resource Manager, consulte [Uso de Azure PowerShell con Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767).
2. Obtenga el nombre de la granja de servidores: 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. Obtenga los recursos compartidos: 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. Obtenga los contenedores de un recurso compartido determinado. Observe que los parámetros count e intent son opcionales:
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   A continuación, examine $containers:

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. Obtenga los mejores recursos compartidos de destino para la migración de contenedores:

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    A continuación, examine $destinationshares:

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. Inicie la migración de un contenedor. Tenga en cuenta que se trata de una implementación asincrónica, así que se pueden repetir todos los contenedores de un recurso compartido y realizar el seguimiento del estado mediante el identificador de trabajo devuelto.

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    A continuación, examine $jobId:

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. Compruebe el estado del trabajo de migración por su identificador de trabajo. Cuando finalice la migración del contenedor, MigrationStatus se establece como "Completado".

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. Puede cancelar un trabajo de migración en curso. De nuevo, es una operación asincrónica, cuyo seguimiento se puede realizar mediante $jobid:

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    Nuevamente, puede comprobar el estado de la cancelación de la migración:

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  