<properties linkid="develop-java-how-to-guides-view-saml-returned-by-acs" urlDisplayName="View ACS SAML" pageTitle="View SAML Returned by the Access Control Service (Java)" metaKeywords="" description="Learn how to view SAML returned by the Access Control Service in Java applications hosted on Azure." metaCanonical="" services="" documentationCenter="Java" title="How to view SAML returned by the Azure Access Control Service" authors="waltpo" videoId="" scriptId="" solutions="" manager="" editor="mollybos" />

Visualización del SAML que devuelve el servicio de control de acceso de Azure
=============================================================================

En esta guía se explica cómo ver el lenguaje de marcado de aserción de seguridad (SAML) subyacente devuelto a la aplicación mediante el servicio de control de acceso de Azure (ACS). La guía se elabora a partir del tema [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse](../active-directory-java-authenticate-users-access-control-eclipse), proporcionando código que muestra la información de SAML. La aplicación completa tendrá un aspecto similar al siguiente.

![Salida de SAML de ejemplo](./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png)

Para obtener más información sobre ACS, consulte la sección [Pasos siguientes](#next_steps).

> [WACOM.NOTE] El filtro de control de los servicios de acceso de Azure (de Microsoft Open Technologies) es una Community Technology Preview. Como versión preliminar de software, no cuenta formalmente con el respaldo de Microsoft Open Technologies, Inc. ni de Microsoft.

Tabla de contenido
------------------

-   [Requisitos previos](#pre)
-   [Incorporación de la biblioteca JspWriter a la ruta de acceso de compilación y el ensamblado de implementación](#add_library)
-   [Modificación del archivo JSP para que muestre el SAML](#modify_jsp)
-   [Ejecución de la aplicación](#run_application)
-   [Pasos siguientes](#next_steps)

Requisitos previos
------------------

A fin de llevar a cabo las tareas de esta guía, complete el ejemplo de [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse](../active-directory-java-authenticate-users-access-control-eclipse) y úselo como punto de partida de este tutorial.

Incorporación de la biblioteca JspWriter a la ruta de acceso de compilación y el ensamblado de implementación
-------------------------------------------------------------------------------------------------------------

Agregue la biblioteca que contiene la clase **javax.servlet.jsp.JspWriter** a la ruta de acceso de la compilación y el ensamblado de implementación. Si usa Tomcat, la biblioteca es **jsp-api.jar** y se ubica en la carpeta **lib** de Apache.

1.  En el explorador de proyectos de Eclipse, haga clic con el botón secundario en **MyACSHelloWorld**, haga clic en **Build Path**, después en **Configure Build Path**, a continuación en la pestaña **Libraries** y, por último, en **Add External JARs**.
2.  En el cuadro de diálogo **JAR Selection**, navegue hasta el JAR necesario, selecciónelo y, a continuación, haga clic en **Open**.
3.  Con el cuadro de diálogo **Properties for MyACSHelloWorld** abierto, haga clic en **Deployment Assembly**.
4.  En el cuadro de diálogo **Web Deployment Assembly**, haga clic en **Add**.
5.  En el cuadro de diálogo **New Assembly Directive**, haga clic en **Java Build Path Entries** y, a continuación, en **Next**.
6.  Seleccione la biblioteca pertinente y haga clic en **Finish**.
7.  Haga clic en **OK** para cerrar el cuadro de diálogo **Properties for MyACSHelloWorld**.

Modificación del archivo JSP para que muestre el SAML
-----------------------------------------------------

Modifique **index.jsp** para que use el código siguiente.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Atributos.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Nodos secundarios.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // Si se trata de un nodo de texto, imprimirlo.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Imprimir los nombres del nodo secundario.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separar los nombres.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finalizar la oración.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Procesar los nodos secundarios.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterar los nodos secundarios del documento.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

Ejecución de la aplicación
--------------------------

1.  Ejecute la aplicación en el emulador del equipo o realice la implementación en Azure; para ello, siga los pasos descritos en [Autenticación de usuarios web con el servicio de control de acceso de Azure mediante Eclipse](../active-directory-java-authenticate-users-access-control-eclipse).
2.  Inicie un explorador y abra la aplicación web. Después de iniciar sesión en la aplicación, verá la información de SAML, incluida la aserción de seguridad proporcionada por el proveedor de identidades.

Pasos siguientes
----------------

Para seguir explorando la funcionalidad de ACS y experimentar con escenarios más sofisticados, consulte [Access Control Service 2.0](http://go.microsoft.com/fwlink/?LinkID=212360).

