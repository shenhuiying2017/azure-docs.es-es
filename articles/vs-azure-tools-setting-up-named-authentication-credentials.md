---
title: "Configuración de credenciales de autenticación con nombre | Microsoft Docs"
description: "Aprenda a proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes a Azure para publicar una aplicación en Azure desde Visual Studio o para supervisar un servicio en la nube existente. "
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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c486676a70e195ec85ad40540ea4b7caaa86bc48
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---
# <a name="setting-up-named-authentication-credentials"></a>Configuración de credenciales de autenticación con nombre
Para publicar una aplicación en Azure desde Visual Studio o supervisar un servicio en la nube existente, debe proporcionar credenciales que Visual Studio pueda usar para autenticar solicitudes a Azure. Existen varios lugares en Visual Studio donde puede iniciar sesión para proporcionar estas credenciales. Por ejemplo, desde el Explorador de servidores, abra el menú contextual para el nodo **Azure** y elija **Conectar a la suscripción de Microsoft Azure...** Al iniciar sesión, la información de suscripción asociada con su cuenta de Azure está disponible en Visual Studio y con eso basta.

Azure Tools también admite una forma anterior de proporcionar credenciales, mediante el archivo de suscripción (archivo .publishsettings). En este tema se describe este método, que todavía se admite en el SDK 2.2 de Azure.

Para efectuar la autenticación en Azure se requieren los siguientes elementos:

* Su Id. de suscripción
* Un certificado X.509 v3 válido

> [!NOTE]
> La longitud de la clave del certificado X.509 v3 debe tener al menos 2048 bits. Azure rechazará cualquier certificado que no cumpla este requisito o que no sea válido.
>
>

Visual Studio usa el Id. de suscripción junto con los datos del certificado como credenciales. Se hace referencia a las credenciales adecuadas en el archivo de suscripción (archivo .publishsettings), que contiene una clave pública para el certificado. El archivo de suscripción puede contener credenciales para más de una suscripción.

Puede editar la información de suscripción en el cuadro de diálogo **Nueva suscripción/Editar suscripción** , como se explica más adelante en este tema.

Si desea crear un certificado por sí mismo, puede consultar las instrucciones de [Creación y carga de un certificado de administración para Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) y después cargar manualmente el certificado en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Estas credenciales que Visual Studio necesita para administrar sus servicios en la nube no coinciden con las necesarias para autenticar una solicitud en los servicios de almacenamiento de Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importación, configuración o edición de las credenciales de autenticación en Visual Studio
Puede importar, configurar o modificar las credenciales de autenticación en el cuadro de diálogo **Nueva suscripción**, que aparece si se lleva a cabo la acción siguiente:

* En el **Explorador de servidores**, abra el menú contextual para el nodo **Azure**, elija **Administrar y filtrar suscripciones...**, elija la pestaña **Certificados** y realice una de las acciones siguientes:

    * Elija **Importar** para abrir el cuadro de diálogo Importar suscripciones de Microsoft Azure donde puede descargar el archivo de suscripciones para la suscripción cargada actualmente, busque su ubicación de descarga y, después, impórtelo para usarlo en la autenticación.
    * Elija **Nuevo** para abrir el cuadro de diálogo Suscripción nueva, donde puede configurar una nueva suscripción usarla en la autenticación.
    * Elija **Editar** (después de elegir su suscripción activa) para abrir el cuadro de diálogo Editar suscripción, donde puede editar una suscripción existente para usarla en la autenticación. 

En el siguiente procedimiento, se da por supuesto que el cuadro de diálogo **Nueva suscripción** está abierto.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Configuración de las credenciales de autenticación en Visual Studio
1. En la lista **Seleccione un certificado existente para la autenticación** , elija un certificado.
2. Elija el vínculo **Copiar la ruta de acceso completa**. La ruta de acceso del certificado (archivo .cer) se copia en el Portapapeles.

   > [!IMPORTANT]
   > Para publicar la aplicación de Azure desde Visual Studio, debe cargar este certificado en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Para cargar el certificado en Azure Portal:

   1. Abra el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   2. Si se le solicita, inicie sesión en el portal y navegue hasta **Configuración**, **Certificados de administración**.
   3. En el panel Certificados de administración, elija **Cargar**.
   4. Seleccione la suscripción de Azure, pegue la ruta de acceso completa del archivo .cer que acaba de crear y elija **Cargar**.

