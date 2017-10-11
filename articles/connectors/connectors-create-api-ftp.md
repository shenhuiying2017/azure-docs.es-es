---
title: Aprenda a usar el conector de FTP en Logic Apps | Microsoft Docs
description: "Cree aplicaciones lógicas con el Servicio de aplicaciones de Azure. Conéctese a un servidor FTP para administrar sus archivos. En FTP, puede realizar diversas acciones, como cargar, actualizar, obtener y eliminar archivos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 61bfbedfd4f1e84b6976099323a32f3a720634c0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-ftp-connector"></a>Introducción al conector de FTP
Use el conector de FTP para supervisar, administrar y crear archivos en un servidor FTP. 

Para poder usar [un conector](apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Conexión a un FTP
Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.  

### <a name="create-a-connection-to-ftp"></a>Creación de una conexión a FTP
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>Uso de un desencadenador de FTP
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!IMPORTANT]
> El conector de FTP necesita un servidor FTP que sea accesible desde Internet y que esté configurado para poder funcionar en modo PASIVO. Además, el conector de FTP **no es compatible con FTPS implícito (FTP por SSL)**. El conector de FTP solo admite FTPS explícito (FTP por SSL).  
> 
> 

En este ejemplo, le enseñaremos a usar el desencadenador **FTP - When a file is added or modified (FTP: cuando se agrega o modifica un archivo)** para que, cuando se agregue o se modifique un archivo en un servidor FTP, se inicie el flujo de trabajo de una aplicación lógica. En un entorno empresarial, podría utilizar este desencadenador para supervisar los nuevos archivos que se agregan a una carpeta FTP y que representan los pedidos de los clientes.  Podría usar una acción del conector de FTP, como **Get file content (Obtener contenido del archivo)**, para obtener el contenido del archivo a fin de procesarlo y almacenarlo después en la base de datos de pedidos.

1. Escriba *ftp* en el cuadro de búsqueda del diseñador de Logic Apps y seleccione el desencadenador **FTP - When a file is added or modified (FTP: cuando se agrega o modifica un archivo)**.   
   ![Imagen de desencadenador de FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   Se abre el control **When a file is added or modified** (Cuando se agrega o modifica un archivo).  
   ![Imagen de desencadenador de FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. Seleccione la opción **...** situada a la derecha del control. Se abrirá el control de selector de carpeta.  
   ![Imagen de desencadenador de FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. Seleccione la opción **>** (flecha derecha) y busque la carpeta donde quiere supervisar los archivos nuevos o modificados. Al seleccionar la carpeta, observará que esta aparece en el control **Carpeta**.  
   ![Imagen de desencadenador de FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

En este punto, la aplicación lógica está configurada con un desencadenador que activará otros desencadenadores y acciones del flujo de trabajo cuando se cree o modifique un archivo en la carpeta FTP especificada. 

> [!NOTE]
> Para que una aplicación lógica sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos que se describen en la sección siguiente para agregar una acción.  
> 
> 

## <a name="use-a-ftp-action"></a>Uso de una acción de FTP
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

Ahora que ha agregado un desencadenador, siga estos pasos para incorporar una acción que obtendrá el contenido del archivo nuevo o modificado detectado por el desencadenador.    

1. Seleccione **+ Nuevo paso** para agregar la acción que obtendrá el contenido del archivo del servidor FTP.  
2. Seleccione el vínculo **Add an action** (Agregar una acción).  
   ![Imagen de acción de FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. Escriba *FTP* para buscar todas las acciones relacionadas con FTP.
4. Seleccione **FTP - Get file content (FTP: obtener contenido del archivo)**. Esta será la acción que se ejecutará cuando se encuentre un archivo nuevo o modificado en la carpeta FTP.      
   ![Imagen de acción de FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   Se abrirá el control **Get file content (Obtener contenido del archivo)**. **Nota**: Si no lo ha hecho previamente, se le pedirá que autorice a la aplicación lógica para que pueda acceder a la cuenta del servidor FTP.  
   ![Imagen de acción de FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. Seleccione el control **Archivo** (el espacio en blanco situado bajo **ARCHIVO***). Aquí, podrá utilizar cualquiera de las propiedades del archivo nuevo o modificado detectado en el servidor FTP.  
6. Seleccione la opción **Contenido del archivo**.  
   ![Imagen de acción de FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. El control se actualiza, lo que indica que la acción **FTP - Get file content (FTP: obtener contenido del archivo)** obtendrá el *contenido del archivo* nuevo o modificado del servidor FTP.      
   ![Imagen de acción de FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. Guarde el trabajo y agregue un archivo a la carpeta FTP para comprobar el flujo de trabajo.    

En este punto, la aplicación lógica está configurada con un desencadenador que supervisa una carpeta de un servidor FTP e inicia el flujo de trabajo cuando se detecta un archivo nuevo o modificado en dicho servidor. 

La aplicación lógica también está configurada con una acción que obtiene el contenido del archivo nuevo o modificado.

Puede agregar otra acción; por ejemplo, [SQL Server - insert row (SQL Server: insertar fila)](connectors-create-api-sqlazure.md), para insertar el contenido del archivo nuevo o modificado en una tabla de SQL Database.  

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/ftpconnector/). 

## <a name="next-steps"></a>Pasos siguientes
[Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)

