---
title: "Instalación de trabajos de base de datos elástica | Microsoft Docs"
description: "Pasos de instalación de la característica de trabajo elástico."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: cbe0aa2b-17e3-4b6f-a16f-6ebc1f5a66af
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 71a5aa4da32e76ff02e4a4dae0d47cf03e323688
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="installing-elastic-database-jobs-overview"></a>Información general sobre la instalación de Trabajos de base de datos elástica
Los [**trabajos de base de datos elástica**](sql-database-elastic-jobs-overview.md) se pueden instalar a través de PowerShell o del Portal de Azure clásico. Obtendrá acceso para crear y administrar trabajos con la API de PowerShell solo si instala el paquete de PowerShell. Además, en este momento, las API de PowerShell proporcionan mucha más funcionalidad que el portal en este momento.

Si ya instaló los **trabajos de base de datos elástica** a través del Portal a partir de un **grupo elástico** existente, la última versión preliminar de PowerShell incluye scripts para actualizar la instalación existente. Es muy recomendable actualizar la instalación a los componentes más recientes de los **trabajos de base de datos elástica** para aprovechar la nueva funcionalidad expuesta a través de las API de PowerShell.

## <a name="prerequisites"></a>Requisitos previos
* Una suscripción de Azure. Para obtener una versión de evaluación gratuita, consulte [Versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell. Instale la versión más reciente mediante el [Instalador de plataforma web](http://go.microsoft.com/fwlink/p/?linkid=320376). Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
* [utilidad de línea de comandos NuGet](https://nuget.org/nuget.exe) sirve para instalar el paquete de trabajos de base de datos elástica. Para obtener más información, consulte http://docs.nuget.org/docs/start-here/installing-nuget.

## <a name="download-and-import-the-elastic-database-jobs-powershell-package"></a>Descarga e importación del paquete de PowerShell de Trabajos de base de datos elástica
1. Inicie la ventana de comandos de Microsoft Azure PowerShell y navegue al directorio donde descargó la utilidad de línea de comandos NuGet (nuget.exe).
2. Descargue e importe el paquete de **trabajos de base de datos elástica** en el directorio actual con el siguiente comando:
   
        PS C:\>.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease
   
    Los archivos de **trabajos de base de datos elástica** se ubican en el directorio local, en una carpeta con el nombre **Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x**, donde *x.x.xxxx.x* indica el número de versión. Los cmdlets de PowerShell (incluidos los archivos .DLL de cliente necesarios) se ubican en el subdirectorio **tools\ElasticDatabaseJobs** y los scripts de PowerShell para instalar, actualizar y desinstalar también residen en el subdirectorio **tools**.
3. Navegue al subdirectorio tools de la carpeta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x. Para ello, escriba cd tools, como en este ejemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

4. Ejecute el script .\InstallElasticDatabaseJobsCmdlets.ps1 para copiar el directorio ElasticDatabaseJobs en $home\Documents\WindowsPowerShell\Modules. Así también importará automáticamente el módulo para su uso, por ejemplo:
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobsCmdlets.ps1

## <a name="install-the-elastic-database-jobs-components-using-powershell"></a>Instalación de componentes de Trabajos de base de datos elástica mediante PowerShell
1. Inicie una ventana de comandos de Microsoft Azure PowerShell y navegue hasta el subdirectorio \tools en la carpeta Microsoft.Azure.SqlDatabase.Jobs.x.x.xxx.x: escriba cd \tools
   
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*>cd tools

2. Ejecute el script .\InstallElasticDatabaseJobs.ps1 de PowerShell y proporcione valores para las variables que solicite. Este script creará los componentes descritos en [Componentes y precios de trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md#components-and-pricing) con la configuración del Servicio en la nube de Azure para usar adecuadamente los componentes dependientes.

        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
        PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\InstallElasticDatabaseJobs.ps1

Al ejecutar este comando, se abrirá una ventana para especificar el **nombre de usuario** y la **contraseña**. No especifique aquí sus credenciales de Azure, sino el nombre de usuario y contraseña serán las credenciales de administrador que desea crear para el nuevo servidor.

Los parámetros proporcionados en esta invocación de ejemplo pueden modificarse para tener la configuración que quiera. La siguiente tabla ofrece más información sobre el comportamiento de cada parámetro:

<table style="width:100%">
  <tr>
    <th>Parámetro</th>
    <th>Description</th>
  </tr>

<tr>
    <td>ResourceGroupName</td>
    <td>Proporciona el nombre del grupo de recursos de Azure creado para contener los componentes de Azure recién creados. El valor predeterminado de este parámetro es "__ElasticDatabaseJob". No se recomienda cambiar este valor.</td>
    </tr>

</tr>

    <tr>
    <td>ResourceGroupLocation</td>
    <td>Proporciona la ubicación de Azure que se usará para los componentes de Azure recién creados. El valor predeterminado de este parámetro es la ubicación Central EE. UU.</td>
</tr>

<tr>
    <td>ServiceWorkerCount</td>
    <td>Proporciona el número de trabajos del servicio que se va a instalar. El valor predeterminado de este parámetro es 1. Puede usarse un número mayor de trabajos para escalar horizontalmente el servicio y ofrecer alta disponibilidad. Se recomienda usar "2" en las implementaciones que requieren alta disponibilidad del servicio.</td>
    </tr>

</tr>
    <tr>
    <td>ServiceVmSize</td>
    <td>Proporciona el tamaño de máquina virtual para su uso dentro del servicio en la nube. El valor predeterminado de este parámetro es A0. Se aceptan los valores de parámetros de A0/A1/A2/A3, que hacen que el rol de trabajo use un tamaño ExtraSmall/Small/Medium/Large, respectivamente. Para obtener más información sobre los tamaños de rol de trabajo, consulte [Componentes y precios de trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerDatabaseSlo</td>
    <td>Proporciona el objetivo de nivel de servicio para un servidor Standard Edition. El valor predeterminado de este parámetro es S0. Se aceptan valores de parámetro de S0/S1/S2/S3/S4/S6/S9/S12 lo que hace que Azure SQL Database use el cierre de sesión único respectivo. Para obtener más información sobre SLO de SQL Database, consulte [Componentes y precios de trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorUserName</td>
    <td>Proporciona el nombre de usuario del administrador del servidor de Base de datos SQL de Azure recién creado. Cuando no se especifica, se abrirá una ventana de credenciales de PowerShell para solicitar las credenciales.</td>
</tr>

</tr>
    <tr>
    <td>SqlServerAdministratorPassword</td>
    <td>Proporciona la contraseña del administrador del servidor de Base de datos SQL de Azure recién creado. Cuando no se proporciona, se abrirá una ventana de credenciales de PowerShell para solicitar las credenciales.</td>
</tr>
</table>

En sistemas destinados a tener grandes cantidades de trabajos ejecutándose en paralelo en gran cantidad de bases de datos, se recomienda especificar parámetros como, por ejemplo: -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\InstallElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\InstallElasticDatabaseJobs.ps1 -ServiceWorkerCount 2 -ServiceVmSize A2 -SqlServerDatabaseSlo S2

## <a name="update-an-existing-elastic-database-jobs-components-installation-using-powershell"></a>Actualización de una instalación existente de componentes de Trabajos de base de datos elástica mediante PowerShell
**trabajos de base de datos elástica** se pueden actualizar en una instalación existente para escalado y alta disponibilidad. Este proceso permite actualizaciones futuras del código de servicio sin tener que quitar y volver a crear la base de datos de control. Este proceso también puede usarse dentro de la misma versión para modificar el tamaño de máquina virtual del servicio o el número de trabajos de servidor.

Para actualizar el tamaño de máquina virtual de una instalación, ejecute el siguiente script con parámetros actualizados con los valores que elija.

    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>Unblock-File .\UpdateElasticDatabaseJobs.ps1
    PS C:\*Microsoft.Azure.SqlDatabase.Jobs.dll.x.x.xxx.x*\tools>.\UpdateElasticDatabaseJobs.ps1 -ServiceVmSize A1 -ServiceWorkerCount 2

<table style="width:100%">
  <tr>
  <th>Parámetro</th>
  <th>Description</th>
</tr>

  <tr>
    <td>ResourceGroupName</td>
    <td>Identifica el nombre del grupo de recursos de Azure usado al instalar inicialmente componentes de Trabajos de base de datos elástica. El valor predeterminado de este parámetro es "__ElasticDatabaseJob". Puesto que no se recomienda cambiar este valor, no debería tener que especificar este parámetro.</td>
    </tr>
</tr>

</tr>

  <tr>
    <td>ServiceWorkerCount</td>
    <td>Proporciona el número de trabajos del servicio que se va a instalar.  El valor predeterminado de este parámetro es 1.  Puede usarse un número mayor de trabajos para escalar horizontalmente el servicio y ofrecer alta disponibilidad.  Se recomienda usar "2" en las implementaciones que requieren alta disponibilidad del servicio.</td>
</tr>

</tr>

    <tr>
    <td>ServiceVmSize</td>
    <td>Proporciona el tamaño de máquina virtual para su uso dentro del servicio en la nube. El valor predeterminado de este parámetro es A0. Se aceptan los valores de parámetros de A0/A1/A2/A3, que hacen que el rol de trabajo use un tamaño ExtraSmall/Small/Medium/Large, respectivamente. Para obtener más información sobre los tamaños de rol de trabajo, consulte [Componentes y precios de trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md#components-and-pricing).</td>
</tr>

</table>

## <a name="install-the-elastic-database-jobs-components-using-the-portal"></a>Instalación de componentes de Trabajos de base de datos elástica mediante el Portal
Una vez creado el [grupo elástico](sql-database-elastic-pool-manage-portal.md), puede instalar componentes de los **trabajos de base de datos elástica** para habilitar la ejecución de tareas administrativas en cada base de datos del grupo. A diferencia de lo que sucede cuando se usan las API de PowerShell de **Trabajos de base de datos elástica** , la interfaz del portal está actualmente restringida exclusivamente a la ejecución en un grupo existente.

**Tiempo estimado para completar el tutorial:** 10 minutos.

1. En la vista del panel del grupo elástico a través de [Azure Portal](https://portal.azure.com/#), haga clic en **Crear trabajo**.
2. Si va a crear un trabajo por primera vez, deberá instalar los **trabajos de base de datos elástica** haciendo clic en **TÉRMINOS DE VISTA PREVIA**.
3. Acepte los términos haciendo clic en la casilla de verificación.
4. En la vista "Instalar servicios", haga clic en **CREDENCIALES DEL TRABAJO**.
   
    ![Instalación de los servicios][1]
5. Escriba un nombre de usuario y una contraseña de administración de la base de datos. Como parte de la instalación, se creará un nuevo servidor de base de datos SQL de Azure. En este nuevo servidor, se creará una nueva base de datos, conocida como base de datos de control, que sirve para contener los metadatos de Trabajos de base de datos elástica. El nombre de usuario y la contraseña que se crean aquí se usan con el fin de iniciar sesión en la base de datos de control. Se usa una credencial diferente para la ejecución de scripts en las bases de datos del grupo.
   
    ![Creación del nombre de usuario y la contraseña][2]
6. Haga clic en el botón Aceptar. Los componentes se crean en pocos minutos en un nuevo [grupo de recursos](../azure-resource-manager/resource-group-overview.md). El nuevo grupo de recursos se ancla al panel de inicio, tal como se muestra a continuación. Una vez creados, los trabajos de bases de datos elásticas (servicio de nube, base de datos SQL, bus de servicio y almacenamiento) se crearán en el grupo.
   
    ![Grupo de recursos en el panel de inicio][3]
7. Si intenta crear o administrar un trabajo mientras se está instalando trabajos de bases de datos elásticas, se mostrará el mensaje siguiente al proporcionar las **Credenciales** .
   
    ![Implementación en curso][4]

Si se requiere la desinstalación, elimine el grupo de recursos. Consulte [Desinstalación de componentes de trabajos de base de datos elástica](sql-database-elastic-jobs-uninstall.md).

## <a name="next-steps"></a>Pasos siguientes
Asegúrese de que se cree una credencial con los derechos adecuados para la ejecución del script en cada base de datos del grupo. Para obtener más información, consulte [Protección de bases de datos SQL](sql-database-manage-logins.md).
Consulte [Creación y administración de trabajos de base de datos elástica](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/not-done.png
