<properties urlDisplayName="Add a Cert to the CA Store" pageTitle="Incorporación de un certificado al almacén de CA de Azure" metaKeywords="Azure Twilio Java, Twilio Java Certificate, Azure Service Bus Certificate" description="Learn how to add a certificate authority (CA) certificate to the Java CA certificate (cacerts) store for Twilio service or Azure Service Bus." metaCanonical="" services="" documentationCenter="Java" title="Adding a Certificate to the Java CA Certificates Store" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# Incorporación de un certificado al almacén de certificados CA de Java
Los siguientes pasos le muestran la manera de agregar un certificado de una entidad de certificación (CA) al almacén de certificados CA (cacert) de Java. El ejemplo hace referencia al certificado CA que es necesario utilizar con el servicio Twilio. La información que se proporciona en este tema describe la forma de instalar el certificado CA para el Bus de servicio de Azure. 

Puede utilizar keytool (herramienta de claves) para agregar el certificado CA antes de comprimir su JDK y agregarlo a la carpeta **approot** de su proyecto de Azure, o incluso podría ejecutar una tarea de arranque de Azure que utilice keytool para agregar el certificado. En este ejemplo se asume que agregará un certificado CA antes de comprimir el JDK. Además, en este ejemplo se utiliza un certificado CA específico, pero los pasos necesarios para obtener un certificado CA diferente e importarlo al almacén de certificados CA serán similares.

## Para agregar un certificado a un almacén de certificados CA

1. En un símbolo del sistema que esté configurado en su carpeta **jdk\jre\lib\security** de JDK, ejecute lo siguiente para visualizar los certificados instalados:

	`keytool -list -keystore cacerts`

	Se le solicitará la contraseña del almacén. La contraseña predeterminada es **changeit**. (Si desea cambiar la contraseña, consulte la documentación de keytool en http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html). En este ejemplo se asume que el certificado con huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 no está en la lista y que se desea importarlo (en particular, este certificado es necesario para el servicio de API Twilio).
2. Obtenga el certificado de la lista de certificados que se muestra en [GeoTrust Root Certificates](http://www.geotrust.com/resources/root-certificates/). Haga clic con el botón secundario en el vínculo al certificado con número de serie 35:DE:F4:CF y guárdelo en la carpeta **jdk\jre\lib\security**. A los efectos de este ejemplo, se ha guardado un archivo con nombre **Equifax\_Secure\_Certificate\_Authority.cer**.
3. Importe el certificado a través del siguiente comando:

	`keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

	Cuando se le solicite la confirmación de que confía en este certificado, si el certificado tiene la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responda escribiendo **y**.
4. Ejecute el siguiente comando para asegurarse de que el certificado CA se ha importado correctamente:

	`keytool -list -keystore cacerts`

5. Comprima el JDK y agréguelo a la carpeta **approot** de Azure.

Para obtener más información acerca de keytool, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

# Certificados raíz de Azure

Las aplicaciones que utilizan servicios de Azure (como Bus de servicio de Azure), deben confiar en el certificado raíz Baltimore CyberTrust Root. (A partir del 15 de abril de 2013, Azure empezó la migración de GTE CyberTrust Global Root a Baltimore CyberTrust Root. Esta migración tardó varios meses en completarse).

El certificado Baltimore podría estar ya instalado en su almacén de certificados CA, por lo que es importante que recuerde ejecutar el comando **keytool -list** en primer lugar para comprobar si ya está disponible.

Si necesita agregar el certificado Baltimore CyberTrust Root, el número de serie es 02:00:00:b9 y la huella digital SHA1 es d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Es posible descargarlo de <https://cacert.omniroot.com/bc2025.crt>, guardarlo en un archivo local con extensión **.cer** y luego importarlo con **keytool** como se ha mostrado anteriormente.

Para obtener más información acerca de los certificados raíz que utiliza Azure, consulte [Migración de certificados raíz de Microsoft Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

