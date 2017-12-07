---
title: "Administración de Azure Key Vault mediante CLI | Microsoft Docs"
description: Use este tutorial para automatizar tareas comunes en Key Vault mediante la CLI 2.0.
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: barclayn
ms.openlocfilehash: eaeb50ca8a83fcfee6689acf549f20ba5d44c51d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Administración de Key Vault mediante CLI 2.0

En este artículo se describe cómo empezar a trabajar con Azure Key Vault mediante la CLI de Azure 2.0. Puede ver información sobre:
- Procedimiento para crear un contenedor protegido (un almacén) en Azure.
- Procedimiento para almacenar y administrar las claves criptográficas y los secretos en Azure. 
- El proceso para usar la interfaz de la línea de comandos entre plataformas de Azure a fin de crear un almacén en el que se guardará una clave o una contraseña para usarla con una aplicación de Azure. 
- Procedimiento para que una aplicación use esa clave o contraseña.

Azure Key Vault está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

**Tiempo estimado para completar el tutorial:** 20 minutos

> [!NOTE]
> Este tutorial no incluye instrucciones sobre cómo escribir la aplicación de Azure incluida en uno de los pasos, en el que se muestra cómo autorizar a una aplicación para que use una clave o un secreto del Almacén de claves.
>

Para obtener información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará lo siguiente:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para obtener una versión de [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* Versión 2.0 de la interfaz de la línea de comandos o posterior. Para instalar la última versión y conectarla a su suscripción de Azure, consulte el artículo sobre la [instalación y configuración de la interfaz de la línea de comandos 2.0 entre plataformas de Azure](/cli/azure/install-azure-cli).
* Una aplicación que se configurará para utilizar la clave o contraseña creada en este tutorial. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo Léame adjunto.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtención de ayuda con la interfaz de la línea de comandos entre plataformas de Azure
Este tutorial asume que está familiarizado con la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema).

Los parámetros --help o -h se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato azure help [command] [options] para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

```azurecli-interactive
az account set --help
az account set -h
```

Si duda sobre los parámetros que necesita un comando, consulte la ayuda usando --help, -h o az help [comando].

También puede leer los tutoriales siguientes para familiarizarse con el Administrador de recursos de Azure en la interfaz de la línea de comandos entre plataformas de Azure:

* [Instalación de la CLI de Azure](/cli/azure/install-azure-cli)
* [Introducción a la CLI de Azure 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Conectarse a sus suscripciones
Para iniciar sesión usando una cuenta profesional, utilice el comando siguiente:

```azurecli-interactive
az login -u username@domain.com -p password
```

o si desea iniciar sesión escribiendo interactivamente

```azurecli-interactive
az login
```

Si tiene varias suscripciones y desea especificar una en concreto para que use Azure Key Vault, escriba lo siguiente para ver las suscripciones de su cuenta:

```azurecli-interactive
az account list
```

A continuación, para especificar la suscripción que se debe usar, escriba:

```azurecli-interactive
az account set --subscription <subscription name or ID>
```

Para más información acerca de cómo configurar la interfaz de la línea de comandos entre plataformas de Azure, consulte el artículo sobre la [instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Creación de un nuevo grupo de recursos
Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado 'ContosoResourceGroup' para este tutorial.

```azurecli-interactive
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

El primer parámetro es el nombre del grupo de recursos y el segundo parámetro es la ubicación. Para obtener una lista de todos los tipos de ubicaciones posibles:

```azurecli-interactive
az account list-locations
``` 

Si necesita más información, escriba: 

```azurecli-interactive
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>Registro del proveedor de recursos de Key Vault
Al intentar crear un almacén de claves, puede ver el error "La suscripción no está registrada para usar el espacio de nombres 'Microsoft.KeyVault'". Si aparece ese mensaje, asegúrese de que el proveedor de recursos de Key Vault está registrado en la suscripción:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
Esto solo se debe hacer una vez por suscripción.

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Utilice el comando `az keyvault create` para crear un Almacén de claves. Este script tiene tres parámetros obligatorios: el nombre del grupo de recursos, el nombre del Almacén de claves y la ubicación geográfica.

Por ejemplo:

- Si usa el nombre de almacén **ContosoKeyVault**
- Nombre de grupo de recursos **ContosoResourceGroup**
- Ubicación **Asia Oriental**

Escribiría:

```azurecli-interactive
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

El resultado de este comando muestra las propiedades del Almacén de claves que acaba de crear. Las dos propiedades más importantes son:

* **name**: en este ejemplo, el nombre es ContosoKeyVault. Utilizará este nombre para otros comandos de Key Vault.
* **vaultUri**: en este ejemplo es https://contosokeyvault.vault.azure.net. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adición de una clave o un secreto al Almacén de claves

Si desea que Azure Key Vault cree una clave protegida mediante software, utilice el comando `az key create` y escriba lo siguiente:
```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
Sin embargo, si tiene una clave existente en un archivo .pem guardado como archivo local en un archivo denominado softkey.pem que desea cargar en Azure Key Vault, escriba lo siguiente para importar la clave desde el archivo .PEM, que protege la clave de software en el servicio de Key Vault:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

Ahora puede utilizar el URI para hacer referencia a la clave que creó o cargó en Azure Key Vault. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obtener siempre la versión actual y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión concreta.

Para agregar un secreto, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, a Azure Key Vault, escriba lo siguiente:

```azurecli-interactive
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

Ahora puede hacer referencia a esta clave que agregó a Azure Key Vault utilizando su URI. Utilice **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión concreta.

Veamos la clave o el secreto que acaba de crear:

* Para ver la clave, escriba: 

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

* Para ver el secreto, escriba: 

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>Registro de una aplicación con Azure Active Directory
Este paso lo haría normalmente un programador en un equipo independiente. No es específico de Azure Key Vault, pero se incluye aquí para fines de información.

> [!IMPORTANT]
> Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure.
>
>

Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. Para ello, el propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:

- **Identificador de la aplicación** 
- Una **clave de autenticación** (también conocida como secreto compartido). 

Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La forma en que se configura la aplicación para ello depende de la aplicación. Para la [aplicación de ejemplo de Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), el propietario de la aplicación establece estos valores en el archivo app.config.

Para obtener los pasos detallados sobre cómo registrar una aplicación con Azure Active Directory, debería revisar el artículo titulado [Integración de aplicaciones con Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md) o [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](../azure-resource-manager/resource-group-create-service-principal-portal.md). Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. En el panel izquierdo, haga clic en **Registros de aplicaciones**. Si no ve los registros de aplicaciones, haga clic en **Más servicios** y búsquelos allí.  
[!NOTE]
Debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que creó la instancia de Key Vault. 
3. Haga clic en **Nuevo registro de aplicaciones**.
4. En la hoja **Crear**, proporcione un nombre para la aplicación y, después, seleccione**APLICACIÓN WEB Y/O API WEB** (valor predeterminado) y especifique la **DIRECCION URL DE INICIO DE SESIÓN** para su aplicación. Si no tiene esta información en este momento, puede inventársela para este paso (por ejemplo, podría especificar http://prueba1.contoso.com). No importa si los sitios existen. 

    ![Nuevo registro de aplicaciones](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    Asegúrese de elegir **APLICACIÓN WEB Y/O API WEB**; si no lo hizo, no verá la opción **Claves** en la configuración.

5. Haga clic en el botón **Crear** .
6. Cuando el registro de la aplicación se completa, puede ver la lista de aplicaciones registradas. Busque la aplicación recién registrada y haga clic en ella.
7. Haga clic en la hoja **Aplicación registrada** y copie el **Id. de aplicación**
8. Haga clic en **Toda la configuración**
9. En la hoja **Configuración**, haga clic en **Claves**
9. Escriba una descripción en el cuadro **Descripción de la clave** y seleccione una duración; a continuación, haga clic en **GUARDAR**. La página se actualiza y muestra ahora un valor de clave. 
10. En el paso siguiente, utilizará la información de **Id. de aplicación** y **Clave** para establecer permisos en el almacén.


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorización de la aplicación para que use la clave o el secreto
Para que la aplicación pueda acceder a la clave o el secreto en el almacén, use el comando `az keyvault set-policy` .

Por ejemplo, si el nombre del almacén es ContosoKeyVault y la aplicación que desea autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute lo siguiente:

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si desea utilizar un módulo de seguridad de hardware (HSM)
Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para crear estas claves protegidas con HSM, debe contar con una suscripción de almacén que admita claves protegidas mediante HSM.

Cuando cree el almacén de claves, agregue el parámetro 'sku':

```azurecli-interactive
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
A este almacén, se pueden agregar claves protegidas mediante software (tal como se ha mostrado anteriormente) y claves protegidas con HSM. Para crear una clave protegida con HSM, establezca el parámetro Destination en 'HSM':

```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Puede utilizar el siguiente comando para importar una clave desde un archivo .pem a su equipo. Este comando importa la clave a HSM en el servicio Key Vault:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Esto permite generar la clave en el HSM local y transferirla al HSM en el servicio de Key Vault, sin que la clave salga del límite del HSM:

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminación del Almacén de claves junto con las claves y secretos asociados
Si ya no necesita la instancia de Key Vault ni la clave o el secreto que contiene, puede eliminarla con el comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name 'ContosoKeyVault'
```

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

```azurecli-interactive
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Otros comandos de la interfaz de la línea de comandos entre plataformas de Azure
Estos son otros comandos que pueden resultar útiles para administrar Azure Key Vault.

Este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

Este comando muestra una lista completa de propiedades para la clave especificada.

```azurecli-interactive
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

Ejemplo de cómo quitar una clave específica:

```azurecli-interactive
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

Ejemplo de cómo quitar un secreto específico:

```azurecli-interactive
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>Pasos siguientes

- Para ver una referencia completa de la CLI de Azure para los comandos de Key Vault, consulte la [guía de referencia de la CLI de Key Vault](/cli/azure/keyvault).

- Para conocer las referencias de programación, consulte la [Guía del desarrollador de Azure Key Vault](key-vault-developers-guide.md).

- Para obtener información sobre Azure Key Vault y los módulos de seguridad de hardware (HSM), vea [Uso de claves protegidas con HSM mediante Azure Key Vault](key-vault-hsm-protected-keys.md).
