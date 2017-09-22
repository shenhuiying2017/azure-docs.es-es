---
title: "Configuración de credenciales de autenticación con nombre | Microsoft Docs"
description: "Aprenda a proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes en Azure para publicar una aplicación en Azure desde Visual Studio o para supervisar un servicio en la nube existente."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.contentlocale: es-es
ms.lasthandoff: 09/20/2017

---
# <a name="set-up-named-authentication-credentials"></a>Configuración de credenciales de autenticación con nombre
Para publicar una aplicación en Azure desde Visual Studio o supervisar un servicio en la nube existente, debe proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes a Azure. Existen varios lugares en Visual Studio donde puede iniciar sesión para proporcionar estas credenciales. Por ejemplo, desde el Explorador de servidores, abra el menú contextual para el nodo **Azure** y elija **Conectar a la suscripción de Microsoft Azure**. Al iniciar sesión, la información de suscripción asociada con su cuenta de Azure está disponible en Visual Studio. No es necesario hacer nada más.

Azure Tools también admite una forma anterior de proporcionar credenciales, mediante el archivo de suscripción (archivo .publishsettings). En este artículo se describe este método, que todavía se admite en el SDK 2.2 de Azure.

Para efectuar la autenticación en Azure se requieren los siguientes elementos:

* Su Id. de suscripción
* Un certificado X.509 v3 válido

> [!NOTE]
> La longitud de la clave del certificado X.509 v3 debe tener al menos 2048 bits. Azure rechazará cualquier certificado que no cumpla este requisito o que no sea válido.
>
>

Visual Studio usa el Id. de suscripción junto con los datos del certificado como credenciales. Se hace referencia a las credenciales adecuadas en el archivo de suscripción (archivo .publishsettings), que contiene una clave pública para el certificado. El archivo de suscripción puede contener credenciales para más de una suscripción.

Puede editar la información de suscripción en el cuadro de diálogo **Nueva suscripción** o **Editar suscripción**, como se explica más adelante en este artículo.

Si desea crear un certificado por sí mismo, puede consultar las instrucciones de [Creación y carga de un certificado de administración para Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) y después cargar manualmente el certificado en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Estas credenciales que Visual Studio necesita para administrar sus servicios en la nube no coinciden con las necesarias para autenticar una solicitud en los servicios de almacenamiento de Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importación, configuración o edición de las credenciales de autenticación en Visual Studio

1. En el Explorador de servidores, abra el menú contextual para el nodo de **Azure** y seleccione **Administrar y filtrar suscripciones**.
2. Seleccione la pestaña **Certificados** y, a continuación, use cualquiera de los métodos siguientes:

    * Seleccione **Importar** para abrir el cuadro de diálogo **Importar suscripciones de Microsoft Azure**. Allí puede descargar el archivo de suscripciones para la suscripción cargada actualmente, ir a la ubicación de descarga y, después, importarlo para usarlo en la autenticación.
    * Seleccione **Nuevo** para abrir el cuadro de diálogo **Nueva suscripción**. Allí, puede configurar una nueva suscripción para su uso en la autenticación.
    * Seleccione **Editar** (después de elegir su suscripción activa) para abrir el cuadro de diálogo **Editar suscripción**. Allí, puede editar una suscripción existente para su uso en la autenticación. 

En el siguiente procedimiento, se da por supuesto que el cuadro de diálogo **Nueva suscripción** está abierto.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Configuración de las credenciales de autenticación en Visual Studio
1. En la lista **Seleccione un certificado existente para la autenticación**, elija un certificado.
2. Seleccione el vínculo **Copiar la ruta de acceso completa**. La ruta de acceso del certificado (archivo .cer) se copia en el Portapapeles.

   > [!IMPORTANT]
   > Para publicar la aplicación de Azure desde Visual Studio, debe cargar este certificado en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Para cargar el certificado en Azure Portal:

   a. Abra el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Si se le solicita, inicie sesión en el portal y vaya a **Configuración** > **Certificados de administración**.
   
   c. En el panel **Certificados de administración**, elija **Cargar**.
   
   d. Seleccione la suscripción de Azure, pegue la ruta de acceso completa del archivo .cer que acaba de crear y seleccione **Cargar**.

## <a name="next-steps"></a>Pasos siguientes
* [Introducción general sobre Web Apps](https://docs.microsoft.com/azure/app-service/)
* [Documentación de implementación de Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Implementar trabajos web con Visual Studio](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [Crear e implementar un servicio en la nube](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
