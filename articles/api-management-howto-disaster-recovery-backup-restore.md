<properties pageTitle="How to implement disaster recovery using service backup and restore in Azure API Management" metaKeywords="" description="Learn how to use backup and restore to perform disaster recovery in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to implement disaster recovery using service backup and restore in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Procedimiento para implementar la recuperación ante desastres mediante copias de seguridad y restauración del servicio en Administración de API de Azure

Si decide publicar y administrar las API a través de Administración de API de Azure, podrá aprovechar numerosas capacidades de infraestructura y tolerancia a errores que con otros recursos tendría que diseñar, implementar y administrar. La plataforma Azure mitiga una gran cantidad de posibles errores a un costo reducido.

Para poder recuperarse de problemas de disponibilidad que afecten a la región donde se hospeda el servicio Administración de API, debe estar preparado para reconstituir el servicio en una región diferente en cualquier momento. En función de sus objetivos de disponibilidad y tiempo de recuperación, tal vez desee reservar un servicio de copia de seguridad en una o varias regiones e intentar mantener sincronizados con el servicio activo el contenido y la configuración correspondientes. La característica de copia de seguridad y restauración del servicio ofrece el apoyo necesario para implementar una estrategia de recuperación ante desastres.

Esta característica se encuentra disponible a través de la API de REST de administración del servicio. Consulte este artículo sobre la [autenticación de las solicitudes del Administrador de recursos de Azure][autenticación de las solicitudes del Administrador de recursos de Azure] si desea obtener las instrucciones necesarias para acceder a la API.

## En este tema

-   [Crear una copia de seguridad del servicio Administración de API][Crear una copia de seguridad del servicio Administración de API]
-   [Restaurar el servicio Administración de API][Restaurar el servicio Administración de API]

## <a name="step1"> </a>Crear una copia de seguridad del servicio Administración de API

Para crear una copia de seguridad del servicio Administración de API, emita esta solicitud HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

donde:

-   `subscriptionId`: identificador de la suscripción que contiene el servicio Administración de API del que desea crear una copia de seguridad.
-   `resourceGroupName`: una cadena de tipo "Api-Default-{service-region}", donde `service-region` identifica la región de Azure donde se hospeda el servicio Administración de API del que desea crear una copia de seguridad (por ejemplo, `North-Central-US`).
-   `serviceName`: el nombre del servicio Administración de API del que desea crear una copia de seguridad que se especificó durante su creación.
-   `api-version` : reemplazar por `2014-02-14`

En el cuerpo de la solicitud, especifique el nombre de la copia de seguridad, el nombre de contenedor de blobs, la clave de acceso y la cuenta de almacenamiento de Azure de destino:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Establezca el valor del encabezado de solicitud `Content-Type` en `application\json`.

La creación de una copia de seguridad es una operación de larga duración que puede tardar varios minutos en completarse. Si la solicitud es correcta y el proceso de copia de seguridad se inicia, recibirá un código de estado de respuesta `202 Accepted` con el encabezado `Location`. Realice solicitudes "GET" en la URL del encabezado `Location` para averiguar el estado de la operación. Mientras se crea la copia de seguridad, recibirá el código de estado "202 Aceptado". El código de respuesta `200 OK` indica que la operación de copia de seguridad se ha completado correctamente.

**Nota**:

-   El **contenedor** que se especifique en el cuerpo de la solicitud **debe ser real**.
-   Mientras se crea la copia de seguridad, **no realice ninguna operación de administración del servicio** (por ejemplo, una actualización o degradación de nivel, o un cambio de nombre de dominio).
-   La restauración de una **copia de seguridad se garantiza solo durante 7 días**, a partir del momento en que esta se crea.
-   Los **datos de uso** con los que se crean informes de análisis **no se incluyen** en la copia de seguridad. La [API de REST de Administración de API de Azure][API de REST de Administración de API de Azure] permite recibir de forma periódica informes de análisis para guardarlos en un lugar seguro.
-   La frecuencia con la que se crean las copias de seguridad afecta al objetivo de punto de recuperación. Para minimizarlo, se recomienda crear las copias de seguridad de forma periódica y también a petición tras realizar cambios importantes en el servicio Administración de API.
-   Es posible que los **cambios** que se realicen en la configuración del servicio (como en API, directivas o apariencia del portal para desarrolladores) mientras se está realizando la copia de seguridad **no se incluyan en la copia de seguridad y se pierdan**.

## <a name="step2"> </a>Restaurar el servicio Administración de API

Para restaurar el servicio Administración de API de una copia de seguridad anterior, realice esta solicitud HTTP:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

donde:

-   `subscriptionId`: identificador de la suscripción que contiene el servicio Administración de API que desea restaurar.
-   `resourceGroupName`: una cadena de tipo "Api-Default-{service-region}", donde `service-region` identifica la región de Azure donde se hospeda el servicio Administración de API en el que desea restaurar una copia de seguridad (por ejemplo, `North-Central-US`).
-   `serviceName`: el nombre del servicio Administración de API que desea restaurar que se especificó durante su creación.
-   `api-version` : reemplazar por `2014-02-14`

En el cuerpo de la solicitud, especifique la ubicación del archivo de copia de seguridad, como una cuenta de almacenamiento de Azure, la clave de acceso, el nombre de contenedor de blobs y el nombre de la copia de seguridad:

    '{  
        storageAccount : "{storage account for the backup}",  
        accessKey : "{access key for the account}",  
        containerName : "{backup container name}",  
        backupName : "{backup blob name}"  
    }'

Establezca el valor del encabezado de solicitud `Content-Type` en `application\json`.

La restauración es una operación de larga duración que puede tardar 30 minutos o más en completarse. Si la solicitud es correcta y el proceso de restauración se inicia, recibirá un código de estado de respuesta `202 Accepted` con el encabezado `Location`. Realice solicitudes "GET" en la URL del encabezado `Location` para averiguar el estado de la operación. Mientras se realiza la restauración, recibirá el código de estado "202 Aceptado". El código de respuesta `200 OK` indica que la operación de restauración se ha completado correctamente.

**Nota**:

-   **El nivel** en el que desea restaurar el servicio **debe coincidir** con el nivel del servicio del que ha creado una copia de seguridad que desea restaurar.
-   Los **cambios** que se realicen en la configuración del servicio (como en API, directivas o apariencia del portal para desarrolladores), con el proceso de restauración en curso, **pueden sobrescribirse**.

  [autenticación de las solicitudes del Administrador de recursos de Azure]: http://msdn.microsoft.com/es-es/library/dn790557.aspx
  [Crear una copia de seguridad del servicio Administración de API]: #step1
  [Restaurar el servicio Administración de API]: #step2
  [API de REST de Administración de API de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn781421.aspx
