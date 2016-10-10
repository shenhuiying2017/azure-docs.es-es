<properties
   pageTitle="Administración de registros y conjuntos de registros en DNS de Azure con la CLI de Azure | Microsoft Azure"
   description="Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de CLI para operaciones en conjuntos de registros y registros."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# Administración de registros y conjuntos de registros DNS mediante la CLI


> [AZURE.SELECTOR]
- [Portal de Azure](dns-operations-recordsets-portal.md)
- [CLI de Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


En este artículo se explica cómo administrar conjuntos de registros y registros de zonas DNS con la interfaz de la línea de comandos (CLI) de Azure multiplataforma.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona con el mismo nombre y el mismo tipo. Para más información, consulte [Descripción de los registros y los conjuntos de registros](dns-getstarted-create-recordset-cli.md).


## Configuración de la CLI de Azure multiplataforma

DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. No tiene una instancia de Azure Service Management API. Asegúrese de que la CLI de Azure está configurada para utilizar el modo de Resource Manager mediante el comando `azure config mode arm`.

Si ve "**error: 'dns' is not an azure command**", lo más probable es que se deba a que está usando CLI de Azure en modo Azure Service Management, no en el modo de Resource Manager.

## Creación de un nuevo conjunto de registros y un registro

Para crear un conjunto de registros en el Portal de Azure, consulte [Creación de conjuntos de registros y registros de DNS con CLI](dns-getstarted-create-recordset-cli.md).


## Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use `azure network dns record-set show`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de registros y el tipo de registro. Use el ejemplo siguiente y reemplace los valores por los suyos propios.

	azure network dns record-set show myresourcegroup contoso.com www A


## Enumeración de conjuntos de registros

Puede mostrar todos los registros de una zona DNS con el comando `azure network dns record-set list`. En ambos casos deberá especificar el nombre del grupo de recursos y el nombre de zona.

### Para mostrar todos los conjuntos de registros

Este ejemplo devolverá todos los conjuntos de registros, independientemente del nombre o tipo de registro:

	azure network dns record-set list myresourcegroup contoso.com

### Para mostrar los conjuntos de registros de un tipo determinado

Este ejemplo devuelve todos los conjuntos de registros que coinciden con el tipo de registro determinado (en este caso, los registros "A"):

	azure network dns record-set list myresourcegroup contoso.com A


## Incorporación de un registro a un conjunto de registros

Los registros se agregan a los conjuntos de registros mediante el comando `azure network dns record-set add-record`. Los parámetros para agregar registros a un conjunto de registros varían según el tipo de registro que se está configurando. Por ejemplo, cuando se utiliza un conjunto de registros de tipo "A", solo puede especificar registros con el parámetro `-a <IPv4 address>`.

Para crear un conjunto de registros, use el comando `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de recursos, el tipo de registro y el período de vida (TTL). Si no se define el parámetro `--ttl`, el valor predeterminado (en segundos) es 4.

	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Después de crear el conjunto de registros "A", agregue la dirección IPv4 utilizando el comando `azure network dns record-set add-record`.

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


En los ejemplos siguientes se muestra cómo crear un conjunto de registros de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[AZURE.INCLUDE [incluir-incorporación-dns-cli](../../includes/dns-add-record-cli-include.md)]


## Actualización de un registro en un conjunto de registros

### Para agregar otra dirección IP (1.2.3.4) a un conjunto de registros A existente (www):

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK

### Para quitar un valor existente de un conjunto de registros
Use `azure network dns record-set delete-record`.

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:    IPv4 address                    : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Eliminación de un registro de un conjunto de registros

Los registros pueden eliminarse de un conjunto de registros con `azure network dns record-set delete-record`. Todos los parámetros del registro que se va a eliminar deben coincidir exactamente con los del registro existente.

Al eliminar el último registro de un conjunto de registros, no se elimina el conjunto de registros. Consulte la sección [Eliminación de un conjunto de registros](#delete) de este artículo para más información.

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Eliminación de un registro AAAA de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Eliminación de un registro CNAME de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### Eliminación de un registro MX de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Eliminación de un registro NS de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Eliminación de un registro PTR de un conjunto de registros
En este caso, 'my-arpa-zone.com' representa la zona ARPA que representa el intervalo IP. Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP.

	azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### Eliminación de un registro SRV de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### Eliminación de un registro TXT de un conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Eliminación de un conjunto de registros

Los conjuntos de registros pueden eliminarse mediante el cmdlet `Remove-AzureRmDnsRecordSet`. No se pueden eliminar los conjuntos de registros SOA ni NS en el vértice de zona (nombre = "@") que se crearon automáticamente cuando se creó la zona. Se eliminarán automáticamente si se elimina la zona.

En el ejemplo siguiente, el conjunto de registros A "test-a" se eliminará de la zona DNS contoso.com:

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

Se puede usar el modificador opcional *-q* para suprimir el mensaje de confirmación.


## Pasos siguientes

Para obtener más información sobre DNS de Azure, consulte [Introducción a DNS de Azure](dns-overview.md). Para más información acerca de la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).

Si desea trabajar con registros DNS inversos, consulte [Administración de registros de DNS inversos para los servicios mediante la CLI de Azure](dns-reverse-dns-record-operations-cli.md).

<!---HONumber=AcomDC_0928_2016-->