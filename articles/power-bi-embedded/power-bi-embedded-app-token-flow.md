<properties
   pageTitle="Acerca del flujo del token de aplicación en Power BI Embedded"
   description="Power BI Embedded sobre los token de la aplicación para la autenticación y autorización"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Acerca del flujo del token de aplicación en Power BI Embedded

El servicio de **Power BI Embedded** usa **tokens de aplicación** para la autenticación y autorización en lugar de la autenticación explícita del usuario final. En el modelo **App Token**, su aplicación administra la autenticación y la autorización para los usuarios finales. Cuando es necesario, la aplicación crea y envía los **tokens de aplicación** que indican a nuestro servicio que presente el informe solicitado. Este diseño no requiere que su aplicación use **Azure Active Directory** para la autenticación y autorización del usuario, aunque puede hacerlo.

**Así es como funciona el flujo de clave del token de aplicación**

1. Copie las claves de API a la aplicación. Puede obtener las claves en el **Portal de Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. El token impone una notificación y tiene un tiempo de expiración.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. El token se firma con las claves de acceso de la API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. El usuario solicita ver un informe.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.	El token se valida con claves de acceso de una API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.	Power BI Embedded envía un informe al usuario.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Después de que **Power BI Embedded** envíe un informe al usuario, este puede ver el informe en su aplicación personalizada. Por ejemplo, si ha importado el [ejemplo .pbix de análisis de datos de venta](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), la aplicación web de ejemplo tendría el siguiente aspecto:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## Otras referencias
- [Get started with Microsoft Power BI Embedded sample (Introducción al ejemplo de Microsoft Power BI Embedded)](power-bi-embedded-get-started-sample.md)
- [Qué es Microsoft Power BI Embedded](power-bi-embedded-what-is-power-bi-embedded.md)
- [Common Microsoft Power BI Embedded Preview scenarios (Escenarios comunes de la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-scenarios.md)
- [Get started with Microsoft Power BI Embedded preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md)

<!---HONumber=AcomDC_0420_2016-->