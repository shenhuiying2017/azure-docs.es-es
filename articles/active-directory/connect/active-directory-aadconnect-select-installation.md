---
title: "Azure AD Connect: selección del tipo de instalación | Microsoft Docs"
description: "En este tema se explica cómo seleccionar el tipo de instalación que se usará para Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: a5697686bd1f41d581554b27ce78897963e38c74
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="select-which-installation-type-to-use-for-azure-ad-connect"></a>Selección del tipo de instalación que se usará para Azure AD Connect
Azure AD Connect tiene dos tipos de instalación para la nueva instalación: rápida y personalizada. Este tema le ayudará a decidir qué opción utilizar durante la instalación.

## <a name="express"></a>Express
La opción rápida es la más común y la utiliza el 90 % de todas las instalaciones nuevas. Se diseñó para proporcionar una configuración que sirva para los escenarios más comunes de clientes.

Se da por supuesto que:

- Tiene un único bosque de Active Directory local.
- Tiene una cuenta de administrador de organización que usa para la instalación.
- Tiene menos de 100 000 objetos en Active Directory local.

Obtendrá:

- [Sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md) desde entornos locales en Azure AD para inicios de sesión únicos.
- Una configuración que sincroniza a los [usuarios, grupos, contactos y equipos de Windows 10](active-directory-aadconnectsync-understanding-default-configuration.md).
- Sincronización de todos los objetos válidos en todos los dominios y todas las unidades organizativas.
- La [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) está habilitada para asegurarse de que se utilice siempre la última versión disponible.

Opciones en las que puede continuar utilizando la instalación rápida:

- Si no desea sincronizar todas las unidades organizativas, puede usar la instalación rápida y, en la última página, anule la selección de **Inicie el proceso de sincronización...***. Después, vuelva a ejecutar el Asistente para instalación y cambie las unidades organizativas en [Opciones de configuración](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options) y habilite la sincronización programada.
- Desea habilitar una de las características de Azure AD Premium, como la Escritura diferida de contraseñas. Utilice primero la instalación rápida para completar la instalación inicial. Después, vuelva a ejecutar el Asistente para instalación y cambie las [opciones de configuración](active-directory-aadconnectsync-installation-wizard.md#customize-synchronization-options).

## <a name="custom"></a>Personalizado
La ruta de acceso personalizada admite muchas más opciones que la instalación rápida. Se debe usar en todos los casos donde la configuración descrita en la sección anterior para la instalación rápida no es representativa para su organización.

Se debe utilizar si:

- No tiene acceso a una cuenta de administrador de empresa en Active Directory.
- Tiene más de un bosque o pretende sincronizar más de un bosque en el futuro.
- Tiene dominios en el bosque a los que no se puede acceder desde el servidor de Connect.
- Planea utilizar la federación o la autenticación de paso a través para el inicio de sesión de usuario.
- Tiene más de 100 000 objetos y necesita utilizar una versión completa de SQL Server.
- Planea usar el filtrado basado en grupos y no solo el filtrado basado en dominios o en unidades organizativas.

## <a name="upgrade-from-dirsync"></a>Actualización desde DirSync
Si actualmente utiliza DirSync, siga los pasos descritos en [Actualización desde DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) para actualizar la configuración existente. Hay dos opciones de actualización distintas:

- Actualización local para instalar Connect en el mismo servidor.
- Implementación paralela para instalar Connect en un servidor nuevo mientras el servidor existente de DirSync continúa operativo.

## <a name="upgrade-from-azure-ad-sync"></a>Actualización desde Sincronización de Azure AD
Si actualmente utiliza Sincronización de Azure AD, puede seguir los [mismos pasos](active-directory-aadconnect-upgrade-previous-version.md) que cuando se actualiza desde una versión de Connect a otra más reciente. Hay dos opciones de actualización distintas:

- Actualización local para instalar Connect en el mismo servidor.
- Migración oscilante para instalar Connect en un servidor nuevo mientras el servidor existente de Sincronización de Azure AD continúa operativo.

## <a name="migrate-from-fim2010-or-mim2016"></a>Migración desde FIM2010 o MIM2016
Si actualmente utiliza Forefront Identity Manager 2010 o Microsoft Identity Manager 2016 con el conector de Azure AD, la única opción es una migración. Siga los pasos descritos en [migración oscilante](active-directory-aadconnect-upgrade-previous-version.md#swing-migration). En los pasos, reemplace cualquier mención de Sincronización de Azure AD con FIM2010/MIM2016.

## <a name="next-steps"></a>Pasos siguientes
Dependiendo de la opción seleccionada, use la tabla de contenido a la izquierda para encontrar su artículo con los pasos detallados.
