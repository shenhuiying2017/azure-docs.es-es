## Sobre los registros

Cada registro DNS tiene un nombre y un tipo. Los registros se organizan en distintos tipos según los datos que contengan. El tipo más común es un registro "A", que asigna un nombre a una dirección IPv4. Otro tipo es un registro "MX", que asigna un nombre a un servidor de correo.

DNS de Azure es compatible con todos los tipos de registro DNS comunes:, incluidos A, AAAA, CNAME, MX, NS, PTR, SOA, SRV y TXT. Observe lo siguiente:
- Los conjuntos de registros SOA se crean automáticamente con cada zona; no se pueden crear por separado.
- Los registros SPF deben crearse mediante el tipo de registro TXT. Para más información, consulte [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

En DNS de Azure, los registros se especifican mediante el uso de nombres relativos. En un nombre de dominio "completo" (FQDN) se incluye el nombre de zona, mientras que uno "relativo", no. Por ejemplo, el nombre de registro relativo "www" de la zona "contoso.com" proporciona el nombre de registro completo "www.contoso.com".

## Sobre los conjuntos de registros

En ocasiones, tendrá que crear más de un registro DNS con un nombre y un tipo concretos. Por ejemplo, supongamos que el sitio web www.contoso.com se hospeda en dos direcciones IP diferentes. En este caso, se requieren dos registros A distintos, uno para cada dirección IP. Este es un ejemplo de un conjunto de registros:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

DNS de Azure administra los registros DNS usando conjuntos de registros. Un conjunto de registros es la colección de registros DNS de una zona con el mismo nombre y el mismo tipo. La mayoría de los conjuntos de registros contienen un registro único, pero es habitual encontrar ejemplos como este, en el que un conjunto de registros contiene más de un registro.

Los conjuntos de registros SOA y CNAME se consideran excepciones. Los estándares DNS no permiten varios registros con el mismo nombre para estos tipos.

El período de vida, o TTL, especifica durante cuánto tiempo los clientes almacenan cada registro en caché antes de volver a consultarlo. En este ejemplo, el TTL es 3600 segundos o 1 hora. El TTL se especifica para el conjunto de registros, no para cada registro, por lo que se utiliza el mismo valor para todos los registros de ese conjunto de registros.

#### Conjuntos de registros de carácter comodín

DNS de Azure admite [registros de carácter comodín](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estos se devuelven en cualquier consulta con un nombre coincidente (a menos que haya una coincidencia más próxima de un conjunto de registros que no sean de caracteres comodín). Los conjuntos de registros de carácter comodín son compatibles con todos los tipos de registro, excepto NS y SOA.

Para crear un conjunto de registros comodín, utilice el nombre de conjunto de registros "*". También puede usar un nombre con la etiqueta "*", por ejemplo, "*.foo".

#### Conjuntos de registros CNAME

Los conjuntos de registros CNAME no pueden coexistir con otros conjuntos de registros que tienen el mismo nombre. Por ejemplo, no se puede crear un conjunto de registros CNAME con el nombre relativo "www" y un registro A con el nombre relativo "www" al mismo tiempo. Dado que el vértice de la zona (nombre = "@") siempre contiene los conjuntos de registros NS y SOA creados cuando se genera la zona, no podrá crear un conjunto de registros CNAME en el vértice de la zona. Estas restricciones surgen de los estándares DNS; no son limitaciones de DNS de Azure.

<!---HONumber=AcomDC_0928_2016-->