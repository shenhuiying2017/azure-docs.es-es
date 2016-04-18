<properties
   pageTitle="Qué es Microsoft Power BI Embedded"
   description=""
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
   ms.author="jocaplan"/>


# Qué es Microsoft Power BI Embedded

**Microsoft Power BI Embedded** le permite integrar informes de Power BI en las aplicaciones web o móviles, por lo que no necesita crear soluciones personalizadas para visualizar los datos de los usuarios.

![](media\powerbi-embedded-whats-is\what-is.png)

**Microsoft Power BI Embedded** es un servicio de Azure que permitirá a los proveedores de software independientes (ISV) exponer las experiencias de datos de Power BI en sus aplicaciones. Como ISV, ha creado aplicaciones. Estas aplicaciones tienen sus propios usuarios y un conjunto de características diferente. También podría ocurrir que estas aplicaciones tengan algunos elementos de datos integrados como gráficos e informes que ahora pueden disponer de la tecnología de **Microsoft Power BI Embedded**. Los usuarios de la aplicación no necesitan una cuenta de Power BI para poder usarla. Pueden seguir iniciando sesión en su aplicación como hacían anteriormente, y ver e interactuar con la experiencia de iconos e informes de Power BI sin necesitar ninguna licencia adicional.

## Licencias de Microsoft Power BI Embedded

En el modelo de uso de **Microsoft Power BI Embedded**, la administración de licencias de Power BI no es responsabilidad del usuario final. En su lugar, el desarrollador de la aplicación que está consumiendo los elementos visuales, adquiere **representaciones** que se cobran a la suscripción que posee esos recursos.

## Modelo conceptual de Microsoft Power BI Embedded

![](media\powerbi-embedded-whats-is\model.png)

Al igual que cualquier otro servicio en Azure, los recursos de **Microsoft Power BI Embedded** se aprovisionan mediante las [API de ARM de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que aprovisiona es una **Colección de área de trabajo de Power BI**.

## Colección de área de trabajo

Una **colección de área de trabajo** es el contenedor de Azure de nivel superior para los recursos que contienen 0 o más **áreas de trabajo**. Una **colección** de **área de trabajo** tiene todas las propiedades estándar de Azure, así como las siguientes:

-	**Claves de acceso**: claves que se usan al llamar de forma segura a las API de Power BI (se describen en una sección posterior).
-	**Usuarios**: los usuarios de Azure Active Directory (AAD) que tienen derechos de administrador para administrar la colección de área de trabajo de Power BI a través del Portal de Azure o de la API de ARM.
-	**Región**: como parte del aprovisionamiento de una **colección de área de trabajo**, puede seleccionar una región para que se aprovisione. Para obtener más información, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

## Área de trabajo

Un **área de trabajo** es un contenedor de contenido de Power BI, que puede incluir conjuntos de datos, informes y paneles. Un **área de trabajo** está vacío cuando se crea por primera vez. Durante la versión preliminar, creará todo el contenido con Power BI Desktop y lo cargará en una de las áreas de trabajo mediante las [API de REST de Power BI](http://docs.powerbi.apiary.io/reference).

## Usar las áreas de trabajo y las colecciones de área de trabajo
Las **colecciones de área de trabajo** y las **áreas de trabajo** son contenedores de contenido que se usan y se organizan de la forma que mejor se adapte al diseño de la aplicación que está creando. Podrá disponer el contenido dentro de ellas de muchas maneras diferentes. Puede decidir colocar todo el contenido en un área de trabajo y después usar los tokens de aplicación para subdividir más el contenido entre sus clientes. También puede decidir colocar todos sus clientes en áreas de trabajo independientes de forma que exista algo de separación entre ellos. O bien, puede elegir organizar los usuarios por región en lugar de por cliente. Este diseño flexible le permite elegir la mejor manera de organizar el contenido.
## Orígenes de datos de la versión preliminar

Habilitaremos un conjunto limitado de orígenes de datos para la versión preliminar, como los siguientes:

### Consulta directa

Admitiremos conexiones de consulta directa en los orígenes de la nube para la versión preliminar. Esto significa que podrá conectarse a los orígenes de datos para mostrar los datos más recientes. Estos orígenes de datos deben ser accesibles desde la nube y deben usar la autenticación básica. Algunos de los candidatos ideales para esto son:

-	SQL Azure
-	SQL Azure DW
-	HD Insight Spark

## Conjuntos de datos en caché

Los conjuntos de datos en caché pueden usarse en la versión preliminar. Sin embargo, no puede actualizar los datos en caché una vez que se han cargado en **Microsoft Power BI Embedded**.

## Autenticación y autorización con tokens de aplicación

**Microsoft Power BI Embedded** aplaza a su aplicación para realizar la autenticación y la autorización de usuario necesaria. No hay ningún requisito explícito por el que los usuarios finales tengan que ser clientes de Azure Active Directory (Azure AD). En su lugar, la aplicación expresará la autorización para representar un informe de Power BI a **Microsoft Power BI Embedded** mediante los **Tokens de autenticación de la aplicación (tokens de aplicación)**. Estos **tokens de aplicación** se crean según sea necesario cuando la aplicación quiere presentar un informe. Consulte [tokens de aplicación](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

### Tokens de autenticación de la aplicación

Los **Tokens de autenticación de la aplicación (tokens de aplicación)** se usan para autenticarse en **Microsoft Power BI Embedded**. Existen tres tipos de **tokens de aplicación**:

1.	Aprovisionar tokens: se usa al aprovisionar una nueva **área de trabajo** en una **colección de área de trabajo**
2.	Desarrollar tokens: se usa al realizar llamadas directamente a las **API de REST de Power BI**
3.	Tokens de incrustación: se usan al realizar llamadas para presentar un informe en el iframe incrustado

Estos tokens se usan para las diversas fases de las interacciones con **Microsoft Power BI Embedded**. Los tokens están diseñados para que pueda delegar permisos desde su aplicación a Power BI.

### Generar tokens de aplicación

Los SDK que se proporcionan en la versión preliminar le permiten generar tokens. En primer lugar, llame a uno de los métodos de Create\_\_\_Token(). En segundo lugar, llame al método Generate() con la clave de acceso recuperada de la **colección de área de trabajo**. Los métodos de creación básicos de los tokens se definen en la clase Microsoft.PowerBI.Security.PowerBIToken y son los siguientes:

-	[CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx)
-	[CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx)
-	[CreateReportEmbedToken](https://msdn.microsoft.com/library/mt710366.aspx)

Para obtener un ejemplo de cómo usar [CreateProvisionToken](https://msdn.microsoft.com/library/mt670218.aspx) y [CreateDevToken](https://msdn.microsoft.com/library/mt670215.aspx), consulte [Get started with Microsoft Power BI Embedded sample code (Introducción al código de ejemplo de Microsoft Power BI Embedded)](power-bi-embedded-get-started-sample.md).


## Otras referencias
- [Common Microsoft Power BI Embedded scenarios (Escenarios comunes de Microsoft Power BI Embedded)](power-bi-embedded-scenarios.md)
- [Get started with Microsoft Power BI Embedded Preview (Introducción a la versión preliminar de Microsoft Power BI Embedded)](power-bi-embedded-get-started.md)
- [App Tokens (Tokens de aplicación)](power-bi-embedded-get-started-sample.md#key-flow)
- [Power BI REST APIs (API de REST de Power BI)](http://docs.powerbi.apiary.io/reference)
- [Regiones de Azure](https://azure.microsoft.com/regions/)

<!---HONumber=AcomDC_0406_2016-->