---
title: Microsoft Threat Modeling Tool (Azure) | Microsoft Docs
description: "Información acerca de todas las características disponibles en Threat Modeling Tool"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 621ff305d7e782f85eeaae6c3fb02031673549c6
ms.contentlocale: es-es
ms.lasthandoff: 08/28/2017

---

# <a name="threat-modeling-tool-feature-overview"></a>Información general de las características de Threat Modeling Tool

Nos alegramos de que decida usar Threat Modeling Tool para sus necesidades de modelado de amenazas. Si no es así, visite  **[Introducción a Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md)**  para obtener información sobre los conceptos básicos.

> Nuestra herramienta se actualiza con frecuencia, de modo que revise esta guía a menudo para ver nuestras últimas características y mejoras.

Al hacer clic en el botón "Create a New Model" (Crear un modelo), se abre una página de inicio en blanco, similar a la imagen siguiente:

![Página de inicio en blanco](./media/azure-security-threat-modeling-tool/tmtstart.png)

Usando el modelo de amenazas creado por nuestro equipo en el ejemplo  **[Introducción](./azure-security-threat-modeling-tool-getting-started.md)** , vamos a comprobar todas las características disponibles en la herramienta hoy en día.

![Modelo de amenaza básica](./media/azure-security-threat-modeling-tool/basictmt.png)

## <a name="navigation"></a>Navegación

Antes de profundizar en las características integradas, veamos los componentes principales de la herramienta

### <a name="menu-items"></a>Elementos de menú

La experiencia debe ser similar a la de otros productos de Microsoft. Comencemos repasando los elementos de menú de nivel superior:

![Elementos de menú](./media/azure-security-threat-modeling-tool/menuitems.png)

| Etiqueta                               | Detalles      |
| --------------------------------------- | ------------ |
| **Archivo** | <ul><li>Abrir, guardar y cerrar archivos</li><li>Iniciar y cerrar sesión en cuentas de OneDrive</li><li>Compartir vínculos (Vista + Edición)</li><li>Ver información de archivo</li><li>Aplicar una plantilla nueva a modelos existentes</li></ul> |
| **Edición** | Acciones de deshacer/rehacer, además de copiar, pegar y eliminar |
| **Vista** | <ul><li>Cambiar entre las vistas **Análisis** y **Diseño**</li><li>Abrir las ventanas cerradas (por ejemplo, galerías de símbolos, propiedades de elementos y mensajes)</li><li>Restablecer el diseño a la configuración predeterminada</li></ul> |
| **Diagrama** | Agregar o eliminar diagramas, y navegar a través de "pestañas" de diagramas |
| **Informes** | Crear informes HTML para compartir con otros usuarios |
| **Ayuda** | Guías para ayudarle a usar la herramienta |

Los iconos son accesos directos para los menús de nivel superior:

| Icono                               | Detalles      |
| --------------------------------------- | ------------ |
| **Abrir** | Abre un nuevo archivo |
| **Guardar** | Guarda el archivo actual |
| **Diseño** | Entra en la vista de diseño, donde puede crear modelos |
| **Análisis** | Muestra las amenazas generadas y sus propiedades |
| **Agregar diagrama** | Agrega el nuevo diagrama (similar a las nuevas pestañas de Excel) |
| **Eliminar diagrama** | Elimina el diagrama actual |
| **Copiar/Cortar/Pegar** | Copia, corta o pega elementos |
| **Deshacer/Rehacer** | Deshace o rehace acciones |
| **Acercar/Alejar** | Acerca y aleja el diagrama para obtener una mejor vista |
| **Comentarios** | Se abre el foro de MSDN |

### <a name="canvas"></a>Lienzo

El espacio donde arrastrar y colocar elementos. Arrastrar y colocar es la manera más rápida y eficaz para crear modelos. También puede hacer clic con el botón derecho y seleccionar en el menú, con lo que se agregan versiones genéricas de los elementos que está utilizando, tal y como se muestra a continuación.

#### <a name="dropping-the-stencil-on-the-canvas"></a>Colocar la galería de símbolos en el lienzo

![Colocación en el lienzo](./media/azure-security-threat-modeling-tool/canvasdrop1.png)

#### <a name="clicking-on-the-stencil"></a>Hacer clic en la galería de símbolos

![Propiedades del elemento](./media/azure-security-threat-modeling-tool/canvasdrop2.png)

### <a name="stencils"></a>Galerías de símbolos

Donde puede encontrar todas las galerías de símbolos disponibles para usarse según la plantilla seleccionada. Si no encuentra los elementos adecuados, intente usar otra plantilla o modificar alguna para ajustarla a sus necesidades. Por lo general, debería ser capaz de encontrar una combinación de categorías, como las siguientes:

| Nombre de la galería de símbolos                               | Detalles      |
| --------------------------------------- | ------------ |
| **Proceso** | Aplicaciones, complementos de explorador, subprocesos, máquinas virtuales |
| **External Interactor** (Interactivo externo) | Proveedores de autenticación, exploradores, usuarios, aplicaciones web |
| **Almacén de datos** | Caché, almacenamiento, archivos de configuración, bases de datos, Registro |
| **Flujo de datos** | Binario, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, Canalización con nombre, RPC/DCOM, SMB, UDP |
| **Trust Line/Border Boundary** (Línea de confianza/Línea de borde) | Redes corporativas, Internet, máquina, espacio aislado, modo Kernel/Usuario |

### <a name="notesmessages"></a>Notas/Mensajes

| Componente                               | Detalles      |
| --------------------------------------- | ------------ |
| **Mensajes** | Lógica de herramienta interna que alerta a los usuarios cada vez que hay un error, por ejemplo, cuando no hay flujos de datos entre los elementos |
| **Notas** | Notas manuales que los equipos de ingeniería agregan al archivo durante el proceso de diseño y revisión |

### <a name="element-properties"></a>Propiedades del elemento

Varían según los elementos seleccionados. Además de los límites de confianza, todos los demás elementos contienen tres selecciones generales:

| Propiedad de elemento                               | Detalles      |
| --------------------------------------- | ------------ |
| **Name** | Resulta útil para asignar nombres a los procesos, almacenes, elementos interactivos y flujos para que se reconozcan con facilidad |
| **Out of Scope** (Fuera de ámbito) | Si se selecciona, el elemento se saca de la matriz de generación de la amenaza (no se recomienda) |
| **Reason for Out of Scope** (Motivo de elegir Fuera de ámbito) | Campo de justificación para que los usuarios sepan por qué se seleccionó la opción de fuera de ámbito |

Se cambian las propiedades de cada categoría de elemento. Haga clic en cada elemento para inspeccionar las opciones disponibles o abra la plantilla para obtener más información. Veamos las características.

## <a name="welcome-screen"></a>Pantalla principal

La pantalla de bienvenida es lo primero que verá al abrir la aplicación.

### <a name="open-a-model"></a>Abrir un modelo

Al mantener el mouse sobre el botón "Abrir un modelo", se muestran dos opciones ocultas: "Abrir desde este equipo" y "Abrir desde OneDrive". La primera abre la pantalla Abrir archivo, mientras que la segunda le guía por el proceso de inicio de sesión de OneDrive, lo que le permite elegir las carpetas y archivos después de autenticarse correctamente.

![Abrir modelo](./media/azure-security-threat-modeling-tool/openmodel.png)

![Abrir desde el equipo o desde OneDrive](./media/azure-security-threat-modeling-tool/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentarios, sugerencias y problemas

Al seleccionar esta opción, ira a los foros de MSDN para las herramientas de SDL. Es una excelente manera de comprobar lo que dicen otros usuarios acerca de la herramienta, también sobre nuevas ideas y soluciones.

![Comentarios](./media/azure-security-threat-modeling-tool/feedback.png)

## <a name="design-view"></a>Vista de diseño

Cada vez que abra o cree un nuevo modelo, se le dirigirá a la vista de diseño.

### <a name="adding-elements"></a>Adición de elementos

Hay dos formas de agregar elementos en la cuadrícula:

- **Arrastrar y colocar**: arrastre el elemento deseado en la cuadrícula y utilice las propiedades del elemento para proporcionar información adicional.
- **Hacer clic con el botón derecho**: haga clic con el botón derecho en cualquier parte de la cuadrícula y seleccione en el menú desplegable. Aparecerá una representación genérica de ese elemento en la pantalla.

### <a name="connecting-elements"></a>Conexión de elementos

Hay dos formas de conectar los elementos de la herramienta:

- **Arrastrar y colocar**: arrastre el flujo de datos deseado a la cuadrícula y conecte ambos extremos a los elementos correspondientes.
- **Hacer clic + Mayús**: haga clic en el primer elemento (enviar datos) y mantenga presionada la tecla Mayús y luego seleccione el segundo elemento (recibir datos). Haga clic con el botón derecho y seleccione "Conectar". Si usa un flujo de datos bidireccional, el orden no es tan importante.

### <a name="properties"></a>Propiedades

Muestra todas las propiedades que pueden modificarse en las galerías de símbolos que se colocan en el diagrama. Para ver las propiedades, haga clic en la galería de símbolos y la información se rellenará según corresponda. En el ejemplo siguiente se muestra la situación antes y después de arrastrar una galería de símbolos "Base de datos" en el diagrama:

#### <a name="before"></a>Antes

![Antes](./media/azure-security-threat-modeling-tool/properties1.png)

#### <a name="after"></a>Después

![Después](./media/azure-security-threat-modeling-tool/properties2.png)

### <a name="messages"></a>error de Hadoop

Si crea un modelo de amenazas y olvida conectar los flujos de datos a los elementos, la ventana de mensaje indica que haga algo. Puede elegir omitirla o seguir las instrucciones para solucionar el problema. 

![error de Hadoop](./media/azure-security-threat-modeling-tool/messages.png)

### <a name="notes"></a>Notas

Cambiar de la pestaña Mensajes a Notas le permite agregar notas al diagrama para registrar todas sus ideas

## <a name="analysis-view"></a>Vista de análisis

Cuando haya terminado de crear el diagrama, cambie a la vista de análisis; para ello, vaya a las selecciones del menú superior y elija la lupa situada junto a la paleta de pintura.

![Vista de análisis](./media/azure-security-threat-modeling-tool/analysisview.png)

### <a name="generated-threat-selection"></a>Selección de amenaza generada

Al hacer clic en una amenaza, puede sacar provecho de tres funciones únicas:

| Característica                               | Información      |
| --------------------------------------- | ------------ |
| **Indicador de leído** | <p>La amenaza ahora está marcada como de lectura, lo que puede ayudarle fácilmente a realizar el seguimiento de los elementos que ya haya completado</p><p>![Indicador de leído o no leído](./media/azure-security-threat-modeling-tool/readmode.png)</p> |
| **Foco de interacción** | <p>Se resalta la interacción en el diagrama que pertenece a esa amenaza</p><p>![Foco de interacción](./media/azure-security-threat-modeling-tool/interactionfocus.png)</p> |
| **Thread Properties** (Propiedades de amenaza) | <p>Se rellena información adicional sobre la amenaza en la ventana Threat Properties (Propiedades de amenaza)</p><p>![Thread Properties (Propiedades de amenaza)](./media/azure-security-threat-modeling-tool/threatproperties.png)</p> |

### <a name="priority-change"></a>Priority change (Cambio de prioridad)

Al cambiar el nivel de prioridad de cada amenaza generada, también se cambian sus colores para que sea fácil identificar las amenazas de prioridad alta, media y baja.

![Priority change (Cambio de prioridad)](./media/azure-security-threat-modeling-tool/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos modificables de las propiedades de amenaza

Tal como se muestra en la imagen anterior, los usuarios pueden cambiar la información generada por la herramienta y también agregar información a algunos de los campos, como la justificación. Estos campos son generados por la plantilla, de modo que, si necesita más información para cada amenaza, le recomendamos que haga modificaciones.

![Thread Properties (Propiedades de amenaza)](./media/azure-security-threat-modeling-tool/threatproperties.png)

## <a name="reports"></a>Informes

Una vez que haya terminado de cambiar las prioridades y actualizar el estado de cada amenaza generada, puede guardar el archivo o imprimir un informe; para ello, vaya a "Informes" y, a continuación, haga clic en "Create Full Report" (Crear un informe completo). Se le pedirá que asigne un nombre al informe y, cuando lo haga, debería ver una imagen parecida a la siguiente:

![Informe](./media/azure-security-threat-modeling-tool/report.png)

## <a name="next-steps"></a>Pasos siguientes

Para contribuir con una plantilla para la comunidad, vaya a nuestra página de  **[GitHub](https://github.com/Microsoft/threat-modeling-templates)** . **[Descargue](https://aka.ms/tmtpreview)**  la herramienta para comenzar hoy mismo.

