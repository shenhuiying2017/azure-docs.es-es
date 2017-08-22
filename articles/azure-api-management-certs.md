---
title: Carga de un certificado de Azure Management API | Microsoft Docs
description: "Aprenda a cargar el certificado de Management API para el Portal de Azure clásico."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.translationtype: HT
ms.sourcegitcommit: 99523f27fe43f07081bd43f5d563e554bda4426f
ms.openlocfilehash: 9dc438e927acd9aef38f06807fabf3dda9b021c9
ms.contentlocale: es-es
ms.lasthandoff: 08/05/2017

---
# <a name="upload-an-azure-management-api-management-certificate"></a>Carga de un certificado de administración de Azure Management API
Los certificados de administración le permiten autenticar con el modelo de implementación clásica que proporciona Azure. Muchos programas y herramientas (como Visual Studio o Azure SDK) utilizan estos certificados para automatizar la configuración y la implementación de diferentes servicios de Azure. 

> [!WARNING]
> Por lo tanto, tenga cuidado. Estos tipos de certificados permiten a quien se autentica con ellos administrar la suscripción a la que están asociados.
>
>

Si deseara más información acerca de los certificados de Azure (incluido cómo crear un certificado autofirmado), consulte [Introducción a los certificados para Azure Cloud Services](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

También puede usar [Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) para autenticar el código de cliente para fines de automatización.

## <a name="upload-a-management-certificate"></a>Carga de un certificado de administración
Una vez que tenga creado un certificado de administración, (archivo .cer con solo la clave pública) puede cargarlo en el portal. Cuando el certificado esté disponible en el portal, cualquiera que tenga un certificado que coincida (clave privada) puede conectarse a través de Management API y obtener acceso a los recursos de la suscripción asociada.

1. Inicie sesión en [Azure Portal](http://portal.azure.com).
2. Haga clic en **Más servicios** en la lista de servicios de Azure en la parte inferior y, a continuación, seleccione **Suscripciones** en el grupo de servicios _General_.

    ![Menú Suscripción](./media/azure-api-management-certs/subscriptions_menu.png)

3. Asegúrese de seleccionar la suscripción correcta a la que desee asociar el certificado.     
4. Después de haber seleccionado la suscripción correcta, presione **Certificados de administración** en el grupo _Configuración_.

    ![Settings](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Presione el botón **Cargar** .

    ![Cargar la página Certificados](./media/azure-api-management-certs/certificates_page.png)
6. Rellene la información del cuadro de diálogo y haga clic en **Cargar**.

    ![Settings](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Pasos siguientes
Ahora que tiene un certificado de administración asociado a una suscripción, puede conectarse mediante programación (después de haber instalado localmente el certificado correspondiente) a la [API de REST del modelo de implementación clásica](https://msdn.microsoft.com/library/azure/mt420159.aspx) y automatizar los distintos recursos de Azure que también están asociados a esa suscripción.

