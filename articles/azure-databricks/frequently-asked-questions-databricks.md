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
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Versión preliminar de Azure Databricks: Preguntas comunes y ayuda

Este artículo muestra las principales consultas que pueden surgir en relación con Azure Databricks. También se enumeran algunos problemas comunes que pueden surgir al usar Azure Databricks. Para obtener más información sobre Azure Databricks, vea [¿Qué es Azure Databricks?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Preguntas frecuentes

En esta sección se enumeran las preguntas comunes relacionadas con Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>¿Puedo usar mis propias claves para el cifrado local? 
En la versión actual, no se admite el uso de sus propias claves de Azure Keyvault. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>¿Puedo usar redes virtuales de Azure con Azure Databricks?
Se crea una red virtual nueva como parte del aprovisionamiento de Azure Databricks. Como parte de esta versión, no puede usar su propia red virtual de Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>¿Cómo accedo a Azure Data Lake Store desde un bloc de notas? 

1. En Azure Active Directory, aprovisione una entidad de servicio y registre su clave.
2. Asigne los permisos necesarios a la entidad de servicio en Azure Data Lake Store.
3. Para acceder a un archivo en Azure Data Lake Store, use las credenciales de la entidad de servicio en el bloc de notas.

Para más información, vea [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store) (Uso de Data Lake Store con Azure Databricks).

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se describe cómo solucionar problemas comunes con Azure Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks"

**Mensaje de error**

Esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones. (Código: MissingSubscriptionRegistration)

**Solución**

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Suscripciones**, en la suscripción que usa y, a continuación, en **Proveedores de recursos**. 
3. En la lista de proveedores de recursos, en **Microsoft.Databricks**, haga clic en **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: la cuenta {correo electrónico} no tiene el rol Propietario o Colaborador en el recurso de área de trabajo de Databricks en Azure Portal.

**Mensaje de error**

Your account {email} does not have Owner or Contributor role on the Databricks workspace resource in the Azure portal (La cuenta {correo electrónico} no tiene el rol Propietario o Colaborador en el recurso de área de trabajo de Databricks en Azure Portal). Este error puede producirse también si es un usuario invitado en el inquilino. Pida al administrador que le conceda acceso o le agregue como un usuario A directamente en el área de trabajo de Databricks. 

**Solución**

A continuación se muestran un par de soluciones para este problema:

* Para inicializar el inquilino, debe haber iniciado sesión como un usuario normal del inquilino, no como un usuario invitado. También debe tener el rol de colaborador en el recurso de área de trabajo de Databricks. Puede conceder acceso de usuario desde la pestaña **Control de acceso (IAM)** en el área de trabajo de Azure Databricks en Azure Portal.

* Este error puede producirse también si el nombre de dominio de correo electrónico se asigna a varios directorios de Active Directory. Para solucionar este problema, cree un nuevo usuario en Active Directory que contenga la suscripción al área de trabajo de Databricks.

    a. En Azure Portal, vaya a Azure Active Directory, haga clic en **Usuarios y grupos** y haga clic en **Agregar un usuario**.

    b. Agregue un usuario con un correo electrónico de `@<tenant_name>.onmicrosoft.com` en lugar de @<su_dominio>. Puede encontrar el elemento <tenant_name>.onmicrosoft.com asociado a su instancia de Active Directory en **Dominios personalizados** bajo Azure Active Directory en Azure Portal.
    
    c. Conceda a este nuevo usuario el rol **Colaborador** en el recurso del área de trabajo de Databricks.
    
    d. Inicie sesión en Azure Portal con el nuevo usuario y busque el área de trabajo de Databricks.
    
    e. Inicie el área de trabajo de Databricks con este usuario.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: La cuenta {correo electrónico} no se ha registrado en Databricks 

**Solución**

Si no ha creado el área de trabajo y se le agrega como usuario del área de trabajo, póngase en contacto con la persona que creó el área de trabajo para agregarlo mediante la consola de administración de Azure Databricks. Para obtener instrucciones, vea [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Adición y administración de usuarios). Si creó el área de trabajo y sigue apareciendo este error, intente hacer clic en "Inicializar área de trabajo" de nuevo desde Azure Portal.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: error de inicio del proveedor de nube (PublicIPCountLimitReached): Se detectó un error de proveedor de nube al configurar el clúster

**Mensaje de error**

Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster (Error de inicio de proveedor en la nube: Se detectó un error de proveedor en la nube al configurar el clúster). Vea la guía de Databricks para obtener más información. Código de error de Azure: PublicIPCountLimitReached. Mensaje de error de Azure: Cannot create more than 60 public IP addresses for this subscription in this region (No se pueden crear más de 60 direcciones IP públicas para esta suscripción en esta región).

**Solución**

Los clústeres de Azure Databricks usan una dirección IP pública por nodo. Si la suscripción ya ha utilizado todas sus direcciones IP públicas, debe [solicitar aumentar la cuota](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Elija **Cuota** como **Tipo de problema**, **Red: ARM** como **Tipo de cuota** y solicite un aumento de cuota de direcciones IP públicas en **Detalles**. Por ejemplo, si el límite es actualmente 60 y desea crear un clúster de 100 nodos, solicite un aumento del límite hasta 160.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: error de inicio de proveedor de nube (MissingSubscriptionRegistration): Se detectó un error de proveedor de nube al configurar el clúster

**Mensaje de error**

Cloud Provider Launch Failure: A cloud provider error was encountered while setting up the cluster (Error de inicio de proveedor en la nube: Se detectó un error de proveedor en la nube al configurar el clúster). Vea la guía de Databricks para obtener más información.
Código de error de Azure: mensaje de error de Azure MissingSubscriptionRegistration: La suscripción no está registrada para usar el espacio de nombres "Microsoft.Compute". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones.

**Solución**

1. Vaya al [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Suscripciones**, en la suscripción que usa y, a continuación, en **Proveedores de recursos**. 
3. En la lista de proveedores de recursos, en **Microsoft.Compute**, haga clic en **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.

Consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md) para instrucciones más detalladas.

## <a name="next-steps"></a>Pasos siguientes
Para obtener instrucciones paso a paso para crear una factoría de datos de la versión 2, consulte los siguientes tutoriales:

- [Quickstart: Get started with Azure Databricks](quickstart-create-databricks-workspace-portal.md) (Inicio rápido: Introducción a Azure Databricks)
- [¿Qué es Azure Databricks?](what-is-azure-databricks.md)

