---
title: Configuración personalizada del entorno de ejecución de integración de Azure-SSIS | Microsoft Docs
description: En este artículo se describe cómo usar la interfaz de instalación personalizada para el entorno de ejecución de integración de Azure SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: douglasl
ms.openlocfilehash: b377b5ca9d46d66fe99a8f60383076920b098a7d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33769722"
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Configuración personalizada del entorno de ejecución de integración de Azure-SSIS

La interfaz de instalación personalizada del entorno de ejecución de integración de Azure-SSIS le permite modificar la configuración o el entorno de operación predeterminado (por ejemplo, para iniciar servicios de Windows adicionales) o instalar componentes adicionales (como ensamblados, controladores o extensiones) en cada nodo del entorno de ejecución de integración de Azure-SSIS. En general, proporciona una interfaz para agregar sus propios pasos de instalación durante el aprovisionamiento o la reconfiguración del entorno de ejecución de integración de Azure-SSIS.

Para llevar a cabo la configuración personalizada, debe preparar un script y sus archivos asociados y cargarlos en un contenedor de blobs en la cuenta de Azure Storage. Proporcione un identificador URI de firma de acceso compartido (SAS) para el contenedor al aprovisionar o volver a configurar el entorno de ejecución de integración de Azure-SSIS. Cada nodo del entorno de ejecución de integración de Azure-SSIS descarga entonces el script y sus archivos asociados en el contenedor y ejecuta la instalación personalizada con privilegios elevados. Una vez finalizada la instalación personalizada, cada nodo carga la salida estándar de la ejecución y otros registros en el contenedor.

Puede instalar tanto componentes libres o sin licencia como componentes de pago o con licencia. Si es un fabricante de software independiente, consulte [How to develop paid or licensed components for the Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md) (Desarrollo de componentes de pago o con licencia para el entorno de ejecución de integración de Azure-SSIS).


## <a name="current-limitations"></a>Limitaciones actuales

-   Si desea utilizar `gacutil.exe` para instalar ensamblados en la caché global de ensamblados (GAC), debe proporcionarla como parte de su configuración personalizada o utilizar la copia proporcionada en el contenedor de versión preliminar pública.

-   Si tiene que unir el entorno de ejecución de integración de Azure-SSIS con la instalación personalizada a una red virtual, tiene que ser la red virtual de Azure Resource Manager. La red virtual clásica no se admite.

## <a name="prerequisites"></a>requisitos previos

Para personalizar el entorno de ejecución de integración de Azure-SSIS, necesita lo siguiente:

-   [Suscripción de Azure](https://azure.microsoft.com/)

-   [Una instancia de Azure SQL Database o servidor de Instancia administrada](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Aprovisionamiento del entorno de ejecución de integración de Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Una cuenta de Azure Storage](https://azure.microsoft.com/services/storage/). Para la instalación personalizada, cargue y almacene el script de la instalación personalizada y sus archivos asociados en un contenedor de blobs. El proceso de instalación personalizada también carga sus registros de ejecución en el mismo contenedor de blobs.

## <a name="instructions"></a>Instrucciones

2.  Descargue e instale [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (versión 5.4 o posterior).

3.  Prepare el script de instalación personalizada y sus archivos asociados (por ejemplo, archivos .bat, .cmd, .exe, .dll, .msi o. ps1).

    1.  Debe tener un archivo de script denominado `main.cmd`, que es el punto de entrada de la configuración personalizada.

    2.  Si desea que otros registros generados por otras herramientas (por ejemplo, `msiexec.exe`) se carguen en el contenedor, especifique la variable de entorno predefinida, `CUSTOM_SETUP_SCRIPT_LOG_DIR`, como la carpeta de registro en sus scripts (por ejemplo, `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Descargue e instale e inicie el [Explorador de Azure Storage](http://storageexplorer.com/).

    1.  En **(Local and Attached)** (Local y asociada), haga clic con el botón derecho en **Cuentas de almacenamiento** y seleccione **Conectar a Azure Storage**.

       ![Conectar a Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Seleccione **Usar un nombre y clave de la cuenta de almacenamiento** y, a continuación, seleccione **Siguiente**.

       ![Usar un nombre y clave de la cuenta de almacenamiento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Escriba el nombre de la cuenta de Azure Storage y la clave, seleccione **Siguiente** y, a continuación, seleccione **Conectar**.

       ![Provisión de nombre y clave de la cuenta de almacenamiento](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  En la cuenta conectada de Azure Storage, haga clic con el botón derecho en **Contenedores de blobs**, seleccione **Crear contenedor de blobs** y ponga un nombre al nuevo contenedor.

       ![Creación de un contenedor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Seleccione el nuevo contenedor y cargue el script de instalación personalizado y sus archivos asociados. Asegúrese de cargar `main.cmd` en el nivel superior del contenedor, no en cualquier carpeta. 

       ![Carga de archivos en el contenedor de blobs](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Haga clic con el botón derecho en el contenedor y seleccione **Obtener firma de acceso compartido**.

       ![Obtención de una firma de acceso compartido para el contenedor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Cree el URI de SAS para el contenedor con un tempo de expiración suficientemente largo y con los permisos de lectura, escritura y lista. Necesita el URI de SAS para descargar y ejecutar el script de instalación personalizada y sus archivos asociados siempre que se restablezca la imagen de cualquier nodo de su entorno de ejecución de integración de Azure-SSIS. Se necesita permiso de escritura para cargar los registros de ejecución del programa de instalación.

       ![Generación de la firma de acceso compartido para el contenedor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Copie y guarde el URI de SAS del contenedor.

       ![Copia y guardado de la firma de acceso compartido](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Al aprovisionar o volver a configurar el entorno de ejecución de integración de Azure-SSIS con PowerShell, antes de iniciar el entorno de ejecución de integración de Azure-SSIS, ejecute el cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` con el URI de SAS del contenedor como el valor para el nuevo parámetro `SetupScriptContainerSasUri`. Por ejemplo: 

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Después de que finalice el programa de instalación personalizado y se inicie el entorno de ejecución de integración de Azure-SSIS, podrá encontrar la salida estándar de `main.cmd` y otros registros de ejecución en la carpeta `main.cmd.log` de su contenedor de almacenamiento.

2.  Para ver otros ejemplos de instalación personalizada, conéctese al contenedor de vista preliminar pública con el Explorador de Azure Storage.

    a.  En **(Local and Attached)** (Local y asociada), haga clic con el botón derecho en **Cuentas de almacenamiento**, seleccione **Conectar a Azure Storage**, luego **Use a connection string or a shared access signature URI** (Use una cadena de conexión o un URI de firma de acceso compartido) y finalmente **Siguiente**.

       ![Conexión a Azure Storage con la firma de acceso compartido](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Seleccione **Use a SAS URI** (Usar un URI de SAS) y escriba el siguiente URI de SAS para el contenedor de versión preliminar pública. Seleccione **Siguiente** y luego **Conectar**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Provisión de la firma de acceso compartido para el contenedor](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Seleccione el contenedor de versión preliminar pública conectado y haga doble clic en la carpeta `CustomSetupScript`. En esta carpeta se encuentran los siguientes elementos:

       1. Una carpeta `Sample`, que contiene una instalación personalizada para instalar una tarea básica en cada nodo del entorno de ejecución de integración de Azure-SSIS. La tarea no hace nada, pero se queda en suspensión durante unos segundos. La carpeta también contiene una carpeta `gacutil`, que contiene `gacutil.exe`.

       2. Una carpeta `UserScenarios`, que contiene varias configuraciones personalizadas para escenarios de usuario real.

    ![Contenido del contenedor de versión preliminar pública](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Haga doble clic en la carpeta `UserScenarios`. En esta carpeta se encuentran los siguientes elementos:

       1. Una carpeta `.NET FRAMEWORK 3.5`, que contiene una instalación personalizada para instalar una versión anterior de .NET Framework que puede ser necesaria para los componentes personalizados en cada nodo del entono de ejecución de integración de Azure-SSIS.

       2. Una carpeta `BCP`, que contiene una instalación personalizada para instalar utilidades de línea de comandos de SQL Server (`MsSqlCmdLnUtils.msi`), incluido el programa de copia masiva (`bcp`), en cada nodo del entorno de ejecución de integración de Azure-SSIS.

       3. Una carpeta `EXCEL`, que contiene una instalación personalizada para instalar ensamblados de código abierto (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` y `ExcelDataReader.dll`) en cada nodo del entorno de ejecución de integración de Azure-SSIS.

       4. Una carpeta `MSDTC`, que contiene una instalación personalizada para modificar las configuraciones de red y seguridad para la instancia del Coordinador de transacciones distribuidas de Microsoft (MSDTC) en cada nodo del entorno de ejecución de integración de Azure-SSIS.

       5. Una carpeta `ORACLE ENTERPRISE`, que contiene un script de instalación personalizada (`main.cmd`) y el archivo de configuración de instalación silenciosa (`client.rsp`) para instalar el controlador de Oracle OCI en cada nodo de la edición Enterprise del entorno de ejecución de integración de Azure-SSIS (versión preliminar privada). Este programa de instalación le permite utilizar el administrador de conexiones, el origen y el destino de Oracle. En primer lugar, tendrá que descargar `winx64_12102_client.zip` de [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) y, a continuación, cargarlo junto con `main.cmd` y `client.rsp` en su contenedor. Si usa TNS para conectar con Oracle, también debe descargar `tnsnames.ora`, editarlo y cargarlo en el contenedor, para que se pueda copiar en la carpeta de instalación de Oracle durante el proceso de instalación.

       6. Una carpeta `ORACLE STANDARD`, que contiene un script de instalación personalizada (`main.cmd`) para instalar el controlador ODP.NET de Oracle en cada nodo del entorno de ejecución de integración de Azure-SSIS. Este programa de instalación le permite utilizar el administrador de conexiones, el origen y el destino de ADO.NET. En primer lugar, descargue `ODP.NET_Managed_ODAC122cR1.zip` de [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) y, a continuación, cárguelo junto con `main.cmd` en su contenedor.

       7. Una carpeta `SAP BW`, que contiene un script de instalación personalizada (`main.cmd`) para instalar el ensamblado del conector (`librfc32.dll`) en cada nodo de la edición Enterprise del entorno de ejecución de integración de Azure-SSIS (versión preliminar privada). Este programa de instalación le permite utilizar el administrador de conexiones, el origen y el destino de SAP BW. En primer lugar, cargue la versión de 64 bits o de 32 bits de `librfc32.dll` desde la carpeta de instalación de SAP en el contenedor, junto con `main.cmd`. El script copia entonces el ensamblado de SAP en la carpeta `%windir%\SysWow64` o `%windir%\System32` durante la instalación.

       8. Una carpeta `STORAGE`, que contiene una instalación personalizada para instalar Azure PowerShell en cada nodo del entorno de ejecución de integración de Azure-SSIS. Este programa de instalación le permite implementar y ejecutar paquetes de SSIS que ejecutan [scripts de PowerShell para manipular la cuenta de Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Copie `main.cmd`, un `AzurePowerShell.msi` de ejemplo (o instale la versión más reciente) y `storage.ps1` en su contenedor. Use PowerShell.dtsx como plantilla para los paquetes. La plantilla de paquetes combina una [tarea de descarga de blobs de Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), que descarga `storage.ps1` como un script de PowerShell modificable, y una [tarea de ejecución de proceso](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) que ejecuta el script en cada nodo.

       9. Una carpeta `TERADATA`, que contiene un script de instalación personalizada (`main.cmd)`), su archivo asociado (`install.cmd`) y los paquetes del instalador (`.msi`). Estos archivos instalan conectores Teradata, la API TPT y el controlador ODBC en cada nodo de la edición Enterprise del entorno de ejecución de integración de Azure-SSIS (versión preliminar privada). Este programa de instalación le permite utilizar el administrador de conexiones, el origen y el destino de Teradata. En primer lugar, descargue el archivo ZIP de 15.x de Teradata Tools and Utilities (por ejemplo, `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) desde [Teradata](http://partnerintelligence.teradata.com) y, a continuación, cárguelo junto con los archivos anteriores `.cmd` y `.msi` en su contenedor.

    ![Carpetas en la carpeta de escenarios de usuario](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Para probar estos ejemplos de instalación personalizada, copie y pegue el contenido de la carpeta seleccionada en el contenedor. Al aprovisionar o volver a configurar el entorno de ejecución de integración de Azure-SSIS con PowerShell, ejecute el cmdlet `Set-AzureRmDataFactoryV2IntegrationRuntime` con el URI de SAS del contenedor como el valor para el nuevo parámetro `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Pasos siguientes

-   [Enterprise Edition of the Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md) (Edición Enterprise para la instancia de Integration Runtime para la integración de SSIS en Azure)

-   [How to develop paid or licensed custom components for the Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md) (Desarrollo de componentes personalizados de pago o con licencia para el entorno de ejecución de integración de Azure-SSIS)