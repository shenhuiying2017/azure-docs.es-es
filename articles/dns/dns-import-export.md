---
title: "Importación y exportación de un archivo de zona de dominio en DNS de Azure mediante la CLI de Azure 1.0 | Microsoft Docs"
description: Aprenda a importar y exportar un archivo de zona DNS en DNS de Azure mediante la CLI de Azure 1.0
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d6d3fa7aa0e8b2462b3a6b4b66d3d87ab5535314
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-10"></a>Importación y exportación de un archivo de zona DNS mediante la CLI de Azure 1.0 

Este artículo le guiará a través de la importación y exportación de archivos de zona DNS para DNS de Azure con la CLI de Azure 1.0.

## <a name="introduction-to-dns-zone-migration"></a>Introducción a la migración de zona DNS

Un archivo de zona DNS es un archivo de texto que contiene los detalles de cada registro DNS (Sistema de nombres de dominio) de la zona. Sigue un formato estándar, por lo que es adecuado para transferir registros DNS entre distintos sistemas DNS. Usar un archivo de zona es una manera rápida, confiable y cómoda de transferir una zona DNS a DNS de Azure o desde él.

DNS de Azure admite la importación y la exportación de archivos de zona mediante la interfaz de la línea de comandos (CLI) de Azure. La importación de archivos de zona **no** se permite actualmente mediante Azure PowerShell o el portal de Azure.

La CLI de Azure 1.0 es una herramienta de línea de comandos multiplataforma que se usa para administrar servicios de Azure. Está disponible para las plataformas Windows, Mac y Linux en la [página de descargas de Azure](https://azure.microsoft.com/downloads/). La compatibilidad multiplataforma es importante para la importación y la exportación de archivos de zona, porque el software de servidor de nombres más común, [BIND](https://www.isc.org/downloads/bind/), se suele ejecutar en Linux.

> [!NOTE]
> Actualmente hay dos versiones de la CLI de Azure. CLI1.0 se basa en Node.js y tiene comandos que comienzan por "azure".
> CLI2.0 se basa en Python y tiene comandos que empiezan por "az". Aunque se admite la importación de archivos de zona en ambas versiones, se recomienda utilizar los comandos CLI1.0, como se describe en esta página.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtención del archivo de zona DNS existente

Antes de importar un archivo de zona DNS a DNS de Azure, debe obtener una copia del archivo de zona. El origen de este archivo varía en función de dónde se hospede la zona DNS.

* Si la zona DNS se hospeda en un servicio de colaboradores (como un registrador de dominios, un proveedor de hospedaje DNS dedicado o un proveedor de nube alternativo), ese servicio debería ofrecer la posibilidad de descargar el archivo de zona DNS.
* Si la zona DNS se hospeda en DNS de Windows, la carpeta predeterminada para los archivos de zona es **%systemroot%\system32\dns**. También se muestra la ruta de acceso completa de cada archivo de zona en la pestaña **General** de la consola de DNS.
* Si la zona DNS se hospeda con BIND, la ubicación del archivo de zona para cada zona se especifica en el archivo de configuración de BIND **named.conf**.

> [!NOTE]
> Los archivos de zona descargados de GoDaddy tienen un formato ligeramente diferente al estándar. Debe corregir esto antes de importar estos archivos de zona a DNS de Azure.
>
> Se especifican los nombres DNS en el campo RDATA de cada registro DNS como nombres completos, pero no terminan en ".". Esto significa que otros sistemas DNS los interpretan como nombres relativos. Debe editar el archivo de zona para anexar el carácter final "." a los nombres antes de importarlos a DNS de Azure.
>
> Por ejemplo, el registro CNAME "www 3600 IN CNAME contoso.com" debe cambiarse a "www 3600 IN CNAME contoso.com."
> (con una terminación ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importación de un archivo de zona DNS a DNS de Azure

Si aún no existe, al importar un archivo de zona se crea una nueva zona DNS de Azure. Si la zona ya existe, los conjuntos de registros de la zona deben combinarse con los conjuntos de registros existentes.

### <a name="merge-behavior"></a>Comportamiento de combinación

* De forma predeterminada, se combinan los conjuntos de registros nuevos y existentes. Los registros idénticos dentro de un conjunto de registros combinado se desduplican.
* También puede especificar la opción `--force`, para que el proceso de importación reemplace los conjuntos de registros existentes por otros nuevos. os conjuntos de registros existentes que no tengan un registro correspondiente en el archivo de zona importado no se eliminan.
* Cuando se combinan conjuntos de registros, se usa el período de vida (TTL) de los conjuntos de registros existentes. Cuando se usa `--force`, se utiliza el TTL del nuevo conjunto de registros.
* Los parámetros de inicio de autoridad (SOA), a excepción de `host`, siempre se toman del archivo de zona importado, independientemente de si se usa `--force` o no. De forma similar, para el conjunto de registros de servidor de nombres en el ápice de zona, el TTL siempre se toma del archivo de zona importado.
* Un registro CNAME importado no reemplaza a un registro CNAME existente con el mismo nombre, a menos que se especifique el parámetro `--force`.
* Cuando surge un conflicto entre un registro CNAME y otro registro del mismo nombre pero de distinto tipo (sin importar cuál sea el existente y cuál el nuevo), se conserva el registro existente. Esto sucede con independencia del uso de `--force`.

### <a name="additional-information-about-importing"></a>Información adicional sobre la importación

En las notas siguientes, se ofrecen detalles técnicos adicionales sobre el proceso de importación de zona.

* La directiva `$TTL` es opcional y se admite. Cuando no se indica ninguna directiva `$TTL`, los registros sin TTL explícito se importan con un TTL predeterminado de 3600 segundos. Cuando dos registros del mismo conjunto de registros especifican diferentes TTL, se usa el valor más bajo.
* La directiva `$ORIGIN` es opcional y se admite. Cuando no se establece ninguna directiva `$ORIGIN` , el valor predeterminado que se usa es el nombre de la zona según lo especificado en la línea de comandos (terminado en ".").
* No se admiten las directivas `$INCLUDE` ni `$GENERATE`.
* Se admiten los tipos de registro siguientes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT.
* DNS de Azure crea automáticamente el registro SOA cuando se crea una zona. Cuando se importa un archivo de zona, todos los parámetros SOA se toman del archivo de zona *excepto* el parámetro `host`. Este parámetro usa el valor proporcionado por DNS de Azure. Esto se debe a que este parámetro debe hacer referencia al servidor de nombres principal proporcionado por DNS de Azure.
* DNS de Azure también crea automáticamente el conjunto de registros de servidor de nombres en el ápice de zona al crear la zona. Solo se importa el TTL de este conjunto de registros. Estos registros contienen los nombres de servidores de nombres proporcionados por DNS de Azure. No se sobrescriben los datos del registro con los valores contenidos en el archivo de zona importado.
* Durante la versión preliminar pública, DNS de Azure admite solamente registros TXT de cadena única. Los registros TXT multicadena se concatenan y se truncan tras 255 caracteres.

### <a name="cli-format-and-values"></a>Valores y formato de la CLI

El formato del comando de CLI de Azure para importar una zona DNS es:

```azurecli
azure network dns zone import [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>` es el nombre del grupo de recursos para la zona en DNS de Azure.
* `<zone name>` es el nombre de la zona.
* `<zone file name>` es la ruta de acceso y el nombre del archivo de zona que se va a importar.

Si no existe una zona con este nombre en el grupo de recursos, se crea automáticamente. Si la zona ya existe, los conjuntos de registros importados se combinan con conjuntos de registros existentes. Para sobrescribir los conjuntos de registros existentes, use la opción `--force` .

Para comprobar el formato de un archivo de zona sin importarlo, use la opción `--parse-only` .

### <a name="step-1-import-a-zone-file"></a>Paso 1. Importación de un archivo de zona

Para importar un archivo de zona para la zona **contoso.com**.

1. Inicie sesión en su suscripción de Azure mediante la CLI de Azure 1.0.

    ```azurecli
    azure login
    ```

2. Seleccione la suscripción en la que quiere crear la nueva zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure, así que se debe cambiar la CLI de Azure al modo del Administrador de recursos.

    ```azurecli
    azure config mode arm
    ```

4. Antes de usar el servicio DNS de Azure, debe registrar la suscripción para que utilice el proveedor de recursos Microsoft.Network. (Se trata de una operación única para cada suscripción).

    ```azurecli
    azure provider register Microsoft.Network
    ```

5. Si todavía no tiene uno, debe crear también un grupo de recursos de Resource Manager.

    ```azurecli
    azure group create myresourcegroup westeurope
    ```

6. Para importar la zona **contoso.com** del archivo **contoso.com.txt** a una nueva zona DNS en el grupo de recursos **myresourcegroup**, ejecute el comando `azure network dns zone import`.<BR>Con este comando se carga el archivo de zona y se analiza. El comando ejecuta una serie de comandos en el servicio DNS de Azure para crear la zona y todos los conjuntos de registros de la zona. El comando notifica el progreso en la ventana de la consola, junto con los errores o las advertencias. Puesto que los conjuntos de registros se crean en serie, puede tardar unos minutos en importar un archivo de zona de gran tamaño.

    ```azurecli
    azure network dns zone import myresourcegroup contoso.com contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>Paso 2: Comprobación de la zona

Para comprobar la zona DNS después de importar el archivo, puede utilizar cualquiera de los métodos siguientes:

* Puede mostrar una lista de los registros mediante el siguiente comando de CLI de Azure:

    ```azurecli
    azure network dns record-set list myresourcegroup contoso.com
    ```

* Puede enumerar los registros mediante el cmdlet de PowerShell `Get-AzureRmDnsRecordSet`.
* O puede utilizar `nslookup` para comprobar la resolución de nombres para los registros. Como la zona aún no está delegada, debe especificar explícitamente los servidores de nombres DNS de Azure correctos. En el ejemplo siguiente se muestra cómo recuperar los nombres de servidores de nombres asignados a la zona. También muestra cómo consultar el registro "www" mediante `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/.../resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Paso 3: Actualización de la delegación de DNS

Una vez que haya comprobado que la zona se importó correctamente, debe actualizar la delegación de DNS para que apunte a los servidores de nombres DNS de Azure. Para más información, consulte el artículo [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportación de un archivo de zona DNS de DNS de Azure

El formato del comando de CLI de Azure para importar una zona DNS es:

```azurecli
azure network dns zone export [options] <resource group> <zone name> <zone file name>
```

Valores:

* `<resource group>` es el nombre del grupo de recursos para la zona en DNS de Azure.
* `<zone name>` es el nombre de la zona.
* `<zone file name>` es la ruta de acceso y el nombre del archivo de zona que se va a exportar.

Al igual que con la importación de zona, en primer lugar necesita iniciar sesión, elegir su suscripción y configurar la CLI de Azure para que use el modo de Resource Manager.

### <a name="to-export-a-zone-file"></a>Para exportar un archivo de zona

1. Inicie sesión en su suscripción de Azure mediante la CLI de Azure.

    ```azurecli
    azure login
    ```

2. Seleccione la suscripción en la que desee crear la zona DNS.

    ```azurecli
    azure account set <subscription name>
    ```

3. DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. Se debe cambiar la CLI de Azure al modo del Administrador de recursos.

    ```azurecli
    azure config mode arm
    ```

4. Para exportar la zona DNS de Azure existente **contoso.com** en el grupo de recursos **myresourcegroup** al archivo **contoso.com.txt** (en la carpeta actual), ejecute `azure network dns zone export`. Este comando llama al servicio DNS de Azure para enumerar los conjuntos de registros de la zona y exportar los resultados a un archivo de zona compatible con BIND.

    ```azurecli
    azure network dns zone export myresourcegroup contoso.com contoso.com.txt
    ```
