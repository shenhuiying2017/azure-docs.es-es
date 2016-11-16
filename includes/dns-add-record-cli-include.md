#### <a name="create-an-a-record-set-with-single-record"></a>Creación de un conjunto de registros A con un único registro

Para crear un conjunto de registros, use `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de recursos, el tipo de registro y el período de vida (TTL).

```azurecli
    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

Después de crear el conjunto de registros A, agregue la dirección IPv4 al conjunto de registros con `azure network dns record-set add-record`.

```azurecli
    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Creación de un conjunto de registros AAAA con un único registro

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"
```

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Creación de un conjunto de registros CNAME con un único registro

Los registros CNAME solo permiten un único valor de cadena.

```azurecli
    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"
```

#### <a name="create-an-mx-record-set-with-a-single-record"></a>Creación de un conjunto de registros MX con un único registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (en este caso, "contoso.com"). Esto es común para los registros MX.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5
```

#### <a name="create-an-ns-record-set-with-a-single-record"></a>Creación de un conjunto de registros NS con un único registro

```azurecli
    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Creación de un conjunto de registros PTR con un único registro

En este caso 'my-arpa-zone.com' representa la zona ARPA que representa el intervalo de IP.  Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP.

```azurecli
    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Creación de un conjunto de registros SRV con un único registro

Si va a crear un registro SRV en la raíz de la zona, especifique "_service" y "_protocol" en el nombre del registro. No es necesario incluir "@" en el nombre del registro.

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"
```

#### <a name="create-a-txt-record-set-with-single-record"></a>Creación de un conjunto de registros TXT con un único registro

```azurecli
    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
```


<!--HONumber=Nov16_HO2-->


