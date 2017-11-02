---
title: "Introducción a la CLI de Azure para Batch | Microsoft Docs"
description: "Obtenga una introducción rápida a los comandos de Batch en la CLI de Azure para administrar los recursos del servicio Azure Batch"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 763a8884b65f64b4807cd42c937f43b2f5517ed5
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="manage-batch-resources-with-azure-cli"></a>Administración de recursos de Batch con la CLI de Azure

La CLI 2.0 de Azure es la nueva experiencia de línea de comandos de Azure para administrar recursos de Azure. Se puede usar en macOS, Linux y Windows. La CLI de Azure 2.0 está optimizada para administrar recursos de Azure desde la línea de comandos. Puede usarla para administrar cuentas de Azure Batch y administrar recursos, como grupos, trabajos y tareas. Con la CLI de Azure, puede realizar mediante scripts muchas de las mismas tareas que lleva a cabo con las API de Batch, Azure Portal y los cmdlets PowerShell de Batch.

En este artículo se proporciona una introducción al uso de la [CLI de Azure versión 2.0](https://docs.microsoft.com/cli/azure/overview) con Batch. Consulte [Introducción a la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para información general sobre el uso de la CLI con Azure.

Microsoft recomienda usar la versión más reciente de la CLI de Azure, la versión 2.0. Para más información acerca de la versión 2.0, consulte [Azure Command Line 2.0 now generally available](https://azure.microsoft.com/blog/announcing-general-availability-of-vm-storage-and-network-azure-cli-2-0/) (La línea de comandos de Azure 2.0 está ahora disponible con carácter general).

## <a name="set-up-the-azure-cli"></a>Configuración de la CLI de Azure

Para instalar la CLI de Azure, siga los pasos descritos en [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!TIP]
> Se recomienda actualizar su instalación de la CLI de Azure con frecuencia para aprovechar las mejoras y actualizaciones del servicio.
> 
> 

## <a name="command-help"></a>Ayuda de comandos

Para mostrar texto de ayuda para todos los comandos de la CLI de Azure, anexe `-h` al comando. Omita el resto de las opciones. Por ejemplo:

* Para obtener ayuda sobre el comando `az` escriba: `az -h`
* Para obtener una lista de todos los comandos de Batch en la CLI, utilice: `az batch -h`
* Para obtener ayuda acerca de la creación de una cuenta de Batch, escriba: `az batch account create -h`

En caso de duda, utilice la opción de línea de comandos `-h` para obtener ayuda sobre cualquier comando de CLI de Azure.

> [!NOTE]
> Las versiones anteriores de la CLI de Azure usaban `azure` delante de un comando de CLI. En la versión 2.0, todos los comandos van precedidos de `az`. Asegúrese de actualizar los scripts para que usen la nueva sintaxis con la versión 2.0.
>
>  

Además, consulte la documentación de referencia de la CLI de Azure para más información sobre los [comandos de la CLI de Azure para Batch](https://docs.microsoft.com/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Inicio de sesión y autenticación

Para utilizar la CLI de Azure con Batch, debe iniciar sesión y autenticarse. Hay dos pasos sencillos que seguir:

1. **Inicie sesión en Azure.** Al iniciar sesión en Azure, logra acceso a los comandos de Azure Resource Manager, incluidos los del [servicio de administración de Batch](batch-management-dotnet.md).  
2. **Inicie sesión en su cuenta de Batch.** Al iniciar sesión en su cuenta de Batch, logra acceso a los comandos del servicio Batch.   

### <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Hay varias maneras diferentes de iniciar sesión en Azure, las cuales se describen en detalle en [Inicio de sesión con la CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli):

1. [Inicie sesión de forma interactiva](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_interactive_log_in). Inicie sesión de forma interactiva cuando ejecute comandos de la CLI de Azure desde la línea de comandos.
2. [Inicie sesión con una entidad de servicio](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_logging_in_with_a_service_principal). Inicie sesión con una entidad de servicio cuando ejecute comandos de la CLI de Azure desde un script o una aplicación.

Para este artículo, se muestra cómo iniciar sesión de forma interactiva en Azure. Escriba [az login](https://docs.microsoft.com/cli/azure/#login) en la línea de comandos:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

El comando `az login` devuelve un token que puede usar para autenticarse, como se muestra aquí. Siga las instrucciones proporcionadas para abrir una página web y enviar el token a Azure:

![Inicie sesión en Azure.](./media/batch-cli-get-started/az-login.png)

Los ejemplos en la sección [Scripts de shell de ejemplo](#sample-shell-scripts) también muestran cómo iniciar la sesión de la CLI de Azure mediante el inicio interactivo en Azure. Una vez que haya iniciado sesión, puede llamar a comandos para trabajar con recursos de administración de Batch, incluidas cuentas de Batch, claves, paquetes de aplicación y cuotas.  

### <a name="log-in-to-your-batch-account"></a>Inicio de sesión en la cuenta de Batch

Para usar la CLI de Azure para administrar recursos de Batch, como grupos, trabajos y tareas, debe iniciar sesión en su cuenta de Batch y autenticarse. Para iniciar sesión en el servicio Batch, use el comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login). 

Tiene dos opciones para autenticarse en su cuenta de Batch:

- **Mediante la autenticación de Azure Active Directory (Azure AD).** 

    La autenticación con Azure AD es el valor predeterminado cuando se usa la CLI de Azure con Batch y se recomienda para la mayoría de los escenarios. 
    
    Cuando inicia sesión en Azure de forma interactiva, como se describe en la sección anterior, sus credenciales se almacenan en caché, por lo que la CLI de Azure puede iniciar sesión en su cuenta de Batch con esas mismas credenciales. Si inicia sesión Azure con una entidad de servicio, esas credenciales también se usan para iniciar sesión en su cuenta de Batch.

    Una ventaja de Azure AD es que ofrece control de acceso basado en rol (RBAC). Con RBAC, el acceso de un usuario depende de su rol asignado, y no de si posee o no las claves de cuenta. En lugar de administrar claves de cuenta, puede administrar roles RBAC y dejar que Azure AD se encargue del acceso y la autenticación.  

     Para iniciar sesión en su cuenta de Batch con Azure AD, llame al comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Mediante la autenticación de clave compartida.**

    La [autenticación de clave compartida](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) usa las claves de acceso de su cuenta para autenticar comandos de la CLI de Azure para el servicio Batch.

    Si va a crear scripts de la CLI de Azure para automatizar la llamada a comandos de Batch, puede usar la autenticación de clave compartida o una entidad de servicio de Azure AD. En algunos escenarios, puede resultar más sencillo usar la autenticación de clave compartida que crear una entidad de servicio.  

    Para iniciar sesión con autenticación de clave compartida, incluya la opción `--shared-key-auth` en la línea de comandos:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Los ejemplos en la sección [Scripts de shell de ejemplo](#sample-shell-scripts) muestran cómo iniciar sesión en su cuenta de Batch con la CLI de Azure mediante Azure AD y la clave compartida.

## <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Uso de plantillas y transferencia de archivos de la CLI de Azure Batch (versión preliminar)

Puede utilizar la CLI de Azure para ejecutar trabajos completos de Batch sin escribir código. Los archivos de plantilla de Batch admiten la creación de grupos, trabajos y tareas con la CLI de Azure. También puede utilizar la CLI de Azure para cargar archivos de entrada de trabajos en la cuenta de Azure Storage asociada con la cuenta de Batch y descargar los archivos de salida de trabajos. Para más información, consulte [Uso de plantillas y transferencia de archivos de la CLI de Azure Batch (versión preliminar)](batch-cli-templates.md).

## <a name="sample-shell-scripts"></a>Scripts de shell de ejemplo

Los scripts de ejemplo en la tabla siguiente muestran cómo usar comandos de la CLI de Azure con el servicio Batch y el servicio de administración de Batch para realizar tareas habituales. Estos scripts de ejemplo tratan muchos de los comandos disponibles en la CLI de Azure para Batch. 

| Script | Notas |
|---|---|
| [Crear una cuenta de Batch](./scripts/batch-cli-sample-create-account.md) | Crea una cuenta de Batch y la asocia con una cuenta de almacenamiento. |
| [Agregar una aplicación](./scripts/batch-cli-sample-add-application.md) | Agrega una aplicación y carga los paquetes de binarios.|
| [Administrar grupos de Batch](./scripts/batch-cli-sample-manage-pool.md) | Muestra cómo crear grupos, cambiarlos de tamaño y administrarlos. |
| [Ejecutar un trabajo y tareas con Batch](./scripts/batch-cli-sample-run-job.md) | Muestra cómo ejecutar un trabajo y agregar tareas. |

## <a name="json-files-for-resource-creation"></a>Archivos JSON para la creación de recursos

Al crear recursos de Batch, como grupos y trabajos, puede especificar un archivo JSON que contenga la configuración del recurso nuevo, en lugar de pasar sus parámetros como opciones de la línea de comandos. Por ejemplo:

```azurecli
az batch pool create my_batch_pool.json
```

Aunque puede crear la mayoría de los recursos de Batch solo con las opciones de la línea de comandos, algunas características requieren que especifique un archivo con formato JSON que contenga los detalles de los recursos. Por ejemplo, si desea especificar archivos de recursos para una tarea de inicio, debe utilizar un archivo JSON.

Para ver la sintaxis JSON necesaria para crear un recurso, consulte la documentación de [referencia de API de REST de Batch][rest_api]. Todos los temas sobre la incorporación de *tipos de recurso* en la referencia de API de REST contienen scripts JSON de ejemplo para crear el recurso correspondiente. Puede usar esos scripts JSON de ejemplo como plantillas para los archivos JSON que se usan con la CLI de Azure. Por ejemplo, para ver la sintaxis JSON para crear grupos, consulte [Add a pool to an account][rest_add_pool] (Incorporación de un grupo a una cuenta).

Para un script de ejemplo que especifica un archivo JSON, consulte [Ejecutar un trabajo y tareas con Batch](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Si especifica un archivo JSON al crear un recurso, se pasan por alto los restantes parámetros que especifique en la línea de comandos de dicho recurso.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Consultas eficaces para recursos de Batch

Cada tipo de recurso de proceso de Batch admite un comando `list` que consulta la cuenta de Batch y enumera los recursos de ese tipo. Por ejemplo, puede enumerar los grupos de su cuenta y las tareas de un trabajo:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Cuando consulta el servicio Batch con una operación `list`, puede especificar una cláusula OData para limitar la cantidad de datos devueltos. Dado que todo el filtrado se produce en el lado servidor, solo se devuelven los datos que solicite. Use estas cláusulas para ahorrar ancho de banda (y, por consiguiente, tiempo) al realizar operaciones de lista.

En la tabla siguiente se describen las cláusulas OData admitidas por el servicio Batch:

| Cláusula | Descripción |
|---|---|
| `--select-clause [select-clause]` | Devuelve un subconjunto de propiedades para cada entidad. |
| `--filter-clause [filter-clause]` | Devuelve solo las entidades que coincidan con la expresión OData especificada. |
| `--expand-clause [expand-clause]` | Obtiene la información de la entidad en una única llamada REST subyacente. Actualmente, la cláusula expand solo admite la propiedad `stats`. |

Para un script de ejemplo que muestra cómo usar una cláusula OData, consulte [Ejecutar un trabajo y tareas con Batch](./scripts/batch-cli-sample-run-job.md).

Para más información sobre cómo realizar consultas de lista eficaces con cláusulas OData, consulte [Consulta del servicio Azure Batch con eficacia](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Sugerencias de solución de problemas

Las siguientes sugerencias pueden ayudarle cuando esté solucionando problemas de la CLI de Azure:

* Use `-h` para obtener **texto de ayuda** de cualquier comando de CLI.
* Use `-v` y `-vv` para mostrar la salida **detallada** de comandos. Cuando se incluye la marca `-vv`, la CLI de Azure muestra las solicitudes y respuestas REST en sí. Estos modificadores son útiles para mostrar la salida completa del error.
* Puede ver la **salida del comando como JSON** con la opción `--json`. Por ejemplo, `az batch pool show pool001 --json` muestra las propiedades de pool001 en formato JSON. Puede copiar y modificar esta salida para usarla en `--json-file` (consulte [Archivos JSON](#json-files) en este mismo artículo).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->
* El [foro de Batch][batch_forum] está supervisado por los miembros del equipo de Batch. Puede publicar en él sus preguntas si experimenta problemas o desea obtener ayuda acerca de una operación concreta.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).
* Para más información sobre los recursos de Batch, consulte [Introducción a Azure Batch para desarrolladores](batch-api-basics.md).
* Para más información sobre el uso de plantillas de Batch para crear grupos, trabajos y tareas sin escribir código, consulte [Uso de plantillas y transferencia de archivos de la CLI de Azure Batch (versión preliminar)](batch-cli-templates.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
