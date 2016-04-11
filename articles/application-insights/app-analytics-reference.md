<properties 
	pageTitle="Material de referencia para Analytics en Application Insights" 
	description="Expresiones regulares de Analytics, la eficaz herramienta de búsqueda de Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights: material de referencia de Analytics

[Analytics](app-analytics.md) permite ejecutar consultas eficaces sobre la telemetría desde su aplicación, recopiladas por [Application Insights](app-insights-overview.md). En estas páginas se describe su lenguaje de consulta.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Expresiones regulares


[> Descripción general de las expresiones regulares](https://github.com/google/re2/wiki/Syntax).

Esta página muestra la sintaxis de expresiones regulares que acepta RE2. 
También muestra la sintaxis aceptada por PCRE, PERL y VIM.

||
|---|---
|Caracteres individuales: | 
|. |cualquier carácter, incluida posiblemente la nueva línea (s=true) 
|[xyz] |clase de caracteres 
|[^xyz] |clase de caracteres negada 
|\d |clase de caracteres de Perl 
|\D |clase de caracteres de Perl negada 
|[[:alpha:]] |clase de caracteres ASCII 
|[[:^alpha:]] |clase de caracteres ASCII negada 
|\pN |clase de caracteres de Unicode (nombre de una sola letra) 
|\p{Greek} |clase de caracteres Unicode 
|\PN |clase de caracteres de Unicode negada (nombre de una sola letra) 
|\P{Greek} |clase de caracteres Unicode negada 
|Compuestos: | 
|xy |x seguido y 
|x&#124;y |x o y (se prefiere x) 
| 
|Repeticiones: | 
| |cero o más x, se prefiere más 
|x+ |uno o más x, se prefiere más 
|x? |cero o un x, se prefiere uno 
|x{n,m} |n o n+1 o ... o m x, se prefiere más 
|x{n,} |n o más x, se prefiere más 
|x{n} |exactamente n x 
|x*? |cero más x, se prefiere menos 
|x+? |uno o más x, se prefiere menos 
|x?? |cero o un x, se prefiere cero 
|x{n,m}? |n o n+1 o ... o m x, se prefiere menos 
|x{n,}? |n o más x, se prefiere menos 
|x{n}? |exactamente n x 
|x{} |(== x*) (NO ADMITIDO) VIM 
|x{-} |(== x*?) (NO ADMITIDO) VIM 
|x{-n} |(== x{n}?) (NO ADMITIDO) VIM 
|x= |(== x?) (NO ADMITIDO) VIM 
|Restricción a la implementación: Las formas de contar x{n,m}, x{n,} y x{n} |
|rechazan formas que creen una cantidad de repeticiones mínima o máxima por encima de 1000. | 
|Las repeticiones ilimitadas no están sujetas a esta restricción.| 
|Repeticiones posesivas: | 
|x*+ |cero o más x, posesivo (NO ADMITIDO) 
|x++ |uno o más x, posesivo (NO ADMITIDO) 
|x?+ |cero o un x, posesivo (NO ADMITIDO) 
|x{n,m}+ |n o ... o m x, posesivo (NO ADMITIDO) 
|x{n,}+ |n o más x, posesivo (NO ADMITIDO) 
|x{n}+ |exactamente x, posesivo (NO ADMITIDO) 
|Agrupación:: | 
|(re) |grupo de captura numerada (subcoincidencia) 
|(?P<name>re) |grupo de captura con nombre y numerado (subcoincidencia) 
|(?<name>re) |grupo de captura con nombre y numerado (subcoincidencia) (NO ADMITIDO) 
|(?'name're) |grupo de captura con nombre y numerado (subcoincidencia) (NO ADMITIDO) 
|(?:re) |grupo de no captura 
|(?flags) |establecer marcadores dentro del grupo actual; no captura 
|(?flags:re) |establecer marcadores durante re; no captura 
|(?#text) |comentar (NO ADMITIDO) 
|(?&#124;x&#124;y&#124;z) |restablecimiento de nombre de rama (NO ADMITIDO) 
|(?>re) |coincidencia posesiva de re (NO ADMITIDO) 
|re@> |VIM de coincidencia posesiva de re (NO ADMITIDO) 
|%(re) |VIM de grupo de no captura (NO ADMITIDO) VIM 
|Marcadores: | 
|i |no distingue mayúsculas de minúsculas (false de manera predeterminada) 
|m |modo multilínea: ^ y $ coinciden con la línea de inicio/fin además del texto de inicio/fin (predeterminado false) 
|s |let . match \n (predeterminado false) 
|U |no codicioso: intercambio del significado de x* y x*?, x+ y x+?, etc (predeterminado false) 
|La sintaxis de marcador es xyz (establecido) o -xyz (vacío) o xy-z (establecido xy, vacío). |
|Segmentos vacíos: | 
|^ |al principio de texto o línea (m=true) 
|$ |al final de texto (como \z no \Z) o línea (m=true) 
|\A |al principio del texto
|\b |en el límite de las palabras ASCII (\w de un lado y \W, \A o \z en el otro) 
|\B |no en el límite de las palabras de ASCII 
|\G |PCRE al principio del subtexto que se busca (NO ADMITIDO) 
|\G |PERL al final de la última coincidencia (NO ADMITIDO) 
|\Z |al final del texto, o al principio de una nueva línea al final del texto (NO ADMITIDO) 
|\z |al final del texto 
|(?=re) |antes del texto que coincide con re (NO ADMITIDO) 
|(?!re) |antes del texto que no coincide con re (NO ADMITIDO) 
|(?<=re) |después del texto que coincide con re (NO ADMITIDO) 
|(?<!re) |después del texto que no coincide con re (NO ADMITIDO) 
|re& |VIM antes del texto que coincide con re (NO ADMITIDO)
|re@= |VIM antes del texto que coincide con re (NO ADMITIDO) 
|re@! |VIM antes del texto que no coincide con re (NO ADMITIDO) 
|re@<= |VIM después del texto que coincide con re (NO ADMITIDO) 
|re@<! |VIM después del texto que no coincide con re (NO ADMITIDO) 
|\zs |VIM establece el inicio de la coincidencia (= \K) (NO ADMITIDO) 
|\ze |VIM establece el final de la coincidencia (NO ADMITIDO) 
|\%^ |VIM del principio del archivo (NO ADMITIDO) 
|\%$ |VIM del final del archivo (NO ADMITIDO) 
|\%V |VIM en pantalla (NO ADMITIDO) 
|\%# |VIM de posición de cursor (NO ADMITIDO) 
|\%'m |VIM de marca en posición (NO ADMITIDO)
|\%23l VIM en línea 23 (NO ADMITIDO) 
|\%23c |VIM en columna 23 (NO ADMITIDO) 
|\%23v |VIM en columna virtual 23 (NO ADMITIDO) 
|Secuencias de escape: | 
|\a |campana (== \007) 
|\f |fuente de forma (== \014) 
|\t |tabulación horizontal (== \011) 
|\n |nueva línea (== \012) 
|\r |retorno de carro (== \015) 
|\v |carácter de tabulación vertical (== \013) 
|* |literal *, para cualquier carácter de puntuación * 
|\123 |código de carácter octal (hasta tres dígitos) 
|\x7F |código de carácter hexadecimal (exactamente dos dígitos) 
|\x{10FFFF} |código de carácter hexadecimal 
|\C |coincide con un byte único incluso en modo UTF-8 
|\Q...\E |texto literal ... incluso si ... tiene puntuación 
|\1 |referencia inversa (NO ADMITIDO) 
|\b |Retroceso (NO ADMITIDO) (use \010) 
|\cK |carácter de control ^K (NO ADMITIDO) (use \001 etc) 
|\e |escape (NOT SUPPORTED) (use \033) 
|\g1 |referencia inversa (NO ADMITIDO) 
|\g{1} |referencia inversa (NO ADMITIDO) 
|\g{+1} |referencia inversa (NO ADMITIDO) 
|\g{-1} |referencia inversa (NO ADMITIDO) 
|\g{name} |referencia inversa con nombre (NO ADMITIDO) 
|\g<name> |llamada de subrutina (NO ADMITIDO) 
|\g'name' |llamada de subrutina (NO ADMITIDO) 
|\k<name> |referencia inversa con nombre (NO ADMITIDO) 
|\k'name' |referencia inversa con nombre (NO ADMITIDO) 
|\lX |X minúscula (NO ADMITIDO) 
|\ux |x mayúscula (NO ADMITIDO) 
|\L...\E |texto en minúscula ... (NO ADMITIDO) 
|\K |restablecer inicio de $0 (NO ADMITIDO) 
|\N{name} |carácter de Unicode con nombre (NO ADMITIDO) 
|\R |salto de línea (NO ADMITIDO) 
|\U...\E |texto en mayúscula ... (NO ADMITIDO) 
|\X |secuencia Unicode ampliada (NO ADMITIDO) 
|\%d123 |VIM de carácter decimal 123 (NO ADMITIDO) 
|\%xFF |VIM de carácter hexadecimal FF (NO ADMITIDO) 
|\%o123 |VIM de carácter octal 123 (NO ADMITIDO) 
|\%u1234 |VIM de carácter Unicode 0x1234 (NO ADMITIDO) 
|\%U12345678 |VIM de carácter Unicode 0x12345678 (NO ADMITIDO) 
|elementos de clase de caracteres: | 
|x |carácter único 
|A-Z |rango de caracteres (inclusivo) 
|\d |clase de caracteres de PERL 
|[:foo:] |clase de caracteres ASCII de foo 
|\p{Foo} |clase de caracteres de Unicode Foo 
|\pF |clase de caracteres de Unicode F (nombre de una letra) 
|clases de caracteres con nombre como elementos de clase de caracteres: | 
|[\d] |dígitos (== \d) 
|[^\d] |no dígitos (== \D) 
|[\D] |no dígitos (== \D) 
|[^\D] |no no dígitos (== \d) 
|[[:name:]] |clase ASCII con nombre dentro de clase de caracteres (== [:name:]) 
|[^[:name:]] |clase ASCII con nombre dentro de clase de caracteres negada (== [:^name:]) 
|[\p{Name}] |propiedad Unicode con nombre dentro de clase de caracteres (== \p{Name}) 
|[^\p{Name}] |propiedad Unicode con nombre dentro de clase de caracteres negada (== \P{Name}) 
|Clases de caracteres Perl (todos solo ASCII): | 
|\d |dígitos (== [0-9]) 
|\D |no dígitos (== [^0-9]) 
|\s |espacio en blanco (== [\t\n\f\r ]) 
|\S |no espacio en blanco (== [^\t\n\f\r ]) 
|\w |caracteres de palabras (== [0-9A-Za-z\_]) 
|\W |no caracteres de palabras (== [^0-9A-Za-z\_]) 
|\h |espacio horizontal (NO ADMITIDO) 
|\H |no espacio horizontal (NO ADMITIDO) 
|\v |espacio vertical (NO ADMITIDO) 
|\V |no espacio vertical (NO ADMITIDO) 
|Clases de caracteres ASCII: | 
|[[:alnum:]] |alfanumérico (== [0-9A-Za-z]) 
|[[:alpha:]] |alfabético (== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\x00-\x7F]) 
|[[:blank:]] |blanco (== [\t ]) 
|[[:cntrl:]] |control (== [\x00-\x1F\x7F]) 
|[[:digit:]] |dígitos (== [0-9]) 
|[[:graph:]] |gráfico (== [!-~] == [A-Za-z0-9!"#$%&'()*+,-./:;<=>?@[\]^\_`{&#124;}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |espacio en blanco (== [\t\n\v\f\r ]) 
|[[:upper:]] |mayúscula (== [A-Z])
|[[:word:]] |caracteres de palabras (== [0-9A-Za-z\_]) 
|[[:xdigit:]] |dígito hexadecimal (== [0-9A-Fa-f]) 
|nombres de clases de caracteres de Unicode –categoría general: | 
|C |otro 
|Cc |control 
|Cf |formato 
|Cn |puntos de código sin asignar (NO ADMITIDO) 
|Co |uso privado 
|Cs |suplente 
|L |letra 
|LC |letra con formato de mayúscula o minúscula (NO ADMITIDO) 
|L& |letra con formato de mayúscula o minúscula (NO ADMITIDO) 
|Ll |letra minúscula 
|Lm |letra modificada 
|Lo |otra letra 
|Lt |letra con formato de título 
|Lu |letra mayúscula 
|M |marca 
|Mc |marca de espacio 
|Me |marca de comillas 
|Mn |marca de no espacio 
|N |número 
|Nd |número decimal 
|Nl |número letra 
|No |otro número 
|P |puntuación 
|Pc |puntuación de conector 
|Pd |puntuación de guion 
|Pe |puntuación de cierre 
|Pf |puntuación final 
|Pi |puntuación inicial 
|Po |otra puntuación 
|Ps |puntuación abierta 
|S |símbolo 
|Sc |símbolo de divisa 
|Sk |símbolo de modificador 
|Sm |símbolo matemático 
|So |otro símbolo 
|Z |separador 
|Zl |separador de líneas 
|Zp |separador de párrafos 
|Zs |separador de espacios 
|nombres de clases de caracteres de Unicode--alfabetos: | 
|Arabic |Árabe 
|Armenian |Armenio 
|Balinese |Balinés 
|Bamum |Bamún 
|Batak |Batak 
|Bengali |Bangla 
|Bopomofo |Bopomofo 
|Brahmi |Brahmi 
|Braille |Braille 
|Buginese |Buginés 
|Buhid |Buhid 
|Canadian_Aboriginal |Aborigen canadiense 
|Carian |Cario 
|Chakma |Chakma 
|Cham |Cham 
|Cherokee |Cheroqui 
|Common |caracteres no específicos de un alfabeto 
|Coptic |Copto
|Cuneiform |Cuneiforme 
|Cypriot |Chipriota 
|Cyrillic |Cirílico 
|Deseret |Deseret 
|Devanagari |Devanagari 
|Egyptian_Hieroglyphs |Egyptian Hieroglyphs 
|Ethiopic |Ethiopic 
|Georgian |Georgian 
|Glagolitic |Glagolítico 
|Gothic |Gótico 
|Greek |Griego 
|Gujarati |Gujarati
|Gurmukhi |Gurmukhi 
|Han |Han 
|Hangul |Hangul 
|Hanunoo |Hanunoo 
|Hebrew |Hebreo 
|Hiragana |Hiragana 
|Imperial_Aramaic |Arameo imperial 
|Heredado |alfabeto heredado de carácter anterior 
|Inscriptional_Pahlavi |Pahlavi inscripcional 
|Inscriptional_Parthian |Partiano inscripcional 
|Javanese |Javanés 
|Kaithi |Kaithi 
|Kannada |Kannada 
|Katakana |Katakana 
|Kayah_Li |Kayah Li 
|Kharoshthi |Kharoshthi 
|Khmer |Khmer 
|Lao |Lao 
|Latin |Latín 
|Lepcha |Lepcha 
|Limbu |Limbu 
|Linear_B |B lineal 
|Lycian |Licio 
|Lydian |Lidio 
|Malayalam |Malayalam 
|Mandaic |Mandeo 
|Meetei_Mayek |Meetei Mayek 
|Meroitic_Cursive |Cursiva meroítica 
|Meroitic_Hieroglyphs |Jeroglíficos meroíticos 
|Miao |Miao 
|Mongolian |Mongol 
|Myanmar |Myanmar 
|New_Tai_Lue |New Tai Lue (conocido como Tai Lue Simplificado)
|Nko |Nko 
|Ogham |Ogham 
|Ol\_Chiki |Ol Chiki 
|Old\_Italic |Antigua bastardilla
|Old\_Persian |Antiguo persa 
|Old\_South\_Arabian |Arábigo meridional antiguo 
|Old\_Turkic |Túrquico antiguo 
|Oriya |Odia 
|Osmanya |Osmaniya
|Phags\_Pa |Phags-Pa 
|Phoenician |Fenicio 
|Rejang |Rejang 
|Runic |Rúnico 
|Saurashtra |Saurashtra 
|Sharada |Sharada 
|Shavian |Shaviano 
|Sinhala |Sinhala 
|Sora\_Sompeng |Sora Sompeng 
|Sundanese |Sundanés 
|Syloti\_Nagri |Syloti Nagri 
|Syriac |Siríaco 
|Tagalog |Tagalo 
|Tagbanwa |Tagbanwa 
|Tai\_Le |Tai Le 
|Tai\_Tham |Tai Tham 
|Tai\_Viet |Tai Viet 
|Takri |Takri 
|Tamil |Tamil 
|Telugu |Telugu 
|Thaana |Thaana 
|Thai |Tailandés 
|Tibetan |Tibetano 
|Tifinagh |Tifinagh 
|Ugaritic |Ugarítico
|Vai |Vai
|Yi |Yi 
|Clases de caracteres de VIM: | 
|\i |VIM de carácter identificador (NO ADMITIDO) 
|\I |\i VIM excepto dígitos (NO ADMITIDO) 
|\k |VIM de carácter de palabra clave (NO ADMITIDO) 
|\K |\k VIM excepto dígitos (NO ADMITIDO) 
|\f |VIM de carácter de nombre de archivo (NO ADMITIDO) 
|\F |\f VIM excepto dígitos (NO ADMITIDO) 
|\p |VIM de carácter imprimible (NO ADMITIDO) 
|\P |\p VIM excepto dígitos (NO ADMITIDO) 
|\s |VIM de carácter de espacio en blanco (== [ \t]) (NO ADMITIDO) 
|\S |VIM de carácter de no espacio en blanco (== [^ \t]) (NO ADMITIDO)
|\d |VIM de dígitos (== [0-9]) 
|\D |VIM no \d VIM 
|\x |VIM de dígitos hexadecimales (== [0-9A-Fa-f]) (NO ADMITIDO) 
|\X |VIM no \x (NO ADMITIDO) 
|\o |VIM de dígitos octales (== [0-7]) (NO ADMITIDO) 
|\O |VIM no \o (NO ADMITIDO) 
|\w |VIM de carácter de palabra
|\W |VIM no \w 
|\h |VIM de encabezado de carácter de palabra (NO ADMITIDO) 
|\H |VIM no \h (NO ADMITIDO) VIM 
|\a |VIM alfabético (NO ADMITIDO) 
|\A |VIM no \a (NO ADMITIDO) VIM 
|\l |VIM de minúscula (NO ADMITIDO) 
|\L |VIM de no minúscula (NO ADMITIDO) 
|\u |VIM mayúscula (NO ADMITIDO) 
|\U |VIM de no mayúscula (NO ADMITIDO) 
|\_x |VIM \x más nueva línea, para cualquier x (NO ADMITIDO) 
|Marcadores de VIM: | 
|\c |VIM de ignorar el formato de mayúscula o minúscula (NO ADMITIDO)
|\C |VIM de coincidir la mayúscula o la minúscula (NO ADMITIDO) 
|\m |VIM mágico (NO ADMITIDO) 
|\M |VIM no mágico (NO ADMITIDO) 
|\v |VIM muy mágico (NO ADMITIDO) 
|\V |VIM muy no mágico (NO ADMITIDO) 
|\Z |VIM de ignorar diferencias en los caracteres de combinación de Unicode (NO ADMITIDO) 
|Mágico: | 
|(?{code}) |PERL de código Perl arbitrario (NO ADMITIDO) 
|(??{code}) |PERL de código Perl arbitrario pospuesto (NO ADMITIDO) 
|(?n) |Llamada recursiva al grupo de captura de regexp n (NO ADMITIDO) 
|(?+n) |llamada recursiva a grupo relativo +n (NO ADMITIDO) 
|(?-n) |llamada recursiva a grupo relativo -n (NO ADMITIDO) 
|(?C) |PCRE de llamada de PCRE (NO ADMITIDO) 
|(?R) |llamada recursiva a regexp entero (== (?0)) (NO ADMITIDO) 
|(?&name) |llamada recursiva a grupo con nombre (NO ADMITIDO) 
|(?P=name) |referencia inversa con nombre (NO ADMITIDO) 
|(?P>name) |llamada recursiva a grupo con nombre (NO ADMITIDO) 
|(?(cond)true&#124;false) |rama condicional (NO ADMITIDO) 
|(?(cond)true) |rama condicional (NO ADMITIDO) 
|(*ACCEPT) |hacer regexps más como Prolog (NO ADMITIDO) 
|(*COMMIT) |(NO ADMITIDO) 
|(*F) |(NO ADMITIDO) 
|(*FAIL) |(NO ADMITIDO) 
|(*MARK) |(NO ADMITIDO) 
|(*PRUNE) |(NO ADMITIDO) 
|(*SKIP) |(NO ADMITIDO) 
|(*THEN) |(NO ADMITIDO) 
|(*ANY) |establecer convención de línea nueva (NO ADMITIDO) 
|(*ANYCRLF) |(NO ADMITIDO) 
|(*CR) |(NO ADMITIDO) 
|(*CRLF) |(NO ADMITIDO) 
|(*LF) |(NO ADMITIDO) 
|(*BSR\_ANYCRLF) |establecer convención de \R (NO ADMITIDO) PCRE 
|(*BSR\_UNICODE) |(NO ADMITIDO) PCRE 




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->