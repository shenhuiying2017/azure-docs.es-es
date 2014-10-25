<properties linkid="notification-hubs-java-back-end-how-to" urlDisplayName="How to use Notification Hubs with Java" pageTitle="How to use Notification Hubs with Java" metaKeywords="" description="Learn how to use Azure Notification Hubs from a Java back-end." metaCanonical="" services="mobile-services,notification-hubs,push,java" documentationCenter="" title="How to use Notification Hubs with Java" authors="elioda" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="elioda"/>

# Uso de los Centros de notificaciones desde Java o PHP

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/es-es/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/es-es/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a>
</div>

Puede acceder a todas las características de los Centros de notificaciones desde un back-end de Java, PHP o Ruby usando la interfaz REST de Centros de notificaciones tal y como se describe en el tema de MSDN [API de REST de Centros de notificaciones][].

En este tema le mostraremos cómo:

-   Crear un cliente REST para las características de Centros de notificaciones en Java;
-   Siga el [tutorial introductorio][] para la plataforma móvil que elija, implementando la parte del back-end en Java.

## <a name="client-interface"></a>Interfaz del cliente

La interfaz del cliente principal puede proporcionar los mismos métodos disponibles en el [SDK de Centros de notificaciones .NET][], que le permitirá trasladar todos los tutoriales y ejemplos actualmente disponibles en este sitio directamente y con el que contribuye la comunidad en Internet.

Puede encontrar todo el código disponible en el [ejemplo de contenedor REST para Java][].

Por ejemplo, para crear un cliente:

    new NotificationHub("connection string", "hubname");    

Para crear un registro de iOS (similar para Windows, Android, Windows Phone y Kindle Fire):

    String id = hub.createRegistrationId();
    AppleRegistration reg = new AppleRegistration(id, DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.upsertRegistration(reg);

Para enviar una notificación nativa de iOS:

    Notification n = Notification.createAppleNotifiation("APNS body");
    hub.sendNotification(n);

## <a name="implementation"></a>Implementación

Si todavía no lo ha hecho, siga nuestro [tutorial introductorio][] hasta la última sección en la que tiene que implementar el back-end.
Asimismo, si lo desea, puede usar el código del [ejemplo de contenedor REST para Java][] e ir directamente a la sección [Finalización del tutorial][].

En [MSDN][] puede encontrar todos los detalles para implementar un contenedor REST completo. En esta sección describiremos la implementación para Java de los principales pasos requeridos para acceder a extremos REST de Centros de notificaciones:

1.  Análisis de la cadena de conexión
2.  Generación del token de autenticación
3.  Realización de la llamada HTTP

En los siguientes fragmentos de código hacemos uso de los siguientes componentes:

-   [Apache HttpComponents][]
-   [Apache Commons-Codec][]
-   [Apache Commons-Io][]

### Análisis de la cadena de conexión

Esta es la clase principal que implementa el cliente, cuyo constructor analiza la cadena de conexión:

    public class NotificationHub {

        private static final String APIVERSION = "?api-version=2013-10";
        private static final String CONTENT_LOCATION_HEADER = "Location";
        private String endpoint;
        private String hubPath;
        private String SasKeyName;
        private String SasKeyValue;

        private HttpClient httpClient;

        public NotificationHub(String connectionString, String hubPath) {
            this.httpClient = HttpClients.createDefault();
            this.hubPath = hubPath;

            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);

            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.endpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.SasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.SasKeyValue = parts[i].substring(16);
                }
            }
        }
    }

### Creación del token de seguridad

Los detalles de la creación del token de seguridad están disponibles [aquí][].
El siguiente método tiene que agregarse a la clase **NotificationHub** para crear el token basándose en el URI de la solicitud actual y en las credenciales extraídas de la cadena de conexión.

    private String generateSasToken(URI uri) {
        String targetUri;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = SasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Convert raw bytes to Hex
            String signature = URLEncoder.encode(
                    Base64.encodeBase64String(rawHmac), "UTF-8");

            // construct authorization string
            String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + SasKeyName;
            return token;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

### Envío de una notificación

En primer lugar, definamos una clase que representa una notificación.

    import java.util.HashMap;
    import java.util.Iterator;
    import java.util.Map;
    import org.apache.http.entity.ContentType;

    public class Notification {
        private Map<String, String> headers = new HashMap<String, String>();
        private String body;
        private ContentType contentType;

        public static Notification createWindowsNotification(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windows");

            if (body.contains("<toast>"))
                n.headers.put("X-WNS-Type", "wns/toast");
            if (body.contains("<tile>"))
                n.headers.put("X-WNS-Type", "wns/tile");
            if (body.contains("<badge>"))
                n.headers.put("X-WNS-Type", "wns/badge");
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createAppleNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "apple");
            return n;
        }

        public static Notification createGcmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "gcm");
            return n;
        }

        public static Notification createAdmNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "adm");
            return n;
        }

        public static Notification createMpnsNotifiation(String body) {
            Notification n = new Notification();
            n.body = body;
            n.headers.put("ServiceBusNotification-Format", "windowsphone");

            if (body.contains("<wp:Toast>")) {
                n.headers.put("X-WindowsPhone-Target", "toast");
                n.headers.put("X-NotificationClass", "2");
            }
            if (body.contains("<wp:Tile>")) {
                n.headers.put("X-WindowsPhone-Target", "tile");
                n.headers.put("X-NotificationClass", "1");
            }
            if (body.startsWith("<")) {
                n.contentType = ContentType.APPLICATION_XML;
            }
            return n;
        }

        public static Notification createTemplateNotification(
                Map<String, String> properties) {
            Notification n = new Notification();
            StringBuffer buf = new StringBuffer();
            buf.append("{");
            for (Iterator<String> iterator = properties.keySet().iterator(); iterator
                    .hasNext();) {
                String key = iterator.next();
                buf.append("\"" + key + "\":\"" + properties.get(key) + "\"");
                if (iterator.hasNext())
                    buf.append(",");
            }
            buf.append("}");
            n.body = buf.toString();
            n.contentType = ContentType.APPLICATION_JSON;
            n.headers.put("ServiceBusNotification-Format", "template");
            return n;
        }

        public Map<String, String> getHeaders() { return headers; }

        public void setHeaders(Map<String, String> headers) { this.headers = headers; }

        public String getBody() { return body; }

        public void setBody(String body) { this.body = body; }

        public ContentType getContentType() { return contentType; }

        public void setContentType(ContentType contentType) { this.contentType = contentType; }
    }

Esta clase es un contenedor para un cuerpo de notificación nativa, o un conjunto de propiedades en el caso de una notificación de plantilla, y un conjunto de encabezados que contienen formato (plataforma o plantilla nativa) y propiedades específicas de la plataforma (como la propiedad de expiración de Apple y los encabezados WNS). También definimos algunos constructores prácticos para generar tipos de notificación comúnmente usados.

Consulte la [documentación de las API de REST de Centros de notificaciones][] y los formatos de las plataformas de notificación específicas para todas las opciones disponibles.

Con esta clase, ahora podemos escribir los métodos de envío de notificaciones dentro de la clase **NotificationHub**.

    public void sendNotification(Notification notification) {
        sendNotification(notification, "");
    }

    public void sendNotification(Notification notification, Set<String> tags) {
        if (tags.isEmpty())
            throw new IllegalArgumentException(
                    "tags has to contain at least an element");

        StringBuffer exp = new StringBuffer();
        for (Iterator<String> iterator = tags.iterator(); iterator.hasNext();) {
            exp.append(iterator.next());
            if (iterator.hasNext())
                exp.append(" || ");
        }

        sendNotification(notification, exp.toString());
    }

    public void sendNotification(Notification notification, String tagExpression) {
        HttpPost post = null;
        try {
            URI uri = new URI(endpoint + hubPath + "/messages" + APIVERSION);
            post = new HttpPost(uri);
            post.setHeader("Authorization", generateSasToken(uri));

            if (tagExpression != null && !"".equals(tagExpression)) {
                post.setHeader("ServiceBusNotification-Tags", tagExpression);
            }

            for (String header : notification.getHeaders().keySet()) {
                post.setHeader(header, notification.getHeaders().get(header));
            }

            post.setEntity(new StringEntity(notification.getBody()));
            HttpResponse response = httpClient.execute(post);

            if (response.getStatusLine().getStatusCode() != 201) {
                String msg = "";
                if (response.getEntity() != null
                        && response.getEntity().getContent() != null) {
                    msg = IOUtils.toString(response.getEntity().getContent());
                }
                throw new RuntimeException("Error: " + response.getStatusLine()
                        + " body: " + msg);
            }

        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (post != null)
                post.releaseConnection();
        }
    }

Los métodos anteriores envían una solicitud POST HTTP al extremo /messages del centro de notificaciones, con el cuerpo y encabezados correctos para enviar la notificación.

## <a name="complete-tutorial"></a>Finalización del tutorial

Ahora puede completar el tutorial introductorio enviando la notificación desde un back-end de Java.

Inicialice el cliente de Centros de notificaciones (sustituya la cadena de conexión y el nombre del centro tal y como se indica en el [tutorial introductorio][]):
 NotificationHub hub = new NotificationHub("{connection string}", "{hubname}");

Después, agregue el código de envío dependiendo de la plataforma móvil de destino.

### Tienda Windows y Windows Phone 8.1 (no Silverlight)

    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);

### iOS

    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);

### Android

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createGcmNotification(message);
    hub.sendNotification(n);

### Windows Phone 8.0 y 8.1 Silverlight

    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);

### Kindle Fire

    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);

La ejecución del código de Java debe generar ahora una notificación que aparece en el dispositivo de destino.

## <a name="next-steps"></a>Pasos siguientes

En este tema hemos mostrado cómo crear un simple cliente REST en Java para Centros de notificaciones. Desde aquí puede:

-   Descargar el [ejemplo de contenedor REST para Java][] completo, que contiene todo el código anterior más la administración de registro.
-   Continuar aprendiendo sobre la característica de etiquetado de Centros de notificaciones en el [tutorial Noticias de última hora]
-   Obtener más información sobre notificaciones de inserción para usuarios individuales en el [tutorial Notificar a los usuarios]

  [Java]: /es-es/documentation/articles/notification-hubs-java-backend-how-to/ "Java"
  [PHP]: /es-es/documentation/articles/notification-hubs-php-backend-how-to/ "PHP"
  [API de REST de Centros de notificaciones]: http://msdn.microsoft.com/en-us/library/dn223264.aspx
  [tutorial introductorio]: http://azure.microsoft.com/es-es/documentation/articles/notification-hubs-ios-get-started/
  [SDK de Centros de notificaciones .NET]: http://msdn.microsoft.com/en-us/library/jj933431.aspx
  [ejemplo de contenedor REST para Java]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-java
  [Finalización del tutorial]: #complete-tutorial
  [MSDN]: http://msdn.microsoft.com/en-us/library/dn530746.aspx
  [Apache HttpComponents]: http://hc.apache.org/httpcomponents-client-ga/
  [Apache Commons-Codec]: http://commons.apache.org/proper/commons-codec/
  [Apache Commons-Io]: http://commons.apache.org/proper/commons-io/
  [aquí]: http://msdn.microsoft.com/en-us/library/dn495627.aspx
  [documentación de las API de REST de Centros de notificaciones]: http://msdn.microsoft.com/en-us/library/dn495827.aspx
