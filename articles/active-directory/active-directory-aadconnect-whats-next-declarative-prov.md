

<properties 
	pageTitle="Uso del aprovisionamiento declarativo de Azure AD Connect" 
	description="Aprenda a usar el aprovisionamiento declarativo de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Aprovisionamiento declarativo de Azure AD Connect


## Uso del aprovisionamiento declarativo 
El aprovisionamiento declarativo es un aprovisionamiento "sin código" que se puede instalar y configurar mediante el editor de reglas de sincronización. Puede utilizar el editor para configurar y crear sus propias reglas de aprovisionamiento.

Una parte esencial del aprovisionamiento declarativo es el lenguaje de expresiones que se usa en flujos de atributos. El lenguaje usado es un subconjunto de Microsoft® Visual Basic® para Aplicaciones. Este lenguaje se usa en Microsoft Office, y los usuarios con experiencia en VBScript también lo reconocerán. El lenguaje de expresiones de aprovisionamiento declarativo solo utiliza funciones y no es un lenguaje estructurado; no hay métodos ni instrucciones. Las funciones se anidarán en su lugar en el flujo de programa rápido.

Para obtener más información sobre el lenguaje de expresiones, vea [Descripción de las expresiones de aprovisionamiento declarativo](https://msdn.microsoft.com/library/azure/dn801048.aspx).
<!--HONumber=62-->