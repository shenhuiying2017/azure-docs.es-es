<properties
   pageTitle="Autenticación y autorización con Power BI Embedded"
   description="Autenticación y autorización con Power BI Embedded"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>


# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticación y autorización con Power BI Embedded

El servicio Power BI Embedded usa **claves** y **tokens de aplicación** para la autenticación y la autorización en lugar de la autenticación explícita de usuario final. En este modelo, la aplicación, la aplicación administra la autenticación y la autorización de sus usuarios finales. Si es necesario, la aplicación crea y envía los tokens de aplicación que indican a nuestro servicio que presente el informe solicitado. En este diseño no es necesario que la aplicación use Azure Active Directory para la autenticación y la autorización de usuarios, aunque puede hacerlo.

## <a name="two-ways-to-authenticate"></a>Dos modos de autenticación

**Clave**: puede usar claves para todas las llamadas de API de REST de Power BI Embedded. Las claves pueden encontrarse en **Azure portal** haciendo clic en **Toda la configuración** y, luego, en **Claves de acceso**. Debe tratar siempre la clave como si fuese una contraseña. Estas claves tienen permisos para llamar a cualquier API de REST en una colección de área de trabajo determinada.

Para utilizar una clave en una llamada de REST, agregue el siguiente encabezado de autorización:            

    Authorization: AppKey {your key}

**Token de aplicación** : los tokens de aplicación se utilizan para todas las solicitudes de inserción. Están diseñados para ejecutarse en el lado del cliente, por lo que están restringidas a un único informe y se recomienda establecer un tiempo de expiración.

Los tokens de aplicación son un JWT (JSON Web Token) que está firmado por una de sus claves.

El token de la aplicación puede contener las siguientes notificaciones:

| Notificación      | Description        |
|--------------|------------|
| **ver**      | La versión del token de aplicación. La versión actual es 0.2.0.       |
| **aud**      | El destinatario previsto del token. Para usar Power BI Embedded: "https://analysis.windows.net/powerbi/api".  |
| **iss**      |  Una cadena que indica la aplicación que emitió el token.    |
| **type**     | El tipo de token de aplicación que se está creando. El único tipo admitido actualmente es **insertar**.   |
| **wcn**      | El nombre de la colección de área de trabajo para el que se emite el token.  |
| **wid**      | El id. del área de trabajo para el que se emite el token.  |
| **rid**      | El id. del informe para el que se emite el token.     |
| **username** (opcional) |  Se utiliza con RLS y es una cadena que ayuda a identificar el usuario cuando se aplican las reglas RLS. |
| **roles** (opcional)   |   Una cadena que contiene los roles que seleccione al aplicar las reglas de seguridad de nivel de fila. Si se pasa más de un rol, se deben pasar como una matriz de cadenas.    |
| **exp** (opcional)    |   Indica la hora a la que caducará el token. Se deben pasar como marcas de tiempo de Unix.   |
| **nbf** (opcional)    |   Indica la hora a la que comienza a ser válido el token. Se deben pasar como marcas de tiempo de Unix.   |

Un ejemplo de token de aplicación tendrá este aspecto:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Cuando se descodifica, tendrá un aspecto similar al siguiente:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="here's-how-the-flow-works"></a>Funcionamiento del flujo

1. Copie las claves de API a la aplicación. Puede obtener las claves en el **Portal de Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. El token impone una notificación y tiene un tiempo de expiración.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. El token se firma con las claves de acceso de la API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. El usuario solicita ver un informe.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  El token se valida con claves de acceso de una API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI Embedded envía un informe al usuario.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Después de que **Power BI Embedded** envíe un informe al usuario, este puede ver el informe en su aplicación personalizada. Por ejemplo, si ha importado el [ejemplo .pbix de análisis de datos de venta](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), la aplicación web de ejemplo tendría el siguiente aspecto:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Otras referencias
- [Get started with Microsoft Power BI Embedded sample (Introducción al ejemplo de Microsoft Power BI Embedded)](power-bi-embedded-get-started-sample.md)
- [Common Microsoft Power BI Embedded scenarios (Escenarios comunes de Microsoft Power BI Embedded)](power-bi-embedded-scenarios.md)
- [Introducción a Microsoft Power BI Embedded](power-bi-embedded-get-started.md)



<!--HONumber=Oct16_HO2-->


