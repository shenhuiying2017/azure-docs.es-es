---
title: "Stream Analytics: Rotación de las credenciales de inicio de sesión para entradas y salidas | Microsoft Docs"
description: "Obtenga información acerca de cómo actualizar las credenciales para las entradas y salidas de Stream Analytics."
keywords: "credenciales de inicio de sesión"
services: stream-analytics
documentationcenter: 
author: SnehaGunda
manager: kfile
editor: cgronlun
ms.assetid: 42ae83e1-cd33-49bb-a455-a39a7c151ea4
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/11/2018
ms.author: sngun
ms.openlocfilehash: c1aded8fefc7b56acd2e9ff36bb2c9641665db76
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Rotación de las credenciales de inicio de sesión para entradas y salidas de un trabajo de Stream Analytics

Cada vez que vuelva a generar credenciales para una entrada o salida de un trabajo de Stream Analytics, debe actualizar el trabajo con nuevas credenciales. Antes de actualizar las credenciales, debe detener el trabajo ya que no es posible reemplazar las credenciales mientras el trabajo está en ejecución. Para reducir el intervalo entre detener y reiniciar el trabajo, Stream Analytics admite reanudar un trabajo desde su última salida. En este tema se describe el proceso de rotación de las credenciales de inicio de sesión y el reinicio del trabajo con nuevas credenciales.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Regeneración de nuevas credenciales y actualización del trabajo con ellas 

En esta sección se le guía en la regeneración de credenciales para Blob Storage, Event Hubs, SQL Database y Table Storage. 

### <a name="blob-storagetable-storage"></a>Almacenamiento de blobs/almacenamiento de tablas
1. Inicie sesión en Azure Portal y busque la cuenta de almacenamiento que usó como entrada/salida para el trabajo de Stream Analytics.    
2. En la sección de configuración, abra **Claves de acceso**. Entre las dos claves predeterminadas (key1, key2), seleccione la que no se usa en su trabajo y vuelva a generarla:  
   ![Regeneración de las claves de la cuenta de almacenamiento](media/stream-analytics-login-credentials-inputs-outputs/image1.png)
3. Copie la clave recién generada:    
4. En Azure Portal, busque el trabajo de Stream Analytics, seleccione **Detener** y espere a que el trabajo se detenga.    
5. Busque la entrada/salida de Blob/Table Storage para la que desea actualizar las credenciales.    
6. Busque el campo **Clave de cuenta de almacenamiento** y pegue la clave recién generada. A continuación, haga clic en **Guardar**.    
7. Al guardar los cambios, se inicia una prueba de conexión automáticamente, que puede ver desde la pestaña de notificaciones. Hay dos notificaciones: una corresponde a guardar la actualización y la otra a probarla:  
   ![Notificaciones después de editar la clave](media/stream-analytics-login-credentials-inputs-outputs/image4.png)
8. Continúe con la sección [Inicio del trabajo desde la última vez que se detuvo] (#start-your-job-from-the-last-stopped-time).

### <a name="event-hubs"></a>Centros de eventos

1. Inicie sesión en Azure Portal y busque el centro de eventos que usó como entrada/salida para el trabajo de Stream Analytics.    
2. En la sección de configuración, abra **Directivas de acceso compartido** y seleccione la directiva de acceso necesaria. Entre la **clave primaria** y la **clave secundaria**, seleccione la que no se usa en el trabajo y vuelva a generarla:  
   ![Regeneración de claves para el centro de eventos](media/stream-analytics-login-credentials-inputs-outputs/image2.png)
3. Copie la clave recién generada:    
4. En Azure Portal, busque el trabajo de Stream Analytics, seleccione **Detener** y espere a que el trabajo se detenga.    
5. Busque la entrada/salida de los centros de eventos para la que desea actualizar las credenciales.    
6. Busque el campo **Clave de la directiva de centro de eventos** y pegue la clave recién generada. A continuación, haga clic en **Guardar**.    
7. Una prueba de conexión se iniciará automáticamente al guardar los cambios; asegúrese de que haya pasado correctamente.    
8. Continúe con la sección [Inicio del trabajo desde la última vez que se detuvo](#start-your-job-from-the-last-stopped-time).

### <a name="sql-database"></a>SQL Database

Debe conectarse a la base de datos SQL para actualizar las credenciales de inicio de sesión de un usuario existente. Para ello, puede usar Azure Portal o una herramienta de cliente, como SQL Server Management Studio. En esta sección se demuestra el proceso de actualizar las credenciales mediante Azure Portal.

1. Inicie sesión en Azure Portal y busque la base de datos SQL que usó como salida para el trabajo de Stream Analytics.    
2. En el **Explorador de datos**, inicie sesión o conéctese a la base de datos, seleccione el tipo de autorización **Autenticación de SQL Server**, escriba sus detalles de **Inicio de sesión** y **Contraseña** y seleccione **Aceptar**.  
   ![Regeneración de credenciales para SQL Database](media/stream-analytics-login-credentials-inputs-outputs/image3.png)

3. En la pestaña de consulta, modifique la contraseña de uno de los usuarios mediante la ejecución de la siguiente consulta (asegúrese de reemplazar `<user_name>` por el nombre de usuario y `<new_password>` por la nueva contraseña):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Anote la nueva contraseña.    
5. En Azure Portal, busque el trabajo de Stream Analytics, seleccione **Detener** y espere a que el trabajo se detenga.    
6. Busque la salida de la base de datos SQL para la que quiere rotar las credenciales. Actualice la contraseña y guarde los cambios.    
7. Una prueba de conexión se iniciará automáticamente al guardar los cambios; asegúrese de que haya pasado correctamente.    
8. Continúe con la sección [Inicio del trabajo desde la última vez que se detuvo](#start-your-job-from-the-last-stopped-time).

### <a name="power-bi"></a>Power BI
1. Inicie sesión en Azure Portal, busque el trabajo de Stream Analytics, seleccione **Detener** y espere a que el trabajo se detenga.    
2. Busque la salida de Power BI para la que quiere renovar credenciales, haga clic en **Renovar autorización** (debe ver un mensaje de operación correcta) y luego en **Guardar** para guardar los cambios.    
3. Al guardar los cambios se iniciará automáticamente una prueba de conexión ; asegúrese de que haya pasado correctamente.    
4. Continúe con la sección [Inicio del trabajo desde la última vez que se detuvo](#start-your-job-from-the-last-stopped-time).

## <a name="start-your-job-from-the-last-stopped-time"></a>Inicio del trabajo desde la última vez que se detuvo

1. Vaya al panel **Información general** del trabajo y seleccione **Iniciar** para iniciar el trabajo.    
2. Seleccione **Hora de la última detención** y haga clic en **Iniciar**. Tenga en cuenta que la opción "Hora de la última detención" solo aparece si previamente ejecutó el trabajo y se generaron algunos resultados. El trabajo se reinicia en función de la hora del último valor de salida.
   ![Inicio del trabajo](media/stream-analytics-login-credentials-inputs-outputs/image5.png)

## <a name="next-steps"></a>pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
