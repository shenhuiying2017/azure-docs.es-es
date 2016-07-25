<properties
   pageTitle="Novedades en Power BI Embedded"
   description="Obtenga la información más reciente sobre las novedades en Power BI Embedded."
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="07/06/2016"
   ms.author="owend"/>

# Novedades en Power BI Embedded

De forma periódica se publican actualizaciones a **Power BI Embedded**. Sin embargo, no todas las versiones incluirán nuevas características de cara al usuario; algunas se centran en las funcionalidades del servicio back-end. Analizaremos aquí las nuevas funcionalidades para el usuario. Asegúrese de revisarlas periódicamente.

## 11 de julio de 2016

En esta versión se incluyen:

-    **Buenas noticias** El servicio Power BI Embedded ya no está en versión preliminar: ahora ya está disponible de manera general.
-    Todas las API de REST han pasado de **/beta** a **/v1.0**.
-    Los SDK de JavaScript y .NET se han actualizado a **v1.0**.
-    Ahora se pueden autenticar las llamadas de API de BI Power directamente mediante claves de API. Los tokens de aplicación solo se necesitan para la inserción. Como parte de esto, los tokens de aprovisionamiento y desarrollo han quedado en desuso en la versión v1.0 API, pero seguirán funcionando en la versión beta hasta el 30 de diciembre de 2016. Para más información, consulte [Acerca del flujo del token de aplicación en Power BI Embedded](power-bi-embedded-app-token-flow.md).
-    Compatibilidad de la seguridad de nivel de fila (RLS) con tokens de aplicación e informes insertados. Para más información, consulte [Seguridad de nivel de fila con Power BI Embedded](power-bi-embedded-rls.md).
-    Aplicación de ejemplo actualizada para todas las llamadas de API **v1.0**.
-    Compatibilidad de Power BI Embedded con SDK de Azure, PowerShell y CLI.
-    Los usuarios pueden exportar datos de visualización a un archivo **.csv**.
-    Power BI Embedded ahora es compatible con los mismos idiomas o configuraciones regionales que Microsoft Azure. Para más información, consulte [Azure - Idiomas](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).

<!---HONumber=AcomDC_0713_2016-->