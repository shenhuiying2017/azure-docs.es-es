---
title: Uso del SDK de .NET para trabajos de Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: "Aprenda a usar el SDK de .NET para iniciar trabajos de StorSimple Data Manager (versión preliminar privada)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---

# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>Uso del SDK de .NET para iniciar la transformación de datos (versión preliminar privada)

## <a name="overview"></a>Información general

En este artículo se explica cómo se usa la característica de transformación de datos en StorSimple Data Manager para transformar los datos de los dispositivos StorSimple. Luego, los datos transformados los consumen otros servicios de Azure en la nube. El artículo también tiene un tutorial que le ayudará a crear una aplicación de consola .NET de ejemplo para iniciar un trabajo de transformación de datos y, después, realizar su seguimiento para asegurarse de que finaliza.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que dispone de:
*   Un sistema con Visual Studio 2012, 2013, 2015 o 2017 instalados.
*   Azure Powershell instalado. [Descarga de Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Valores de configuración para inicializar el trabajo de transformación de datos (se incluyen las instrucciones necesarias para obtener dichos valores).
*   Una definición del trabajo que se ha configurado correctamente en un recurso de datos híbridos de un grupo de recursos.
*   Todos los archivos dll requeridos. Dichos archivos se pueden descargar del [repositorio de GitHub](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   El [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` del repositorio de GitHub.

## <a name="step-by-step"></a>Paso a paso

Realice los pasos siguientes si desea usar .NET para iniciar un trabajo de transformación de datos.

1. Para recuperar los parámetros de configuración, siga estos pasos:
    1. Descargue `Get-ConfigurationParams.ps1` del script del repositorio de GitHub que se encuentra en la ubicación `C:\DataTransformation`.
    1. Ejecute el script `Get-ConfigurationParams.ps1` del repositorio de GitHub. Escriba el siguiente comando: 

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Puede usar cualquier valor para ActiveDirectoryKey y AppName.


2. Este script genera los siguientes valores:
    * Id. de cliente
    * Id. de inquilino
    * Clave de Active Directory (la misma que escribió anteriormente)
    * Id. de suscripción

3. Con Visual Studio 2012, 2013 o 2015, cree una aplicación de consola .NET de C#.

    1. Inicie **Visual Studio 2012/2013/2015**.
    1. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.
    2. Expanda **Plantillas** y seleccione **Visual C#**.
    3. Seleccione **Aplicación de consola** en la lista de tipos de proyecto de la derecha.
    4. Escriba **DataTransformationApp** en **Nombre**.
    5. Seleccione **C:\DataTransformation** en **Ubicación**.
    6. Haga clic en **Aceptar** para crear el proyecto.

4.  Ahora, agregue todos los archivos DLL de la carpeta [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) como **referencias** en el proyecto que ha creado. Para descargar los archivos dll, realice estas operaciones:

    1. En Visual Studio, vaya a **Vista > Explorador de soluciones**.
    1. Haga clic en la flecha situada a la izquierda del proyecto de la aplicación de transformación de datos. Haga clic en **Referencias** y, después, haga clic con el botón derecho en **Agregar referencia**.
    2. Vaya a la ubicación de la carpeta de paquetes, seleccione todos los archivos DLL, haga clic en **Agregar** y, luego, haga clic en **Aceptar**.

5. Agregue las siguientes instrucciones **using** al archivo de origen (Program.cs) en el proyecto.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. El siguiente código inicializa la instancia del trabajo de transformación de datos. Agréguelo al **Método principal**. Reemplace los valores de los parámetros de configuración que obtuvo antes. Emplee los valores de **Nombre del grupo de recursos** y **Nombre del recurso de datos híbridos**. El **nombre del grupo de recursos** es el que hospeda el recurso de datos híbridos en el que se configuró la definición del trabajo.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);

    ```

7. Especifique los parámetros con los que hay que ejecutar la definición del trabajo

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);

    ```

    O BIEN

    Si desea cambiar los parámetros de la definición del trabajo en tiempo de ejecución, agregue después el código siguiente:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    
    ```

8. Después de la inicialización, agregue el código siguiente para desencadenar un trabajo de transformación de datos en la definición del trabajo. Emplee el **nombre de definición de trabajo** apropiado.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. Este trabajo carga en el contenedor especificado los archivos coincidentes presentes en el directorio raíz del volumen de StorSimple. Cuando se carga un archivo, se coloca un mensaje en la cola (en la misma cuenta de almacenamiento que el contenedor) con el mismo nombre que la definición del trabajo. Dicho mensaje se puede usar como desencadenador para iniciar el posterior procesamiento del archivo.

10. Una vez que el trabajo se haya desencadenado, agregue el código siguiente para realizar un seguimiento de la finalización del mismo.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```


## <a name="next-steps"></a>Pasos siguientes

[Manage using the StorSimple Data Manager service UI](storsimple-data-manager-ui.md) (Administración del uso de la interfaz de usuario del servicio StorSimple Data Manager).

