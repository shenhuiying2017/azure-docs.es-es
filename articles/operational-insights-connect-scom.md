<properties 
   pageTitle="Conexión a Vista operativa desde System Center Operations Manager" 
   description="Obtenga información acerca de cómo conectarse a Vista operativa a través de Operations Manager."
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Conexión a Vista operativa desde System Center Operations Manager 

Puede conectar Vista operativa a un entorno de System Center Operations Manager existente. Esto le permitirá utilizar los agentes de Operations Manager existentes como agentes de Vista operativa.

 >[AZURE.NOTE] La compatibilidad con Vista operativa está disponible a partir de Operations Manager 2012 SP1 UR6 y Operations Manager 2012 R2 UR2. La compatibilidad con proxy se agregó en SCOM 2012 SP1 UR7 y SCOM 2012 R2 UR3.

## Conexión de SCOM a Vista operativa y adición de agentes

1. En la consola de Operations Manager, haga clic en **Administración**.

2. Expanda el nodo **Vista operativa** y haga clic en **Conexión a Vista operativa**.

3. Haga clic en el vínculo **Registrar en Vista operativa** y siga las instrucciones en pantalla. 

4. Después de completar el asistente de registro, haga clic en **Agregar un equipo/grupo**.

5. En el cuadro de diálogo **Búsqueda de equipo** puede seleccionar los equipos o grupos supervisados por Operations Manager. Seleccione los equipos o grupos que incorporar a Vista operativa, haga clic en **Agregar** y, a continuación, haga clic en **Aceptar**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

[Configuración de los ajustes del proxy y del firewall (opcional)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->