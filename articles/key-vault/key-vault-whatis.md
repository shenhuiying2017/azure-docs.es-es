---
title: "¿Qué es el Almacén de claves de Azure? | Microsoft Docs"
description: "El Almacén de claves de Azure ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Almacén de claves de Azure, los clientes pueden cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través de claves que están protegidas por módulos de seguridad de hardware (HSM)."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/16/2017
ms.author: barclayn
ms.openlocfilehash: f4981996a92abb3ecb4b6b92add8f1a0c7b1cbef
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="what-is-azure-key-vault"></a>¿Qué es el Almacén de claves de Azure?
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción
El Almacén de claves de Azure ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Key Vault, puede cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través del uso de claves que están protegidas por módulos de seguridad de hardware (HSM). Para tener mayor seguridad, puede importar o generar las claves en HSM. Si decide hacerlo, Microsoft procesa las claves en los HSM validados de FIPS 140-2 nivel 2 (hardware y firmware).  

Almacén de claves agiliza el proceso de administración de claves y le permite mantener el control de claves que obtienen acceso a sus datos y los cifran. Los desarrolladores pueden crear claves para desarrollo y prueba en minutos y, a continuación, migrarlas sin problemas a claves de producción. Los administradores de seguridad pueden conceder (y revocar) permisos a las claves según sea necesario.

Utilice la tabla siguiente para comprender mejor cómo Almacén de claves puede ayudar a satisfacer las necesidades de los programadores y de los administradores de seguridad.

| Rol | Declaración del problema | Resuelto por Almacén de claves de Azure |
| --- | --- | --- |
| Desarrollador para una aplicación de Azure |“Quiero escribir una aplicación para Azure que use claves para la firma y el cifrado, pero quiero que dichas claves no estén en la propia aplicación, con el fin de que la solución sea adecuada para una aplicación que se distribuya geográficamente. <br/><br/>También quiero que tanto las claves como los secretos estén protegidos sin tener que escribir el código. Por último, quiero poder usar fácilmente las claves y los secretos desde mis aplicaciones, y que el rendimiento sea óptimo". |√ Las claves se almacenan en un almacén y las invoca un identificador URI cuando se necesitan.<br/><br/> √ Las claves se protegen mediante Azure, para lo que se usan algoritmos estándar del sector, longitudes de clave y módulos de seguridad de hardware (HSM).<br/><br/> √ Las claves se procesan en los HSM que residen en los mismos centros de datos de Azure que la aplicaciones. Esto proporciona mayor confiabilidad y menor latencia que si las claves se encuentran en una ubicación independiente, como por ejemplo si son locales. |
| Desarrollador para software como servicio (SaaS): |“No quiero asumir la responsabilidad, ni tampoco la posible responsabilidad, de las claves y los secretos de inquilino de mis clientes. <br/><br/>Quiero que los clientes posean y administren sus claves de modo que pueda concentrarse en hacer lo que hago mejor, que es proporcionar las características de software principales.” |√ Los clientes pueden importar sus propias claves a Azure y administrarlas. Cuando una aplicación SaaS necesita realizar operaciones criptográficas mediante las claves de sus clientes, Key Vault las hace en nombre de la aplicación. La aplicación no ve las claves de los clientes. |
| Responsable principal de la seguridad (CSO) |“Quiero saber que nuestras aplicaciones cumplen con HSM FIPS 140-2 de nivel 2 para administración de claves segura. <br/><br/>Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de las mismas. <br/><br/>Y aunque usamos varios servicios y recursos de Azure, quiero administrar las claves desde una ubicación única en Azure.” |√ Los HSM tienen la validación FIPS 140-2 de nivel 2.<br/><br/>√ Key Vault está diseñado de modo que Microsoft no pueda ver ni extraer sus claves.<br/><br/>√ Registro del uso de claves casi en tiempo real.<br/><br/>√ El almacén proporciona una sola interfaz, independientemente del número de almacenes que tenga en Azure, las regiones que admitan y las aplicaciones que los usen. |

Cualquiera con una suscripción a Azure puede crear y usar instancias de Key Vault. Aunque Key Vault beneficia a los desarrolladores y los administradores de seguridad, el administrador de una organización que administra otros servicios de Azure, podría implementarlo y administrarlo. Por ejemplo, dicho administrador iniciaría sesión con una suscripción de Azure, crearía un almacén para la organización en el que almacenaría las claves y, después, asumiría la responsabilidad de las tareas operativas, como:

* Crear o importar una clave o un secreto
* Revocar o eliminar una clave o un secreto
* Autorizar usuarios o aplicaciones para acceder al almacén de claves para que puedan administrar o usar sus claves y secretos
* Configurar el uso de claves (por ejemplo, para firmar o cifrar)
* Supervisar el uso de claves

Este administrador podría ofrecer después a los desarrolladores los URI para llamar desde sus aplicaciones y proporcionar a su administrador de seguridad la información de registro de uso de claves. 

   ![Información general del Almacén de claves de Azure][1]

Los desarrolladores también pueden administrar las claves directamente mediante API. Para más información, consulte la [guía para desarrolladores del Almacén de claves](key-vault-developers-guide.md).

## <a name="next-steps"></a>Pasos siguientes
Para consultar un tutorial de introducción para que un administrador, vea [Introducción al Almacén de claves de Azure](key-vault-get-started.md).

Para obtener más información acerca del registro de uso para el Almacén de claves, consulte [Registro del Almacén de claves de Azure](key-vault-logging.md).

Para más información acerca del uso de claves y secretos con Azure Key Vault, Consulte [About Keys, Secrets, and Certificates](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx) (Acerca de claves, secretos y certificados).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
