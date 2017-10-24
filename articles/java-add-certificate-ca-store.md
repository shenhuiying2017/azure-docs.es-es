---
title: "Incorporación de un certificado al almacén de certificados CA de Java | Microsoft Docs"
description: "Obtenga información acerca de cómo agregar un certificado de entidad de certificación (CA) al almacén de certificados CA de Java (cacerts) para el servicio de Twilio o el Bus de servicio de Azure."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: b6e1a305e19415ab1c4b4c208dac98ad1e2689c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Incorporación de un certificado al almacén de certificados CA de Java
Los siguientes pasos le muestran la manera de agregar un certificado de una entidad de certificación (CA) al almacén de certificados CA (cacert) de Java. El ejemplo hace referencia al certificado CA que es necesario utilizar con el servicio Twilio. La información que se proporciona en este tema describe la forma de instalar el certificado de entidad de certificación para el Bus de servicio de Azure. 

Puede usar keytool para agregar el certificado de entidad de certificación antes de comprimir su JDK y agregarlo a la carpeta **approot** de su proyecto de Azure, o puede ejecutar una tarea de inicio de Azure que use keytool para agregar el certificado. En este ejemplo se asume que agregará un certificado CA antes de comprimir el JDK. Además, en este ejemplo se utiliza un certificado CA específico, pero los pasos necesarios para obtener un certificado CA diferente e importarlo al almacén de certificados CA serán similares.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para agregar un certificado a un almacén de certificados CA
1. En un símbolo del sistema de administrador establecido en la carpeta **jdk\jre\lib\security** de su JDK, ejecute el siguiente comando para ver qué certificados están instalados:
   
    `keytool -list -keystore cacerts`
   
    Se le solicitará la contraseña del almacén. La contraseña predeterminada es **changeit**. (Si desea cambiar la contraseña, consulte la documentación de keytool en <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>). En este ejemplo se asume que el certificado con huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 no está en la lista y que se desea importarlo (en particular, este certificado es necesario para el servicio de API Twilio).
2. Obtenga el certificado de la lista de certificados que se muestra en [Certificados raíz de GeoTrust](http://www.geotrust.com/resources/root-certificates/). Haga clic con el botón derecho en el vínculo al certificado con número de serie 35:DE:F4:CF y guárdelo en la carpeta **jdk\jre\lib\security**. A los efectos de este ejemplo, se ha guardado un archivo con el nombre **Equifax\_Secure\_Certificate\_Authority.cer**.
3. Importe el certificado a través del siguiente comando:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Cuando se le solicite la confirmación de que confía en este certificado, si el certificado tiene la huella digital MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responda escribiendo **y**.
4. Ejecute el siguiente comando para asegurarse de que el certificado CA se ha importado correctamente:
   
    `keytool -list -keystore cacerts`
5. Comprima el JDK y agréguelo a la carpeta **approot** del proyecto de Azure.

Para más información acerca de keytool, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados raíz de Azure
Las aplicaciones que utilizan servicios de Azure (como Bus de servicio de Azure), deben confiar en el certificado raíz Baltimore CyberTrust Root. (A partir del 15 de abril de 2013, Azure empezó la migración de GTE CyberTrust Global Root a Baltimore CyberTrust Root. Esta migración tardó varios meses en completarse).

El certificado Baltimore podría estar ya instalado en su almacén de certificados de entidad de certificación, por lo que es importante que recuerde ejecutar el comando **keytool -list** en primer lugar para comprobar si ya está disponible.

Si necesita agregar Baltimore CyberTrust Root, tiene el número de serie 02:00:00:b9 y la huella digital SHA1 d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Es posible descargarlo desde <https://cacert.omniroot.com/bc2025.crt>, guardarlo en un archivo local con la extensión **.cer** y luego importarlo mediante **keytool** como se mostró anteriormente.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre los certificados raíz que usa Azure, consulte [Azure Root Certificate Migration (Migración de certificados raíz de Azure)](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para más información sobre Java, consulte [Azure para desarrolladores de Java](/java/azure).

