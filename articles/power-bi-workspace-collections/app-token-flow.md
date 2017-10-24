---
title: "Autenticación y autorización con Colecciones de áreas de trabajo de Power BI | Microsoft Docs"
description: "Autenticación y autorización con Colecciones de áreas de trabajo de Power BI."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: ae9627c6bb5e7bb099598acaa2eb29375c35593e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Autenticación y autorización con Colecciones de áreas de trabajo de Power BI

Colecciones de áreas de trabajo de Power BI usa **claves** y **tokens de aplicación** para realizar la autenticación y la autorización en lugar de la autenticación explícita de usuario final. En este modelo, la aplicación, la aplicación administra la autenticación y la autorización de sus usuarios finales. Si es necesario, la aplicación crea y envía los tokens de aplicación que indican a nuestro servicio que presente el informe solicitado. En este diseño no es necesario que la aplicación use Azure Active Directory para la autenticación y la autorización de usuarios, aunque puede hacerlo.

> [!IMPORTANT]
> Colecciones de áreas de trabajo de Power BI está en desuso y estará disponible hasta junio de 2018 o hasta cuando lo indique su contrato. Se recomienda planear la migración a Power BI Embedded para evitar la interrupción de la aplicación. Para obtener más información sobre cómo migrar los datos a Power BI Embedded, consulte [Migración de contenido de la colección de áreas de trabajo de Power BI Embedded a Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dos modos de autenticación

**Clave**: puede usar claves para todas las llamadas de API de REST de Colecciones de áreas de trabajo de Power BI. Las claves pueden encontrarse en **Microsoft Azure Portal** seleccionando **Toda la configuración** y, luego, **Claves de acceso**. Debe tratar siempre la clave como si fuese una contraseña. Estas claves tienen permisos para llamar a cualquier API de REST en una colección de área de trabajo determinada.

Para utilizar una clave en una llamada de REST, agregue el siguiente encabezado de autorización:

    Authorization: AppKey {your key}

**Token de aplicación** : los tokens de aplicación se utilizan para todas las solicitudes de inserción. Están diseñados para ejecutarse en el lado cliente, por lo que están restringidos a un único informe y se recomienda establecer un tiempo de expiración.

Los tokens de aplicación son un JWT (JSON Web Token) que está firmado por una de sus claves.

El token de la aplicación puede contener las siguientes notificaciones:

| Notificación | Description |
| --- | --- |
| **ver** |La versión del token de aplicación. La versión actual es 0.2.0. |
| **aud** |El destinatario previsto del token. Para Colecciones de áreas de trabajo de Power BI, use: "https://analysis.windows.net/powerbi/api". |
| **iss** |Una cadena que indica la aplicación que emitió el token. |
| **type** |El tipo de token de aplicación que se está creando. El único tipo admitido actualmente es **insertar**. |
| **wcn** |El nombre de la colección de área de trabajo para el que se emite el token. |
| **wid** |El id. del área de trabajo para el que se emite el token. |
| **rid** |El id. del informe para el que se emite el token. |
| **username** (opcional) |Se utiliza con RLS y es una cadena que ayuda a identificar el usuario cuando se aplican las reglas RLS. |
| **roles** (opcional) |Una cadena que contiene los roles que seleccione al aplicar las reglas de seguridad de nivel de fila. Si se pasa más de un rol, se deben pasar como una matriz de cadenas. |
| **scp** (opcional) |Cadena que contiene los ámbitos de los permisos. Si se pasa más de un rol, se deben pasar como una matriz de cadenas. |
| **exp** (opcional) |Indica la hora a la que caducará el token. Se deben pasar como marcas de tiempo de Unix. |
| **nbf** (opcional) |Indica la hora a la que comienza a ser válido el token. Se deben pasar como marcas de tiempo de Unix. |

Un token de aplicación de ejemplo tiene el siguiente aspecto:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Cuando se descodifica, es similar a lo siguiente:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Hay métodos disponibles en los SDK que facilitan la creación de tokens de aplicación. Por ejemplo, para .NET puede observar la clase [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) y los métodos [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_).

Para el SDK de .NET, puede consultar [Ámbitos](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Ámbitos

Si usa tokens de inserción, puede restringir el uso de los recursos a los que proporciona acceso. Por este motivo, puede generar un token con permisos con ámbito.

Los siguientes son los ámbitos disponibles para Colecciones de áreas de trabajo de Power BI.

|Scope|Descripción|
|---|---|
|Dataset.Read|Proporciona permiso para leer el conjunto de datos especificado.|
|Dataset.Write|Proporciona permiso para escribir en el conjunto de datos especificado.|
|Dataset.ReadWrite|Proporciona permiso para leer el conjunto de datos especificado y escribir en él.|
|Report.Read|Proporciona permiso para ver el informe especificado.|
|Report.ReadWrite|Proporciona permiso para ver y editar el informe especificado.|
|Workspace.Report.Create|Proporciona permiso para crear un informe nuevo dentro del área de trabajo especificada.|
|Workspace.Report.Copy|Proporciona permiso para clonar un informe existente dentro del área de trabajo especificada.|

Puede suministrar varios ámbitos; para ello, use un espacio entre los ámbitos de la siguiente manera.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Notificaciones necesarias: ámbitos**

scp: {scopesClaim} scopesClaim puede ser una cadena o una matriz de cadenas, teniendo en cuenta los permisos asociados a los recursos del área de trabajo (Report, Dataset, etc.)

Un token descodificado, con ámbitos definidos, tendría un aspecto similar al siguiente:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operaciones y ámbitos

|Operación|Recurso de destino|Permisos de token|
|---|---|---|
|Crea (en memoria) un informe nuevo basado en un conjunto de datos.|Dataset|Dataset.Read|
|Crea (en memoria) un informe nuevo basado en un conjunto de datos y guarda el informe.|Dataset|* Dataset.Read<br>* Workspace.Report.Create|
|Ve y explora o edite (en memoria) un informe existente. Report.Read implica Dataset.Read. Report.Read no permite guardar las ediciones.|Informe|Report.Read|
|Edita y guarda un informe existente.|Informe|Report.ReadWrite|
|Guarda una copie de un informe (Guardar como).|Informe|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Funcionamiento del flujo
1. Copie las claves de API a la aplicación. Puede obtener las claves en el **Azure Portal**.
   
    ![Dónde encontrar las claves de API en Azure Portal](media/get-started-sample/azure-portal.png)
1. El token impone una notificación y tiene un tiempo de expiración.
   
    ![Flujo de tokens de aplicación: el token impone una notificación](media/get-started-sample/token-2.png)
1. El token se firma con las claves de acceso de la API.
   
    ![Flujo de tokens de aplicación: el token se firma](media/get-started-sample/token-3.png)
1. El usuario solicita ver un informe.
   
    ![Flujo de tokens de aplicación: el usuario solicita ver un informe](media/get-started-sample/token-4.png)
1. El token se valida con claves de acceso de una API.
   
   ![Flujo de tokens de aplicación: el token se valida](media/get-started-sample/token-5.png)
1. Colecciones de áreas de trabajo de Power BI envía un informe al usuario.
   
   ![Flujo de tokens de aplicación: el servicio envía un informe al usuario](media/get-started-sample/token-6.png)

Después de que **Colecciones de áreas de trabajo de Power BI** envíe un informe al usuario, este podrá ver el informe en su aplicación personalizada. Por ejemplo, si ha importado el [archivo PBIX de ejemplo de análisis de datos de venta](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), la aplicación web de ejemplo tendría el siguiente aspecto:

![Ejemplo de informe insertado en la aplicación](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Otras referencias

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Introducción a ejemplos de Colecciones de áreas de trabajo de Power BI](get-started-sample.md)  
[Escenarios comunes de Colecciones de áreas de trabajo de Power BI](scenarios.md)  
[Introducción a Colecciones de áreas de trabajo de Power BI](get-started.md)  
[Repositorio GIT PowerBI-CSharp](https://github.com/Microsoft/PowerBI-CSharp)

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)