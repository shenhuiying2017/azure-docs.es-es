---
title: Cifrado de discos en una VM de Linux en Azure | Microsoft Docs
description: "Cómo cifrar discos en una máquina virtual Linux mediante la CLI de Azure y el modelo de implementación de Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 97dd91986751031daef24fc806adc7021b2f94fc
ms.lasthandoff: 01/31/2017


---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Cifrado de discos en una máquina virtual Linux mediante la CLI de Azure
Para mejorar la seguridad y el cumplimiento de las máquinas virtuales, los discos virtuales en Azure se pueden cifrar en reposo. Los discos se cifran mediante claves criptográficas que están protegidas en Azure Key Vault. Estas claves criptográficas se pueden controlar y se puede auditar su uso. En este artículo se explica cómo cifrar discos virtuales en una máquina virtual Linux mediante la CLI de Azure y el modelo de implementación de Resource Manager.

## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, en la siguiente sección se detallan los comandos base para cifrar discos virtuales en la máquina virtual. Se puede encontrar información más detallada y contexto para cada paso en el resto del documento, [comenzando aquí](#overview-of-disk-encryption).

Necesita tener instalada la [CLI de Azure más reciente](../xplat-cli-install.md) y haber iniciado sesión con el modo Resource Manager de la forma siguiente:

```azurecli
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetros de ejemplo incluyen `myResourceGroup`, `myKeyVault` y `myVM`.

En primer lugar, habilite el proveedor de Azure Key Vault en su suscripción de Azure y cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Cree un almacén de Azure Key Vault. En el ejemplo siguiente se crea un almacén de Azure Key Vault llamado `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Cree una clave criptográfica en su almacén de Key Vault y habilítela para el cifrado de discos. En el ejemplo siguiente se crea una clave llamada `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Cree un punto de conexión mediante Azure Active Directory para controlar la autenticación y el intercambio de claves criptográficas desde Key Vault. No es necesario que `--home-page` y `--identifier-uris` sean direcciones enrutables reales. Para el nivel más alto de seguridad, deben usarse secretos de cliente en lugar de contraseñas. La CLI de Azure actualmente no puede generar secretos de cliente. Los secretos de cliente solo pueden generarse en Azure Portal. En el ejemplo siguiente se crea un punto de conexión de Azure Active Directory llamado `myAADApp` y se usa la contraseña `myPassword`. Especifique su propia contraseña del siguiente modo:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Anote el valor de `applicationId` que se muestra en la salida del comando anterior. Este identificador de aplicación se usa en los pasos siguientes:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Agregue un disco de datos a una máquina virtual existente. En el ejemplo siguiente se agrega un disco de datos a una máquina virtual llamada `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Revise los detalles de su almacén de Key Vault y la clave que creó. Necesitará el identificador del almacén de Key Vault, el URI y la dirección URL de la clave en el paso final. El ejemplo siguiente revisa los detalles de un almacén de Key Vault llamado `myKeyVault` y de la clave llamada `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Cifre los discos como se indica a continuación, con sus propios parámetros:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

La CLI de Azure no proporciona errores detallados durante el proceso de cifrado. Para obtener información adicional para la solución de problemas, consulte `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Como el comando anterior tiene muchas variables y es posible que no obtenga muchas indicaciones de por qué se produce un error en el proceso, un ejemplo de comando completo sería este:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Por último, vuelva a revisar el estado del cifrado para confirmar que los discos virtuales ahora están cifrados. En el ejemplo siguiente se comprueba el estado de una máquina virtual llamada `myVM` en el grupo de recursos `myResourceGroup`:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Introducción al cifrado de discos
Los discos virtuales en máquinas virtuales Linux se cifran en reposo mediante [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). El cifrado de los discos virtuales en Azure no conlleva ningún cargo. Las claves criptográficas se almacenan en Azure Key Vault con protección de software, o puede importar o generar las claves en módulos de seguridad de hardware (HSM) certificados conforme a las normas FIPS 140-2 de nivel 2. Estas claves criptográficas se pueden controlar y se puede auditar su uso. Las claves criptográficas se usan para cifrar y descifrar los discos virtuales conectados a la máquina virtual. Como las máquinas virtuales se encienden y se apagan, los puntos de conexión de Azure Active Directory ofrecen un mecanismo seguro para la emisión de estas claves criptográficas.

El proceso para cifrar una máquina virtual es el siguiente:

1. Cree una clave criptográfica en Azure Key Vault.
2. Configure la clave criptográfica para poder utilizarla para el cifrado de discos.
3. Para leer la clave criptográfica de Azure Key Vault, cree un punto de conexión mediante Azure Active Directory con los permisos adecuados.
4. Emita el comando para cifrar los discos virtuales y especifique el punto de conexión de Azure Active Directory y la clave criptográfica que se deberá utilizar.
5. El punto de conexión de Azure Active Directory solicita la clave criptográfica necesaria a Azure Key Vault.
6. Los discos virtuales se cifran con la clave criptográfica proporcionada.

## <a name="supporting-services-and-encryption-process"></a>Servicios de soporte y proceso de cifrado
El cifrado de discos utiliza los siguientes componentes adicionales:

* **Azure Key Vault**: se usa para proteger las claves criptográficas y los secretos usados para el proceso de cifrado y descifrado de discos. 
  * Si ya existe un almacén de Azure Key Vault, puede utilizarlo. No es necesario dedicar un almacén de Key Vault para el cifrado de discos.
  * Para separar los límites administrativos y la visibilidad de las claves, puede crear un almacén de Key Vault dedicado.
* **Azure Active Directory**: controla el intercambio seguro de las claves cifradas necesarias y la autenticación para las acciones solicitadas. 
  * Normalmente, puede usar un almacén existente de Azure Active Directory para hospedar la aplicación. 
  * La aplicación es básicamente un punto de conexión para que los servicios Key Vault y Virtual Machines soliciten y obtengan las claves criptográficas apropiadas. No está desarrollando una aplicación real que se integrará con Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
Requisitos y escenarios admitidos para el cifrado de discos:

* Las siguientes SKU de servidor Linux: Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES), y Red Hat Enterprise Linux.
* Todos los recursos (por ejemplo: almacén de Key Vault, cuenta de almacenamiento, máquina virtual, etc.) deben pertenecer a la misma región y suscripción de Azure.
* Máquinas virtuales estándar de las series A, D, DS, G y GS.

El cifrado del disco no se admite actualmente en los siguientes escenarios:

* Máquina s virtuales de nivel básico
* Máquinas virtuales creadas con el modelo de implementación clásica.
* Deshabilitado del cifrado de disco de sistema operativo en máquinas virtuales Linux.
* Actualización de las claves criptográficas en una máquina virtual Linux ya cifrada.

## <a name="create-the-azure-key-vault-and-keys"></a>Creación del almacén de Azure Key Vault y las claves
Para completar el resto de esta guía, necesita tener instalada la [CLI de Azure más reciente](../xplat-cli-install.md) y haber iniciado sesión con el modo Resource Manager de la forma siguiente:

```azurecli
azure config mode arm
```

En todos los ejemplos de comandos, reemplace todos los parámetros de ejemplo por sus propios nombres, ubicaciones y valores de clave. Los ejemplos siguientes usan la convención `myResourceGroup`, `myKeyVault`, `myAADApp`, etc.

El primer paso es crear un almacén de Azure Key Vault para almacenar las claves criptográficas. Azure Key Vault puede almacenar claves, secretos o contraseñas que permiten su implementación segura en las aplicaciones y los servicios. Para el cifrado de discos virtuales, se usa Key Vault para almacenar una clave criptográfica que se utiliza para cifrar o descifrar los discos virtuales. 

Habilite el proveedor de Azure Key Vault en su suscripción de Azure y cree un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la ubicación `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

El almacén de Azure Key Vault que contiene las claves criptográficas y los recursos de proceso asociados, como el almacenamiento y la propia máquina virtual, debe residir en la misma región. En el ejemplo siguiente se crea un almacén de Azure Key Vault llamado `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Puede almacenar las claves criptográficas mediante software o protección del modelo de seguridad de hardware (HSM). Para usar HSM se necesita un almacén premium de Key Vault. La creación de un almacén premium de Key Vault conlleva un coste, frente al almacén estándar de Key Vault, que almacena las claves protegidas por software. Para crear un almacén de Key Vault premium, en el paso anterior, agregue `--sku Premium` al comando. En el ejemplo siguiente se usa claves protegidas por software ya que hemos creado un almacén de Key Vault estándar. 

En ambos modelos de protección, la plataforma Windows Azure debe tener acceso para solicitar las claves criptográficas cuando la máquina virtual arranca para descifrar los discos virtuales. Cree una clave de cifrado en su almacén de Key Vault y habilítela para el cifrado de discos virtuales. En el ejemplo siguiente se crea una clave llamada `myKey` y, después, se habilita para el cifrado de discos:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Creación de la aplicación de Azure Active Directory
Cuando los discos virtuales se cifran o descifran, se usa un punto de conexión para controlar la autenticación y el intercambio de claves criptográficas desde el almacén de Key Vault. Este punto de conexión, una aplicación de Azure Active Directory, permite a la plataforma de Azure solicitar las claves criptográficas adecuadas en nombre de la máquina virtual. Su suscripción dispone de una instancia de Azure Active Directory predeterminada, aunque muchas organizaciones tienen directorios de Azure Active Directory dedicados.

Como no está creando una aplicación de Azure Active Directory completa, no es necesario que los parámetros `--home-page` y `--identifier-uris` del ejemplo siguiente sean direcciones enrutables reales. El ejemplo siguiente también especifica un secreto basado en contraseña, en lugar de generar claves desde dentro de Azure Portal. En este momento, la generación de claves no puede realizarse desde la CLI de Azure. 

Cree una aplicación de Azure Active Directory. En el ejemplo siguiente se crea una aplicación llamada `myAADApp` y se usa la contraseña `myPassword`. Especifique su propia contraseña del siguiente modo:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Tome nota del valor de `applicationId` que se devuelve en la salida del comando anterior. Este identificador de aplicación se usará en algunos de los pasos restantes. A continuación, cree un nombre principal de servicio (SPN) para que la aplicación sea accesible desde su entorno. Para cifrar o descifrar los discos virtuales correctamente, debe establecer los permisos de la clave criptográfica que se almacena en el almacén de Key Vault para que permitan que la aplicación de Azure Active Directory lea las claves. 

Cree el SPN y establezca los permisos adecuados de la siguiente manera:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Incorporación de un disco virtual y revisión del estado del cifrado
Para cifrar realmente algunos discos virtuales, vamos a agregar un disco a una máquina virtual existente. Agregue un disco de datos de 5 GB a una máquina virtual existente, de la siguiente manera:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Los discos virtuales no están cifrados actualmente. Revise el estado actual del cifrado de la máquina virtual, de la siguiente manera:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Cifrado de discos virtuales
Para cifrar los discos virtuales, reúna todos los componentes anteriores:

1. Especifique una aplicación de Azure Active Directory y una contraseña.
2. Especifique el almacén de Key Vault donde se almacenarán los metadatos de los discos cifrados.
3. Especifique las claves criptográficas que se utilizarán para el cifrado y descifrado.
4. Especifique si desea cifrar el disco del sistema operativo, los discos de datos o todos.

Vamos a revisar los detalles del almacén de Azure Key Vault y la clave que creó, porque necesita el identificador de almacén de Key Vault, el URI y la dirección URL de la clave en el paso final:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Cifre los discos virtuales con los valores obtenidos de los comandos `azure keyvault show` y `azure keyvault key show`, como se indica a continuación:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como el comando anterior tiene muchas variables, el siguiente es el comando completo como referencia:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

La CLI de Azure no proporciona errores detallados durante el proceso de cifrado. Para obtener información adicional para la solución de problemas, consulte `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` en la máquina virtual que está cifrando.

Por último, vamos a revisar el estado del cifrado para confirmar que los discos virtuales ahora están cifrados:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Adición de discos de datos adicionales
Una vez cifrados los discos de datos, más adelante podrá agregar más discos virtuales a la máquina virtual, y también cifrarlos. Al ejecutar el comando `azure vm enable-disk-encryption`, se incrementa la versión de la secuencia mediante el parámetro `--sequence-version`. Este parámetro de versión de secuencia permite realizar operaciones repetidas en la misma máquina virtual.

Por ejemplo, vamos a agregar un segundo disco virtual a la máquina virtual de la siguiente manera:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Vuelva a ejecutar el comando para cifrar los discos virtuales, esta vez con el parámetro `--sequence-version`, e incremente el valor de la primera ejecución de la siguiente manera:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Pasos siguientes
* Para más información acerca de cómo administrar Azure Key Vault, incluido cómo eliminar claves criptográficas y almacenes, consulte [Administración de Key Vault mediante CLI](../key-vault/key-vault-manage-with-cli.md).
* Para obtener más información acerca del cifrado de discos, por ejemplo, cómo preparar una máquina virtual personalizada cifrada para cargar en Azure, consulte [Azure Disk Encryption](../security/azure-security-disk-encryption.md).


