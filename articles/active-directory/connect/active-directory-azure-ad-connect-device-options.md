---
title: 'Azure AD Connect: opciones de dispositivo | Microsoft Docs'
description: En este documento se detallan las opciones de dispositivo disponibles en Azure AD Connect
services: active-directory
documentationcenter: ''
author: anandy
manager: samueld
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: anandy
ms.openlocfilehash: d592cbb55643099b6b628a44f182d0a079a6c71e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354530"
---
#<a name="azure-ad-connect-device-options"></a>Azure AD Connect: opciones de dispositivo

La siguiente documentación proporciona información acerca de las distintas opciones de dispositivo disponibles en Azure AD Connect. Azure AD Connect se puede usar para configurar las dos operaciones siguientes: 
* **Combinación de Azure AD híbrido**: si su entorno tiene un uso local de AD y también quiere aprovechar las funcionalidades que proporciona Azure Active Directory, puede implementar dispositivos híbridos unidos a Azure AD. Se trata de dispositivos que están unidos tanto al entorno local de Active Directory como a Azure Active Directory.
* **Escritura diferida de dispositivos**: la escritura diferida de dispositivos se utiliza para habilitar el acceso condicional basado en dispositivos a los dispositivos protegidos de AD FS (2012 R2 o posterior)

## <a name="configure-device-options-in-azure-ad-connect"></a>Configurar opciones de dispositivo en Azure AD Connect

1.  Ejecute Azure AD Connect. En la página **Tareas adicionales**, seleccione **Configurar opciones de dispositivo**.
    ![Configurar opciones de dispositivo](./media/active-directory-aadconnect-device-options/deviceoptions.png) 

    Al hacer clic en Siguiente, se muestra la página **Introducción**, en la que se detallan las operaciones que se pueden realizar.
    ![Información general](./media/active-directory-aadconnect-device-options/deviceoverview.png)

    >[!NOTE]
    > Configurar opciones de dispositivo está disponible únicamente en la versión 1.1.819.0, y en las posteriores.

2.  Después de proporcionar las credenciales de Azure AD, en la página Opciones de dispositivo puede elegir la operación que se va a realizar.
    ![Operaciones de dispositivo](./media/active-directory-aadconnect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de dispositivos híbridos unidos a Azure Active Directory](../device-management-hybrid-azuread-joined-devices-setup.md)
* [Configuración/deshabilitación de escritura diferida de dispositivo](./active-directory-aadconnect-feature-device-writeback.md)

