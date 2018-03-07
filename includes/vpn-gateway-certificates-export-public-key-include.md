Después de crear un certificado raíz autofirmado, exporte el archivo .cer de clave pública de certificado de raíz (no la clave privada). Este archivo se cargará más adelante en Azure. Los pasos siguientes le ayudan a exportar el archivo .cer para el certificado raíz autofirmado:

1. Para obtener un archivo .cer del certificado, abra **Administrar certificados de usuario**. Busque el certificado raíz autofirmado; normalmente se encuentra en Certificados - Usuario actual\Personal\Certificados y haga clic en el botón derecho. Haga clic en **Todas las tareas** y, luego, en **Exportar**. Se abre el **Asistente para exportar certificados**.

  ![Exportación](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. En el asistente, haga clic en **Siguiente**.

  ![Exportación de certificado](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Seleccione **No exportar la clave privada** y, después, haga clic en **Siguiente**.

  ![No exportar la clave privada](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. En la página **Formato de archivo de exportación**, seleccione **X.509 codificado base 64 (.CER)** y, luego, haga clic en **Siguiente**.

  ![Codificado con Base 64](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.

  ![Examinar](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Haga clic en **Finalizar** para exportar el certificado.

  ![Finalizar](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. El certificado se exportó correctamente.

  ![Correcto](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. El certificado exportado tiene un aspecto similar al siguiente:

  ![Exportado](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Si abre el certificado exportado mediante el Bloc de notas, verá algo parecido a este ejemplo. La sección en azul contiene la información que se carga en Azure. Si abre el certificado con el Bloc de notas y no se parece a este, normalmente eso significa que no lo exportó mediante el formato X.509 codificado base 64 (. CER). Además, si desea utilizar un editor de texto diferente, debe comprender que algunos editores pueden introducir formatos no deseados en segundo plano. Esto puede crear problemas al cargar el texto de este certificado en Azure.

  ![Abrir con el Bloc de notas](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)