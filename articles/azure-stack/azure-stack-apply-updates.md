---
title: "Aplicación de actualizaciones en Azure Stack | Microsoft Docs"
description: "Aprenda a importar e instalar paquetes de actualización de Microsoft para un sistema integrado de Azure Stack."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 8fbed3c15b385e385e64093331df37363a558185
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="apply-updates-in-azure-stack"></a>Aplicación de actualizaciones en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Para aplicar paquetes de actualizaciones de Microsoft a Azure Stack los operadores de Azure Stack deben hacer clic en el icono Actualización del portal de administración. Debe descargar el paquete de actualización de Microsoft, importar los archivos del paquete en Azure Stack e instalar el paquete de actualización. 

## <a name="download-the-update-package"></a>Descarga del paquete de actualización

Cuando haya disponible un paquete de actualización de Microsoft para Azure Stack, descárguelo a una ubicación al que se pueda acceder desde Azure Stack y revise el contenido del paquete. Normalmente, un paquete de actualización consta de los siguientes archivos:

- Un archivo *PackageName*.exe autoextraíble. Este archivo contiene la carga útil de la actualización, por ejemplo la actualización acumulativa más reciente de Windows Server.   
- Los archivos *PackageName*.bin correspondientes. Estos archivos proporcionan compresión para la carga que está asociada el archivo *PackageName*.exe. 
- Un archivo Metadata.xml. Este archivo contiene información esencial acerca de la actualización, como el editor, nombre, requisito previo, tamaño y dirección URL de ruta de acceso de soporte.

## <a name="import-and-install-updates"></a>Importación e instalación de actualizaciones

El siguiente procedimiento muestra cómo importar e instalar actualizaciones en el portal de administración.

1. En el portal de administración, seleccione **Más servicios**. Luego, en la categoría **Datos y almacenamiento**, seleccione **Cuentas de almacenamiento** (o bien, en el cuadro de filtro, empiece a escribir **storage accounts** y selecciónelo).

    ![Muestra dónde se encuentran las cuentas de almacenamiento en el portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. En el cuadro de filtro, escriba **update**y seleccione la cuenta de almacenamiento **updateadminaccount**.

    ![Muestra cómo buscar updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. En los detalles de la cuenta de almacenamiento, en **Servicios**, seleccione **Blobs**.
 
    ![Muestra como acceder a Blobs para la cuenta de almacenamiento](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. En **Blob service**, seleccione **+ Contenedor** para crear un contenedor. Escriba un nombre (por ejemplo *Update-1709*) y, después, haga clic en **Aceptar**.
 
     ![Muestra cómo agregar un contenedor en la cuenta de almacenamiento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Tras crear el contenedor, haga clic en su nombre y, después, haga clic en **Cargar** para cargar los archivos de paquete en el contenedor.
 
    ![Muestra cómo cargar los archivos de paquete](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. En **Cargar blob**, haga clic en el icono de la carpeta, vaya al archivo .exe del paquete de actualización y haga clic en **Abrir** en la ventana del explorador de archivos.
  
7. En **Cargar blob**, haga clic en **Cargar**. 
 
    ![Muestra dónde se carga cada archivo de paquete](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita los pasos 6 y 7 para los archivos *PackageName*.bin y Metadata.xml. 
9. Cuando haya finalizado, puede revisar las notificaciones (el icono de campana de la esquina superior derecha del portal). Las notificaciones deben indicar que la carga se ha completado. 
10. Vuelva al icono de actualización del panel. El icono debe indicar que hay una actualización disponible. Haga clic en el icono para revisar el paquete de actualización recién agregado.
11. Para instalar la actualización, seleccione el paquete que está marcado como **Listo** y haga clic con el botón derecho en él y seleccione **Actualizar ahora**, o bien haga clic en la acción **Actualizar ahora** cerca de la parte superior.
12. Al hacer clic en el paquete de actualización de instalación, puede ver el estado en el área **Update run details** (Detalles de ejecución de actualización). Aquí también puede hacer clic en **Download full logs** (Descargar registros completos) para descargar los archivos de registro.
13. Cuando la actualización finaliza, el icono de actualización muestra la versión actualizada de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

- [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md)