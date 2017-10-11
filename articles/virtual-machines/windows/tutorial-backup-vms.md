---
title: "Copia de seguridad de máquinas virtuales Windows de Azure | Microsoft Docs'"
description: "Para proteger las máquinas virtuales Windows, realice una copia de seguridad de ellas mediante Azure Backup."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e58a2290e5034ef393f65cbcddb86e18cf4a6ec
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Copia de seguridad de máquinas virtuales Windows en Azure

Para proteger sus datos realice copias de seguridad a intervalos regulares. Azure Backup crea puntos de recuperación que se almacenan en almacenes de recuperación con redundancia geográfica. Cuando se realiza una restauración desde un punto de recuperación, se puede restaurar toda una máquina virtual o solo determinados archivos. En este artículo se explica cómo restaurar un único archivo en una máquina virtual que ejecuta Windows Server e IIS. Si aún no tiene una máquina virtual que pueda usar, puede crear una mediante la guía de [inicio rápido de Windows](quick-create-portal.md). En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una copia de seguridad de una máquina virtual.
> * Programar una copia de seguridad diaria.
> * Restaurar un archivo desde una copia de seguridad.




## <a name="backup-overview"></a>Introducción a Backup

Cuando el servicio Azure Backup inicia una copia de seguridad, desencadena la extensión de copia de seguridad para que tome una instantánea de un momento dado. El servicio Azure Backup usa la extensión _VMSnapshot_. La extensión se instala cuando se realiza la primera copia de seguridad de la máquina virtual, en caso de que esta esté en ejecución. Si no se está ejecutando la máquina virtual, el servicio Azure Backup toma una instantánea del almacenamiento subyacente (ya que no se produce ninguna escritura de la aplicación mientras se detiene la máquina virtual).

Cuando se toma una instantánea de las máquinas virtuales de Windows, el servicio Azure Backup se coordina con el servicio de instantáneas de volumen (VSS) para obtener una instantánea coherente de los discos de la máquina virtual. Después de que el servicio Azure Backup toma la instantánea, se transfieren los datos al almacén. Para que el proceso resulte más eficaz, el servicio identifica y transfiere únicamente los bloques de datos que han cambiado desde la última copia de seguridad.

Cuando finaliza la transferencia de datos, se elimina la instantánea y se crea un punto de recuperación.


## <a name="create-a-backup"></a>Creación de una copia de seguridad
Cree una copia de seguridad diaria programada simple en un almacén de Recovery Services. 

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Máquinas virtuales**. 
3. En la lista, seleccione la máquina virtual de la que quiere realizar una copia de seguridad.
4. En la hoja de la máquina virtual, en la sección **Configuración**, haga clic en **Copia de seguridad**. Se abre la hoja **Habilitar copia de seguridad**.
5. En **Almacén de Recovery Services**, haga clic en **Create new** (Crear nuevo) y especifique el nombre del nuevo almacén. Se crea un nuevo almacén en el grupo de recursos y ubicación en que se encuentra la máquina virtual.
6. Haga clic en **Directiva de copia de seguridad**. En este ejemplo, conserve los valores predeterminados y haga clic en **Aceptar**.
7. En la hoja **Habilitar copia de seguridad**, haga clic en **Habilitar copia de seguridad**. De esta forma se crea una copia de seguridad diaria según la programación predeterminada.
10. Para crear un punto de recuperación inicial, en la hoja **Copia de seguridad** haga clic en **Realizar copia de seguridad ahora**.
11. En la hoja **Realizar copia de seguridad ahora**, haga clic en el icono del calendario, use el control de calendario para seleccionar el último día en que se mantendrá este punto de recuperación y haga clic en **Copia de seguridad**.
12. En la hoja **Copia de seguridad** de la máquina virtual, verá el número de puntos de recuperación completos.

    ![Puntos de recuperación](./media/tutorial-backup-vms/backup-complete.png)
    
La primera copia de seguridad tarda aproximadamente 20 minutos. Cuando la copia de seguridad finalice, pase a la parte siguiente de este tutorial.

## <a name="recover-a-file"></a>Recuperación de un archivo

Si accidentalmente elimina o realiza cambios en un archivo, puede usar Recuperación de archivos para recuperar el archivo del almacén de Backup. Recuperación de archivos usa un script que se ejecuta en la máquina virtual para montar el punto de recuperación como unidad local. Estas unidades permanecerán montadas durante 12 horas para que pueda copiar archivos desde el punto de recuperación y restaurarlos en la máquina virtual.  

En este ejemplo, se muestra cómo recuperar el archivo de imagen que se usa en la página web predeterminada de IIS. 

1. Abra un explorador y conéctese a la dirección IP de la máquina virtual para mostrar la página predeterminada de IIS.

    ![Página web predeterminada de IIS](./media/tutorial-backup-vms/iis-working.png)

2. Conéctese a la máquina virtual.
3. En la máquina virtual, abra el **Explorador de archivos**, vaya a \inetpub\wwwroot y elimine el archivo **iisstart.png**.
4. En el equipo local, actualice el explorador para ver que la imagen en la página predeterminada de IIS ha desaparecido.

    ![Página web predeterminada de IIS](./media/tutorial-backup-vms/iis-broken.png)

5. En el equipo local, abra una nueva pestaña y vaya al [portal de Azure](https://portal.azure.com).
6. En el menú de la izquierda, seleccione **Máquinas virtuales** y seleccione la máquina virtual de la lista.
8. En la hoja de la máquina virtual, en la sección **Configuración**, haga clic en **Copia de seguridad**. Se abre la hoja **Copia de seguridad**. 
9. En el menú de la parte superior de la hoja, seleccione **Recuperación de archivos**. Se abrirá la hoja **Recuperación de archivos**.
10. En **Paso 1: Seleccionar punto de recuperación**, seleccione un punto de recuperación en la lista desplegable.
11. En **Paso 2: Descargar script para examinar y recuperar archivos**, haga clic en el botón **Download Executable** (Descargar ejecutable). Guarde el archivo en la carpeta **Descargas**.
12. En el equipo local, abra el **Explorador de archivos**, vaya a la carpeta **Descargas** y copie el archivo .exe descargado. El nombre de archivo llevará delante el nombre de su máquina virtual. 
13. En la máquina virtual (a través de la conexión RDP), pegue el archivo .exe en el escritorio de la máquina virtual. 
14. Vaya al escritorio de la máquina virtual y haga doble clic en el archivo .exe. Se inicia un símbolo del sistema y luego se monta el punto de recuperación como un recurso compartido de archivos al que puede obtener acceso. Cuando se termine de crear el recurso compartido, escriba **q** para cerrar el símbolo del sistema.
15. En la máquina virtual, abra el **Explorador de archivos** y vaya a la letra de unidad que se usó para el recurso compartido de archivos.
16. Vaya a \inetpub\wwwroot, copie **iisstart.png** del recurso compartido de archivos y péguelo en \inetpub\wwwroot. Por ejemplo, copie F:\inetpub\wwwroot\iisstart.png y péguelo en c:\inetpub\wwwroot para recuperar el archivo.
17. En el equipo local, abra la pestaña del explorador en el que está conectado a la dirección IP de la máquina virtual que muestra la página predeterminada de IIS. Presione CTRL + F5 para actualizar la página del explorador. Ahora debería ver que la imagen se ha restaurado.
18. En el equipo local, vuelva a la pestaña de explorador de Azure Portal y en **Paso 3: Desmontar los discos después de la recuperación**, haga clic en el botón **Desmontar discos**. Si olvida realizar este paso, la conexión al punto de montaje se cierra automáticamente tras 12 horas. A las 12 horas, es preciso que descargue un script nuevo para crear un nuevo punto de montaje.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear una copia de seguridad de una máquina virtual.
> * Programar una copia de seguridad diaria.
> * Restaurar un archivo desde una copia de seguridad.

En el siguiente tutorial se explica cómo supervisar máquinas virtuales.

> [!div class="nextstepaction"]
> [Supervisión de máquinas virtuales](tutorial-monitoring.md)









