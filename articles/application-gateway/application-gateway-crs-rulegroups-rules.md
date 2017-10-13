---
title: Reglas y grupos de reglas de CRS de firewall de aplicaciones web de Azure Application Gateway | Microsoft Docs
description: "Esta página proporciona información sobre las reglas y grupos de reglas de CRS de firewall de aplicaciones web."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: davidmu
ms.openlocfilehash: 9265be4ac4258115c9302189d84b20e4894d42bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="list-of-web-application-firewall-crs-rule-groups-and-rules-offered"></a>Lista de reglas y grupos de reglas de CRS de firewall de aplicaciones web que se ofrecen

El firewall de aplicaciones web de Application Gateway protege las aplicaciones web de las vulnerabilidades más habituales. Esto se realiza a través de reglas que se definen basándose en las versiones 2.2.9 o 3.0 de OWASP Core Rule Set. Estas reglas se pueden deshabilitar individualmente. En este artículo se incluyen las reglas y los conjuntos de reglas actuales que se ofrecen.

Las tablas siguientes son los grupos de reglas y las reglas que están disponibles al utilizar Application Gateway con el firewall de aplicaciones web.  Cada tabla representa las reglas incluidas en un grupo de reglas para una versión específica de CRS.

##<a name="owasp30"></a> OWASP_3.0

### <a name="crs910"></a>  <p x-ms-format-detection="none">REQUEST-910-IP-REPUTATION</p>

|Identificador de regla|Descripción|
|---|---|
|910011|Regla 910011|
|910012|Regla 910012|
|910000|Solicitud de cliente malintencionado conocido (según anteriores infracciones de tráfico)|
|910100|Dirección IP del cliente procedente de una ubicación de un país de ALTO riesgo|
|910120|Regla 910120|
|910130|Regla 910130|
|910150|Dirección IP de motor de búsqueda incluida en la lista negra HTTP|
|910160|Dirección IP de spammer incluida en la lista negra HTTP|
|910170|Dirección IP sospechosa incluida en la lista negra HTTP|
|910180|Dirección IP de recolector incluida en la lista negra HTTP|
|910013|Regla 910013|
|910014|Regla 910014|
|910015|Regla 910015|
|910016|Regla 910016|
|910017|Regla 910017|
|910018|Regla 910018|

### <a name="crs911"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|Identificador de regla|Descripción|
|---|---|
|911011|Regla 911011|
|911012|Regla 911012|
|911100|Método no permitido por la directiva|
|911013|Regla 911013|
|911014|Regla 911014|
|911015|Regla 911015|
|911016|Regla 911016|
|911017|Regla 911017|
|911018|Regla 911018|

### <a name="crs912"></a> <p x-ms-format-detection="none">REQUEST-912-DOS-PROTECTION</p>

|Identificador de regla|Descripción|
|---|---|
|912100|Regla 912100|
|912012|Regla 912012|
|912120|Ataque de denegación de servicio (DoS) identificado desde %@{tx.real_ip} (%@{tx.dos_block_counter} incidencias desde la última alerta)|
|912130|Regla 912130|
|912140|Regla 912140|
|912150|Regla 912150|
|912160|Regla 912160|
|912170|Posible ataque de denegación de servicio (DoS) de %@{tx.real_ip} - Número de ráfagas de solicitud = %@{ip.dos_burst_counter}|
|912013|Regla 912013|
|912014|Regla 912014|
|912019|Regla 912019|
|912171|Posible ataque de denegación de servicio (DoS) de %@{tx.real_ip} - Número de ráfagas de solicitud = %@{ip.dos_burst_counter}|
|912015|Regla 912015|
|912016|Regla 912016|
|912017|Regla 912017|
|912018|Regla 912018|

### <a name="crs913"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|Identificador de regla|Descripción|
|---|---|
|913011|Regla 913011|
|913012|Regla 913012|
|913100|Se ha encontrado un agente de usuario asociado a un examen de seguridad.|
|913110|Se ha encontrado un encabezado de solicitud asociado a un examen de seguridad.|
|913120|Se ha encontrado un argumento o nombre de archivo de solicitud asociado a un examen de seguridad.|
|913013|Regla 913013|
|913014|Regla 913014|
|913101|Se ha encontrado un agente de usuario asociado al cliente HTTP genérico o de scripts.|
|913102|Se ha encontrado un agente de usuario asociado a un robot o agente de búsqueda.|
|913015|Regla 913015|
|913016|Regla 913016|
|913017|Regla 913017|
|913018|Regla 913018|

### <a name="crs920"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|Identificador de regla|Descripción|
|---|---|
|920011|Regla 920011|
|920012|Regla 920012|
|920100|Línea de solicitud HTTP no válida|
|920130|Error al analizar el cuerpo de la solicitud|
|920140|El cuerpo de la solicitud de varias partes no superó la validación estricta =     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|920160|El encabezado Content-Length HTTP no es numérico.|
|920170|Solicitud GET o HEAD con contenido del cuerpo|
|920180|Falta el encabezado Content-Length en la solicitud POST.|
|920190|Intervalo = Último valor de bytes no válido|
|920210|Se han encontrado múltiples datos de encabezado de conexión en conflicto.|
|920220|Intento de ataque de abuso de codificación de direcciones URL|
|920240|Intento de ataque de abuso de codificación de direcciones URL|
|920250|Intento de ataque de abuso de codificación UTF8|
|920260|Intento de ataque de abuso de caracteres de ancho medio y ancho completo Unicode|
|920270|Carácter no válido en la solicitud (carácter nulo)|
|920280|Falta un encabezado host en la solicitud.|
|920290|Encabezado host vacío|
|920310|La solicitud tiene un encabezado de aceptación (Accept) vacío.|
|920311|La solicitud tiene un encabezado de aceptación (Accept) vacío.|
|920330|Encabezado de agente de usuario vacío|
|920340|La solicitud tiene contenido, pero falta el encabezado Content-Type.|
|920350|El encabezado host es una dirección IP numérica.|
|920380|Hay demasiados argumentos en la solicitud.|
|920360|Nombre de argumento demasiado largo|
|920370|Valor de argumento demasiado largo|
|920390|Se ha superado el tamaño total de argumentos.|
|920400|El tamaño del archivo cargado es demasiado grande.|
|920410|El tamaño total de los archivos cargados es demasiado grande.|
|920420|Tipo de contenido de solicitud no permitido por una directiva|
|920430|Versión del protocolo HTTP no permitida por una directiva|
|920440|Extensión de archivo URL restringida por una directiva|
|920450|Encabezado HTTP restringido por una directiva (%@{MATCHED_VAR})|
|920013|Regla 920013|
|920014|Regla 920014|
|920200|Intervalo = Demasiados campos (6 o más)|
|920201|Intervalo = Demasiados campos para solicitud PDF (35 o más)|
|920230|Varias codificaciones de direcciones URL detectadas|
|920300|Falta un encabezado de aceptación (Accept) en la solicitud.|
|920271|Carácter no válido en la solicitud (caracteres no imprimibles)|
|920320|Falta el encabezado de agente de usuario.|
|920015|Regla 920015|
|920016|Regla 920016|
|920272|Carácter no válido en la solicitud (fuera de los caracteres imprimibles, por debajo de ASCII 127)|
|920017|Regla 920017|
|920018|Regla 920018|
|920202|Intervalo = Demasiados campos para solicitud PDF (6 o más)|
|920273|Carácter no válido en la solicitud (fuera de conjunto muy estricto)|
|920274|Carácter no válido en encabezados de solicitud (fuera de conjunto muy estricto)|
|920460|Regla 920460|

### <a name="crs921"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|Identificador de regla|Descripción|
|---|---|
|921011|Regla 921011|
|921012|Regla 921012|
|921100|Ataque de contrabando de solicitudes HTTP|
|921110|Ataque de contrabando de solicitudes HTTP|
|921120|Ataque de división de respuestas HTTP|
|921130|Ataque de división de respuestas HTTP|
|921140|Ataque por inyección de encabezado HTTP a través de encabezados|
|921150|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921160|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro/Avance de línea y header-name detectados)|
|921013|Regla 921013|
|921014|Regla 921014|
|921151|Ataque por inyección de encabezado HTTP a través de la carga (Retorno de carro / Avance de línea detectado)|
|921015|Regla 921015|
|921016|Regla 921016|
|921170|Regla 921170|
|921180|Polución de parámetros HTTP (%@{TX.1})|
|921017|Regla 921017|
|921018|Regla 921018|

### <a name="crs930"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|Identificador de regla|Descripción|
|---|---|
|930011|Regla 930011|
|930012|Regla 930012|
|930100|Ataque punto punto barra (/.. /)|
|930110|Ataque punto punto barra (/.. /)|
|930120|Intento de acceso a archivo del sistema operativo|
|930130|Intento de acceso a archivo restringido|
|930013|Regla 930013|
|930014|Regla 930014|
|930015|Regla 930015|
|930016|Regla 930016|
|930017|Regla 930017|
|930018|Regla 930018|

### <a name="crs931"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|Identificador de regla|Descripción|
|---|---|
|931011|Regla 931011|
|931012|Regla 931012|
|931100|Posible ataque remoto de inclusión de archivos (RFI) = El parámetro de dirección URL utiliza una dirección IP|
|931110|Posible ataque remoto de inclusión de archivos (RFI) = Nombre de parámetro vulnerable de RFI común utilizado con carga de dirección URL|
|931120|Posible ataque remoto de inclusión de archivos (RFI) = Carga de dirección URL utilizada con carácter de interrogación de cierre (?)|
|931013|Regla 931013|
|931014|Regla 931014|
|931130|Posible ataque remoto de inclusión de archivos (RFI) = Referencia o vínculo fuera del dominio|
|931015|Regla 931015|
|931016|Regla 931016|
|931017|Regla 931017|
|931018|Regla 931018|

### <a name="crs932"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|Identificador de regla|Descripción|
|---|---|
|932011|Regla 932011|
|932012|Regla 932012|
|932120|Ejecución de comando remoto = Comando de Windows PowerShell encontrado|
|932130|Ejecución de comando remoto = Expresión de shell de Unix encontrada|
|932140|Ejecución de comando remoto = Comando FOR/IF de Windows encontrado|
|932160|Ejecución de comando remoto = Código de shell de Unix encontrado|
|932170|Ejecución de comando remoto = Shellshock (CVE-2014-6271)|
|932171|Ejecución de comando remoto = Shellshock (CVE-2014-6271)|
|932013|Regla 932013|
|932014|Regla 932014|
|932015|Regla 932015|
|932016|Regla 932016|
|932017|Regla 932017|
|932018|Regla 932018|

### <a name="crs933"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|Identificador de regla|Descripción|
|---|---|
|933011|Regla 933011|
|933012|Regla 933012|
|933100|Ataque por inyección en PHP = Se ha encontrado etiqueta de apertura o cierre|
|933110|Ataque por inyección en PHP = Se ha encontrado carga de archivo de script PHP|
|933120|Ataque por inyección en PHP = Se ha encontrado directiva de configuración|
|933130|Ataque por inyección en PHP = Se han encontrado variables|
|933150|Ataque por inyección en PHP = Se ha encontrado nombre de función de PHP de alto riesgo|
|933160|Ataque por inyección en PHP = Se ha encontrado llamada de función de PHP de alto riesgo|
|933180|Ataque por inyección en PHP = Se ha encontrado llamada de función de variable|
|933013|Regla 933013|
|933014|Regla 933014|
|933151|Ataque por inyección en PHP = Se ha encontrado nombre de función de PHP de riesgo medio|
|933015|Regla 933015|
|933016|Regla 933016|
|933131|Ataque por inyección en PHP = Se han encontrado variables|
|933161|Ataque por inyección en PHP = Se ha encontrado llamada de función de PHP de valor bajo|
|933111|Ataque por inyección en PHP = Se ha encontrado carga de archivo de script PHP|
|933017|Regla 933017|
|933018|Regla 933018|

### <a name="crs941"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|Identificador de regla|Descripción|
|---|---|
|941011|Regla 941011|
|941012|Regla 941012|
|941100|Ataque XSS detectado mediante libinjection|
|941110|Filtro XSS - Categoría 1 = Vector de etiqueta de script|
|941130|Filtro XSS - Categoría 3 = Vector de atributo|
|941140|Filtro XSS - Categoría 4 = Vector URI de JavaScript|
|941150|Filtro XSS - Categoría 5 = Atributos HTML no permitidos|
|941180|Palabras clave de lista negra de node-validator|
|941190|Filtros XSS de IE: ataque detectado|
|941200|Filtros XSS de IE: ataque detectado|
|941210|Filtros XSS de IE: ataque detectado|
|941220|Filtros XSS de IE: ataque detectado|
|941230|Filtros XSS de IE: ataque detectado|
|941240|Filtros XSS de IE: ataque detectado|
|941260|Filtros XSS de IE: ataque detectado|
|941270|Filtros XSS de IE: ataque detectado|
|941280|Filtros XSS de IE: ataque detectado|
|941290|Filtros XSS de IE: ataque detectado|
|941300|Filtros XSS de IE: ataque detectado|
|941310|Filtro XSS de codificación mal formulada US-ASCII: ataque detectado|
|941350|XSS de IE con codificación UTF-7: ataque detectado|
|941013|Regla 941013|
|941014|Regla 941014|
|941320|Posible ataque XSS detectado: controlador de etiquetas HTML|
|941015|Regla 941015|
|941016|Regla 941016|
|941017|Regla 941017|
|941018|Regla 941018|

### <a name="crs942"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|Identificador de regla|Descripción|
|---|---|
|942011|Regla 942011|
|942012|Regla 942012|
|942100|Ataque por inyección de código SQL detectado mediante libinjection|
|942140|Ataque por inyección de código SQL = nombres de base de datos comunes detectados|
|942160|Detección de pruebas de inyección de código SQL a ciegas mediante sleep() o benchmark()|
|942170|Detección de intentos de inyección con las funciones benchmark y sleep que incluyen consultas condicionales|
|942230|Detección de intentos de inyección de código SQL condicionales|
|942270|Búsqueda de inyección de código SQL básico Cadena de ataque común para Oracle MySQL y otras bases de datos|
|942290|Búsqueda de intentos de inyección de código SQL MongoDB básico|
|942320|Detección de inyecciones de funciones o procedimientos almacenados de MySQL y PostgreSQL|
|942350|Detección de intentos de inyección de UDF MySQL y otras manipulaciones de datos o estructuras|
|942013|Regla 942013|
|942014|Regla 942014|
|942150|Ataque por inyección de código SQL|
|942410|Ataque por inyección de código SQL|
|942440|Secuencia de comentario SQL detectada|
|942450|Codificación hexadecimal de SQL identificada|
|942015|Regla 942015|
|942016|Regla 942016|
|942251|Detección de inyecciones HAVING|
|942460|Alerta de detección de anomalías de metacaracteres: repetición de caracteres que no se usan en las palabras|
|942017|Regla 942017|
|942018|Regla 942018|

### <a name="crs943"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|Identificador de regla|Descripción|
|---|---|
|943011|Regla 943011|
|943012|Regla 943012|
|943100|Posible ataque de fijación de sesión = Definición de valores de cookies en HTML|
|943110|Posible ataque de fijación de sesión = Nombre del parámetro SessionID con origen de referencia fuera del dominio|
|943120|Posible ataque de fijación de sesión = Nombre del parámetro SessionID con origen de referencia fuera del dominio|
|943013|Regla 943013|
|943014|Regla 943014|
|943015|Regla 943015|
|943016|Regla 943016|
|943017|Regla 943017|
|943018|Regla 943018|

##<a name="owasp229"></a> OWASP_2.2.9

### <a name="crs20"></a> crs_20_protocol_violations

|Identificador de regla|Descripción|
|---|---|
|960911|Línea de solicitud HTTP no válida|
|981227|Error de Apache = URI no válido en la solicitud|
|960912|Error al analizar el cuerpo de la solicitud|
|960914|El cuerpo de la solicitud de varias partes no superó la validación estricta =     PE %@{REQBODY_PROCESSOR_ERROR}     BQ %@{MULTIPART_BOUNDARY_QUOTED}     BW %@{MULTIPART_BOUNDARY_WHITESPACE}     DB %@{MULTIPART_DATA_BEFORE}     DA %@{MULTIPART_DATA_AFTER}     HF %@{MULTIPART_HEADER_FOLDING}     LF %@{MULTIPART_LF_LINE}     SM %@{MULTIPART_SEMICOLON_MISSING}     IQ %@{MULTIPART_INVALID_QUOTING}     IH %@{MULTIPART_INVALID_HEADER_FOLDING}     FLE %@{MULTIPART_FILE_LIMIT_EXCEEDED}|
|960915|El analizador de varias partes ha detectado un posible límite no coincidente.|
|960016|El encabezado Content-Length HTTP no es numérico.|
|960011|Solicitud GET o HEAD con contenido del cuerpo|
|960012|Falta el encabezado Content-Length en la solicitud POST.|
|960902|Uso no válido de codificación de identidades|
|960022|Encabezado esperado no permitido para HTTP 1.0|
|960020|El encabezado Pragma requiere el encabezado Cache-Control para las solicitudes HTTP/1.1.|
|958291|Intervalo = El campo existe y comienza por 0|
|958230|Intervalo = Último valor de bytes no válido|
|958295|Se han encontrado múltiples datos de encabezado de conexión en conflicto.|
|950107|Intento de ataque de abuso de codificación de direcciones URL|
|950109|Varias codificaciones de direcciones URL detectadas|
|950108|Intento de ataque de abuso de codificación de direcciones URL|
|950801|Intento de ataque de abuso de codificación UTF8|
|950116|Intento de ataque de abuso de caracteres de ancho medio y ancho completo Unicode|
|960901|Carácter no válido en la solicitud|
|960018|Carácter no válido en la solicitud|

### <a name="crs21"></a> crs_21_protocol_anomalies

|Identificador de regla|Descripción|
|---|---|
|960008|Falta un encabezado host en la solicitud.|
|960007|Encabezado host vacío|
|960015|Falta un encabezado de aceptación (Accept) en la solicitud.|
|960021|La solicitud tiene un encabezado de aceptación (Accept) vacío.|
|960009|Falta un encabezado de agente de usuario en la solicitud|
|960006|Encabezado de agente de usuario vacío|
|960904|La solicitud tiene contenido, pero falta el encabezado Content-Type.|
|960017|El encabezado host es una dirección IP numérica.|

### <a name="crs23"></a> crs_23_request_limits

|Identificador de regla|Descripción|
|---|---|
|960209|Nombre de argumento demasiado largo|
|960208|Valor de argumento demasiado largo|
|960335|Hay demasiados argumentos en la solicitud.|
|960341|Se ha superado el tamaño total de argumentos.|
|960342|El tamaño del archivo cargado es demasiado grande.|
|960343|El tamaño total de los archivos cargados es demasiado grande.|

### <a name="crs30"></a> crs_30_http_policy

|Identificador de regla|Descripción|
|---|---|
|960032|Método no permitido por la directiva|
|960010|Tipo de contenido de solicitud no permitido por una directiva|
|960034|Versión del protocolo HTTP no permitida por una directiva|
|960035|Extensión de archivo URL restringida por una directiva|
|960038|Encabezado HTTP restringido por una directiva|

### <a name="crs35"></a> crs_35_bad_robots

|Identificador de regla|Descripción|
|---|---|
|990002|La solicitud indica que un examen de seguridad ha analizado el sitio|
|990901|La solicitud indica que un examen de seguridad ha analizado el sitio|
|990902|La solicitud indica que un examen de seguridad ha analizado el sitio|
|990012|Agente de búsqueda de sitios web malintencionados|

### <a name="crs40"></a>crs_40_generic_attacks

|Identificador de regla|Descripción|
|---|---|
|960024|Alerta de detección de anomalías de metacaracteres: repetición de caracteres que no se usan en las palabras|
|950008|Inyección de etiquetas de ColdFusion no documentadas|
|950010|Ataque por inyección de LDAP|
|950011|Ataque por inyección de SSI|
|950018|Dirección URL de XSS de PDF universal detectada|
|950019|Ataque por inyección de correo electrónico|
|950012|Ataque de contrabando de solicitudes HTTP|
|950910|Ataque de división de respuestas HTTP|
|950911|Ataque de división de respuestas HTTP|
|950117|Ataque remoto de inclusión de archivos|
|950118|Ataque remoto de inclusión de archivos|
|950119|Ataque remoto de inclusión de archivos|
|950120|Posible ataque remoto de inclusión de archivos (RFI) = Referencia o vínculo fuera del dominio|
|981133|Regla 981133|
|981134|Regla 981134|
|950009|Ataque de fijación de sesión|
|950003|Fijación de sesión|
|950000|Fijación de sesión|
|950005|Intento de acceso a archivo remoto|
|950002|Acceso a comando del sistema|
|950006|Inyección de comando del sistema|
|959151|Ataque por inyección en PHP|
|958976|Ataque por inyección en PHP|
|958977|Ataque por inyección en PHP|

### <a name="crs41sql"></a> crs_41_sql_injection_attacks

|Identificador de regla|Descripción|
|---|---|
|981231|Secuencia de comentario SQL detectada|
|981260|Codificación hexadecimal de SQL identificada|
|981320|Ataque por inyección de código SQL = nombres de base de datos comunes detectados|
|981300|Regla 981300|
|981301|Regla 981301|
|981302|Regla 981302|
|981303|Regla 981303|
|981304|Regla 981304|
|981305|Regla 981305|
|981306|Regla 981306|
|981307|Regla 981307|
|981308|Regla 981308|
|981309|Regla 981309|
|981310|Regla 981310|
|981311|Regla 981311|
|981312|Regla 981312|
|981313|Regla 981313|
|981314|Regla 981314|
|981315|Regla 981315|
|981316|Regla 981316|
|981317|Alerta de detección de anomalías de instrucción SELECT SQL|
|950007|Ataque por inyección de código SQL a ciegas|
|950001|Ataque por inyección de código SQL|
|950908|Ataque por inyección de código SQL|
|959073|Ataque por inyección de código SQL|
|981272|Detección de pruebas de inyección de código SQL a ciegas mediante sleep() o benchmark()|
|981250|Detección de intentos de inyección con las funciones benchmark y sleep que incluyen consultas condicionales|
|981241|Detección de intentos de inyección de código SQL condicionales|
|981276|Búsqueda de inyección de código SQL básico Cadena de ataque común para Oracle MySQL y otras bases de datos|
|981270|Búsqueda de intentos de inyección de código SQL MongoDB básico|
|981253|Detección de inyecciones de funciones o procedimientos almacenados de MySQL y PostgreSQL|
|981251|Detección de intentos de inyección de UDF MySQL y otras manipulaciones de datos o estructuras|

### <a name="crs41xss"></a> crs_41_xss_attacks

|Identificador de regla|Descripción|
|---|---|
|973336|Filtro XSS - Categoría 1 = Vector de etiqueta de script|
|973338|Filtro XSS - Categoría 3 = Vector URI de JavaScript|
|981136|Regla 981136|
|981018|Regla 981018|
|958016|Ataque de scripts de sitios (XSS)|
|958414|Ataque de scripts de sitios (XSS)|
|958032|Ataque de scripts de sitios (XSS)|
|958026|Ataque de scripts de sitios (XSS)|
|958027|Ataque de scripts de sitios (XSS)|
|958054|Ataque de scripts de sitios (XSS)|
|958418|Ataque de scripts de sitios (XSS)|
|958034|Ataque de scripts de sitios (XSS)|
|958019|Ataque de scripts de sitios (XSS)|
|958013|Ataque de scripts de sitios (XSS)|
|958408|Ataque de scripts de sitios (XSS)|
|958012|Ataque de scripts de sitios (XSS)|
|958423|Ataque de scripts de sitios (XSS)|
|958002|Ataque de scripts de sitios (XSS)|
|958017|Ataque de scripts de sitios (XSS)|
|958007|Ataque de scripts de sitios (XSS)|
|958047|Ataque de scripts de sitios (XSS)|
|958410|Ataque de scripts de sitios (XSS)|
|958415|Ataque de scripts de sitios (XSS)|
|958022|Ataque de scripts de sitios (XSS)|
|958405|Ataque de scripts de sitios (XSS)|
|958419|Ataque de scripts de sitios (XSS)|
|958028|Ataque de scripts de sitios (XSS)|
|958057|Ataque de scripts de sitios (XSS)|
|958031|Ataque de scripts de sitios (XSS)|
|958006|Ataque de scripts de sitios (XSS)|
|958033|Ataque de scripts de sitios (XSS)|
|958038|Ataque de scripts de sitios (XSS)|
|958409|Ataque de scripts de sitios (XSS)|
|958001|Ataque de scripts de sitios (XSS)|
|958005|Ataque de scripts de sitios (XSS)|
|958404|Ataque de scripts de sitios (XSS)|
|958023|Ataque de scripts de sitios (XSS)|
|958010|Ataque de scripts de sitios (XSS)|
|958411|Ataque de scripts de sitios (XSS)|
|958422|Ataque de scripts de sitios (XSS)|
|958036|Ataque de scripts de sitios (XSS)|
|958000|Ataque de scripts de sitios (XSS)|
|958018|Ataque de scripts de sitios (XSS)|
|958406|Ataque de scripts de sitios (XSS)|
|958040|Ataque de scripts de sitios (XSS)|
|958052|Ataque de scripts de sitios (XSS)|
|958037|Ataque de scripts de sitios (XSS)|
|958049|Ataque de scripts de sitios (XSS)|
|958030|Ataque de scripts de sitios (XSS)|
|958041|Ataque de scripts de sitios (XSS)|
|958416|Ataque de scripts de sitios (XSS)|
|958024|Ataque de scripts de sitios (XSS)|
|958059|Ataque de scripts de sitios (XSS)|
|958417|Ataque de scripts de sitios (XSS)|
|958020|Ataque de scripts de sitios (XSS)|
|958045|Ataque de scripts de sitios (XSS)|
|958004|Ataque de scripts de sitios (XSS)|
|958421|Ataque de scripts de sitios (XSS)|
|958009|Ataque de scripts de sitios (XSS)|
|958025|Ataque de scripts de sitios (XSS)|
|958413|Ataque de scripts de sitios (XSS)|
|958051|Ataque de scripts de sitios (XSS)|
|958420|Ataque de scripts de sitios (XSS)|
|958407|Ataque de scripts de sitios (XSS)|
|958056|Ataque de scripts de sitios (XSS)|
|958011|Ataque de scripts de sitios (XSS)|
|958412|Ataque de scripts de sitios (XSS)|
|958008|Ataque de scripts de sitios (XSS)|
|958046|Ataque de scripts de sitios (XSS)|
|958039|Ataque de scripts de sitios (XSS)|
|958003|Ataque de scripts de sitios (XSS)|
|973300|Posible ataque XSS detectado: controlador de etiquetas HTML|
|973301|Ataque XSS detectado|
|973302|Ataque XSS detectado|
|973303|Ataque XSS detectado|
|973304|Ataque XSS detectado|
|973305|Ataque XSS detectado|
|973306|Ataque XSS detectado|
|973307|Ataque XSS detectado|
|973308|Ataque XSS detectado|
|973309|Ataque XSS detectado|
|973311|Ataque XSS detectado|
|973313|Ataque XSS detectado|
|973314|Ataque XSS detectado|
|973331|Filtros XSS de IE: ataque detectado|
|973315|Filtros XSS de IE: ataque detectado|
|973330|Filtros XSS de IE: ataque detectado|
|973327|Filtros XSS de IE: ataque detectado|
|973326|Filtros XSS de IE: ataque detectado|
|973346|Filtros XSS de IE: ataque detectado|
|973345|Filtros XSS de IE: ataque detectado|
|973324|Filtros XSS de IE: ataque detectado|
|973323|Filtros XSS de IE: ataque detectado|
|973348|Filtros XSS de IE: ataque detectado|
|973321|Filtros XSS de IE: ataque detectado|
|973320|Filtros XSS de IE: ataque detectado|
|973318|Filtros XSS de IE: ataque detectado|
|973317|Filtros XSS de IE: ataque detectado|
|973329|Filtros XSS de IE: ataque detectado|
|973328|Filtros XSS de IE: ataque detectado|

### <a name="crs42"></a> crs_42_tight_security

|Identificador de regla|Descripción|
|---|---|
|950103|Ataque punto punto barra|

### <a name="crs45"></a> crs_45_trojans

|Identificador de regla|Descripción|
|---|---|
|950110|Acceso a puerta trasera|
|950921|Acceso a puerta trasera|
|950922|Acceso a puerta trasera|

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de cómo deshabilitar reglas de WAF, consulte: [Personalización de reglas de WAF](application-gateway-customize-waf-rules-portal.md).

[1]: ./media/application-gateway-integration-security-center/figure1.png