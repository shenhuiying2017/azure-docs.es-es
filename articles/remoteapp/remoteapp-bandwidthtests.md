---
title: 'Azure RemoteApp: probar el uso de ancho de banda de red con algunos escenarios comunes | Microsoft Docs'
description: Obtenga más información sobre escenarios de uso comunes que pueden ayudarle a descubrir las necesidades de ancho de banda de la red de Azure RemoteApp.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Azure RemoteApp: probar el uso de ancho de banda de red con algunos escenarios comunes
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Como se explicó en [Calcular el uso del ancho de banda de red de Azure RemoteApp](remoteapp-bandwidth.md), la mejor manera de averiguar cuál es el impacto de Azure RemoteApp en la red es ejecutar algunas pruebas de uso. Ejecute estas pruebas para establecer un período de tiempo y medir el ancho de banda necesario para cada escenario. Si dispone de la funcionalidad, también puede medir la vibración de red y la pérdida de paquetes de red para comprender los patrones de red que se crean en su entorno específico.

Al evaluar el uso de ancho de banda, recuerde que el uso varía de un usuario a otro dentro de la empresa. Por ejemplo, los usuarios que escriben y leen texto suelen consumir menos ancho de banda que los usuarios que trabajan con vídeo. Para obtener mejores resultados, estudie las necesidades de sus propios usuarios y cree una combinación de los siguientes escenarios que mejor represente a los usuarios de la empresa. No olvide [revisar los factores que afectan al uso de ancho de banda y a la experiencia del usuario](remoteapp-bandwidthexperience.md). Esto lo ayudará a identificar las pruebas ideales.

Infórmese primero sobre las pruebas, seleccione la combinación y después ejecútelas. Puede usar la tabla siguiente para realizar un seguimiento del rendimiento.

> [!NOTE]
> Si no puede realizar sus propias pruebas de red, o no dispone de tiempo para hacerlo, consulte nuestras [recomendaciones y cálculos básicos de ancho de banda de red](remoteapp-bandwidthguidelines.md). Como es posible que el consumo por kilómetro varíe, debe ejecutar sus propias pruebas si *puede*.
> 
> 

## Pruebas de uso
Cada una de estas pruebas se ejecutan durante distintos períodos de tiempo y prueban diferentes funciones y características que consumen ancho de banda de red. Recuerde que debe elegir la combinación de pruebas que mejor se ajuste a los usuarios individuales de la empresa.

### PowerPoint executivo/complejo: ejecución entre 1000 y 900 segundos
Un usuario presenta entre 45 y 50 diapositivas de alta fidelidad mediante Microsoft Office PowerPoint en modo de pantalla completa. Las diapositivas deben contener imágenes, transiciones (con animaciones) y fondos con degradado de color que sean habituales en su empresa. El usuario debe dedicar al menos 20 segundos a cada diapositiva.

En este escenario se crea tráfico por ráfagas cuando pasa de una diapositiva a la siguiente en la presentación.

### PowerPoint simple: ejecución durante aprox. 620 segundos
Un usuario presenta un archivo sencillo de PowerPoint de aproximadamente 30 diapositivas mediante Microsoft Office PowerPoint en modo de pantalla completa. Las diapositivas incluyen más texto que en el escenario de PowerPoint ejecutivo/complejo y tienen fondos e imágenes más sencillos (diagramas en negro).

### Internet Explorer: ejecución durante aprox. 250 segundos
Un usuario explora la web mediante Internet Explorer. El usuario explora una combinación de texto, imágenes naturales y algunos diagramas esquemáticos y se desplaza por ellos. Las páginas web se almacenan en la unidad de disco local del servidor host de sesión de Escritorio remoto como archivo .MHT. El usuario se desplaza con las teclas Re Pág, Av Pág, Inicio y Fin, con distintos intervalos para cada tecla/tipo de desplazamiento.

    - Down - 250 keystrokes very 500 ms
    - Page Up - 36 keystrokes every 1000 ms
    - Down - 75 keystrokes every 100 ms
    - Page Down - 20 keystrokes every 500 ms
    - Up - 120 keystrokes every 300 ms

### Documento PDF - simple: ejecución durante aprox. 610 segundos
El usuario busca y lee un documento PDF de diversas maneras mediante Adobe Acrobat Reader. El documento debe constar de tablas, gráficos sencillos y varias fuentes de texto. El documento tiene entre 35 y 40 páginas. El usuario se desplaza por el documento en dos direcciones distintas, hacia atrás y hacia delante, en cuatro tamaños de zoom diferentes (ajustar a la página, ajustar al ancho, 100% y otro de su elección). La función de zoom garantiza que el texto (fuente) se representa en distintos tamaños. El desplazamiento se reduce al uso de las teclas Re Pág, Av Pág, Inicio y Fin, con distintos intervalos para cada desplazamiento.

### Documento PDF - mixto: ejecución durante aprox. 320 segundos
El usuario busca y lee un documento PDF de diversas maneras mediante Adobe Acrobat Reader. El documento consta de imágenes de alta calidad (como fotografías), tablas, gráficos sencillos y varias fuentes de texto. El usuario se desplaza por el documento en dos direcciones distintas, hacia atrás y hacia delante, en cuatro tamaños de zoom diferentes (ajustar a la página, ajustar al ancho, 100% y otro de su elección). La función de zoom garantiza que el texto (fuente) se representa en distintos tamaños. El desplazamiento se reduce al uso de las teclas Re Pág, Av Pág, Inicio y Fin, con distintos intervalos para cada desplazamiento.

### Reproducción de vídeo Flash: ejecución durante aprox. 180 segundos
Un usuario ve un vídeo con codificación de Adobe Flash insertado en una página web. La página web se almacena en el disco duro local del servidor host de sesión de Escritorio remoto. El vídeo se reproduce en Internet Explorer mediante un complemento de reproductor incrustado.

En este escenario se emulan a los usuarios que ven páginas web con contenido enriquecido que incluye contenido multimedia. La mayoría de los datos debe ir a través de VOBR.

### Escritura remota en Word: ejecución durante aprox. 1800 segundos
Un usuario escribe un documento en una sesión de RDP. Las pulsaciones de teclas se envían desde el cliente a través de la sesión de RDP hasta un documento de Microsoft Word que se ejecuta en la sesión remota. La velocidad de escritura es un carácter cada 250 ms (total 7050 caracteres).

Se trata de una de las situaciones más comunes en el caso de un trabajador del conocimiento. En este escenario se comprueba la capacidad de respuesta de un usuario que escribe en un procesador de textos moderno. Este escenario es sensible a los más mínimos cambios en el uso de ancho de banda.

## Seguimiento de los resultados de pruebas
Puede usar la siguiente tabla para evaluar los escenarios en su entorno. Los datos que se ofrecen a continuación son meramente ilustrativos: pueden ser considerablemente diferentes de lo que observe.

Para simplificar, se asume que todos los escenarios se prueban utilizando la misma resolución de pantalla de 1920 x 1080 píxeles y se transmiten por TCP en una red con una latencia (retraso) inferior a 200 ms y una vibración de red de aproximadamente un 1 % en la marca de más de 120 ms.

Acerca de la tabla:

* **Experiencia promedio** contiene el ancho de banda de red donde la productividad del usuario no se ve afectada significativamente, pero no excluye problemas ocasionales de audio o vídeo. El sistema es capaz de recuperarse rápidamente aprovechando las ventajas de la lógica dinámica. Los cálculos aproximados de ancho de banda de red intentan garantizar la calidad de la experiencia del usuario.
  * **Problemas perceptibles (punto de interrupción)** contiene el ancho de banda de red donde los usuarios pueden notar problemas importantes en su experiencia, y donde la productividad se ve afectada durante períodos de tiempo mensurables. En este momento, los algoritmos de RDP tienen dificultades para garantizar la calidad de la experiencia del usuario y no pueden debido a falta de ancho de banda.
  * **Recomendado** contiene el ancho de banda de red recomendado para una experiencia buena o excelente. Suele ser el nivel superior siguiente al valor de la columna **Experiencia promedio** correspondiente.
  * En **Notas** se incluyen observaciones y comentarios.

| Prueba | Experiencia promedio | Problemas perceptibles(punto de interrupción) | Ancho de banda de red recomendado | Notas |
| --- | --- | --- | --- | --- |
| PPT ejecutivo/complejo |10 MB/s |1 MB/s |>10 MB/s, 100 MB/s (preferible) |A 1 MB/s se pierden muchas animaciones |
| PPT simple |5 MB/s |256 KB/s |10 MB/s |A 256 KB/s las diapositivas se cargan con retraso evidente |
| Internet Explorer |10 MB/s |1 MB/s |>10 MB/s, 100 MB/s (preferible) |A 1 MB/s, los vídeos web son borrosos y se entrecortan, el desplazamiento rápido presenta problemas |
| PDF simple |1 MB/s |256 KB/s |5 MB/s |A 256 KB/s se tarda cierto tiempo en cargar la página |
| PDF mixto |1 MB/s |256 KB/s |5 MB/s |A 256 KB/s se tarda una cantidad considerable de tiempo en cargar la página |
| Reproducción de vídeo Flash |10 MB/s |1 MB/s |>10 MB/s, 100 MB/s (preferible) |A 1 MB/s, el vídeo tiene una textura granulada y se pierden algunos fotogramas |
| Escritura remota en Word |256 KB/s |128 KB/s |1 MB/s |A 256 KB/s el usuario puede notar el tiempo entre pulsaciones de teclas |

Para evaluar el ancho de banda de red por usuario, cree una combinación de los escenarios anteriores y la proporción correspondiente de ancho de banda de red necesario. Elija el número más alto que se requiera para los escenarios. Debido a que los usuarios no usan casi nunca el sistema por sí solo, considere la posibilidad de reservar una parte para usuarios que trabajen simultáneamente en la misma red.

## Más información
* [Cálculo aproximado del uso del ancho de banda de red de Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp: ¿cómo funcionan el ancho de banda de red y la calidad de la experiencia conjuntamente?](remoteapp-bandwidthexperience.md)
* [Ancho de banda de red de Azure RemoteApp: directrices generales (si no puede probarlo por sí mismo)](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0817_2016-->