---
title: "Creación de una máquina virtual Windows de SQL Server en Azure Portal | Microsoft Docs"
description: "Este tutorial muestra cómo crear una máquina virtual Windows con SQL Server 2017 en Azure Portal."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 2bf2382094f88c8c57077df10473b5f3a2e4c127
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>Creación de una máquina virtual Windows de SQL Server 2017 en Azure Portal

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Esta guía de inicio rápido le ayuda a crear una máquina virtual de SQL Server en Azure Portal.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a id="select"></a> Seleccionar una imagen de máquina virtual de SQL Server

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta.

1. En Azure Portal, haga clic en **Nuevo**. El portal abre la ventana **Nuevo**.

1. En el campo de búsqueda, escriba **SQL Server 2017 Developer on Windows Server 2016** y presione ENTRAR.

1. Seleccione la imagen denominada **Free SQL Server License: SQL Server 2017 Developer on Windows Server 2016**.

   ![Nueva ventana de búsqueda](./media/quickstart-sql-vm-create-portal/newsearch.png)

   > [!TIP]
   > La edición Developer se usa en este tutorial porque es una edición completa de SQL Server que es gratuita para desarrollo y pruebas. Solo paga por el costo de ejecutar la máquina virtual. Para conocer al detalle las consideraciones sobre precios, consulte la [guía de precios para máquinas virtuales de Azure de SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

1. Haga clic en **Crear**.

## <a id="configure"></a> Proporcionar los detalles básicos

En la ventana **Datos básicos**, especifique la siguiente información:

1. En el campo **Nombre**, escriba un nombre único para la máquina virtual. 

1. En el campo **Nombre de usuario**, escriba un nombre para la cuenta de administrador local en la máquina virtual.

1. Proporcione una contraseña segura en **Contraseña**.

1. Escriba un nuevo nombre para el **grupo de recursos**. Este grupo ayuda a administrar todos los recursos asociados con la máquina virtual.

1. Compruebe el resto de valores de configuración predeterminados y haga clic en **Aceptar** para continuar.

   ![Ventana Datos básicos de SQL](./media/quickstart-sql-vm-create-portal/azure-sql-basic.png)

## <a name="choose-virtual-machine-size"></a>Elección del tamaño de la máquina virtual

En el paso **Tamaño**, elija un tamaño de máquina virtual en la ventana **Elegir un tamaño**. En la ventana, aparecen los tamaños de máquina recomendados con arreglo a la imagen seleccionada. 

1. Haga clic en **Ver todo** para ver todos los tamaños de máquina disponibles.

1. Para esta guía de inicio rápido, seleccione **D2S_V3**. El portal muestra el costo mensual estimado de la máquina por el uso continuo (sin incluir los costos de licencia de SQL Server). Tenga en cuenta que la edición Developer no agrega ningún costo adicional a la licencia de SQL Server. Para obtener información de precios más específica, consulte la [página de precios](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

   > [!TIP]
   > El tamaño de máquina **D2S_V3** ahorra dinero durante las pruebas. Para cargas de trabajo de producción, consulte las recomendaciones de tamaño de máquina y configuración en [Procedimientos recomendados de SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

1. Haga clic en **Seleccionar** para continuar.

## <a name="configure-optional-features"></a>Configuración de características opcionales

En la ventana **Configuración**, haga clic en **Aceptar** para seleccionar los valores predeterminados.

## <a name="sql-server-settings"></a>Configuración de SQL Server

En la ventana **Configuración de SQL Server**, configure las siguientes opciones.

1. En la lista desplegable **Conectividad SQL**, seleccione **Publica (Internet)**. Esta opción permite conexiones de SQL Server través de Internet.

1. Cambie el valor de **Puerto** a **1401** para evitar el uso de un nombre de puerto conocido por todos en el escenario público.

1. En **Autenticación de SQL**, haga clic en **Habilitar**. El inicio de sesión de SQL se establece en el mismo nombre de usuario y contraseña que ha configurado para la máquina virtual.

1. Cambie cualquier otro valor de configuración, si es necesario, y haga clic en **Aceptar** para completar la configuración de la máquina virtual de SQL Server.

   ![Configuración de SQL Server](./media/quickstart-sql-vm-create-portal/sql-settings.png)

## <a name="create-the-sql-server-vm"></a>Creación de la máquina virtual de SQL Server

En la ventana **Resumen**, revise el resumen y haga clic en **Comprar** para crear la instancia de SQL Server, el grupo de recursos y los recursos especificados para esta máquina virtual.

Puede supervisar la implementación desde Azure Portal. En el botón **Notificaciones** de la parte superior de la pantalla, se muestra el estado básico de la implementación.

> [!TIP]
> La implementación de una máquina virtual Windows de SQL Server puede llevar varios minutos.

## <a name="connect-to-sql-server"></a>Conexión con SQL Server

1. En el portal, busque la **dirección IP pública** de la máquina virtual en la sección **Información general** de las propiedades de la máquina virtual.

1. En un equipo conectado a Internet diferente, abra SQL Server Management Studio (SSMS).

   > [!TIP]
   > Si no tiene SQL Server Management Studio, puede descargarla [aquí](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. En el cuadro de diálogo **Conectar al servidor** o **Conectarse al motor de base de datos**, edite el valor de **Nombre del servidor**. Escriba la dirección IP pública de la máquina virtual. A continuación, agregue una coma y el puerto personalizado **1401**, que se especificó cuando configuró la nueva máquina virtual. Por ejemplo: `11.22.33.444,1401`.

1. En el cuadro **Autenticación**, seleccione **Autenticación de SQL Server**.

1. En el cuadro **Inicio de sesión** , escriba un nombre de inicio de sesión de SQL válido.

1. En el cuadro **Contraseña** , escriba la contraseña de inicio de sesión.

1. Haga clic en **Conectar**.

    ![conexión ssms](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a> Iniciar sesión en la máquina virtual de forma remota

Use los pasos siguientes para conectarse a la máquina virtual de SQL Server con Escritorio remoto:

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Después de conectarse a la máquina virtual de SQL Server, puede iniciar SQL Server Management Studio y conectarse con la autenticación de Windows mediante sus credenciales de administrador local. Si ha habilitado la autenticación de SQL Server, también puede conectarse con la autenticación de SQL mediante el inicio de sesión de SQL y la contraseña configurada durante el aprovisionamiento.

El acceso a la máquina le permite cambiar directamente la máquina y la configuración de SQL Server según sus necesidades. Por ejemplo, podría configurar el firewall o cambiar la configuración de SQL Server.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita que la máquina virtual de SQL se ejecute continuamente, puede detenerla cuando no esté en uso y así evitar cargos innecesarios. También puede eliminar de forma definitiva todos los recursos asociados con la máquina virtual mediante la eliminación de su grupo de recursos asociado en el portal. Como esta acción también elimina la máquina virtual definitivamente, use este comando con cuidado. Para más información, consulte [Administración de recursos de Azure en el portal](../../../azure-resource-manager/resource-group-portal.md).

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una máquina virtual de SQL Server 2017 en Azure Portal. Para aprender más sobre la migración de los datos a la nueva instancia de SQL Server, consulte el artículo siguiente.

> [!div class="nextstepaction"]
> [Migración de una base de datos a una máquina virtual de SQL](virtual-machines-windows-migrate-sql.md)
