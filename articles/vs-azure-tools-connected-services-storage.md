---
title: "Adición de Azure Storage con Servicios conectados en Visual Studio | Microsoft Docs"
description: "Adición de almacenamiento de Azure a la aplicación mediante el cuadro de diálogo Agregar servicios conectados de Visual Studio"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 521ec044-ad4b-4828-8864-01decde2e758
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c7ca2909de28936f17ab7ac03815d693032b04f


---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Adición de almacenamiento de Azure mediante Servicios conectados de Visual Studio
## <a name="overview"></a>Información general
Con Visual Studio 2015, puede conectar cualquier servicio en la nube de C#, cualquier servicio móvil de back-end de .NET, cualquier servicio o sitio web de ASP.NET, cualquier servicio de ASP.NET 5 o cualquier servicio de trabajo web de Azure al Almacenamiento de Azure mediante el cuadro de diálogo **Agregar servicios conectados** . La funcionalidad del servicio conectado agrega todo el código de conexión y las referencias necesarios y modifica los archivos de configuración de forma adecuada. El cuadro de diálogo también le lleva a documentación que le indica cuáles son los pasos siguientes para iniciar el almacenamiento de blobs, las colas y las tablas.

## <a name="supported-project-types"></a>Tipos de proyecto compatibles
Puede usar el cuadro de diálogo Servicios conectados para conectarse al Almacenamiento de Azure en los siguientes tipos de proyecto

* Proyectos web de ASP.NET
* Proyectos de ASP.NET 5
* Proyectos de rol de trabajo y de rol web de servicio en la nube de .NET
* Proyectos de servicios móviles de .NET
* Proyectos de trabajos web de Azure

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Conexión con Almacenamiento de Azure mediante el cuadro de diálogo Servicios conectados
1. Asegúrese de que tiene una cuenta de Azure. Si aún no tiene ninguna, puede registrarse para obtener una [evaluación gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Cuando tenga una cuenta de Azure, puede crear cuentas de almacenamiento, crear servicios móviles y configurar Azure Active Directory.
2. Abra el proyecto en Visual Studio, abra el menú contextual del nodo **Referencias** en el Explorador de soluciones y luego elija **Agregar servicio conectado**.
   
    ![Adición de un servicio conectado](./media/vs-azure-tools-connected-services-storage/IC796702.png)
3. En el cuadro de diálogo **Agregar servicio conectado**, seleccione **Azure Storage** y elija el botón **Configurar**. Es posible que se le pida que inicie sesión en Azure si aún no lo ha hecho.
   
    ![Cuadro de diálogo Agregar servicio conectado - Almacenamiento](./media/vs-azure-tools-connected-services-storage/IC796703.png)
4. En el cuadro de diálogo **Azure Storage**, elija una cuenta de almacenamiento existente y seleccione **Agregar**.
   
    Si necesita crear una nueva cuenta de almacenamiento, vaya al siguiente paso. De lo contrario, vaya al paso 6.
   
    ![Cuadro de diálogo Almacenamiento de Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)
5. Para crear una cuenta de almacenamiento: 
   
   1. Elija el botón **Crear una nueva cuenta de almacenamiento** en la parte inferior del cuadro de diálogo Almacenamiento de Azure.
   2. Rellene el diálogo cuadro **Crear cuenta de almacenamiento** y elija el botón **Crear**.
      
       ![Cuadro de diálogo Almacenamiento de Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
       Cuando vuelva al cuadro de diálogo **Almacenamiento de Azure** , el nuevo almacenamiento debe aparecer en la lista.
   3. Seleccione el nuevo almacenamiento en la lista y haga clic en **Agregar**.
6. El servicio de almacenamiento conectado aparece bajo el nodo Referencias de servicio  del proyecto de trabajo web.
   
    ![Almacenamiento de Azure en el proyecto de trabajos web](./media/vs-azure-tools-connected-services-storage/IC796705.png)
7. Revise la página Introducción que aparece y consulte cómo se ha modificado el proyecto. Una página Introducción aparece en el explorador siempre que se agregue un servicio conectado. Puede revisar los pasos siguientes sugeridos y los ejemplos de código, o pasar a la página ¿Qué ha ocurrido? para ver qué referencias se agregaron al proyecto y cómo se han modificado los archivos de configuración y códigos.

## <a name="how-your-project-is-modified"></a>¿Cómo se modifica el proyecto?
Cuando haya terminado con el cuadro de diálogo, Visual Studio agrega referencias y modifica determinados archivos de configuración. Los cambios específicos dependen del tipo de proyecto. 

* Para los proyectos de ASP.NET, vea [¿Qué ha ocurrido? - Proyectos de ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
* Para los proyectos de ASP.NET 5, vea [¿Qué ha ocurrido? - Proyectos de ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
* Para los proyectos de servicio en la nube (roles web y roles de trabajo), vea [¿Qué ha ocurrido? – Proyectos de servicio en la nube](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
* Para los proyectos de trabajo web, consulte [¿Qué ha ocurrido? - Proyectos de trabajo web](storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Pasos siguientes
1. Con los ejemplos de código de introducción como guía, cree el tipo de almacenamiento que desee y luego comience a escribir código para acceder a su cuenta de almacenamiento.
2. Formule preguntas y obtenga ayuda
   
   * [Foro de MSDN: Almacenamiento de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
   * [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
   * [Almacenamiento en azure.microsoft.com](https://azure.microsoft.com/services/storage/)
   * [Documentación de almacenamiento en azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)




<!--HONumber=Nov16_HO3-->


