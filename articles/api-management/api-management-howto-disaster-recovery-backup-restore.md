---
title: "Implementación de la recuperación ante desastres mediante copias de seguridad y restauración en Azure API Management | Microsoft Docs"
description: "Obtenga información acerca de cómo usar las tareas de copias de seguridad y restauración para llevar a cabo la recuperación ante desastres en Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 3fcd2fc4162cfbf549be979e15745934c2e4c6ff
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Procedimiento para implementar la recuperación ante desastres mediante copias de seguridad y restauración del servicio en Azure API Management

Si decide publicar y administrar las API a través de Azure API Management, podrá aprovechar numerosas capacidades de infraestructura y tolerancia a errores que con otros recursos tendría que diseñar, implementar y administrar. La plataforma Azure mitiga una gran cantidad de posibles errores a un costo reducido.

Para poder recuperarse de problemas de disponibilidad que afecten a la región donde se hospeda el servicio API Management, debe estar preparado para reconstituir el servicio en una región diferente en cualquier momento. En función de sus objetivos de disponibilidad y tiempo de recuperación, tal vez desee reservar un servicio de copia de seguridad en una o varias regiones e intentar mantener sincronizados con el servicio activo el contenido y la configuración correspondientes. La característica de "copia de seguridad y restauración" del servicio ofrece el apoyo necesario para implementar una estrategia de recuperación ante desastres.

En esta guía se explica cómo autenticar las solicitudes de Azure Resource Manager y cómo realizar copias de seguridad y restauraciones de las instancias de servicio de API Management.

> [!NOTE]
> El proceso de copia de seguridad y restauración de una instancia del servicio de administración de API para recuperación ante desastres también puede utilizarse para replicar las instancias de servicio de administración de API para escenarios como almacenamiento provisional.
>
> Cada copia de seguridad expira después de treinta días. Si intenta restaurar una copia de seguridad una vez transcurrido el período de expiración de treinta días, se producirá un error en la restauración con un mensaje `Cannot restore: backup expired`.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Solicitudes de autenticación del Administrador de recursos de Azure

> [!IMPORTANT]
> La API de REST para copia de seguridad y restauración utiliza el Administrador de recursos de Azure y tiene un mecanismo de autenticación diferente que las API de REST para administrar las entidades de la administración de API. Los pasos de esta sección describen cómo autenticar las solicitudes del Administrador de recursos de Azure. Par obtener más información, consulte [Solicitudes de autenticación del Administrador de recursos de Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Todas las tareas que se realizan en los recursos mediante Azure Resource Manager deben autenticarse con Azure Active Directory con los siguientes pasos:

* Agregue una aplicación al inquilino de Azure Active Directory.
* Establezca permisos para la aplicación que ha agregado.
* Obtenga el token para autenticar solicitudes al Administrador de recursos de Azure.

### <a name="create-an-azure-active-directory-application"></a>Creación de una aplicación de Azure Active Directory

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). 
2. Mediante la suscripción que contiene la instancia de servicio de API Management, vaya a la pestaña **Registros de aplicaciones**.

    > [!NOTE]
    > Si el directorio predeterminado de Azure Active Directory no está visible en su cuenta, póngase en contacto con el administrador de la suscripción de Azure para que le conceda los permisos necesarios para su cuenta.
3. Haga clic en **Nuevo registro de aplicaciones**.

    Aparecerá la ventana **Crear** a la derecha. Es donde debe especificar la información pertinente de la aplicación de AAD.
4. Escriba un nombre para la aplicación.
5. En el tipo de aplicación, seleccione **Nativa**.
6. Escriba una dirección URL de marcador de posición como `http://resources` para el **URI de redireccionamiento**, ya que es un campo obligatorio, pero el valor no se utiliza más adelante. Haga clic en la casilla para guardar la aplicación.
7. Haga clic en **Create**(Crear).

### <a name="add-an-application"></a>Adición de una aplicación

1. Una vez creada la aplicación, haga clic en **Configuración**.
2. Haga clic en **Permisos necesarios**.
3. Haga clic en **+Agregar**.
4. Haga clic en **Seleccionar una API**.
5. Elija **Windows** **Azure Service Management API**.
6. Haga clic en **Seleccionar**. 

    ![Adición de permisos](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Haga clic en **Permisos delegados** al lado de la aplicación recién agregada, active la casilla **Access Azure Service Management (preview)** [Acceso a Azure Service Management (versión preliminar)].
8. Haga clic en **Seleccionar**.

### <a name="configuring-your-app"></a>Configuración de la aplicación

Antes de invocar las API que generan la copia de seguridad y la restauran, es necesario obtener un token. En el ejemplo siguiente se utiliza el paquete de NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar el token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Reemplace `{tentand id}`, `{application id}` y `{redirect uri}` mediante las siguientes instrucciones:

1. Reemplace `{tenant id}` con el identificador del inquilino de la aplicación de Azure Active Directory creada. Para acceder al identificador, haga clic en **Registros de aplicaciones** -> **Puntos de conexión**.

    ![Puntos de conexión][api-management-endpoint]
2. Reemplace `{application id}` con el valor obtenido en la página **Configuración**.
3. Reemplace la dirección URL de la pestaña **URI de redirección** de la aplicación de Azure Active Directory.

    Una vez especificados los valores, el ejemplo de código debe devolver un token similar al ejemplo siguiente:

    ![Se necesita el cifrado de tokens][api-management-arm-token]

## <a name="calling-the-backup-and-restore-operations"></a>Llamada a operaciones de copia de seguridad y restauración

Antes de llamar a las operaciones de "copia de seguridad y restauración" descritas en las secciones siguientes, establezca el encabezado de solicitud de autorización para la llamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"></a>Crear una copia de seguridad del servicio API Management
Para crear una copia de seguridad del servicio API Management, emita esta solicitud HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

donde:

* `subscriptionId`: identificador de la suscripción que contiene el servicio API Management del que desea crear una copia de seguridad.
* `resourceGroupName`: una cadena de tipo "Api-Default-{service-region}", donde `service-region` identifica la región de Azure donde se hospeda el servicio API Management del que desea crear una copia de seguridad (por ejemplo, `North-Central-US`).
* `serviceName` : el nombre del servicio Administración de API del que desea crear una copia de seguridad que se especificó durante su creación.
* `api-version`: reemplazar por `2014-02-14`

En el cuerpo de la solicitud, especifique el nombre de la copia de seguridad, el nombre del contenedor de blobs, la clave de acceso y el nombre de la cuenta de almacenamiento de Azure de destino:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Establezca el valor del encabezado de solicitud `Content-Type` en `application/json`.

La creación de una copia de seguridad es una operación de larga duración que puede tardar varios minutos en completarse.  Si la solicitud es correcta y el proceso de copia de seguridad se inicia, recibirá un código de estado de respuesta `202 Accepted` con un encabezado `Location`.  Realice solicitudes "GET" en la URL del encabezado `Location` para averiguar el estado de la operación. Mientras se crea la copia de seguridad, recibirá el código de estado "202 Aceptado". El código de respuesta `200 OK` indica que la operación de copia de seguridad se ha completado correctamente.

Tenga en cuenta las siguientes restricciones al realizar una solicitud de copia de seguridad.

* El **contenedor** que se especifique en el cuerpo de la solicitud **debe ser real**.
* Mientras se crea la copia de seguridad, **no realice ninguna operación de administración del servicio** (por ejemplo, una actualización o degradación de SKU o un cambio de nombre de dominio).
* La restauración de una **copia de seguridad se garantiza solo durante 30 días** a partir del momento en que esta se crea.
* Los **datos de uso** con los que se crean informes de análisis **no se incluyen** en la copia de seguridad. La [API de REST de Azure API Management][Azure API Management REST API] permite recibir de forma periódica informes de análisis para guardarlos en un lugar seguro.
* La frecuencia con la que se crean las copias de seguridad afecta al objetivo de punto de recuperación. Para minimizarlo, se recomienda implementar las copias de seguridad de forma periódica y también a petición tras realizar cambios importantes en el servicio API Management.
* Es posible que los **cambios** que se realicen en la configuración del servicio (por ejemplo, en la API, las directivas o la apariencia del portal para desarrolladores) mientras se está realizando la copia de seguridad **no se incluyan en la copia de seguridad y se pierdan**.

### <a name="step2"></a>Restaurar el servicio Administración de API
Para restaurar el servicio Administración de API de una copia de seguridad anterior, realice esta solicitud HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

donde:

* `subscriptionId`: identificador de la suscripción que contiene el servicio Administración de API en el que se restaura una copia de seguridad.
* `resourceGroupName`: una cadena de tipo "Api-Default-{service-region}", donde `service-region` identifica la región de Azure donde se hospeda el servicio API Management en el que desea restaurar una copia de seguridad (por ejemplo, `North-Central-US`).
* `serviceName` : el nombre del servicio Administración de API que desea restaurar que se especificó durante su creación.
* `api-version`: reemplazar por `2014-02-14`

En el cuerpo de la solicitud, especifique la ubicación del archivo de copia de seguridad, es decir, el nombre de la copia de seguridad, el nombre del contenedor de blobs, la clave de acceso y el nombre de la cuenta de almacenamiento de Azure:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Establezca el valor del encabezado de solicitud `Content-Type` en `application/json`.

La restauración es una operación de larga duración que puede tardar 30 minutos o más en completarse. Si la solicitud es correcta y el proceso de restauración se inicia, recibirá un código de estado de respuesta `202 Accepted` con un encabezado `Location`. Realice solicitudes "GET" en la URL del encabezado `Location` para averiguar el estado de la operación. Mientras se realiza la restauración, recibirá el código de estado "202 Aceptado". El código de respuesta `200 OK` indica que la operación de restauración se ha completado correctamente.

> [!IMPORTANT]
> **La SKU** en la que desea restaurar el servicio **debe coincidir** con la SKU del servicio del que ha creado una copia de seguridad que desea restaurar.
>
> Los **cambios** que se realicen en la configuración del servicio (por ejemplo, en la API, las directivas o la apariencia del portal para desarrolladores) con la operación de restauración en curso **pueden sobrescribirse**.
>
>

## <a name="next-steps"></a>pasos siguientes
Consulte los siguientes blogs de Microsoft para dos tutoriales diferentes del proceso de copia de seguridad y restauración.

* [Replicate Azure API Management Accounts (Réplica de cuentas de Administración de API de Azure)](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Azure API Management: Backing Up and Restoring Configuration (Administración de API de Azure: copia de seguridad y restauración de la configuración)](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * El enfoque detallado por Stuart no coincide con la orientación oficial, pero es interesante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
