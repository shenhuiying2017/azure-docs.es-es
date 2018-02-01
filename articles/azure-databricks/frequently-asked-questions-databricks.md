---
title: 'Azure Databricks: Preguntas comunes y ayuda | Microsoft Docs'
description: "Obtenga respuestas a las preguntas comunes e información para solucionar problemas acerca de Azure Databricks."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: nitinme
ms.openlocfilehash: b56253e7b9c9ab144ebc4006511631756de4f89b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Preguntas más frecuentes sobre Azure Databricks

Este artículo muestra las principales consultas que pueden surgir en relación con Azure Databricks. También se muestran algunos problemas comunes que puede encontrar al usar Databricks. Para más información, consulte [¿Qué es Azure Databricks?](what-is-azure-databricks.md) 

## <a name="can-i-use-my-own-keys-for-local-encryption"></a>¿Puedo usar mis propias claves para el cifrado local? 
En la versión actual, no se admite el uso de sus propias claves de Azure Key Vault. 

## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>¿Puedo usar redes virtuales de Azure con Databricks?
Se crea una red virtual como parte del aprovisionamiento de Databricks. En esta versión, no puede usar su propia red virtual de Azure.

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>¿Cómo accedo a Azure Data Lake Store desde un bloc de notas? 

Siga estos pasos:
1. En Azure Active Directory (Azure AD), aprovisione una entidad de servicio y registre su clave.
2. Asigne los permisos necesarios a la entidad de servicio en Data Lake Store.
3. Para acceder a un archivo en Data Lake Store, use las credenciales de la entidad de servicio en el Bloc de notas.

Para más información, vea [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store) (Uso de Data Lake Store con Azure Databricks).

## <a name="fix-common-problems"></a>Corrección de problemas comunes

Estos son algunos de los problemas que podría encontrar con Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks"

#### <a name="error-message"></a>Mensaje de error

"Esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones. (Código: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Solución

1. Vaya a [Azure Portal](https://portal.azure.com).
2. Seleccione **Suscripciones**, la suscripción que usa y, a continuación, **Proveedores de recursos**. 
3. En la lista de proveedores de recursos, en **Microsoft.Databricks**, seleccione **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: La cuenta {correo electrónico} no tiene el rol Propietario o Colaborador en el recurso de área de trabajo de Databricks en Azure Portal

#### <a name="error-message"></a>Mensaje de error

"La cuenta {correo electrónico} no tiene el rol Propietario o Colaborador en el recurso de área de trabajo de Databricks en Azure Portal. Este error puede producirse también si es un usuario invitado en el inquilino. Pida al administrador que le conceda acceso o le agregue como un usuario directamente en el área de trabajo de Databricks". 

#### <a name="solution"></a>Solución

A continuación se muestra un par de soluciones para este problema:

* Para inicializar el inquilino, debe haber iniciado sesión como un usuario normal del inquilino, no como un usuario invitado. También debe tener el rol colaborador en el recurso de área de trabajo de Databricks. Puede conceder acceso de usuario desde la pestaña **Control de acceso (IAM)** en el área de trabajo de Databricks en Azure Portal.

* Este error también puede producirse si el nombre de dominio de correo electrónico se asigna a varios directorios de Azure AD. Para solucionar este problema, cree un nuevo usuario en el directorio que contiene la suscripción al área de trabajo de Databricks.

    a. En Azure Portal, vaya a Azure AD. Seleccione **Usuarios y grupos** > **Agregar un usuario**.

    b. Agregue un usuario con un correo electrónico de `@<tenant_name>.onmicrosoft.com` en lugar de `@<your_domain>`. Puede encontrar esta opción en **Dominios personalizados**, en Azure AD de Azure Portal.
    
    c. Conceda a este nuevo usuario el rol **Colaborador** en el recurso del área de trabajo de Databricks.
    
    d. Inicie sesión en Azure Portal con el nuevo usuario y busque el área de trabajo de Databricks.
    
    e. Inicie el área de trabajo de Databricks con este usuario.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: La cuenta {correo electrónico} no se ha registrado en Databricks 

#### <a name="solution"></a>Solución

Si no creó el área de trabajo y se le agrega como usuario, póngase en contacto con la persona que creó el área de trabajo. Pídale a esa persona que lo agregue mediante la Consola de administración de Azure Databricks. Para obtener instrucciones, vea [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Adición y administración de usuarios). Si creó el área de trabajo y sigue recibiendo este error, intente volver a seleccionar **Inicializar área de trabajo** en Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problema: Error de inicio del proveedor de nube al configurar el clúster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Mensaje de error

"Error de inicio de proveedor en la nube: Se detectó un error de proveedor en la nube al configurar el clúster. Para más información, vea la guía de Databricks. Código de error de Azure: PublicIPCountLimitReached. Mensaje de error de Azure: No se pueden crear más de 60 direcciones IP públicas para esta suscripción en esta región".

#### <a name="solution"></a>Solución

Los clústeres de Databricks usan una dirección IP pública por nodo. Si la suscripción ya ha utilizado todas sus direcciones IP públicas, debe [solicitar aumentar la cuota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Elija **Cuota** como el **Tipo de problema** y **Redes: ARM** como el **Tipo de cuota**. En **Details**, solicite un aumento de la cuota de la dirección IP pública. Por ejemplo, si el límite es actualmente 60 y desea crear un clúster de 100 nodos, solicite un aumento del límite hasta 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problema: Un segundo tipo de error al iniciar el proveedor de nube al configurar el clúster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Mensaje de error

"Error de inicio de proveedor en la nube: Se detectó un error de proveedor en la nube al configurar el clúster. Para más información, vea la guía de Databricks.
Código de error de Azure: mensaje de error de Azure MissingSubscriptionRegistration: La suscripción no está registrada para usar el espacio de nombres "Microsoft.Compute". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones".

#### <a name="solution"></a>Solución

1. Vaya a [Azure Portal](https://portal.azure.com).
2. Seleccione **Suscripciones**, la suscripción que usa y, a continuación, **Proveedores de recursos**. 
3. En la lista de proveedores de recursos, en **Microsoft.Compute**, seleccione **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.

Para instrucciones más detalladas, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problema: Azure Databricks necesita permisos para acceder a recursos de su organización que solo un administrador puede conceder

#### <a name="background"></a>Fondo

Azure Databricks está integrado con Azure AD. Esto le permite establecer permisos dentro de Azure Databricks (por ejemplo, en equipos portátiles o clústeres) mediante la especificación de los usuarios de Azure AD. Para que Azure Databricks pueda generar una lista de los nombres de los usuarios de Azure AD, necesita permisos de lectura de dicha información. Para ello, se requiere una autorización. Si la autorización no está disponible aún, aparecerá el error.

#### <a name="solution"></a>Solución

Inicie sesión en Azure Portal como administrador global. En Azure Active Directory, vaya a la pestaña **Configuración de usuario** y asegúrese de que la opción **Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre** está establecida en **Sí**.

## <a name="next-steps"></a>pasos siguientes

- [Quickstart: Get started with Azure Databricks](quickstart-create-databricks-workspace-portal.md) (Inicio rápido: Introducción a Azure Databricks)
- [¿Qué es Azure Databricks?](what-is-azure-databricks.md)

