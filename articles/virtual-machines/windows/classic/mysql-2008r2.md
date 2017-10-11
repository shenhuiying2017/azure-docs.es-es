---
title: "Creación de una máquina virtual de Azure clásico con MySQL | Microsoft Docs"
description: "Cree una máquina virtual de Azure que ejecute la Base de datos MySQL y Windows Server 2012 R2 con el modelo de implementación clásica."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>Instalación de MySQL en una máquina virtual creada con el modelo de implementación clásica con Windows Server 2016
[MySQL](https://www.mysql.com) L es una conocida base de datos SQL de código abierto. Este tutorial muestra cómo instalar y ejecutar la **versión de comunidad de MySQL 5.7.18** como un servidor MySQL en una máquina virtual con **Windows Server 2016**. La experiencia podría ser ligeramente diferente con otras versiones de Windows Server o de MySQL.

Para obtener instrucciones sobre la instalación de MySQL en Linux, consulte: [Instalación de MySQL en Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Crear una máquina virtual con Windows Server 2016
Si todavía no tiene una máquina virtual con Windows Server 2016, puede usar este [tutorial](./tutorial.md) para crearla.

## <a name="attach-a-data-disk"></a>Acoplamiento de un disco de datos
Después de crear la máquina virtual, puede adjuntar un disco de datos. La adición de un disco de datos se recomienda para las cargas de trabajo de producción y para evitar quedarse sin espacio en la unidad de sistema operativo (C:), que incluye el sistema operativo.

Consulte [Acoplamiento de un disco de datos a una máquina virtual de Windows](../attach-managed-disk-portal.md) y siga las instrucciones para conectar un disco vacío. Establezca la configuración de la caché de host en **Ninguna** o **Solo lectura**.

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sesión en la nueva máquina virtual
A continuación, [inicie sesión en la máquina virtual](./connect-logon.md) para poder instalar MySQL.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalación y ejecución de MySQL Community Server en la máquina virtual
Siga estos pasos para instalar, configurar y ejecutar la versión de la comunidad de MySQL Server:

> [!NOTE]
> Al descargar elementos mediante Internet Explorer, puede establecer la opción **Configuración de seguridad mejorada** de IE en Desactivada y simplificar el proceso de descarga. En el menú Inicio, haga clic en Herramientas administrativas/Administrador del servidor/Servidor local y luego en **Configuración de seguridad mejorada** de IE y establezca la configuración en Desactivada.
>
>

1. Una vez conectado a la máquina virtual mediante el Escritorio remoto, haga clic en **Internet Explorer** desde la pantalla de inicio.
2. Seleccione el botón **Herramientas** de la esquina superior derecha (el icono de la rueda dentada) y después haga clic en **Opciones de Internet**. Haga clic en la pestaña **Seguridad**, haga clic en el icono **Sitios de confianza** y luego haga clic en el botón **Sitios**. Agregue http://*.mysql.com a la lista de sitios de confianza. Haga clic en **Cerrar** y después en **Aceptar**.
3. En la barra de direcciones de Internet Explorer, escriba https://dev.mysql.com/downloads/mysql/.
4. Utilice el sitio de MySQL para buscar y descargar la versión más reciente del instalador de MySQL para Windows. Al elegir el instalador de MySQL, descargue la versión que tenga el conjunto completo de archivos (por ejemplo, mysql-installer-community-5.7.18.0.msi con un tamaño de archivo de 352,8 MB) y guarde el instalador.
5. Cuando el instalador haya terminado de descargarse, haga clic en **Ejecutar** para iniciar el programa de instalación.
6. En la página **Contrato de licencia**, acepte el contrato de licencia y haga clic en **Siguiente**.
7. En la página **Choosing a Setup Type** (Elección de un tipo de instalación), haga clic en el tipo de instalación que desee y, a continuación, haga clic en **Siguiente**. Los pasos siguientes presuponen que se ha seleccionado el tipo de instalación **Solo servidor** .
8. Si aparece la página **Comprobar requisitos**, haga clic en **Ejecutar** para permitir que el instalador instale los componentes que falten. Siga las instrucciones que aparecen, como el runtime redistribuible de C++.
9. En la página **Instalación**, haga clic en **Ejecutar**. Cuando la instalación se haya completado, haga clic en **Siguiente**.

10. En la página **Configuración del producto**, haga clic en **Siguiente**.

11. En la página **Tipo y redes** , especifique las opciones de conectividad y de tipo de configuración que desea, incluido el puerto TCP si es necesario. Seleccione **Mostrar opciones avanzadas** y después haga clic en **Siguiente**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. En la página **Cuentas y roles** , especifique una contraseña de raíz segura de MySQL. Agregue cuentas de usuario de MySQL adicionales según sea necesario y haga clic en **Siguiente**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. En la página **Servicio de Windows**, especifique los cambios en la configuración predeterminada para ejecutar MySQL Server como un servicio de Windows según sea necesario y, a continuación, haga clic en **Siguiente**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. Las opciones de la página **Complementos y extensiones** son opcionales. Haga clic en **Siguiente** para continuar.
15. En la página **Opciones avanzadas**, especifique los cambios en las opciones de registro según sea necesario y, a continuación, haga clic en **Siguiente**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. En la página **Apply Server Configuration** (Aplicar la configuración del servidor), haga clic en **Ejecutar**. Cuando haya completado los pasos de configuración, haga clic en **Finalizar**.
17. En la página **Configuración del producto**, haga clic en **Siguiente**.
18. En la página **Instalación completada**, haga clic en **Copiar el registro en el Portapapeles** si desea examinarlo más tarde y, a continuación, haga clic en **Finalizar**.
19. En la pantalla de inicio, escriba **mysql** y luego haga clic en **MySQL 5.7 Command Line Client** (Cliente de línea de comandos de MySQL 5.7).
20. Escriba la contraseña raíz que ha especificado en el paso 12 y se mostrará un símbolo del sistema donde puede emitir comandos para configurar MySQL. Para obtener detalles de los comandos y la sintaxis, consulte [Manuales de referencia de MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. También puede establecer opciones predeterminadas de configuración del servidor, como las unidades y los directorios base y de datos. Para más información, vea [6.1.2 Server Configuration Defaults (Valores predeterminados de configuración de Server 6.1.2)](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configuración de extremos

Para que el servicio MySQL esté disponible para los equipos cliente en Internet, debe configurar un punto de conexión para el puerto TCP y crear una regla de Firewall de Windows. El valor del puerto predeterminado en el que el servicio MySQL Server escucha a los clientes MySQL es 3306. Puede especificar otro puerto siempre que sea coherente con el valor proporcionado en la página **Tipo y redes** (paso 11 del procedimiento anterior).

> [!NOTE]
> Para uso en producción, considere las implicaciones de seguridad de que el servicio MySQL Server esté disponible para todos los equipos en Internet. Puede definir el conjunto de direcciones IP de origen que tienen permiso para usar el extremo con una lista de control de acceso (ACL). Para más información, consulte [Configuración de puntos de conexión en una máquina virtual](setup-endpoints.md).
>
>

Para configurar un extremo del servicio de MySQL Server:

1. En Azure Portal, haga clic en **Máquinas virtuales (clásico)**, en el nombre de la máquina virtual de MySQL y luego en **Puntos de conexión**.
2. En la barra de comandos, haga clic en **Agregar**.
3. En la página **Agregar punto de conexión**, escriba un nombre único para **Nombre**.
4. Seleccione **TCP** como protocolo.
5. Escriba el número de puerto, por ejemplo, **3306**, tanto en **Puerto público** como en **Puerto privado** y luego haga clic en **Aceptar**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Agregar una regla de Firewall de Windows para permitir el tráfico de MySQL
Para agregar una regla de Firewall de Windows que permita tráfico de MySQL desde Internet, ejecute el siguiente comando en un _símbolo del sistema de Windows PowerShell con privilegios elevados_ en la máquina virtual del servidor MySQL.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>Probar la conexión remota
Para probar la conexión remota a la máquina virtual de Azure con el servicio MySQL Server, debe proporcionar el nombre DNS del servicio en la nube que contiene el VN.

1. En Azure Portal, haga clic en **Máquinas virtuales (clásico)**, en el nombre de la máquina virtual del servidor MySQL y luego en **Información general**.
2. En el panel de la máquina virtual, anote el valor de **Nombre DNS**. Aquí tiene un ejemplo:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. Desde un equipo local que tenga MySQL o el cliente de MySQL en ejecución, ejecute el siguiente comando para iniciar sesión como un usuario de MySQL.

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   Por ejemplo, con el nombre de usuario de MySQL _dbadmin3_ y el nombre DNS _testmysql.cloudapp.net_ para la máquina virtual, podría iniciar MySQL con el siguiente comando:

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la ejecución de MySQL, consulte la [documentación de MySQL](http://dev.mysql.com/doc/).
