---
title: "Delegación de dominios en DNS de Azure | Microsoft Docs"
description: "Información sobre cómo cambiar la delegación de dominios y usar los servidores de nombres DNS de Azure para ofrecer hospedaje de dominios."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 5145418159aa457be6d1fc9ed5bb1a43a955791c

---

# <a name="delegate-a-domain-to-azure-dns"></a>Delegación de un dominio en DNS de Azure

DNS de Azure le permite hospedar una zona DNS y administrar los registros DNS de un dominio en Azure. Para que las consultas DNS de un dominio lleguen a DNS de Azure, es necesario haber delegado dicho dominio en DNS de Azure desde el dominio primario. Tenga en cuenta que DNS de Azure no es el registrador de dominios. En este artículo se explica cómo funciona la delegación de dominios y cómo delegar dominios en DNS de Azure.

## <a name="how-dns-delegation-works"></a>Funcionamiento de la delegación de DNS

### <a name="domains-and-zones"></a>Dominios y zonas

El sistema de nombres de dominio es una jerarquía de dominios. La jerarquía empieza por el dominio "raíz", cuyo nombre es sencillamente "**.**".  Después de él, se encuentran los dominios de primer nivel, a saber, “com”, “net”, “org”, “uk” o “jp”.  A continuación, se colocan los dominios de segundo nivel, como “org.uk” o “co.jp”.  y así sucesivamente. Los dominios de la jerarquía de DNS se hospedan en zonas DNS independientes. Estas zonas están distribuidas globalmente, hospedadas por servidores de nombres DNS de todo el mundo.

**Zona DNS**

Un dominio es un nombre exclusivo dentro del sistema de nombres de dominio, como por ejemplo “contoso.com”. Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Por ejemplo, el dominio “contoso.com” puede contener una serie de registros DNS como “mail.contoso.com” (para un servidor de correo) y “www.contoso.com” (para un sitio web).

**Registrador de dominios**

Un registrador de dominios es una empresa que puede ofrecer nombres de dominio de Internet. Verificará si el dominio de Internet deseado se encuentra disponible para que pueda adquirirlo. Tras haber registrado el nombre de dominio, será el titular legal de dicho nombre. Si ya dispone de un dominio de Internet, utilizará al registrador de dominios actual para delegar en DNS de Azure.

> [!NOTE]
> Para obtener más información sobre quién posee un nombre de dominio determinado, o para obtener información sobre cómo comprar un dominio, consulte [Administración de dominios de Internet en Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Resolución y delegación

Existen dos tipos de servidores DNS:

* Un servidor DNS *autoritativo* hospeda zonas DNS. Solo responde a las consultas DNS de los registros de dichas zonas.
* Un servidor DNS *recursivo* no hospeda zonas DNS. Responde a todas las consultas DNS, para lo que realiza una llamada a los servidores DNS autoritativos a fin de recopilar los datos que necesita.

> [!NOTE]
> DNS de Azure proporciona un servicio DNS autoritativo.  No proporciona un servicio DNS recursivo.
>
> Los Servicios en la nube y las máquinas virtuales de Azure se configuran automáticamente para que utilicen un servicio DNS recursivo que se proporciona por separado como parte de la infraestructura de Azure.  Para más información sobre cómo cambiar esta configuración de DNS, consulte [Resolución de nombres de Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Los clientes DNS de equipos o dispositivos móviles suelen llamar a un servidor DNS recursivo para realizar todas las consultas DNS que las aplicaciones cliente necesitan.

Cuando un servidor DNS recursivo recibe una consulta para un registro DNS, como “www.contoso.com”, primero necesita encontrar el servidor de nombres que hospeda la zona para el dominio “contoso.com”. Para ello, empieza por los servidores de nombres raíz y, a partir de ahí, busca los servidores de nombres que hospedan la zona “com”. A continuación, consulta los servidores de nombres “com” para encontrar los servidores de nombres que hospedan la zona “contoso.com”.  Por último, puede consultar estos servidores de nombres para “www.contoso.com”.

A esto se le denomina la resolución del nombre DNS. En el sentido estricto, la resolución DNS incluye pasos adicionales, como seguir los CNAME, pero no es importante saber cómo funciona la delegación DNS.

¿Cómo “apunta” una zona primaria a los servidores de nombres de una zona secundaria? Se consigue usando un tipo especial de registro DNS, denominado registro NS (NS, por sus siglas en inglés, significa “servidor de nombres”). Por ejemplo, la zona raíz contiene registros NS para “com”, donde se muestran los servidores de nombres de la zona “com”. A su vez, la zona “com” contiene registros NS para “contoso.com”, donde se muestran los servidores de nombres para la zona “contoso.com”. A la acción que conlleva configurar los registros NS para una zona secundaria en una zona primaria se le denomina delegación de dominios.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

Cada delegación realmente contiene dos copias de los registros NS, una en la zona primaria que apunta a la secundaria y otra en la misma zona secundaria. La zona “contoso.com” contiene los registros NS para “contoso.com” (además de los registros NS de “com”). Estos registros se denominan registros NS autoritativos y se sitúan en la cúspide de la zona secundaria.

## <a name="delegating-a-domain-to-azure-dns"></a>Delegación de un dominio en DNS de Azure
Después de haber creado la zona DNS en DNS de Azure, es necesario configurar los registros NS en la zona primaria para que DNS de Azure se convierta en el origen de autoridad para la resolución de nombres de la zona correspondiente. Si se trata de dominios adquiridos a un registrador, este último ofrecerá la opción de configurar tales registros NS.

> [!NOTE]
> No tiene que poseer un dominio para poder crear una zona DNS con dicho nombre de dominio en DNS de Azure. No obstante, sí necesita disponer de un dominio para configurar la delegación en DNS de Azure con un registrador.

Por ejemplo, supongamos que adquiere el dominio “contoso.com” y que crea una zona con el nombre “contoso.com” en DNS de Azure. Como propietario del dominio, el registrador le ofrecerá la opción de configurar las direcciones del servidor de nombres (es decir, los registros NS) para el dominio. El registrador almacenará estos registros NS en el dominio primario, en este caso, “.com”. A los clientes de todo el mundo se les remitirá entonces al dominio en cuestión en la zona DNS de Azure al tratar de resolver registros DNS en “contoso.com”.

### <a name="finding-the-name-server-names"></a>Búsqueda de los nombres del servidor de nombres
Antes de poder delegar la zona DNS a DNS de Azure, primero debe conocer los nombres del servidor de nombres de la zona. El DNS de Azure asigna los servidores de nombres de un grupo cada vez que se crea una zona.

Para ver los servidores de nombres asignados a su zona, lo más sencillo es mediante el Portal de Azure.  En este ejemplo, a la zona “contoso.net” se le han asignado los servidores de nombres “ns1-01.azure-dns.com”, “ns2-01.azure-dns.net”, “ns3-01.azure-dns.org” y “ns4-01.azure-dns.info”’:

 ![Dns-nameserver](./media/dns-domain-delegation/viewzonens500.png)

El DNS de Azure crea automáticamente los registros NS autoritativos en la zona que contiene los servidores de nombres asignados.  Para ver los nombres del servidor de nombres mediante Azure PowerShell o CLI de Azure, simplemente necesita recuperar estos registros.

Con el uso de PowerShell de Azure, los registros NS autoritativos pueden obtenerse como sigue. Tenga en cuenta que el nombre del registro "@" se usa para referirse a registros en la cúspide de la zona.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName MyResourceGroup
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

El ejemplo siguiente es la respuesta.

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : MyResourceGroup
Ttl               : 3600
Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
RecordType        : NS
Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                    ns4-01.azure-dns.info}
Tags              : {}
```

También puede utilizar la CLI de Azure multiplataforma para recuperar los registros NS autoritativos y, por tanto, detectar los servidores de nombres asignados a la zona:

```azurecli
azure network dns record-set show MyResourceGroup contoso.net @ NS
```

El ejemplo siguiente es la respuesta.

```
info:    Executing command network dns record-set show
    + Looking up the DNS Record Set "@" of type "NS"
data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
data:    Name                            : @
data:    Type                            : Microsoft.Network/dnszones/NS
data:    Location                        : global
data:    TTL                             : 172800
data:    NS records
data:        Name server domain name     : ns1-01.azure-dns.com.
data:        Name server domain name     : ns2-01.azure-dns.net.
data:        Name server domain name     : ns3-01.azure-dns.org.
data:        Name server domain name     : ns4-01.azure-dns.info.
data:
info:    network dns record-set show command OK
```

### <a name="to-set-up-delegation"></a>Configuración de la delegación

Cada registrador dispone de sus propias herramientas de administración de DNS para cambiar los registros de servidores de nombres de un dominio. En la página de administración de DNS del registrador, edite los registros NS y reemplácelos con los que DNS de Azure ha creado.

Al delegar un dominio a DNS de Azure, debe usar los nombres de servidor DNS proporcionados por DNS de Azure.  Debe usar siempre los cuatro nombres de servidor DNS, independientemente del nombre de su dominio.  La delegación de dominios no requiere que el nombre del servidor de nombres use el mismo dominio de primer nivel que su dominio.

No debe usar 'registros de adherencia' para apuntar a direcciones IP del servidor DNS de Azure, ya que estas direcciones IP pueden cambiar en el futuro. Las delegaciones que usan nombres de servidor en su propia zona (a veces denominados "servidores DNS personalizados") no se admiten de momento en DNS de Azure.

### <a name="to-verify-name-resolution-is-working"></a>Comprobación del funcionamiento de la resolución de nombres

Cuando finalice la delegación, puede verificar que la resolución de nombres funciona usando una herramienta como “nslookup” para consultar el registro SOA para su zona (que también se crea automáticamente cuando se crea la zona).

Tenga en cuenta que no es necesario especificar los servidores de nombres DNS de Azure, ya que el proceso de resolución DNS normal encontrará los servidores de nombres automáticamente si la delegación se ha configurado correctamente.

```
nslookup -type=SOA contoso.com

Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegating-sub-domains-in-azure-dns"></a>Delegación de subdominios en DNS de Azure

Si desea configurar una zona secundaria independiente, puede delegar un subdominio en DNS de Azure. Por ejemplo, después de configurar y delegar "contoso.com" en DNS de Azure, suponga que desea configurar una zona secundaria independiente, "partners.contoso.com".

Configurar un subdominio sigue un proceso similar al de una delegación normal. La única diferencia es que en el paso 3 los registros NS deben crearse en la zona primaria "contoso.com" de DNS de Azure, en lugar de configurarse a través de un registrador de dominios.

1. Cree la zona secundaria 'partners.contoso.com' en DNS de Azure.
2. Busque los registros NS autoritativos en la zona secundaria para obtener los servidores de nombres que hospedan la zona secundaria en DNS de Azure.
3. Delegue la zona secundaria mediante la configuración de los registros NS de la zona principal que apuntan a la zona secundaria.

### <a name="to-delegate-a-sub-domain"></a>Delegación de un subdominio.

En el siguiente ejemplo de PowerShell se muestra cómo funciona. Los mismos pasos se pueden ejecutar mediante el Portal de Azure, o mediante la CLI de Azure multiplataforma.

#### <a name="step-1-create-the-parent-and-child-zones"></a>Paso 1. Creación de las zonas primarias y secundarias
Primero se crean las zonas primarias y secundarias Se pueden crear dentro del mismo grupo de recursos o en grupos de recursos diferentes.

```powershell
$parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
$child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1
```

#### <a name="step-2-retrieve-ns-records"></a>Paso 2: Recuperación de registros NS

A continuación, se recuperan los registros NS autoritativos de la zona secundaria, tal como se muestra en el ejemplo siguiente:  Contiene los servidores de nombres asignados a la zona secundaria.

```powershell
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS
```

#### <a name="step-3-delegate-the-child-zone"></a>Paso 3: Delegación de la zona secundaria

Se crea el conjunto de registros NS correspondiente en la zona primaria para completar la delegación. Tenga en cuenta que el nombre del conjunto de registros en la zona primaria coincide con el nombre de la zona secundaria, en este caso "partners".

```powershell
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

### <a name="to-verify-name-resolution-is-working"></a>Comprobación del funcionamiento de la resolución de nombres

Se puede comprobar que todo está configurado correctamente mirando el registro SOA de la zona secundaria.

```
nslookup -type=SOA partners.contoso.com

Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Pasos siguientes

[Administración de zonas DNS](dns-operations-dnszones.md)

[Administración de registros DNS](dns-operations-recordsets.md)




<!--HONumber=Feb17_HO2-->


