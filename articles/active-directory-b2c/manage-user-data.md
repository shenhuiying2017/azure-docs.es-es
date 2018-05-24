---
title: Administración de datos de usuario en Azure AD B2C | Microsoft Docs
description: Obtenga información sobre cómo eliminar o exportar datos de usuario en Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/06/2018
ms.author: davidmu
ms.openlocfilehash: 414221c3e4942801b5792835d520ec936c8c4bbb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="manage-user-data-in-azure-ad-b2c"></a>Administración de datos de usuario en Azure AD B2C

 En este artículo se proporciona información sobre cómo administrar los datos de usuario en Azure Active Directory (AD) B2C mediante las operaciones proporcionadas por la [Graph API de Azure Active Directory](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog). La administración de los datos de usuario incluye la capacidad de eliminar datos o exportar datos de los registros de auditoría.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Eliminación de los datos de usuario

Los datos de usuario se almacenan en el directorio de Azure AD B2C y en los registros de auditoría. Todos los datos de auditoría de usuario se conservan durante el período de retención de datos de 30 días en Azure AD B2C. Si desea eliminar los datos de usuario dentro de esos 30 días, puede usar la operación [Eliminar un usuario](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#DeleteUser). Se requiere una operación DELETE para cada uno de los inquilinos de Azure AD B2C donde puedan residir los datos. 

A cada usuario en Azure AD B2C se le asigna un identificador de objeto. El identificador de objeto proporciona un identificador inequívoco, que puede usar para eliminar datos de usuario en Azure AD B2C.  Según la arquitectura, el identificador de objeto puede ser un identificador de correlación útil a través de otros servicios, como bases de datos de administración de relación con los clientes, finanzas y marketing.  

La manera más precisa para obtener el identificador de objeto para un usuario es hacerlo como parte de un proceso de autenticación con Azure AD B2C.  Si se recibe una solicitud de datos válida de un usuario utilizando otros métodos, puede ser necesario un proceso sin conexión, como una búsqueda por parte de un agente de soporte técnico y servicio al cliente, para encontrar al usuario y anotar el identificador de objeto asociado. 

En el ejemplo siguiente, se muestra un flujo posible de eliminación de datos:

1. El usuario inicia sesión y selecciona **Delete my data** (Eliminar mis datos).
2. La aplicación ofrece una opción para eliminar los datos dentro de una sección de administración de la aplicación.
3. La aplicación fuerza una autenticación en Azure AD B2C. Azure AD B2C devuelve a la aplicación un token con el identificador de objeto del usuario. 
4. La aplicación recibe el token y el identificador de objeto se usa para eliminar los datos de usuario a través de una llamada a Graph API de Azure AD. Graph API de Azure AD elimina los datos de usuario y devuelve un código de estado 200 OK.
5. La aplicación organiza la eliminación de datos de usuario en otros sistemas de la organización según sea necesario con el identificador de objeto u otros identificadores.
6. La aplicación confirma la eliminación de los datos e indica al usuario los pasos siguientes.

## <a name="export-customer-data"></a>Exportación de los datos del cliente

El proceso para exportar los datos del cliente de Azure AD B2C es similar al proceso de eliminación.

Los datos de usuario de Azure AD B2C están limitados a:

- **Datos almacenados en Azure Active Directory**: los datos se pueden recuperar en un recorrido del usuario de autenticación de Azure AD B2C con el identificador de objeto o cualquier nombre de inicio de sesión, como el correo electrónico o el nombre de usuario.  
- **Informe de eventos de auditoría específicos del usuario**: los datos se indizan con el identificador de objeto.

En el siguiente ejemplo de un flujo de datos de exportación, los pasos que se describen como realizados por la aplicación también pueden realizarse mediante un proceso de back-end o mediante un usuario con un rol de administrador en el directorio:

1. El usuario inicia sesión en la aplicación. Azure AD B2C exige la autenticación con autorización multifactor si es necesario.
2. La aplicación usa las credenciales de usuario para llamar a una operación de Graph API de Azure AD para recuperar los atributos del usuario. La Graph API de Azure AD proporciona los datos del atributo en formato JSON. Según el esquema, el contenido del token de identificador puede establecerse para incluir todos los datos personales sobre un usuario.
3. La aplicación recupera la actividad de auditoría del usuario final. Graph API de Azure AD proporciona los datos del evento a la aplicación.
4. La aplicación agrega los datos y los pone a disposición del usuario.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo los usuarios pueden administrar el acceso a la aplicación en [Administrar el acceso de los usuarios](manage-user-access.md)




