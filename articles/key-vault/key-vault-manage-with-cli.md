---
title: "Administración de Azure Key Vault mediante CLI | Microsoft Docs"
description: Use este tutorial para automatizar las tareas comunes en Key Vault mediante el uso de la CLI.
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 66be6e44-684a-411b-802e-884628458ae7
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: barclayn
ms.openlocfilehash: 94ea95e7f40c8d47dd18422a9c0795655dae365b
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="manage-key-vault-using-cli"></a>Administración de Key Vault mediante CLI

Use este tutorial para empezar a trabajar con Azure Key Vault para crear un contenedor (un almacén) reforzado en Azure. Azure Key Vault se usa para almacenar y administrar secretos y claves criptográficas. En este artículo se le guiará por el proceso de uso de la interfaz de la línea de comandos multiplataforma de Azure para crear un almacén. A continuación, deberá interactuar con el almacén para realizar varias de las operaciones habituales. 

Azure Key Vault está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

**Tiempo estimado para completar el tutorial:** 20 minutos

> [!NOTE]
> En este tutorial no se incluyen instrucciones sobre cómo escribir la aplicación de Azure presente en uno de los pasos. Esta aplicación de ejemplo se usa para mostrar cómo se puede autorizar a una aplicación para emplear una clave o un secreto almacenado en el almacén de claves.
> Este artículo se centra en la configuración de Azure Key Vault mediante la interfaz de la línea de comandos multiplataforma de Azure. Para obtener instrucciones de Azure PowerShell, vea [este tutorial equivalente](key-vault-get-started.md).

Para obtener información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para obtener una versión de [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* Versión 0.9.1 o una posterior de la interfaz de la línea de comandos. Para instalar la última versión y conectarla a su suscripción de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](../cli-install-nodejs.md).
* Una aplicación que se configurará para utilizar la clave o contraseña creada en este tutorial. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo Léame adjunto.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtención de ayuda con la interfaz de la línea de comandos entre plataformas de Azure

Este tutorial asume que está familiarizado con la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema).

Los parámetros --help o -h se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato azure help [command] [options] para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

    azure account set --help

    azure account set -h

    azure help account set

Si duda de qué parámetros necesita un comando, consulte la ayuda usando --help, -h o azure help [command].

También puede leer los tutoriales siguientes para familiarizarse con el modelo de implementación de Azure Resource Manager en la interfaz de la línea de comandos multiplataforma de Azure:

* [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure.](../cli-install-nodejs.md)
* [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md)

## <a name="connect-to-your-subscriptions"></a>Conectarse a sus suscripciones

Para iniciar sesión en Azure con el siguiente comando:

```azurecli-interactive
azure login -u username -p password
```

o si desea iniciar sesión escribiendo interactivamente

```azurecli-interactive
azure login
```

Si tiene varias suscripciones y desea especificar una en concreto para que use Azure Key Vault, escriba lo siguiente para ver las suscripciones de su cuenta:

```azurecli-interactive
azure account list
```

A continuación, para especificar la suscripción que se debe usar, escriba:

```azurecli-interactive
azure account set <subscription name>
```

Para obtener más información sobre la configuración de la interfaz de la línea de comandos entre plataformas de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](../cli-install-nodejs.md).

## <a name="switch-to-using-azure-resource-manager"></a>Cambio al Administrador de recursos de Azure
El almacén de claves necesita Azure Resource Manager. Por lo tanto, escriba lo siguiente para cambiar al modo de Azure Resource Manager:

```azurecli-interactive
azure config mode arm
```

## <a name="create-a-new-resource-group"></a>Creación de un nuevo grupo de recursos
Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado 'ContosoResourceGroup' para este tutorial.

```azurecli-interactive
azure group create 'ContosoResourceGroup' 'East Asia'
```

El primer parámetro es el nombre del grupo de recursos y el segundo parámetro es la ubicación. Para la ubicación, use el comando `azure location list` para identificar cómo se debe especificar una ubicación alternativa a la usada en este ejemplo. Si necesita más información, escriba: `azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registro del proveedor de recursos de Key Vault
Asegúrese de que el proveedor de recursos de Key Vault está registrado en la suscripción:

```azurecli-interactive
azure provider register Microsoft.KeyVault
```

Esto solo se debe hacer una vez por suscripción.

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves

Utilice el comando `azure keyvault create` para crear un Almacén de claves. Este script tiene tres parámetros obligatorios: el nombre del grupo de recursos, el nombre del Almacén de claves y la ubicación geográfica.

Por ejemplo:
- Si usa el nombre de almacén **ContosoKeyVault**
- Nombre de grupo de recursos **ContosoResourceGroup**
- Ubicación del tipo **Asia Oriental**:

```azurecli-interactive
azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

El resultado de este comando muestra las propiedades del Almacén de claves que acaba de crear. Las dos propiedades más importantes son:

* **Name**: en este ejemplo, el nombre es ContosoKeyVault. Utilizará este nombre para otros cmdlets de Key Vault.
* **vaultUri**: en este ejemplo es https://contosokeyvault.vault.azure.net. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adición de una clave o un secreto al Almacén de claves

Si desea que Azure Key Vault cree una clave protegida mediante software, utilice el comando `azure key create` y escriba lo siguiente:

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software
````

Sin embargo, si tiene una clave existente en un archivo .pem guardado como archivo local en un archivo denominado softkey.pem que desea cargar en Azure Key Vault, escriba lo siguiente para importar la clave desde el archivo .PEM, que protege la clave de software en el servicio de Key Vault:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software
```

Ahora puede utilizar el URI para hacer referencia a la clave que creó o cargó en Azure Key Vault. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obtener siempre la versión actual y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión concreta.

Para agregar un secreto, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, a Azure Key Vault, escriba lo siguiente:

```azurecli-interactive
azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'
```

Ahora puede hacer referencia a esta clave que agregó a Azure Key Vault utilizando su URI. Utilice **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión concreta.

Veamos la clave o el secreto que acaba de crear:

* Para ver la clave, escriba: `azure keyvault key list --vault-name 'ContosoKeyVault'`
* Para ver el secreto, escriba: `azure keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registro de una aplicación con Azure Active Directory

Este paso lo haría normalmente un programador en un equipo independiente. No es específico de Azure Key Vault, pero se incluye aquí a fin de que la información ofrecida sea lo más completa posible.

> [!IMPORTANT]
> Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure Active Directory.

Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. Para ello, el propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:

- **Id. de aplicación** 
- **Clave de autenticación** (también conocida como "secreto compartido") 

Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La forma en que se configura la aplicación para ello depende de la aplicación. Para la [aplicación de ejemplo de Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), el propietario de la aplicación establece estos valores en el archivo app.config.

Para obtener los pasos detallados sobre cómo registrar una aplicación con Azure Active Directory, debería revisar el artículo titulado [Integración de aplicaciones con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) o [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md). Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Registros de aplicaciones** en la parte izquierda. Si no ve los registros de aplicaciones, haga clic en **Más servicios** y búsquelos allí.  
    >[!NOTE]
    Debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que creó la instancia de Key Vault. 
3. Haga clic en **Nuevo registro de aplicaciones**.
4. En la hoja **Crear**, proporcione un nombre para la aplicación y, después, seleccione **APLICACIÓN WEB Y/O API WEB** (valor predeterminado) y especifique la **DIRECCION URL DE INICIO DE SESIÓN** para su aplicación web. Si no dispone de esta información, puede inventársela para este paso (por ejemplo, podría especificar "http://prueba1.contoso.com"). No importa si los sitios existen. 

   ![Nuevo registro de aplicaciones](./media/key-vault-manage-with-cli/new-application-registration.png)

5. Haga clic en el botón **Crear** .
6. Una vez que haya completado el registro de la aplicación, podrá ver la lista de aplicaciones registradas. Busque la aplicación que acaba de registrar y haga clic en ella.
7. Haga clic en la hoja **Aplicación registrada** y copie el **id. de aplicación**.
8. Haga clic en **Toda la configuración**.
9. En la hoja **Configuración**, haga clic en **claves**.
10. Escriba una descripción en el cuadro **Descripción de la clave** y seleccione una duración; a continuación, haga clic en **GUARDAR**. La página se actualiza y muestra ahora un valor de clave. 
11. En el paso siguiente, usará la información del **id. de aplicación** y la **clave** para establecer permisos en el almacén.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorización de la aplicación para que use la clave o el secreto
Para autorizar que la aplicación obtenga acceso a la clave o al secreto en el almacén, use lo siguiente:

```azurecli-interactive
azure keyvault set-policy
```

Por ejemplo, si el nombre del almacén es ContosoKeyVault y la aplicación que desea autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute lo siguiente:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '[\"decrypt\",\"sign\"]'
```

> [!NOTE]
> Si se ejecuta en un símbolo del sistema de Windows, debe reemplazar las comillas simples por comillas dobles y, además, insertar un carácter de escape en las comillas dobles internas. Por ejemplo: "[\"decrypt\",\"sign\"]".
> 

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:

```azurecli-interactive
azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '[\"get\"]'
```

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si desea utilizar un módulo de seguridad de hardware (HSM)
Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para crear estas claves protegidas con HSM, debe contar con una suscripción de almacén que admita claves protegidas mediante HSM.

Cuando cree el almacén de claves, agregue el parámetro 'sku':

```azurecli-interactive
azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```

A este almacén, se pueden agregar claves protegidas mediante software (tal como se ha mostrado anteriormente) y claves protegidas con HSM. Para crear una clave protegida con HSM, establezca el parámetro Destination en 'HSM':

```azurecli-interactive
azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'
```

Puede utilizar el siguiente comando para importar una clave desde un archivo .pem a su equipo. Este comando importa la clave a HSM en el servicio Key Vault:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'
```

El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Esto permite generar la clave en el HSM local y transferirla al HSM en el servicio de Key Vault, sin que la clave salga del límite del HSM:

```azurecli-interactive
azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'
```

Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminación del Almacén de claves junto con las claves y secretos asociados
Si ya no necesita el Almacén de claves ni la clave o el secreto que contiene, puede eliminar el Almacén de claves utilizando el comando azure keyvault delete:

```azurecli-interactive
azure keyvault delete --vault-name 'ContosoKeyVault'
```

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

```azurecli-interactive
azure group delete --name 'ContosoResourceGroup'
```


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Otros comandos de la interfaz de la línea de comandos entre plataformas de Azure
Estos son otros comandos que pueden resultar útiles para administrar Azure Key Vault.

Este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.

```azurecli-interactive
azure keyvault key list --vault-name 'ContosoKeyVault'
```

Este comando muestra una lista completa de propiedades para la clave especificada.

```azurecli-interactive
azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.

```azurecli-interactive
azure keyvault secret list --vault-name 'ContosoKeyVault'
```

Ejemplo de cómo quitar una clave específica:

```azurecli-interactive
azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'
```

Ejemplo de cómo quitar un secreto específico:

```azurecli-interactive
azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'
```


## <a name="next-steps"></a>Pasos siguientes
- Para conocer las referencias de programación, consulte la [Guía del desarrollador de Azure Key Vault](key-vault-developers-guide.md).
- Para obtener información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](key-vault-whatis.md)
- Para obtener información sobre cómo trabajar con Azure Key Vault mediante PowerShell, consulte [Introducción a Azure Key Vault](key-vault-get-started.md).


