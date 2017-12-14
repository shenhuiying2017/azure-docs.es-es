---
title: 'Azure Active Directory B2C: Disponibilidad en regiones y residencia de datos | Microsoft Docs'
description: Un tema sobre los tipos de inquilinos de Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: mtillman
editor: bryanla
ms.assetid: 8a0644da-b825-4edc-8ce9-541c3c976afb
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2017
ms.author: gsacavdm
ms.openlocfilehash: 752a98ca7f3c77c434de296461790f2cf37e2d5c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Disponibilidad en regiones y residencia de datos
La disponibilidad en regiones y residencia de datos son dos conceptos muy diferentes que se aplican de forma diferente a Azure AD B2C del resto de Azure. En este artículo se explican las diferencias entre estos dos conceptos y se compara cómo se aplican a Azure frente a Azure AD B2C.

## <a name="summary"></a>Resumen
Azure AD B2C suele estar **disponible en todo el mundo** con la opción de **residencia de datos en Estados Unidos o en Europa**.

## <a name="concepts"></a>Conceptos
* **Disponibilidad en regiones**  se refiere a que un servicio esté disponible para su uso.
* **Residencia de datos** hace referencia a dónde se almacenan los datos de usuario.

## <a name="region-availability"></a>Disponibilidad en regiones
Azure AD B2C está disponible en todo el mundo a través de la nube pública de Azure. 

Esto difiere del modelo de la mayoría de los demás servicios de Azure que aúnan la disponibilidad con la residencia de datos. Puede ver ejemplos de esto en la página [Productos disponibles por región](https://azure.microsoft.com/regions/services/) de Azure y en la [calculadora de precios de Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Residencia de datos
Azure AD B2C almacena los datos de usuarios en Estados Unidos o Europa.

La residencia de datos se determina en función del país o región seleccionado al [crear un inquilino de Azure AD B2C](active-directory-b2c-get-started.md).

![Captura de pantalla de un inquilino de versión preliminar](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Los datos residen en Estados Unidos para los países o regiones siguientes:

> Estados Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Puerto Rico y Trinidad y Tobago

Los datos residen en Europa para los países o regiones siguientes:

> Alemania, Arabia Saudí, Argelia, Austria, Azerbaiyán, Bahréin, Bélgica, Bielorrusia, Bulgaria, Croacia, Chipre, Dinamarca, Egipto, Emiratos Árabes Unidos Eslovaquia, Eslovenia, España, Estonia, Finlandia, Francia, Grecia, Hungría, Irlanda, Islandia, Israel, Italia, Jordania, Kazajistán, Kenia, Kuwait, Letonia, Líbano, Liechtenstein, Lituania, Luxemburgo, Macedonia, Malta, Marruecos, Montenegro, Nigeria, Noruega, Omán, Países Bajos, Pakistán, Polonia, Portugal, Qatar, Reino Unido, República Checa, Rumanía, Rusia, Serbia, Sudáfrica, Suecia, Suiza, Túnez, Turquía y Ucrania.

Los países o regiones restantes están a punto de agregarse a la lista.  De momento, todavía puede usar Azure AD B2C escogiendo cualquiera de los países o regiones anteriores.

> Afganistán, Argentina, Australia, Brasil, Chile, Colombia, Corea, Ecuador, Filipinas, India, Indonesia, Iraq, Japón, Malasia, Nueva Zelanda, Paraguay, Perú, RAE de Hong Kong, Singapur, Sri Lanka, Taiwán, Tailandia, Uruguay y Venezuela.

## <a name="preview-tenant"></a>Inquilino de versión preliminar
Si había creado un inquilino de B2C durante el período de versión preliminar de Azure AD B2C, es probable que en **Tipo de inquilino** aparezca **Inquilino de vista previa**. En ese caso, al inquilino se DEBE usar solo para fines de prueba y desarrollo, NO para aplicaciones de producción.

> [!IMPORTANT]
> No hay ninguna ruta de migración de un inquilino de B2C de versión preliminar a un inquilino de B2C de escala de producción. Tenga en cuenta que existen problemas conocidos al eliminar un inquilino de B2C preliminar y volver a crear un inquilino B2C a escala de producción con el mismo nombre de dominio. Es necesario crear un inquilino de B2C a escala de producción con otro nombre de dominio.


![Captura de pantalla de un inquilino de versión preliminar](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
