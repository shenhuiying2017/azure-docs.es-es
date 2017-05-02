### <a name="record-names"></a>Nombres de registro

En DNS de Azure, los registros se especifican mediante el uso de nombres relativos. En un nombre de dominio *completo* (FQDN) se incluye el nombre de zona, mientras que uno *relativo*, no. Por ejemplo, el nombre de registro relativo "www" de la zona "contoso.com" proporciona el nombre de registro completo "www.contoso.com".

Un registro de *vértice* es un registro DNS en la raíz (o *vértice*) de una zona DNS. Por ejemplo, en la zona DNS "contoso.com", un registro de vértice también tiene el nombre completo "contoso.com" (que a veces se denomina dominio *simple*).  Por convención, el nombre relativo '@' se utiliza para representar registros de vértice.

### <a name="record-types"></a>Tipos de registro

Cada registro DNS tiene un nombre y un tipo. Los registros se organizan en distintos tipos según los datos que contengan. El tipo más común es un registro "A", que asigna un nombre a una dirección IPv4. Otro tipo común es un registro "MX", que asigna un nombre a un servidor de correo.

DNS de Azure es compatible con todos los tipos de registro DNS comunes: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV y TXT. Tenga en cuenta que [los registros de SPF se representan mediante registros TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Conjuntos de registros

En ocasiones, tendrá que crear más de un registro DNS con un nombre y un tipo concretos. Por ejemplo, supongamos que el sitio web www.contoso.com se hospeda en dos direcciones IP diferentes. En este caso, se requieren dos registros A distintos, uno para cada dirección IP. Este es un ejemplo de un conjunto de registros:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS administra todos los registros DNS con *conjuntos de registros*. Un conjunto de registros (también denominado conjunto de registros de *recurso*) es la colección de registros DNS de una zona con el mismo nombre y del mismo tipo. La mayoría de conjuntos de registros contienen un único registro. Sin embargo, es habitual encontrar ejemplos como el anterior, en el que un conjunto de registros contiene más de un registro.

Por ejemplo, supongamos que ya ha creado un registro "www" en la zona "contoso.com", que apunta a la dirección IP "134.170.185.46" (el primer registro anterior).  Para crear el segundo registro se agregaría ese registro al conjunto de registros existente, en lugar de crear otro conjunto de registros.

Los tipos de registros SOA y CNAME se consideran excepciones. Los estándares DNS no permiten varios registros con el mismo nombre para estos tipos, por lo tanto, los conjuntos de estos registros solo pueden contener un único registro.