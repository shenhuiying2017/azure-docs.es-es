---
title: "Actualización asincrónica de modelos de Azure Analysis Services | Microsoft Docs"
description: "Obtenga información sobre cómo escribir el código de actualización asincrónica mediante el uso de la API de REST."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/18/2017
ms.author: owend
ms.openlocfilehash: 06d807b83f700c675c6979998dd8f74372a4845f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Actualización asincrónica con la API de REST
Mediante el uso de cualquier lenguaje de programación que admita llamadas REST, puede realizar operaciones de actualización de datos asincrónicas en los modelos tabulares de Azure Analysis Services. Esto incluye la sincronización de réplicas de solo lectura para la escalabilidad horizontal de consultas. 

Las operaciones de actualización de datos pueden tardar algún tiempo dependiendo de una serie de factores, como el volumen de datos, el nivel de optimización con particiones, etc. Estas operaciones se han invocado tradicionalmente con métodos existentes, como [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Modelo de objetos tabulares), cmdlets de [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) o [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). Sin embargo, estos métodos pueden requerir a menudo conexiones HTTP poco confiables y de larga ejecución.

La API de REST de Azure Analysis Services permite la realización asincrónica de las operaciones de actualización de datos. Mediante el uso de la API de REST, las conexiones HTTP de larga ejecución de las aplicaciones cliente no son necesarias. También hay otras características integradas para la confiabilidad, como reintentos automáticos y confirmaciones por lotes.

## <a name="base-url"></a>URL base

La dirección URL base sigue este formato:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Por ejemplo, considere la posibilidad de un modelo denominado AdventureWorks, en un servidor llamado myserver, ubicado en la región de Azure Oeste de EE. UU., donde el nombre del servidor es:

```
asazure://westus.asazure.windows.net/myserver 
```

La dirección URL base para este nombre de servidor es:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Mediante el uso de la dirección URL base, los recursos y las operaciones se pueden anexar basándose en lo siguiente: 

![Actualización asincrónica](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Todo lo que termina en **s** es una colección.
- Todo lo que termina en **()** es una función.
- Todo lo demás es un recurso/objeto.

Por ejemplo, puede utilizar el verbo POST en la colección de actualizaciones para realizar una operación de actualización, similar a la siguiente:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Autenticación

Todas las llamadas deben autenticarse con un token de Azure Active Directory (OAuth 2) válido en el encabezado de autorización y deben cumplir los siguientes requisitos:

- El token debe ser un token de usuario o una entidad de servicio de aplicación.
- El usuario o la aplicación deben tener permisos suficientes en el servidor o el modelo para realizar la llamada solicitada. El nivel de permiso lo determinan los roles dentro del modelo o del grupo de administradores del servidor.
- El token debe tener la audiencia correcta establecida en `https://*.asazure.windows.net`.

## <a name="post-refreshes"></a>POST /refreshes

Para realizar una operación de actualización, puede utilizar el verbo POST en la colección /refreshes para agregar un nuevo elemento de actualización a la colección. El encabezado de ubicación de la respuesta incluye el identificador de la actualización. La aplicación cliente puede desconectarse y comprobar el estado más adelante si es necesario porque es asincrónica.

Solo se acepta una operación de actualización a la vez para un modelo. Si hay una operación de actualización en ejecución actualmente y se envía otra, se devuelve el código de estado HTTP 409 - Conflicto.

El cuerpo debe ser similar al siguiente:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parámetros
No es necesario especificar parámetros. Se aplica el valor predeterminado.

|NOMBRE  |type  |DESCRIPCIÓN  |Valor predeterminado  |
|---------|---------|---------|---------|
|type     |  Enum       |  El tipo de procesamiento que desea realizar. Los tipos se alinean con los tipos de [comandos de actualización ](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) de TMSL: full, clearValues, calculate, dataOnly, automatic, add y defragment.       |   automatic      |
|CommitMode     |  Enum       |  Determina si los objetos se confirmarán en lotes o solo cuando hayan finalizado. Los modos incluyen: default, transactional y partialBatch.  |  transactional       |
|MaxParallelism     |   int      |  Este valor determina el número máximo de subprocesos en los que ejecutar los comandos de procesamiento en paralelo. Se alinea con la propiedad MaxParallelism que se puede establecer en el [comando de secuencia](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) de TMSL o mediante otros métodos.       | 10        |
|RetryCount    |    int     |   Indica el número de veces que se volverá a intentar la operación antes de un error.      |     0    |
|Objetos     |   Matriz      |   Una matriz de objetos que se va a procesar. Cada objeto incluye: "table" al procesar la tabla completa o "table" y "partition" al procesar una partición. Si no se especifica ningún objeto, se actualiza el modelo completo. |   Process the entire model      |

CommitMode es igual a partialBatch. Se utiliza cuando se realiza una carga inicial de grandes conjuntos de datos que podría tardar horas. Si se produce un error en la operación de actualización después de confirmar correctamente uno o varios lotes, los lotes confirmados correctamente permanecerán confirmados (no se revertirán los lotes confirmados correctamente).

> [!NOTE]
> En el momento de escribir este artículo, el tamaño del lote es el valor de MaxParallelism, pero este valor puede cambiar.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Para comprobar el estado de una operación de actualización, use el verbo GET en el identificador de la actualización. Este es un ejemplo del cuerpo de la respuesta. Si la operación está en curso, **inProgress** se devuelve en el estado.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET /refreshes

Para obtener una lista de las operaciones de actualización históricas de un modelo, use el verbo GET en la colección /refreshes. Este es un ejemplo del cuerpo de la respuesta. 

> [!NOTE]
> En el momento de escribir este artículo, se almacenan y se devuelven los últimos treinta días de las operaciones de actualización, pero este número podría cambiar.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Para cancelar una operación de actualización en curso, utilice el verbo DELETE en el identificador de la actualización.

## <a name="post-sync"></a>POST /sync

Tras realizar las operaciones de actualización, puede ser necesario sincronizar los nuevos datos con réplicas para la escalabilidad horizontal de la consulta. Para llevar a cabo una operación de sincronización de un modelo, use el verbo POST en la función /sync. El encabezado de ubicación de la respuesta incluye el identificador de la operación de sincronización.

## <a name="get-sync-status"></a>GET /sync status

Para comprobar el estado de una operación de sincronización, utilice el verbo GET pasando el identificador de la operación como un parámetro. Este es un ejemplo del cuerpo de la respuesta:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Valores de syncstate:

- 0: Replicando. Los archivos de base de datos se replican en una carpeta de destino.
- 1: Rehidratando. La base de datos se rehidrata en las instancias de servidor de solo lectura.
- 2: Completado. La operación de sincronización se completó correctamente.
- 3: Error. Error en la operación de sincronización.
- 4: Finalizando. La operación de sincronización se completó, pero está realizando los pasos de limpieza.

## <a name="code-sample"></a>Código de ejemplo

Este es un ejemplo de código de C# para comenzar, [RestApiSample en GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Para usar el código de ejemplo

1.  Clone o descargue el repositorio. Abra la solución RestApiSample.
2.  Busque la línea **client.BaseAddress = …** y proporcione su [dirección URL base](#base-url).

El código de ejemplo puede utilizar el inicio de sesión interactivo, el nombre de usuario/contraseña o la [entidad de servicio](#service-principle).

#### <a name="interactive-login-or-usernamepassword"></a>Inicio de sesión interactivo o nombre de usuario/contraseña

Este tipo de autenticación requiere que se cree una aplicación de Azure con los permisos de API necesarios asignados. 

1.  En Azure Portal, seleccione **Nuevo** > **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

    ![Nuevo registro de aplicaciones](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  En **Crear**, escriba un nombre y seleccione **Nativa** en el tipo de aplicación. En **URI de redirección**, escriba **urn:ietf:wg:oauth:2.0:oob** y después haga clic en **Crear**.

    ![Configuración](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Seleccione la aplicación y después copie y guarde el **identificador de la aplicación**.

    ![Copia del identificador de la aplicación](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  En **Configuración**, haga clic en **Permisos necesarios** > **Agregar**.

    ![Agregar acceso de API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  En **Seleccionar una API**, escriba **SQL Server Analysis Services** en el cuadro de búsqueda y después seleccione **Azure Analysis Services (SQL Server Analysis Services Azure)**.

    ![Seleccionar API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Seleccione **Read and Write all Models** (Leer y escribir todos los modelos) y después haga clic en **Seleccionar**. Una vez seleccionadas las dos opciones, haga clic en **Listo** para agregar los permisos. La propagación puede tardar unos minutos.

    ![Seleccionar la lectura y escritura de todos los modelos](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  En el código de ejemplo, busque el método **UpdateToken()**. Observe el contenido de este método.
8.  Busque **string clientID = …** y después escriba el **identificador de la aplicación** que copió en el paso 3.
9.  Ejecute el ejemplo.

#### <a name="service-principal"></a>Entidad de servicio

Vea la entrada de blog [Automation of Azure Analysis Services with Service Principals and PowerShell](https://azure.microsoft.com/blog/automation-of-azure-analysis-services-with-service-principals-and-powershell/) (Automatización de Azure Analysis Services con entidades de servicio y PowerShell) para obtener información sobre cómo configurar una entidad de servicio y asignar los permisos necesarios en Azure Analysis Services. Una vez completados los pasos detallados en la entrada de blog, siga estos pasos adicionales:

1.  En el código de ejemplo, busque **string authority = …**, reemplace **common** por el identificador del inquilino de su organización.
2.  Comente o quite la marca de comentario para que se use la clase ClientCredential para crear una instancia del objeto creado. Asegúrese de que se accede a los valores \<Id. de aplicación> y \<Clave de la aplicación> de forma segura o use una autenticación basada en certificado para las entidades de servicio.
3.  Ejecute el ejemplo.


## <a name="see-also"></a>Otras referencias

[Ejemplos](analysis-services-samples.md)   
[API de REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


