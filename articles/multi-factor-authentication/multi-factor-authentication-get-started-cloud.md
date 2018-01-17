---
title: "Introducción a Azure MFA en la nube | Microsoft Docs"
description: "En esta página de Microsoft Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA en la nube."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
ms.openlocfilehash: b7c82dc6f09b147651d999a831c657864fa59134
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Introducción a Azure Multi-Factor Authentication en la nube
En este artículo aprenderá a usar Azure Multi-Factor Authentication en la nube.

> [!NOTE]
> La siguiente documentación proporciona información sobre cómo habilitar usuarios mediante **Azure Portal**. Si busca información sobre cómo configurar Azure Multi-Factor Authentication para usuarios de Office 365, consulte [Configurar la autenticación multifactor para usuarios de Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US).

![MFA en la nube](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>Requisito previo
[Registrarse para obtener una suscripción a Azure](https://azure.microsoft.com/pricing/free-trial/) : si todavía no dispone de una suscripción de Azure, es necesario que se registre para obtener una. Si simplemente está comenzando a utilizar Azure MFA, puede usar una suscripción de prueba.

## <a name="enable-azure-multi-factor-authentication"></a>Habilitación de Azure Multi-Factor Authentication
Siempre y cuando los usuarios tengan licencias que incluyen Azure Multi-Factor Authentication, no hay nada que se pueda hacer para activar Azure MFA. Puede iniciar la solicitud de la verificación en dos pasos en cada usuario individual. Las licencias que habilitan Azure MFA son:
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Si no dispone una de estas tres licencias o no tiene suficientes licencias para cubrir a todos los usuarios, es correcto también. Solo tiene que realizar un paso adicional y [crear un proveedor de Muti-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) en el directorio.

## <a name="turn-on-two-step-verification-for-users"></a>Activación de la verificación en dos pasos para los usuarios

Utilice uno de los procedimientos enumerados en el artículo sobre la [exigencia de verificación en dos pasos para un usuario o un grupo](multi-factor-authentication-get-started-user-states.md) para empezar a usar Azure MFA. Puede elegir exigir la verificación en dos pasos para todos los inicios de sesión o crear directivas de acceso condicional para exigir la verificación en dos pasos únicamente cuando usted lo necesite.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha configurado Azure Multi-Factor Authentication en la nube, puede configurar la implementación. Consulte [Configuración de Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) para conocer más detalles.

