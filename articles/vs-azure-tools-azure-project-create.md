---
title: "Creación de un proyecto de Azure con Visual Studio | Microsoft Docs"
description: "Creación de un proyecto de Azure en Visual Studio"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d933117cf513436e04c79a335998f2ddba848dc4


---
# <a name="creating-an-azure-project-with-visual-studio"></a>Creación de un proyecto de Azure en Visual Studio
Azure Tools para Visual Studio ofrece una plantilla que permite crear un servicio en la nube para Azure. Las herramientas también ayudan a configurar, depurar e implementar el servicio en la nube en Azure.

Una solución de servicios en la nube de Azure contiene los siguientes tipos de proyecto:

* **Proyecto de Azure**
  
    El proyecto de Azure tiene asociaciones a los proyectos de rol de la solución. También incluye los archivos de definición del servicio y de configuración del servicio. El archivo de definición de servicio define la configuración de tiempo de ejecución de la aplicación, incluidos los roles que se requieren, los extremos y el tamaño de máquina virtual. El archivo de configuración de servicio configura el número de instancias de un rol que se ejecutan y los valores de la configuración definida para un rol. Para obtener más información sobre estas opciones, consulte [Configuración de los roles para un servicio en la nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
* **Proyecto de rol web**
  
    Un rol de trabajo realiza el procesamiento en segundo plano. Un rol de trabajo puede comunicarse con servicios de almacenamiento y con otros servicios basados en Internet. Un rol de trabajo puede tener varios extremos HTTP, HTTPS o TCP.
  
  * **Rol web de ASP.NET**, para compilar una aplicación ASP.NET con un front-end web
  * **Rol web de ASP.NET MVC5**
  * **Rol web de ASP.NET MVC4**
  * **Rol web de ASP.NET MVC3**
  * **Rol web de servicio WCF**, para crear un servicio WCF
  * **Rol web de aplicación de negocios de Silverlight** (requiere Visual Studio 2012)
* **Rol de trabajo de caché**
  
    Rol que proporciona una memoria caché dedicada a la aplicación.
* **Rol de trabajo con cola de bus de servicio**
  
    Cola de bus de servicio que ofrece la funcionalidad de Message Queue Server para comunicarse con el proceso de trabajo. Para obtener más información, consulte [Utilización de las colas del bus de servicio](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Abra el proyecto de servicio en la nube en Visual Studio.
1. Inicie Microsoft Visual Studio como administrador.
2. En la barra de menús, elija **Archivo**, **Nuevo**, **Proyecto**.
3. En el panel **Tipos de proyecto**, elija **Nube** en los nodos de plantilla del proyecto de Visual C# o Visual Basic.
4. En el panel **Plantillas**, elija **Servicio en la nube de Azure**.
5. Especifique la versión de .NET Framework que quiere usar para desarrollar el proyecto.
6. Escriba un nombre y una ubicación para el proyecto y un nombre para la solución. Elija el botón **Aceptar** .
7. En el cuadro de diálogo **Nuevo proyecto de Azure** , elija los roles que quiera agregar y elija el botón de flecha derecha para agregarlos a la solución. Puede agregar tantos roles como sea necesario.
8. Para cambiar el nombre de un rol que agregó al proyecto, mantenga puntero sobre el rol en el cuadro de diálogo **Nuevo proyecto de Azure** y elija el icono **Cambiar nombre** situado a la derecha del rol. También puede cambiar el nombre de un rol dentro de la solución una vez agregado.




<!--HONumber=Nov16_HO3-->


