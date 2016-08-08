### Requisitos previos

- Una cuenta de [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol).


Antes de poder usar la cuenta de SMTP en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.

Aquí se explica cómo autorizar a la aplicación lógica a conectarse a su cuenta de SMTP:
1. Para crear una conexión a SMTP, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba *SMTP* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar: ![](./media/connectors-create-api-smtp/smtp-1.png)
2. Si no ha creado ninguna conexión a SMTP antes, se le pedirá que indique sus credenciales de SMTP. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de SMTP: ![](./media/connectors-create-api-smtp/smtp-2.png)
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de los pasos en la aplicación lógica: ![](./media/connectors-create-api-smtp/smtp-3.png)

<!---HONumber=AcomDC_0727_2016-->