---
title: "Expresiones regulares en búsquedas de registros en OMS Log Analytics | Microsoft Docs"
description: "Puede usar la palabra clave RegEx en las búsquedas de registros en Log Analytics para filtrar los resultados según una expresión regular.  En este artículo se proporciona la sintaxis para estas expresiones con varios ejemplos."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Uso de expresiones regulares para filtrar búsquedas de registros en Log Analytics

Las [búsquedas de registros](log-analytics-log-searches.md) permiten extraer información del repositorio de Log Analytics.  Las [expresiones de filtro](log-analytics-search-reference.md#filter-expressions) permiten filtrar los resultados de la búsqueda según criterios específicos.  La palabra clave **RegEx** permite especificar una expresión regular para este filtro.  

En este artículo se proporcionan detalles sobre la sintaxis de expresión regular utilizada por Log Analytics.

> [!NOTE]
> Solo puede utilizar expresiones regulares con campos de búsqueda.  Para más información sobre los campos de búsqueda, consulte **Tipos de campo** en [Descripción de las búsquedas de registros en Log Analytics](log-analytics-log-searches.md#use-additional-filters).


## <a name="regex-keyword"></a>Palabra clave RegEx

Use la siguiente sintaxis para usar la palabra clave **RegEx** en una búsqueda de registros.  Puede usar las otras secciones de este artículo para determinar la sintaxis de la expresión regular.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Por ejemplo, para usar una expresión regular para devolver registros de alerta con un tipo de *Advertencia* o *Error*, usaría la siguiente búsqueda de registros.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Coincidencias parciales
Tenga en cuenta que la expresión regular debe coincidir con todo el texto de la propiedad.  Las coincidencias parciales no devolverán ningún registro.  Por ejemplo, si intentaba devolver registros de un equipo denominado srv01.contoso.com, la siguiente búsqueda de registros **no** devolvería ningún registro.

    Computer=RegEx("srv..")

Esto es porque solo la primera parte del nombre coincide con la expresión regular.  Las dos búsquedas de registros siguientes devolverían registros de este equipo porque coinciden con el nombre completo.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Caracteres
Especifique diferentes caracteres.

| Character | Descripción | Ejemplo | Coincidencias de ejemplo |
|:--|:--|:--|:--|
| a | Una repetición del carácter. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | Cualquier carácter individual. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | Ninguna o una repetición del carácter. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Ninguna o más repeticiones del carácter. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Una o más repeticiones del carácter. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Coincidencia con cualquier carácter individual entre corchetes. | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Coincidencia con un carácter individual del intervalo.  Puede incluir varios intervalos. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Ninguno de los caracteres entre corchetes. | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Ninguno de los caracteres del intervalo. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Coincidencia con un intervalo de caracteres numéricos. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | Cualquier cadena de caracteres. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Varias repeticiones de carácter
Especifique varias repeticiones de un determinado carácter.

| Character | Descripción | Ejemplo | Coincidencias de ejemplo |
|:--|:--|:--|:--|
| a{n} |  *n* repeticiones del carácter. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  *n* o más repeticiones del carácter. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  De *n* a *m* repeticiones del carácter. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Expresiones lógicas
Seleccione entre varios valores.

| Character | Descripción | Ejemplo | Coincidencias de ejemplo |
|:--|:--|:--|:--|
| &#124; | O lógico.  Devuelve el resultado si hay coincidencia en una de las expresiones. | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | Warning (Advertencia)<br>Error |
| & | Y lógico.  Devuelve el resultado si hay coincidencia en ambas expresiones. | EventData=regex("(Security.\*&.\*success.\*)") | Auditoría correcta de seguridad |


## <a name="literals"></a>Literales
Convierta caracteres especiales en caracteres literales.  Esto incluye caracteres que proporcionan funcionalidad a las expresiones regulares, como ?-\*^\[\]{}\(\)+\|.&.

| Character | Descripción | Ejemplo | Coincidencias de ejemplo |
|:--|:--|:--|:--|
| \\ | Convierte un carácter especial en un literal. | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error]Archivo no encontrado.<br>Error-Archivo no encontrado. |


## <a name="next-steps"></a>Pasos siguientes

* Familiarícese con las [búsquedas de registros](log-analytics-log-searches.md) para ver y analizar datos en el repositorio de Log Analytics.
