---
title: "Administración de Azure Key Vault mediante CLI | Microsoft Docs"
description: Use este tutorial para automatizar tareas comunes en Key Vault mediante la CLI 2.0.
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Administración de Key Vault mediante CLI 2.0
Almacén de claves de Azure está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios del Almacén de claves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción
Use este tutorial para empezar a trabajar con el Almacén de claves de Azure para crear un contenedor (un almacén) reforzado en Azure en el que almacenar y administrar las claves criptográficas y los secretos en Azure. Se describe el proceso para usar la interfaz de la línea de comandos entre plataformas de Azure fin de crear un almacén en el que se guardará una clave o una contraseña para usarla con una aplicación de Azure. A continuación, se explica cómo utiliza una aplicación esa clave o contraseña.

**Tiempo estimado para completar el tutorial:** 20 minutos

> [!NOTE]
> Este tutorial no incluye instrucciones sobre cómo escribir la aplicación de Azure incluida en uno de los pasos, en el que se muestra cómo autorizar a una aplicación para que use una clave o un secreto del Almacén de claves.
>
> Este tutorial usa la CLI 2.0 de Azure más reciente.
>
>

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos
Para realizar este tutorial, necesitará lo siguiente:

* Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para obtener una versión de [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial).
* Versión 2.0 de la interfaz de la línea de comandos o posterior. Para instalar la última versión y conectarla a su suscripción de Azure, consulte el artículo sobre la [instalación y configuración de la interfaz de la línea de comandos 2.0 entre plataformas de Azure](/cli/azure/install-azure-cli).
* Una aplicación que se configurará para utilizar la clave o contraseña creada en este tutorial. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo Léame adjunto.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtención de ayuda con la interfaz de la línea de comandos entre plataformas de Azure
Este tutorial asume que está familiarizado con la interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema).

Los parámetros --help o -h se pueden usar para ver la ayuda de comandos específicos. También puede usar el formato azure help [command] [options] para devolver la misma información. Por ejemplo, todos los comandos siguientes devuelven la misma información:

```
az account set --help
az account set -h
```

Si duda sobre los parámetros que necesita un comando, consulte la ayuda usando --help, -h o az help [comando].

También puede leer los tutoriales siguientes para familiarizarse con el Administrador de recursos de Azure en la interfaz de la línea de comandos entre plataformas de Azure:

* [Instalación de la CLI de Azure](/cli/azure/install-azure-cli)
* [Introducción a la CLI de Azure 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Conectarse a sus suscripciones
Para iniciar sesión usando una cuenta profesional, utilice el comando siguiente:

```
az login -u username@domain.com -p password
```

o si desea iniciar sesión escribiendo interactivamente

```
az login
```

Si tiene varias suscripciones y desea especificar una en concreto para que use el Almacén de claves de Azure, escriba lo siguiente para ver las suscripciones de su cuenta:

```
az account list
```

A continuación, para especificar la suscripción que se debe usar, escriba:

```
az account set --subscription <subscription name or ID>
```

Para más información acerca de cómo configurar la interfaz de la línea de comandos entre plataformas de Azure, consulte el artículo sobre la [instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Creación de un nuevo grupo de recursos
Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado 'ContosoResourceGroup' para este tutorial.

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

El primer parámetro es el nombre del grupo de recursos y el segundo parámetro es la ubicación. Para la ubicación, use el comando `az account list-locations` para identificar cómo se debe especificar una ubicación alternativa a la usada en este ejemplo. Si necesita más información, escriba: `az account list-locations -h`.

## <a name="register-the-key-vault-resource-provider"></a>Registro del proveedor de recursos de Almacén de claves
Asegúrese de que el proveedor de recursos de Almacén de claves está registrado en la suscripción:

```
az provider register -n Microsoft.KeyVault
```

Esto solo se debe hacer una vez por suscripción.

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves
Utilice el comando `az keyvault create` para crear un Almacén de claves. Este script tiene tres parámetros obligatorios: el nombre del grupo de recursos, el nombre del Almacén de claves y la ubicación geográfica.

Por ejemplo, si utiliza el nombre del almacén de ContosoKeyVault, el nombre del grupo de recursos ContosoResourceGroup y la ubicación East Asia, deberá escribir:
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

El resultado de este comando muestra las propiedades del Almacén de claves que acaba de crear. Las dos propiedades más importantes son:

* **name**: en este ejemplo, el nombre es ContosoKeyVault. Utilizará este nombre para otros comandos de Key Vault.
* **vaultUri**: en este ejemplo es https://contosokeyvault.vault.azure.net. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adición de una clave o un secreto al Almacén de claves
Si desea que el Almacén de claves de Azure cree una clave protegida mediante software, utilice el comando `az key create` y escriba lo siguiente:
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
Sin embargo, si tiene una clave existente en un archivo .pem guardado como archivo local en un archivo denominado softkey.pem que desea cargar en el Almacén de claves de Azure, escriba lo siguiente para importar la clave desde el archivo .PEM, que protege la clave de software en el servicio de Almacén de claves:
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
Ahora puede utilizar el URI para hacer referencia a la clave que creó o cargó en el Almacén de claves de Azure. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obtener siempre la versión actual y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión concreta.

Para agregar un secreto, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, al Almacén de claves de Azure, escriba lo siguiente:
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
Ahora puede hacer referencia a esta clave que agregó al Almacén de claves de Azure utilizando su URI. Utilice **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión concreta.

Veamos la clave o el secreto que acaba de crear:

* Para ver la clave, escriba: `az keyvault key list --vault-name 'ContosoKeyVault'`
* Para ver el secreto, escriba: `az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registro de una aplicación con Azure Active Directory
Este paso lo haría normalmente un programador en un equipo independiente. No es específico del Almacén de claves de Azure, pero se incluye aquí a fin de que la información ofrecida sea lo más completa posible.

> [!IMPORTANT]
> Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure.
>
>

Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. Para ello, el propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:

* Un **identificador de la aplicación** (también conocido como un identificador de cliente) y una **clave de autenticación** (también conocida como secreto compartido). Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La forma en que se configura la aplicación para ello depende de la aplicación. Para la aplicación de ejemplo del Almacén de claves, el propietario de la aplicación establece estos valores en el archivo app.config.

Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el Portal de Azure.
2. A la izquierda, haga clic en **Azure Active Directory** y seleccione el directorio en el que va a registrar la aplicación. <br> <br> 

> [!Note] 
> Debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que creó la instancia de Key Vault. Si no sabe qué directorio es, haga clic en **Configuración**, identifique la suscripción con la que creó la instancia de Key Vault y anote el nombre del directorio que se muestra en la última columna.

3. Haga clic en **Aplicaciones**. Si no se han agregado aplicaciones a su directorio, esta página muestra solo el vínculo **Agregar una aplicación** . Haga clic en el vínculo, o como alternativa, puede hacer clic en **Agregar** en la barra de comandos.
4. En el Asistente para **agregar aplicaciones**, en la página **¿Qué desea hacer?**, haga clic en **Agregar una aplicación que mi organización está desarrollando**.
5. En la página **Proporcione información sobre su aplicación**, especifique un nombre para la aplicación y seleccione **Aplicación web y/o API web** (valor predeterminado). Haga clic en el icono Siguiente.
6. En la página **Agregar propiedades**, especifique los valores de **URL de inicio de sesión** y **URI de id. de aplicación** para la aplicación web. Si la aplicación no tiene estos valores, puede inventárselos en este paso (por ejemplo, puede especificar http://test1.contoso.com en ambos cuadros). No importa si estos sitios existen; lo importante es que el URI del identificador de la aplicación de cada aplicación sea diferente en cada aplicación del directorio. El directorio utiliza esta cadena para identificar la aplicación.
7. Haga clic en el icono Completar para guardar los cambios en el Asistente.
8. En la página de inicio rápido, haga clic en **Configurar**.
9. Desplácese hasta la sección de **claves**, seleccione la duración y haga clic en **Guardar**. La página se actualiza y muestra ahora un valor de clave. Debe configurar la aplicación con este valor de clave y el valor del **identificador del cliente** . (Las instrucciones para realizar esta configuración serán específicas para la aplicación).
10. Copie el valor del identificador del cliente en esta página. Lo utilizará en el paso siguiente para definir los permisos del almacén.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorización de la aplicación para que use la clave o el secreto
Para que la aplicación pueda acceder a la clave o el secreto en el almacén, use el comando `az keyvault set-policy` .

Por ejemplo, si el nombre del almacén es ContosoKeyVault y la aplicación que desea autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute lo siguiente:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si desea utilizar un módulo de seguridad de hardware (HSM)
Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para crear estas claves protegidas con HSM, debe contar con una suscripción de almacén que admita claves protegidas mediante HSM.

Cuando cree el almacén de claves, agregue el parámetro 'sku':

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
A este almacén, se pueden agregar claves protegidas mediante software (tal como se ha mostrado anteriormente) y claves protegidas con HSM. Para crear una clave protegida con HSM, establezca el parámetro Destination en 'HSM':

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Puede utilizar el siguiente comando para importar una clave desde un archivo .pem a su equipo. Este comando importa la clave a HSM en el servicio de Almacén de claves:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Esto permite generar la clave en el HSM local y transferirla al HSM en el servicio del Almacén de claves, sin que la clave salga del límite del HSM:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para el Almacén de claves de Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminación del Almacén de claves junto con las claves y secretos asociados
Si ya no necesita la instancia de Key Vault ni la clave o el secreto que contiene, puede eliminarla con el comando `az keyvault delete`:

```
az keyvault delete --name 'ContosoKeyVault'
```

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Otros comandos de la interfaz de la línea de comandos entre plataformas de Azure
Estos son otros comandos que pueden resultar útiles para administrar el Almacén de claves de Azure.

Este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.

az keyvault key list --vault-name 'ContosoKeyVault'

Este comando muestra una lista completa de propiedades para la clave especificada.

az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.

az keyvault secret list --vault-name 'ContosoKeyVault'

Ejemplo de cómo quitar una clave específica:

az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Ejemplo de cómo quitar un secreto específico:

az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'


## <a name="next-steps"></a>Pasos siguientes
Para ver una referencia completa de CLI de Azure para los comandos de Key Vault, consulte la [guía de referencia de la CLI de Key Vault](/cli/azure/keyvault).

Para conocer las referencias de programación, consulte la [Guía del desarrollador del Almacén de claves de Azure](key-vault-developers-guide.md).
