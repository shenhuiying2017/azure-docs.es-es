---
title: "Instalación de puerta de enlace de datos local | Microsoft Docs"
description: Aprenda a instalar y configurar una puerta de enlace de datos local.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/30/2017
ms.author: owend
ms.openlocfilehash: 9ba36c4cf932cab2e449b12a307639184feab049
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2017
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalación y configuración de una puerta de enlace de datos local
Se requiere una puerta de enlace de datos local cuando uno o varios servidores de Azure Analysis Services de la misma región se conectan a orígenes de datos locales. Para más información acerca de la puerta de enlace, consulte [Puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="prerequisites"></a>Requisitos previos
**Requisitos mínimos:**

* .NET Framework 4.5
* versión de 64 bits de Windows 7 o Windows Server 2008 R2 (o posterior)

**Se recomienda que use:**

* CPU de 8 núcleos
* 8 GB de memoria
* versión de 64 bits de Windows 2012 R2 (o posterior)

**Consideraciones importantes:**

* Durante la instalación, al registrar la puerta de enlace en Azure, se selecciona la región predeterminado para su suscripción. Puede elegir otra región. Si tiene servidores en varias regiones, debe instalar una puerta de enlace en cada una de ellas. 
* La puerta de enlace no se puede instalar en un controlador de dominio.
* Solo se puede instalar una puerta de enlace en un equipo.
* Instale la puerta de enlace en un equipo que permanezca encendido y no entre en suspensión.
* No instale la puerta de enlace en un equipo conectado de forma inalámbrica a la red. Puede disminuir el rendimiento.
* Inicie sesión en Azure con una cuenta de Azure AD que tenga el mismo [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) que la suscripción donde va a registrar la puerta de enlace. No se pueden utilizar cuentas B2B (invitadas) de Azure para instalar y registrar una puerta de enlace.


## <a name="download"></a>Descarga
 [Descargar la puerta de enlace](https://aka.ms/azureasgateway)

## <a name="install"></a>Instalación

1. Ejecute la configuración.

2. Seleccione una ubicación, acepte los términos y haga clic en **Instalar**.

   ![Ubicación de instalación y términos de la licencia](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Inicie sesión en Azure. La cuenta debe estar en la instancia de Azure Active Directory de su inquilino. Dicha cuenta se usa para el administrador de la puerta de enlace. No se pueden utilizar cuentas B2B (invitadas) de Azure para instalar y registrar la puerta de enlace.

   ![Inicio de sesión en Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Si inicia sesión con una cuenta de dominio, se asignará a la cuenta profesional de Azure AD. La cuenta profesional se usará como administrador de la puerta de enlace.

## <a name="register"></a>Registro
Para crear un recurso de puerta de enlace en Azure, debe registrar la instancia local que instaló con el servicio en la nube de la puerta de enlace. 

1.  Seleccione **Registrar una nueva puerta de enlace en este equipo**.

    ![Registro](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Escriba el nombre y la clave de recuperación de la puerta de enlace. De forma predeterminada, la puerta de enlace usa la región predeterminada de la suscripción. Si tiene que seleccionar otra región diferente, elija **Cambiar la región**.

    > [!IMPORTANT]
    > Guarde la clave de recuperación en un lugar seguro. La clave de recuperación es necesaria para adquirir, migrar o restaurar una puerta de enlace. 

   ![Registro](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Creación de un recurso de puerta de enlace de Azure
Una vez que ha instalado y registrado la puerta de enlace, debe crear un recurso de puerta de enlace en su suscripción de Azure. Inicie sesión en Azure con la misma cuenta que usó al registrar la puerta de enlace.

1. En Azure Portal, haga clic en **Crear un nuevo servicio** > **Enterprise Integration** > **Puerta de enlace de datos local** > **Crear**.

   ![Creación de un recurso de puerta de enlace](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. En **Crear puerta de enlace de conexión**, escriba estos valores:

    * **Nombre**: escriba un nombre para el recurso de puerta de enlace. 

    * **Suscripción**: seleccione la suscripción de Azure que se asociará al recurso de puerta de enlace. 
   
      La suscripción predeterminada se basa en la cuenta de Azure que usó para iniciar sesión.

    * **Grupo de recursos**: cree un grupo de recursos o seleccione uno existente.

    * **Ubicación**: seleccione la región en que se ha registrado la puerta de enlace.

    * **Nombre de instalación**: si la instalación de la puerta de enlace no está seleccionada, seleccione la puerta de enlace registrada. 

    Cuando haya terminado, haga clic en **Crear**.

## <a name="connect-servers"></a>Conexión de servidores al recursos de la puerta de enlace

1. En la introducción al servidor de Azure Analysis Services, haga clic en **Puerta de enlace de datos local**.

   ![Conectar un servidor a una puerta de enlace](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. En **Seleccione una puerta de enlace de datos local que conectar**, seleccione el recurso de puerta de enlace y, después, haga clic en **Conectar la puerta de enlace seleccionada**.

   ![Conectar un servidor a un recurso de puerta de enlace](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Si la puerta de enlace no aparece en la lista, es probable que el servidor no esté en la región que especificó al registrarla. 

Eso es todo. Si necesita abrir puertos o solucionar cualquier problema, asegúrese de consultar [Puerta de enlace de datos local](analysis-services-gateway.md).

## <a name="next-steps"></a>Pasos siguientes
* [Administración de Analysis Services](analysis-services-manage.md)   
* [Obtención de datos de Azure Analysis Services](analysis-services-connect.md)
