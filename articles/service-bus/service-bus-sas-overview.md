<properties
   pageTitle="Información general de firmas de acceso compartido"
   description="¿Qué son las firmas acceso compartido, cómo funcionan y cómo usarlas desde Node, PHP y C#?"
   services="service-bus,event-hubs"
   documentationCenter="na"
   authors="djrosanova"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="darosa"/>

# Las firmas de acceso compartido

*Firmas de acceso compartido* (SAS) es el mecanismo de seguridad principal para Bus de servicio, incluidos centros de eventos, mensajería asíncrona (colas y temas) y mensajería retransmitida. Este artículo se tratan las firmas acceso compartido, cómo funcionan y cómo usarlas de forma independiente de plataforma.

## Información general de SAS

Firmas de acceso compartido son un mecanismo de autenticación basado en URI y valores hash seguros SHA-256. SAS es un mecanismo muy eficaz que se usa por todos los servicios del Bus de servicio. En el uso real, SAS tiene dos componentes: una *directiva de acceso compartido* y una *firma de acceso compartido* (a menudo denominada *token*).

Puede encontrar información más detallada sobre las firmas de acceso compartido con el Bus de servicio en [Autenticación de firmas de acceso compartido con el Bus de servicio](https://msdn.microsoft.com/library/azure/dn170477.aspx).

## Directiva de acceso compartido

Una cuestión importante de entender acerca de SAS es que todo empieza con una directiva. Para cada directiva, decida tres tipos de información: **nombre**, **ámbito** y **permisos**. El **nombre** es solo eso; un nombre único dentro de ese ámbito. El ámbito es bastante sencillo: es el URI del recurso en cuestión. Para un espacio de nombres del Bus de servicio, el ámbito es el nombre de dominio completo (FQDN), como **`https://<yournamespace>.servicebus.windows.net/`**.

Los permisos disponibles para una directiva son en gran parte explicativos:

  + Los métodos Send
  + Escuchar
  + Manage

Después de crear la directiva, se le asigna una *clave principal*y una *clave secundaria*. Son claves de alta seguridad criptográfica. No las pierda; siempre estarán disponibles en el portal. Puede usar cualquiera de las claves generadas y regenerarlas en cualquier momento. Sin embargo, si regenera o cambia la clave principal en la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

Cuando se crea un espacio de nombres del Bus de servicio, se crea automáticamente una directiva para todo el espacio de nombres denominado **RootManageSharedAccessKey** y esta directiva tiene todos los permisos. No inicia sesión como **raíz**; por tanto, no use esta directiva a menos que exista realmente una buena razón. Puede crear directivas adicionales en la pestaña **Configurar** para el espacio de nombres en el portal de administración de Azure. Es importante tener en cuenta que un nivel de árbol único en el Bus de servicio (espacio de nombres, cola, concentrador de eventos, etc.) solo puede tener hasta 12 directivas asociadas a él.

## Firma de acceso compartido (token)

La propia directiva no es el token de acceso para el Bus de servicio. Es el objeto desde el que se genera el token de acceso, mediante la clave principal o la clave secundaria. El token se genera elaborando cuidadosamente una cadena con el siguiente formato:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Donde `signature-string` es el hash SHA-256 del ámbito del token (**ámbito** como se describe en la sección anterior) con un CRLF anexado y una hora de expiración (en segundos desde la época: `00:00:00 UTC` el 1 de enero de 1970).

El hash es similar al siguiente pseudocódigo y devuelve 32 bytes.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Los valores que no son de hash se encuentran en la cadena **SharedAccessSignature** para que el destinatario puede calcular el hash con los mismos parámetros, para asegurarse de que devuelve el mismo resultado. El URI especifica el ámbito y el nombre de la clave identifica la directiva que se usará para calcular el hash. Esto es importante desde una perspectiva de seguridad. Si la firma no coincide con la que el destinatario calcula (Bus de servicio), se denegará el acceso. En este punto podemos estar seguros de que el remitente ha tenido acceso a la clave y que se le debería haber concedido los derechos especificados en la directiva.

## Generación de una firma desde una directiva

¿Cómo hace esto realmente en el código? Echemos un vistazo a varias de estas.

### NodeJS

```
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
}
``` 

### Java

```
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```

### PHP

```
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
$targetUri = strtolower(rawurlencode(strtolower($uri))); 
$expires = time(); 	
$expiresInMins = 60; 
$week = 60*60*24*7;
$expires = $expires + $week; 
$toSign = $targetUri . "\n" . $expires; 
$signature = rawurlencode(base64_encode(hash_hmac('sha256', 			
 $toSign, $sasKeyValue, TRUE))); 

$token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires . 		"&skn=" . $sasKeyName; 
return $token; 
}
```
 
### C&#35;

```
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## Usar la firma de acceso compartido
 
Ahora que sabe cómo crear firmas de acceso compartido para cualquier entidad del Bus de servicio, estará listo para llevar a cabo una solicitud HTTP POST:

```
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 
	
Recuerde que esto funciona para todo. Puede crear SAS para una cola, un tema, una suscripción, un concentrador de eventos o una retransmisión. Si usa una identidad por publicador para centros de eventos, sencillamente anexa `/publishers/< publisherid>`.

Si le da un token de SAS a un remitente o cliente, no tiene la clave directamente y no pueden invertir el hash para obtenerla. Como tal, tiene control sobre a lo que pueden tener acceso y durante cuánto tiempo. Algo importante de recordar es que si cambia la clave principal de la directiva, se invalidará cualquier firma de acceso compartido creada a partir de ella.

## Pasos siguientes

Vea la [referencia de la API de REST de Bus de servicio](https://msdn.microsoft.com/library/azure/hh780717.aspx) para obtener más información sobre lo que puede hacer con estos tokens de SAS.

Para obtener más información sobre SAS, vea el nodo de [autenticación del Bus de servicio](https://msdn.microsoft.com/library/azure/dn155925.aspx) en MSDN.

<!---HONumber=July15_HO5-->