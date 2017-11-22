---
title: "Configuración de un nombre de dominio personalizado para el punto de conexión de Azure Blob Storage | Microsoft Docs"
description: "Utilice Azure Portal para asignar su propio nombre canónico (CNAME) al punto de conexión de Blob Storage en una cuenta de Azure Storage."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 4dfca37e3a369fdfb41b4064b72ccd027bd8cbd3
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2017
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar un nombre de dominio personalizado para el punto de conexión de Almacenamiento de blobs

Puede configurar un dominio personalizado para obtener acceso a los datos Blob en la cuenta de almacenamiento de Azure. El punto de conexión predeterminado del almacenamiento de blobs es `<storage-account-name>.blob.core.windows.net`. Si asigna un subdominio y un dominio personalizado, como **www.contoso.com**, al punto de conexión del blob para la cuenta de almacenamiento, entonces los usuarios pueden obtener acceso a los datos de blob en la cuenta de almacenamiento a través de ese dominio.

> [!IMPORTANT]
> Azure Storage no admite aún HTTPS con dominios personalizados de forma nativa. Actual mente puede [usar la red CDN de Azure para obtener acceso a blobs con dominios personalizados a través de HTTPS](storage-https-custom-domain-cdn.md).
>

La siguiente tabla muestra algunas direcciones URL de ejemplo para datos de blob ubicados en una cuenta de almacenamiento denominada **mystorageaccount**. El dominio personalizado registrado para la cuenta de almacenamiento es **www.contoso.com**:

| Tipo de recurso | Dirección URL predeterminada | URL de dominio personalizado |
| --- | --- | --- |
| Cuenta de almacenamiento | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Contenedor raíz | http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob o http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Asignación de dominios directa frente a intermedia

Hay dos maneras de dirigir su dominio personalizado al punto de conexión de blob para la cuenta de almacenamiento: la asignación directa de CNAME y mediante el subdominio intermedio *asverify*.

### <a name="direct-cname-mapping"></a>Asignación directa de CNAME

El primer método, y el más simple, es crear un registro de nombre canónico (CNAME) que se asigne el dominio y el subdominio personalizados directamente al punto de conexión del blob. Un registro CNAME es una característica del sistema de nombres de dominio (DNS) que asigna un dominio de origen a un dominio de destino. En este caso, el dominio de origen es su propio dominio y subdominio personalizados, por ejemplo, *www.contoso.com*. El dominio de destino es su punto de conexión de Blob service, por ejemplo, *mystorageaccount.blob.core.windows.net*.

El método directo se trata en [Registro de un dominio personalizado](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Asignación intermedia con *asverify*

El segundo método también utiliza los registros CNAME, pero primero emplea un subdominio especial reconocido por Azure para evitar tiempos de inactividad: **asverify**.

El proceso de asignación de un dominio personalizado a un punto de conexión de blob puede provocar un período breve de inactividad en el dominio mientras lo registra [Azure Portal](https://portal.azure.com). Si el dominio personalizado ya incluye una aplicación con un acuerdo de nivel de servicio (SLA) que requiere que no exista tiempo de inactividad, puede usar el subdominio *asverify* de Azure como paso de registro intermedio. Este paso intermedio garantiza que los usuarios puedan obtener acceso al dominio mientras se realiza la asignación de DNS.

El método intermedio se trata en [Registro de un dominio personalizado mediante el subdominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registro de un dominio personalizado
Use este procedimiento para registrar el dominio personalizado si no le preocupa que el dominio deje de estar disponible brevemente para sus usuarios o si el dominio personalizado no hospeda actualmente una aplicación. Puede utilizar Azure DNS para configurar un nombre DNS personalizado para el almacén de blobs de Azure. Para más información, consulte [Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Si el dominio personalizado ya incluye una aplicación que no puede tener ningún tiempo de inactividad, use el procedimiento descrito en [Registro de un dominio personalizado mediante el subdominio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

Para configurar un nombre de dominio personalizado, debe crear un nuevo registro CNAME en DNS. El registro CNAME especifica un alias para un nombre de dominio. En este caso, asigna la dirección del dominio personalizado al punto de conexión de Blob Storage para la cuenta de almacenamiento.

Por lo general, puede administrar la configuración de DNS de su dominio en el sitio web del registrador de su dominio. Cada registrador dispone de un método similar pero ligeramente distinto de especificación de un registro CNAME. Sin embargo, el concepto es el mismo. Algunos paquetes de registro de domino básicos no ofrecen configuración DNS, por lo que puede que sea necesario actualizar el paquete de registro de dominios antes de crear el registro CNAME.

1. Vaya a la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).
1. En **BLOB SERVICE** en la hoja de menú, seleccione **Dominio personalizado** para abrir la hoja *Dominio personalizado*.
1. Inicie sesión en el sitio web del registrador de su dominio y vaya a la página de administración de DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
1. Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.
1. Cree un nuevo registro CNAME y proporcione un alias de subdominio como **www** o **fotos**. A continuación, proporcione un nombre de host, que es el punto de conexión de Blob service, en el formato **mystorageaccount.blob.core.windows.net** (donde *mystorageaccount* es el nombre de la cuenta de almacenamiento). El nombre de host que se usará parece en el elemento n.º 1 de la hoja *Dominio personalizado* en [Azure Portal](https://portal.azure.com).
1. En el cuadro de texto de la hoja *Dominio personalizado* en [Azure Portal](https://portal.azure.com), escriba el nombre de su dominio personalizado, incluido el subdominio. Por ejemplo, si su dominio es **contoso.com** y su alias de subdominio es **www**, escriba **www.contoso.com**. Si su subdominio es **fotos**, escriba **fotos.contoso.com**. El subdominio es *obligatorio*.
1. Seleccione **Guardar** en la hoja *Dominio personalizado* hoja para registrar su dominio personalizado. Si el registro se realiza correctamente, verá una notificación del portal que indica que la cuenta de almacenamiento se actualizó correctamente.

Una vez que el nuevo registro CNAME se propaga a través de DNS, los usuarios pueden ver datos de blob mediante su dominio personalizado, siempre y cuando tengan los permisos adecuados.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registro de un dominio personalizado mediante el subdominio *asverify*
Use este procedimiento para registrar el dominio personalizado si este es actualmente compatible con una aplicación con un contrato de nivel de servicio que requiera que no exista tiempo de inactividad. Mediante la creación de un CNAME que dirija de `asverify.<subdomain>.<customdomain>` a `asverify.<storageaccount>.blob.core.windows.net`, puede registrar previamente el dominio en Azure. Luego, puede crear un segundo CNAME que dirija de `<subdomain>.<customdomain>` a `<storageaccount>.blob.core.windows.net`; en este punto el tráfico a su dominio personalizado se dirigirá al punto de conexión del blob.

El subdominio **asverify** es un subdominio especial reconocido por Azure. Si antepone `asverify` a su propio subdominio, permite que Azure reconozca el dominio personalizado sin modificar el registro DNS para el dominio. Cuando modifique el registro DNS para el dominio, se asignará al punto de conexión del blob sin que exista tiempo de inactividad.

1. Vaya a la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).
1. En **BLOB SERVICE** en la hoja de menú, seleccione **Dominio personalizado** para abrir la hoja *Dominio personalizado*.
1. Inicie sesión en el sitio web del proveedor de DNS y vaya a la página de administración de DNS. Podría encontrarlo en una sección como **Nombre de dominio**, **DNS** o **Administración del servidor de nombres**.
1. Busque la sección para la administración de CNAME. Tiene que dirigirse a la página de configuración avanzada y buscar las palabras **CNAME**, **Alias** o **Subdomains**.
1. Cree un nuevo registro CNAME y proporcione un alias de subdominio que incluya el subdominio *asverify*. Por ejemplo, **asverify.www** o **asverify.fotos**. A continuación, proporcione un nombre de host, que es el punto de conexión de Blob service, en el formato **asverify.mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de la cuenta de almacenamiento). El nombre de host que se usará parece en el elemento n.º 2 de la hoja *Dominio personalizado* en [Azure Portal](https://portal.azure.com).
1. En el cuadro de texto de la hoja *Dominio personalizado* en [Azure Portal](https://portal.azure.com), escriba el nombre de su dominio personalizado, incluido el subdominio. No incluya *asverify*. Por ejemplo, si su dominio es **contoso.com** y su alias de subdominio es **www**, escriba **www.contoso.com**. Si su subdominio es **fotos**, escriba **fotos.contoso.com**. El subdominio es obligatorio.
1. Seleccione la casilla **Usar validación CNAME indirecta**.
1. Seleccione **Guardar** en la hoja *Dominio personalizado* hoja para registrar su dominio personalizado. Si el registro se realiza correctamente, verá una notificación del portal que indica que la cuenta de almacenamiento se actualizó correctamente. En este momento, Azure ha comprobado el dominio personalizado, pero no se ha realizado el enrutamiento del tráfico al dominio en la cuenta de almacenamiento.
1. Vuelva al sitio web del proveedor de DNS y cree otro registro CNAME que asigne su subdominio al punto de conexión de Blob service. Por ejemplo, especifique el subdominio como **www** o **fotos** (sin el *asverify*) y el nombre de host como **mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de la cuenta de almacenamiento). Con este paso se completa el registro del dominio personalizado.
1. Finalmente, puede eliminar el registro CNAME que creó y que contiene el subdominio **asverify**, ya que solo era necesario como paso intermedio.

Una vez que el nuevo registro CNAME se propaga a través de DNS, los usuarios pueden ver datos de blob mediante su dominio personalizado, siempre y cuando tengan los permisos adecuados.

## <a name="test-your-custom-domain"></a>Prueba de un dominio personalizado

Para confirmar que el dominio personalizado se haya asignado realmente al punto de conexión de Blob service, cree un blob en un contenedor público en la cuenta de almacenamiento. A continuación, en un explorador web, use un URI con el mismo formato para obtener acceso al blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por ejemplo, puede usar el siguiente URI para obtener acceso a un formulario web del contenedor **myforms** en el subdominio personalizado **fotos.contoso.com**:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Anulación del registro de un dominio personalizado

Para anular el registro de un dominio personalizado para su punto de conexión de Blob Storage, use uno de los procedimientos siguientes.

### <a name="azure-portal"></a>Portal de Azure

Para quitar la configuración de dominio personalizado en Azure Portal, haga lo siguiente:

1. Vaya a la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).
1. En **BLOB SERVICE** en la hoja de menú, seleccione **Dominio personalizado** para abrir la hoja *Dominio personalizado*.
1. Borre el contenido del cuadro de texto que contiene el nombre de dominio personalizado.
1. Seleccione el botón **Guardar**.

Cuando se haya quitado correctamente el dominio personalizado, verá una notificación del portal que indica que la cuenta de almacenamiento se actualizó correctamente.

### <a name="azure-cli-20"></a>CLI de Azure 2.0

Use el comando de CLI [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) y especifique una cadena vacía (`""`) para el valor del argumento `--custom-domain` para quitar un registro de dominio personalizado.

* Formato de comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Ejemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Use el cmdlet de PowerShell [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) y especifique una cadena vacía (`""`) para el valor del argumento `-CustomDomainName` para quitar un registro de dominio personalizado.

* Formato de comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Ejemplo de comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Pasos siguientes
* [Asignación de un dominio personalizado a un punto de conexión de Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Uso de la red CDN de Azure para obtener acceso a blobs con dominios personalizados a través de HTTPS](storage-https-custom-domain-cdn.md)
