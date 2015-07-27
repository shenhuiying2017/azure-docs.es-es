<properties
	pageTitle="Comparación: Apache Storm frente al Análisis de transmisiones de Azure | Microsoft Azure"
	description="Aprenda a usar Análisis de transmisiones para el análisis en tiempo real de opinión de Twitter. Pasos desde la generación de eventos a los datos en un panel dinámico."
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="07/01/2015"
	ms.author="jeffstok"/>

# Comparación de Apache Storm y el Análisis de transmisiones de Azure #

## Introducción ##

Este documento ilustra la situación de Análisis de transmisiones de Azure y de Apache Storm como un servicio administrado en HDInsight. El objetivo es ayudar a los clientes a comprender las oportunidades que ofrecen ambos servicios y a decidir cuál de ellos usar en sus negocios.

Aunque ambos proporcionan las ventajas de una solución PaaS, hay algunas capacidades distintivas principales que diferencian estos servicios. Al ofrecer una lista de las funciones de estos servicios, así como de sus limitaciones, nuestros clientes podrán encontrar la mejor solución para lograr sus objetivos.

## General ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de transmisiones de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Código abierto</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    No, Análisis de transmisiones de Azure es una oferta propiedad de Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Sí, Apache Storm es una tecnología con licencia de Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Compatible con Microsoft</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Sí
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Sí
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisitos de hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    No hay requisitos de hardware. Análisis de transmisiones de Azure es un servicio de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    No hay requisitos de hardware. Apache Storm es un servicio de Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unidad de nivel superior</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Con Análisis de transmisiones de Azure, puede implementar clientes y supervisar los trabajos de transmisión.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Con Apache Storm en HDInsight, puede implementar clientes y supervisar todo un clúster que pueda hospedar varios trabajos de Storm y otras cargas de trabajo (lotes incluidos).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Precio</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    El precio de Análisis de transmisiones varía según el volumen de datos procesados y el número de unidades de streaming necesarias (por cada hora en la que se está ejecutando el trabajo).
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Puede encontrar más información sobre los precios aquí.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Para Apache Storm en HDInsight, la unidad de compra está basada en el clúster y se cobra según la hora en la que el clúster se ejecuta, independientemente de los trabajos implementados.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Puede encontrar más información sobre los precios aquí.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Creación ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de transmisiones de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: SQL DLS</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Sí, tiene disponible la compatibilidad de idioma de SQL que es muy fácil de usar.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    No, los usuarios deben escribir el código en Java C# o usar las API de Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacidades: operadores temporales</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Los agregados de ventana y las uniones temporales son compatibles y están listos para usar.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Los operadores temporales deben ser implementados por el usuario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Experiencia de desarrollo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Experiencias de creación y depuración interactivas de los datos de ejemplo mediante el Portal de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Las experiencias de desarrollo, depuración y supervisión se realizan siempre a través de la experiencia de Visual Studio para usuarios. NET, mientras que para Java y otros desarrolladores de lenguajes puede utilizar el IDE que desee.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Compatibilidad con la depuración</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Análisis de transmisiones le ofrece un estado de trabajo básico y registros de operaciones a modo de depuración, pero tenga en cuenta que actualmente no es flexible con lo que se puede incluir en estos registros o en qué cantidad; el modo detallado es un ejemplo de ello.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Tiene disponibles registros detallados con fines de depuración. El usuario puede acceder a los registros de dos formas: a través de Visual Studio o conectando mediante un RDP (protocolo de escritorio remoto) al clúster para así poder acceder a los registros.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Compatibilidad con UDF (funciones definidas por el usuario)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Actualmente no es compatible con los UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Puede escribir los UDF en C#, Java o en el idioma de su elección.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensible: código personalizado </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    El código extensible no es compatible con Análisis de transmisiones.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Sí, puede escribir en Storm el código personalizado en C#, Java u otros lenguajes compatibles.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Entrada y salida ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de transmisiones de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm en HDInsight</strong>
                </p>
            </td>
        </tr>
		<tr>
            <td width="174" valign="top">
				<p>
				 <strong>Orígenes de datos de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Los orígenes de entrada que se admiten son los centros de eventos de Azure y los blobs de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Hay conectores disponibles para los centros de eventos, el bus de servicio, Kafka, etc. Aquellos conectores que sean incompatibles se pueden implementar a través del código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de datos de entrada</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Los formatos de entrada admitidos son Avro, JSON y CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Cualquier otro formato puede implementarse a través del código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Salidas</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Un trabajo de streaming puede tener varias salidas. Las salidas compatibles son: centros de eventos de Azure, almacenamiento de blobs de Azure, tablas de Azure, Azure SQL DB y PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Compatible con muchas de las salidas en una topología; cada salida puede tener una lógica personalizada para el procesamiento de nivel inferior. Esta versión de Storm ya está lista para usar e incluye conectores para PowerBi, los Centros de eventos de Azure, el Almacenamiento de blobs de Azure, Azure DocumentDB, SQL y HBase. Aquellos conectores que sean incompatibles se pueden implementar a través del código personalizado.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formatos de codificación de datos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Ha de tener el formato de datos UTF-8 para poder usar Análisis de transmisiones.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Cualquier formato de codificación de datos ha de implementarse a través del código personalizado.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Administración y operaciones ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de transmisiones de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modelo de implementación de trabajo</strong>
                </p>
                <p>
                    - <strong>Portal de Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    La implementación se realiza a través del Portal de Azure, PowerShell y las API de REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    La implementación se realiza a través del Portal de Azure, PowerShell, Visual Studio y las API de REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supervisión (estadísticas, contadores, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    La supervisión se implementa a través del Portal de Azure y las API de REST.
                </p>
                <p>
                    A parte, el usuario también puede configurar alertas de Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    La supervisión se implementa a través de la interfaz de usuario de Storm y las API de REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Escalabilidad</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Número de unidades de streaming para cada trabajo. Cada unidad de streaming puede procesar hasta 1 MB/s. Hay un máximo de 50 unidades de forma predeterminada. Puede llamar para aumentar el límite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Número de nodos en el clúster de HDI Storm. No hay límite en el número de nodos (el límite máximo lo define la cuota de Azure). Puede llamar para aumentar el límite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Límite de procesamiento de datos</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Los usuarios pueden escalar o reducir verticalmente el número de unidades de streaming para aumentar el procesamiento de datos u optimizar los costos.
                </p>
                <p>
                    Escalar verticalmente hasta 1 GB/s
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    El usuario puede escalar o reducir verticalmente el tamaño de clúster según le convenga.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Detener o reanudar</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Puede detenerlo y reanudarlo en el punto donde lo detuvo.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Puede detenerlo y reanudarlo en el último punto donde lo detuvo en función de la marca de agua.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Actualización del servicio y marco</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Revisión automática sin tiempo de inactividad.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Revisión automática sin tiempo de inactividad.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuidad del negocio a través de un servicio altamente disponible con Contrato de nivel de servicio garantizado</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Contrato de nivel de servicio con un tiempo de actividad del 99,9%
                </p>
                <p>
                    Recuperación automática de errores
                </p>
                <p>
                    La recuperación de los operadores con estado temporal está integrada.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Contrato de nivel de servicio con un tiempo de actividad del clúster de Storm del 99,9%. Apache Storm es una plataforma de streaming a prueba de errores, pero es responsabilidad de los clientes asegurarse de que sus trabajos de streaming se ejecutan sin interrupciones.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Características avanzadas ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Análisis de transmisiones de Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm en HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Llegada tardía y control de eventos desordenados</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Directivas configurables integradas para reordenar, quitar eventos o ajustar la hora del evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    El usuario debe implementar la lógica para controlar este escenario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Datos de referencia</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Datos de referencia disponibles en los blobs de Azure con un tamaño máximo de 100 MB de caché de búsqueda en memoria. El servicio administra la actualización de los datos de referencia.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Sin límites en el tamaño de los datos. Conectores disponibles para HBase, DocumentDB, SQL Server y Azure. Aquellos conectores que sean incompatibles se pueden implementar a través del código personalizado. 
                </p>
                <p>
                    El código personalizado es el que debe encargarse de actualizar los datos de referencia.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integración de aprendizaje automático</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Sí, mediante la configuración de los modelos publicados de aprendizaje automático de Azure a modo de funciones durante la creación del trabajo ASA.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Disponible a través de Storm Bolts.
                </p>
            </td>
        </tr>
    </tbody>
</table>

<!---HONumber=July15_HO2-->