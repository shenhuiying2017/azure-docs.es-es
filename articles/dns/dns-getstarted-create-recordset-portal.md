<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS con el Portal de Azure | Microsoft Azure"
   description="Creación de registros de host para DNS de Azure y generación de registros y conjuntos de registros con el Portal de Azure"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="cherylmc"/>


# Creación de registros y conjuntos de registros DNS mediante el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)


Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

## Descripción de los registros y los conjuntos de registros

### Sobre los registros

Cada registro DNS tiene un nombre y un tipo.

En un nombre de dominio "completo" (FQDN) se incluye el nombre de zona, mientras que uno "relativo", no. Por ejemplo, el nombre de registro relativo "www" en la zona "contoso.com" proporciona el nombre de registro completo "www.contoso.com".

>[AZURE.NOTE] En DNS de Azure, los registros se especifican mediante la utilización de nombres relativos.

Los registros se presentan en distintos tipos en función de los datos que contienen. El tipo más común es un registro "**A**", que asigna un nombre a una dirección IPv4. Otro tipo es un registro "**MX**", que asigna un nombre a un servidor de correo.

DNS de Azure es compatible con todos los tipos de registro DNS comunes: A, AAAA, CNAME, MX, NS, SOA, SRV y TXT. Tenga en cuenta que los registros SPF deben crearse mediante el tipo de registro TXT. Consulte [esta página](http://tools.ietf.org/html/rfc7208#section-3.1) para obtener más información.


### Sobre los conjuntos de registros

En ocasiones, tendrá que crear más de un registro DNS con un nombre y un tipo concretos. Por ejemplo, supongamos que el sitio web www.contoso.com se hospeda en dos direcciones IP diferentes. En este caso, se requieren dos registros A diferentes, uno para cada dirección IP: Este es un ejemplo de un conjunto de registros.

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

DNS de Azure administra los registros DNS con la utilización de conjuntos de registros. Un conjunto de registros es la colección de registros DNS de una zona con el mismo nombre y el mismo tipo. La mayoría de conjuntos de registros contienen un registro único, pero es habitual encontrar ejemplos como el anterior, en el que un conjunto de registros contiene más de un registro.

Los conjuntos de registros del tipo SOA y CNAME constituyen una excepción; los estándares DNS no permiten varios registros con el mismo nombre para estos tipos.

El período de vida, o TTL, especifica durante cuánto tiempo los clientes almacenan cada registro en caché antes de volver a consultarlo. En el ejemplo anterior, el TTL es 3600 segundos o 1 hora. El TTL se especifica para el conjunto de registros, no para cada registro, por lo que se utiliza el mismo valor para todos los registros de ese conjunto de registros.

#### Conjuntos de registros de carácter comodín

DNS de Azure admite [registros de carácter comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos se devuelven en cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más próxima de un conjunto de registros que no sean de caracteres comodín). Los conjuntos de registros de carácter comodín son compatibles con todos los tipos de registro, excepto NS y SOA.

Para crear un conjunto de registros de carácter comodín, use el nombre de conjunto de registros "\*" o un nombre cuya primera etiqueta sea "\*", por ejemplo, "\*.foo".

#### Conjuntos de registros CNAME

Los conjuntos de registros CNAME coexisten con otros conjuntos de registros con el mismo nombre. Por ejemplo, no se puede crear un registro CNAME con el nombre relativo "www" y un registro A con el nombre relativo "www" al mismo tiempo. Habida cuenta de que el ápice de zona (nombre = “@”) siempre contiene los conjuntos de registros NS y SOA creados cuando se crea la zona, significa que no puede crear un conjunto de registros CNAME en el ápice de zona. Estas restricciones surgen de los estándares DNS; no son limitaciones de DNS de Azure.


## Creación de un nuevo conjunto de registros y un registro

En el siguiente ejemplo se explicará cómo crear registros y un conjunto de registros mediante el Portal de Azure. Utilizaremos el tipo de registro DNS "A".

1. Inicie sesión en el Portal de Azure.

2. Vaya a la hoja **Zona DNS** en la que quiera crear un conjunto de registros.

3. En la parte superior de la hoja Zona DNS, haga clic en **Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.
 
	![nuevo conjunto de registros](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. En la hoja **Agregar conjunto de registros**, especifique un nombre para el conjunto de registros. Por ejemplo, podría llamar "**www**" a su conjunto de registros.
  
	![agregar conjunto de registros](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Seleccione el tipo de registro que quiere crear. Por ejemplo, **A**.

6. Establezca el **TTL**. El valor predeterminado en el Portal es de 1 hora.

7. Agregue las direcciones IP; una por línea. Con el nombre sugerido del conjunto de registros y el tipo de registro anterior, se agregarán direcciones IP de tipo IPv4 al registro A para el conjunto de registros www.

8. Cuando haya terminado de agregar direcciones IP, haga clic en **Aceptar** en la parte inferior de la hoja. Se creará el conjunto de registros de DNS.

## Pruebe la zona DNS mediante las herramientas DNS.


Si aún no ha delegado el dominio para usar la nueva zona DNS en Azure, necesitará dirigir la consulta DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se muestran en el panel Essentials de la hoja Zona DNS. Consulte el artículo [Delegación de dominios en DNS de Azure](dns-domain-delegation.md) para obtener más información.

Puede probar su zona DNS con herramientas DNS, como nslookup, dig o el [cmdlet de PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).


## Pasos siguientes

Para obtener más información sobre DNS de Azure, consulte [Introducción a DNS de Azure](dns-overview.md). Para obtener información sobre la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).

<!------HONumber=AcomDC_0406_2016-->