﻿<link href="prism.css" rel="stylesheet"></link>
<script src="prism.js"></script>
# MESSAGING API
### Resumen  
La comunicación entre las diferentes partes de una extensión se realiza mediante el proceso de fondo o la API de mensajería.
El script de fondo y injected script están aislados unos de otros y por lo tanto, deben utilizar la API de mensajería para comunicarse.
El proceso en segundo plano, como su nombre indica, es un proceso constantemente funcionando en segundo plano durante toda la vida de una extensión. Es responsable de los elementos de interfaz de usuario del navegador (por ejemplo la barra de herramientas botón y popup windows) y las acciones del navegador (apertura y cierre de una tab, etc..).  
### opera.extension.bgProcess  
### Descripción  
Una referencia al objeto window del proceso en segundo plano. El objeto bgProcess puede utilizarse para establecer y obtener las variables y funciones entre el guión de fondo y una página de preferencias o ventana emergente. Tenga en cuenta que no puede utilizarse en inyección de secuencias de comandos por razones de seguridad.
### Ejemplo (variable)  
El valor de una variable en el proceso de fondo aparece cuando se abre la página de preferencias.
<pre>
<code class="language-javascript">
// The background process ('/background.js'). 

var data = 'This is some important data in the backround process';
</code>
</pre>  
<pre>
<code class="language-javascript">
// An extension environment (e.g. '/options.js')

window.addEventListener('DOMContentLoaded', function() {
  var myElement = document.createElement('p');
  myElement.textContent = opera.extension.bgProcess.data;
  document.body.appendChild(myElement);
}, false);
</code>
</pre>  
### Ejemplo (función):  
Ejecutar una función en el proceso de fondo de la página de preferencias.
<pre>
<code class="language-javascript">
// The background process ('/background.js'). 

function doReverse(text) {
  var output = '';
  for (var i = text.length - 1; i &gt;= 0; i--) {
    output += text.charAt(i);
  }
  return output;
}
</code>
</pre>  
<pre>
<code class="language-javascript">
// An extension environment (e.g. '/options.js')

window.addEventListener('DOMContentLoaded', function() {
  var reversedText = opera.extension.bgProcess.doReverse('enihcam ym no skrow tI');
}, false);
</code>
</pre>  
### opera.extension.onconnect  
Este detector de eventos se invoca cuando un script inyectado, popup o el medio ambiente de preferencias es creado y permite la comunicacion.
<pre>
<code class="language-javascript">
// The background process ('/background.js').

opera.extension.onconnect = function(event){
  var connected = true;
};
</code>
</pre>  
### opera.extension.ondisconnect  
### opera.extension.onmessage  
### Descripción  
Este detector de eventos se invoca cuando se recibe un mensaje de un injected script, popup o pagina de preferencias. event's source es un messagePort para el entorno de conexión.
### Ejemplo  
<pre>
<code class="language-javascript">
// The background process ('/background.js'). 

opera.extension.onmessage = function(event) {
// Get the message content from the event is data property
  var message = event.data;
};
</code>
</pre>  
### opera.extension.addEventListener()   
### Descripción  
Este método se utiliza para detectar eventos que se envían. Para opera.extension, incluye 'connect', 'message', y 'disconnect'.
### Parámetros  
-tipo: tipo de evento; permite los valores : "message", "disconnect", y "connect".
-eventListener: la función que se ejecutará cuando se produzca el evento. 
-useCapture: Boolean, false por ahora; Nota: este valor no tiene actualmente ningún propósito.
### Sintaxis  
void addEventListener (&lt;DOMString> type, eventListerner, &lt;boolean> useCapture)
### Ejemplo  
<pre>
<code class="language-javascript">
//
// The background process ('/background.js'). 
//

// Listen for a new environment being created (like the popup window) 
opera.extension.addEventListener('connect', function(event) {
  var connected = true;
}, false);

// Listen for messages being sent
opera.extension.addEventListener('message', function(event) {
  var message = event.data;
}, false);

// Listen for environments being destroyed (and communication disabled)
opera.extension.addEventListener('disconnect', function(event) {
  var connected = false;
}, false);
</code>
</pre>  
### opera.extension.removeEventListener()

### opera.extension.broadcastMessage()

### Ejemplos.
Los fragmentos de dos código a continuación envían un mensaje, en este caso un URL, entre un script inyectado y el proceso de fondo.
<pre>
<code class="language-javascript">
//
// The injected script ('/includes/injectedScript.js')
//

window.addEventListener('DOMContentLoaded', function() {      
// send message to background script telling it what URL we are visiting
  opera.extension.postMessage(document.URL);
}, false);
</code>
</pre>
El script inyectado (arriba) envía su mensaje usando opera.extension.postMessage(message) que es recibido por el proceso de fondo con el controlador de evento opera.extension.onmessage.
<pre>
<code class="language-javascript">
//
// The background process ('/background.js'). 
//

// Listen for injected script messages (i.e. for image tags)
opera.extension.onmessage = function(event) {
// got the URL from the injected script
  url = event.data;  
};
</code>
</pre>  

#  INJECTED SCRIPTS

### opera.addEventListener()
### Descripción
Registra una escucha para un evento específico para el lado del script inyectado. El oyente necesita utilizar la interfaz EventListener estándar - una función con un objeto de evento como su primer argment, por ejemplo var myListener = function(event) {alert(event.type)};
### Parámetros
*tipo: el tipo de evento para escuchar; ver la lista de tipos de eventos a continuación. 
*listener: la función que se llamará. 
*useCapture: Boolean. Si es true, el detector de eventos sólo se agrega para la fase de captura y destino.
### Sintaxis
void addEventListener (<DOMString> type, <EventListener> listener, useCapture)
### Ejemplo
<pre>
<code class="language-javascript">
opera.addEventListener('BeforeScript', function(userJSEvent) {
  userJSEvent.element.text = userJSEvent.element.text.replace(/function\s+window\.onload\(/g, 'window.onload = function(');
}, false);
</code>
</pre>
### Tipos de evento:
### BeforeExternalScript
Se activa cuando se encuentra un elemento script con el atributo src. El elemento de script está disponible en el atributo de elemento de la UserJSEvent. En caso de anulación, la fuente externa no sera cargada y el elemento de la secuencia de comandos no se ejecuta. Además, si se cancela, no se disparará el evento BeforeScript.
### BeforeScript

### AfterScript

### BeforeEvent

### BeforeEvent.{type}

### AfterEvent

### AfterEvent.{type}

### BeforeEventListener

### BeforeEventListener.{type}

### AfterEventListener

### AfterEventListener.{type}

### BeforeJavascriptURL

### AfterJavascriptURL

### BeforeCSS

### AfterCSS

### pluginInitialized

### opera.removeEventListener()
» Removes an existing listener from an event.
### opera.defineMagicFunction()
» This method can be used to override global functions defined by regular scripts in a page.
### opera.defineMagicVariable()
» This method can be used by to override global variables defined by regular scripts in a page.
### opera.postError()

### Resumen
Puede agregar JavaScript a un sitio Web utilizando la "inyección de secuencias de comandos". Estos son archivos de JavaScript normales dentro de la carpeta "includes" en la extensión. Opera ejecute los scripts dado a todas las páginas, o determinados sitios o dominios que han sido el objetivo.

Puede, por ejemplo, crear secuencias de comandos inyectadas que Ocultan comentarios en YouTube que se escriben en mayúsculas, o mostrar todos los enlaces de texto en todas las páginas en color de rosa.

### Ejemplos
El ejemplo siguiente es un script inyectado (/ includes/injectedScript.js) que agrega un tooltip (mediante el atributo de título) para todos los enlaces en BBC News (pero no en BBC Sport).
<pre>
<code class="language-javascript">
// ==UserScript==
// @include http://www.bbc.co.uk/news/*
// @include http://news.bbc.co.uk/*
// @exclude http://news.bbc.co.uk/sport/*
// ==/UserScript==

window.addEventListener('DOMContentLoaded', function() {
  
  // Loop through all  elements (links) in the page
  var links = document.querySelectorAll('a');
  for (var i = 0, len = links.length; i < len; i++) {
    // For each link, show the URL as a title attribute (tooltip)
    links[i].title = links[i].href;
  }    
}, false);
</code>
</pre>
A continuación es otro ejemplo, esta vez mostrando cómo el script inyectado puede enviar mensajes al proceso de backgrond. 

El script inyectado (p. ej. /includes/injectedScript.js):
<pre>
<code class="language-javascript">
// ==UserScript==
// @include http://*.facebook.com/*
// ==/UserScript==

opera.extension.postMessage("He's on facebook again!");
</code>
</pre>
El guión de fondo (por ejemplo, en index.html):
<pre>
<code class="language-javascript">
//
// Store the number of Facebook visits in the extension's preferences
//

// Get the current Facebook visit count, or zero if it doesn't exist
var facebookCount = (widget.preferences['facebookCount']) ? widget.preferences['facebookCount'] : 0;

// Listen for injected script messages 
opera.extension.onmessage = function(event) {
  // User has been on facebook again
  if (event.data === "He's on facebook again!") {
    facebookCount += 1;
  }

  // Store the updated count in the preferences
  widget.preferences['facebookCount'] = facebookCount;
};
</code>
</pre>
## BUTTON & BADGE API

### opera.contexts.toolbar.createItem()
» Creates a button that can be added to the browser toolbar.
### opera.contexts.toolbar.addItem()
» Adds a button to the toolbar in the browser. To create a button see the createItem() method.
### opera.contexts.toolbar.removeItem()
» Removes a given button from the toolbar in the browser.
### opera.contexts.toolbar.length
» The number of buttons that exist in the toolbar for this extension (0 or 1).
### Button.badge
» A read-only property representing the badge for a button.
### Button.disabled
» A read-only property indicating whether a button is disabled. Set to false by default (meaning the item is enabled).
### Button.icon
» A read-only representation of the icon for a button.
### Button.onclick
» Represents the function executed when the button is clicked.
### Button.popup
» A read-only property representing the popup file path for a button.
### Button.title
» The title of a button which is exposed to the user (e.g., as a tooltip when hovering over the item with a mouse).

### Button.addEventListener()
### Descripción
Este método se utiliza para detectar eventos que se envían.

### Parámetros
*type: tipo de evento; permite los valores : click y remove. *eventListener: la función que se ejecuta cuando se produce el evento. 
*useCapture: Boolean y debe mantenerse falsa por ahora. Nota: este valor no tiene actualmente ningún propósito.
### Sintaxis
void addEventListener(<DOMString> type, eventListener, <boolean> useCapture)

### Ejemplo
<pre>
<code class="language-javascript">
//
// The background process (e.g. index.html)
//

// Set the button's properties
var properties = {
  disabled: false,
  title: "My Test Extension",
  icon: "icon.18x18.png"
};  

// Add the button to the browser UI
var button = opera.contexts.toolbar.createItem(properties);
opera.contexts.toolbar.addItem(button);

// Update the button title each time the button is clicked
var i = 1; 
button.addEventListener('click', handleClick, false);

function handleClick() {
  button.title = "You've clicked the button " + i + " time(s)"; 
  i++;
}

</code>
</pre>

### Button.removeEventListener()
» Removes a listener from receiving an event.
### Badge.backgroundColor
» The background color for a badge.
### Badge.color
» The text color of a badge.
### Badge.display
» Determines whether a badge should be displayed.
### Badge.textContent
» The text that will be shown in a badge.
___
# Opera extensions: mensajería
Con extensiones, tiene la capacidad de crear y agregar emocionante nueva funcionalidad en el navegador de escritorio de Opera. Como se ha mencionado en otros artículos, las extensiones de Opera contienen un proceso en segundo plano, inyección de scripts y a veces scripts emergente tambien. En este artículo, veremeos cómo comunicarse entre los tres.

### Contenido
### Comunicación Entre Procesos De Fondo Y Script Inyectado
Ópera utiliza postMessage() para publicar un mensaje, y lo utilizaremos la mayoría del tiempo.
Sin embargo, si desea enviar un mensaje desde el proceso de fondo a todos los recursos de esa extensión particular (como scripts inyectados, ventana emergente, página de preferencia etc.), escriba lo siguiente en el proceso de fondo:
 
<pre>
<code class="language-javascript">opera.extension.broadcastMessage("Hello there");
</code>
</pre>
Ahora ha enviado un mensaje, el script inyectado tiene que cogerlo, que se realiza así:

<pre>
<code class="language-javascript">
opera.extension.onmessage = function(event){
  var thecatch = event.data; // event.data in this case will contain the string "Hello there"
}
</code>
</pre>
Es tan simple como eso. En el código anterior, el proceso en segundo plano está disparando un mensaje usando broadcastMessage al script inyectado, y el script inyectado está escuchando para ello. Cuando llega el mensaje, se almacenará en una variable llamada thecatch. Sin embargo, tenga en cuenta que utilizando broadcastMessage enviará el mensaje a todos los scripts inyectados y ventanas emergentes, por lo que debe utilizarse con moderación.
Normalmente, debe comunicarse con un popup o script inyectado (uno dentro de su propia extensión) usando postMessage o canales de mensajes.

El proceso del fondo tiene un detector de eventos para carga, y todo el código esta en el interior de la llamada a la función para él.

<pre>
<code class="language-javascript">
window.addEventListener("load", setupConnection, false);

function setupConnection(){
   // When the injected script is activated, it connects with the background process.
   opera.extension.onconnect = function(event){
        // Post message to the source, that is, the thing which connected to us (in this case the injected script)
   event.source.postMessage("something");
        // Post this message in the opera error console
  opera.postError("sent message to injected script");
   }

   // Listen for messages
   opera.extension.onmessage = function(event){
         // Post a sentence (which includes the message received) to the opera error console
       opera.postError("This is what I got from injected script: "+event.data);
   }
}
</code>
</pre>
Vamos a concentrarnos primero en el siguiente fragmento de código:

<pre>
<code class="language-javascript">
opera.extension.onconnect = function(event){
   event.source.postMessage("something");
   opera.postError("sent message to injected script");
}
</code>
</pre>
OnConnect es despedido cuando algo conecta con el proceso de fondo, en este caso, cuando se activa el script inyectado en un documento, creando una conexión con el proceso de fondo. Así que en cuanto se carga el script inyectado, se desencadena el evento onconnect.

El controlador onconnect en el fondo contiene una referencia a la secuencia de comandos inyectada en event.
Este puerto de mensaje puede utilizarse entonces para comunicarnos directamente con el script inyectado, que aquí se realiza mediante el envío de la cadena "something" como un mensaje usando event. También enviamos una pequeña cadena en la consola de error, para realizar un seguimiento de lo que está sucediendo.

Vamos a dejar el proceso en segundo plano durante un tiempo y centrarnos ahora en la secuencia de comandos inyectada:
<pre>
<code class="language-javascript">
opera.extension.onmessage = function(event){
  // Get content of incoming message.
  var message  = event.data;
  opera.postError("background process sent: " + message);

  //  Replies back to background process.
  var reply = "background process's message only had " + (message ? message.length : 0) + " characters.";
  event.source.postMessage(reply);
};
</code>
</pre>
Aquí, onmessage se invoca cuando el script inyectado recibe un mensaje. Guardamos el contenido del mensaje en la variable message. A continuación usamos opera.postError para mostrar una frase en la consola de error junto con el mensaje enviado por el proceso de fondo.
Para acusar recibo del mensaje, atrás publicamos un mensaje para el proceso de fondo con la event.source del mensaje entrante.
Esto sucede siempre: un mensaje de event.source siempre señala al remitente. Ahora, hemos visto cómo los mensajes son recibidos por el script inyectado, y también cómo puede transmitirse hacia el proceso de fondo.
El siguiente paso es que el proceso de fondo atrape a este mensaje y haga algo con el.
Así que vamos a centrarnos en el procesao de fondo  nuevamente, esta vez en la otra parte de la secuencia de comandos:

<pre>
<code class="language-javascript">
// Listen for injected script messages
opera.extension.onmessage = function(event){
   // Post a sentence (which includes the message received) to the opera error console.
   opera.postError("This is what I got from the injected script: " + event.data);
}
</code>
</pre>
Aquí, cada vez que se recibe un mensaje, registra una frase que contiene los datos del mensaje en la consola de errores.
Así que en resumen, esto es lo que sucede en la extensión:
*El proceso de fondo envía un mensaje a las secuencias de comandos inyectadas
*El script inyectado atrapa ese mensaje y escribe algo en la consola de errores, que incluye el mensaje dado por el proceso de fondo
*El script inyectado luego responde, enviando un mensaje nuevo al proceso en segundo plano 
*El proceso de fondo escribe una frase en una cadena en la consola de errores, que incluye información sobre el mensaje enviado por el script inyectado.
___ 
## Communicating Between Popup And Background Process        

Usaremos el segundo ejemplo de extensión para ello. Observe que no hay carpeta /includes o cualquier inyección de secuencias de comandos, sólo los archivos proceso de fondo, y el código HTML  junto con un icono y el archivo config.xml.
<pre>
<code class="language-javascript">
opera.extension.onconnect = function(event){
  event.source.postMessage("sending something");
  opera.postError("sent message to popup");
}
</code>
</pre>
Como sabemos por el ejemplo anterior, se ejecutarán cuando algo se conecta al proceso de fondo, en este caso, el menú emergente.
La función envía el mensaje "sending something" a la ventana emergente y el mensaje "Sent message to popup" para la consola de errores.
Vamos ahora a la página del menú emergente:
<pre>
<code class="language-javascript">
&lt;script>
    window.addEventListener("load", function(){
    opera.extension.onmessage = function(event){
      event.source.postMessage("do whatever you want with this message");
      opera.postError("sent from popup to background process");
      }
    }, false);
&lt;/script>
</code>
</pre>  
Aquí escuchamos si llega un mensaje, y cuando lo hace enviamos un mensaje a la fuente. También publicamos algo a la consola de errores una vez más. Ahora el script emergente recibe un mensaje y envía un mensaje hacia el proceso de fondo. Todo lo que queda ahora es que el proceso en segundo plano lo agarre.  
Volvamos otra vez al proceso en segundo plano a estudiar el código siguiente:
<pre>
<code class="language-javascript">
opera.extension.onmessage = function(event){
   opera.postError("This is what I got from injected script: " + event.data);
}
</code>
</pre>  
Aquí el proceso de fondo escucha un mensaje, y cuando hay uno, publica algo en la consola de errores, con el contenido del mensaje recibido como parte del mensaje.
Así que en resumen, esto es lo que sucede en la extensión:  
*El proceso de fondo envía un mensaje a la secuencia de comandos del menú emergente.  
*La secuencia de comandos del menú emergente atrapa ese mensaje y escribe algo en la consola de errores, que incluye el mensaje dado por el proceso en segundo plano.
*La ventana emergente, a continuación, envía un mensaje hacia el proceso de fondo  
*El proceso de fondo escribe algo en una cadena en la consola de errores, que incluye el mensaje enviado por la secuencia de comandos de menú emergente.  
Por lo que el comportamiento es bastante similar al del ejemplo anterior, sólo que con este ejemplo, no teníamos un archivo de script inyectado en absoluto en la carpeta /includes. Por el contrario, utilizamos un popup y un script del documento emergente que se comunicó con el proceso de fondo.  
___
### Communicating between popup and injected script

Vamos a ver ahora cómo comunicarse entre un <em>popup</em> y un <em>script inyectado</em>. El proceso en segundo plano (<em>background process</em>) se utilizará inicialmente para hacer la conexión y, a continuación, el <em>popup</em> y el </em>script inyectado</em> hablaran sin el proceso de fondo.
Logramos esto mediante la creación de un canal de mensaje. 
Vamos a explicar esto con el tercer ejemplo de prueba, por lo que use su editor de texto y mire en el código.
Analicemos primero el proceso en segundo plano. Usted notará algunas cosas familiares, como el código para agregar un botón y un popup, etc. Vamos a profundizar en lo interesante.
Vamos a ver el <em>evento <b>onconnect</b></em> aquí:
<pre>
<code class="language-javascript">
//background process
opera.extension.onconnect = function( event ){
	if( event.origin.indexOf("popup.html") > -1 && event.origin.indexOf('widget://') > -1 ){
		var tab = opera.extension.tabs.getFocused();
		if( tab ){
			tab.postMessage( "Send a port", [event.source] );
			opera.postError('sent a message to injected script');
		} 
	} 
}
</code>
</pre>  
Primero comprobamos si el origen de la conexión es el archivo de popup (popup.html) y si es el mismo popup.html que se incluye en nuestro archivo de extensión o no (comprobando ' widget: / /'). Si es así, entonces enviará un mensaje al <em>script inyectado</em>, junto con una referencia a la fuente de la conexión (el popup).
Ahora veamos el script inyectado:
<pre>
<code class="language-javascript">
//injected script
var counter = 0;
var background;

opera.extension.onmessage = function( event ){
	if( event.data == "Send a port" ){
		background = event.source; // in case you need to send anything to background, just do background.postMessage()
		var channel = new MessageChannel();
		
		event.ports[0].postMessage( "Here is a port to the currently focused tab", [channel.port2] );
		opera.postError('post sent from injected script');
		
		channel.port1.onmessage = handlePopupMessage;
	}
}
</code>
</pre>  
Aqui inicialmente ponemos el contador a cero. El <em>script inyectado</em> escucha cualquier mensaje y si el mensaje es 'send a port' (el mismo que el mensaje de fondo envía cuando el popup se conecta a el), a continuación, se configura un canal de mensajes.
Esencialmente en canales de mensaje:
*MessageChannel.port1.postMessage -> se dispara MessageChannel.port2.onmessage
*MessageChannel.port2.postMessage -> se dispara MessageChannel.port1.onmessage
Así que en este caso, queremos enviar port2 a la ventana emergente (<em>popup</em>). Cuando el popup postea al port2, el <em>script inyectado</em> puede escuchar desde el port1.
Cuando se recibe un mensaje de la ventana emergente del port1, llamamos a la función 'handlePopupMessage' (más sobre eso más adelante).
El mensaje enviado por el <em>script de fondo</em> tenía también una referencia a la ventana emergente. Esta referencia al <em>popup</em> está disponible como event.ports[0]. Ahora podemos publicar un mensaje en la ventana emergente, junto con una referencia al puerto 2 del canal mensaje.
Ahora vamos a ver en el archivo del menú emergente:
<pre>
<code class="language-javascript">
//popup
var theport;

function handleMessageFromInjectedScript( event ){
	opera.postMessage( "Message received from the injected script: " + event.data );
}
 
opera.extension.onmessage = function ( event ){
	if( event.data == "Here is a port to the currently focused tab" ){
		if(event.ports.length > 0 ){
		theport = event.ports[0];
			theport.onmessage = handleMessageFromInjectedScript;
		} 
	}
}


function sendMessage(message){
var sendstring = message;
	if (theport){
		theport.postMessage(sendstring);
		opera.postError('the send sent is: '+sendstring);
	}
}
</code>
</pre>  
Vamos a empezar por la parte <b>onmessage</b>. Aquí comprueba si el mensaje es el mismo que se supone que viene desde el proceso en segundo plano.  Si es así, guarda una referencia a ese puerto (port2) en la variable <em>'theport'</em>. Cuando un mensaje recibe un mensaje en él, llama a la función 'handleMessageFromInjectedScript', que sería enviar un mensaje a la consola. Luego al final publica un mensaje en este puerto, que será recibido por el <em>script inyectado</em>.
En el código HTML, tenemos
<pre>
<code class="language-markup">
&lt;p>&lt;a href="javascript:sendMessage('change title');">Change Title&lt;/a>&lt;/p>
</code>
</pre>  
Esto llama a la función <em>SendMessage</em>, que toma la cadena 'change title' y lo envía al script inyectado.
Por último, volvemos al script inyectado. ¿Recuerda que tenemos una función para gestionar los mensajes de la ventana emergente? Echemos un vistazo a esa función
<pre>
<code class="language-javascript">
function handlePopupMessage(e){
			opera.postError( "Message received from the popup: " + event.data );
			
			if (event.data == "change title"){
				document.title = "You have clicked " + counter + " times";
				counter++;
			}
		}
</code>
</pre>
La función 'handlePopupMessage' se llama cuando un mensaje es recibido desde el menú emergente en port1. Comprueba si el mensaje es 'change title', y si es así, cambia el título del documento y se incrementa el contador.  
Así que en resumen, sucede lo siguiente:
*Carga de proceso en segundo plano
*La página se carga, y también lo hace el script inyectado.
*El usuario hace clic en el menú emergente, que se conecta al proceso en segundo plano. El <b>evento onconnect</b> es disparado en el <em>proceso en segundo plano</em> que dispara un mensaje al <em>script inyectado</em>. El mensaje que se envía al script inyectado, tiene también una referencia a la ventana emergente.
*El <b>script inyectado</b> escucha este mensaje y establece un canal de mensaje. Toma la referencia a la ventana emergente (que llegó a traves del mensaje que ha recibido) y envía un mensaje a ella. Ese mensaje tiene una referencia al canal 2 del canal del mensaje.
*El <b>popup</b> escucha este mensaje y almacena la referencia al segundo canal.
*Ahora siempre que un mensaje debe enviarse al script inyectado, el menú emergente sólo envía un <b>postMessage</b> a él.
*Ese mensaje enviado a port2 por el <em>popup</em> será escuchado en port1 del <em>script inyectado</em>. Cuando llega un mensaje, comprueba qué tipo de mensaje es y realiza la acción adecuada (en el caso del ejemplo cambia el título del documento)
Envolviéndolo
La comunicación entre las distintas partes de una extensión es bastante fácil una vez que llegas a saber cómo utilizar <em>postMessage</em> junto con los controladores <b>onconnect</b> y <b>onmessage</b>.
También puede utilizar <em>canales de mensaje</em>, que se requiere cuando se trata de comunicación entre un <b>popup</b> y un <b>script inyectado</b>.
Este artículo ha arrojar algo de luz sobre cómo pasar mensajes en tres escenarios: entre el proceso en segundo plano y un script inyectado; entre el proceso en segundo plano y un popup; y entre un popup y un script inyectado.