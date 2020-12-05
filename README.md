# Entrega Login

Para este ejercicio se ha escogido el ejemplo de Login número uno (https://www.javatpoint.com/servlet-login-and-logout-example-using-cookies). Se ha descargado el código de la aplicación y como pone en la página que el IDE utilizado ha sido Eclipse se ha escogido el mismo para evitar problemas. Por resumir, se incluyen las características del entorno y de la versión de Java utilizados:

```
Eclipse IDE for Java Developers (includes Incubating components)
Version: 2020-09 (4.17.0)
Build id: 20200910-1200

openjdk 11.0.9.1 2020-11-04
OpenJDK Runtime Environment AdoptOpenJDK (build 11.0.9.1+1)
OpenJDK 64-Bit Server VM AdoptOpenJDK (build 11.0.9.1+1, mixed mode)
```
Para iniciar el programa primero se importa el proyecto. Una vez hecho esto, se selecciona con el botón derecho la carpeta "loginappcookie" que se ha creado y se ejecuta de la siguiente manera: Click derecho > Run as > Run On Server > Finish

## Issues and solutions
Siguiendo las reglas y recomendaciones en SEI CERT Oracle Coding Standard for Java se han encontrado los fallos que se exponen a continuación. Se organizarán estos fallos según el fichero en el que se han encontrado. 

LoginServlet.java:
- *ERR01-J. Do not allow exceptions to expose sensitive information:* Las excepciones revelan información que puede ser utilizada por los atacantes para conocer las vulnerabilidades del software. *Solución:* Se deben atrapar las excepciones y poner mensajes genéricos para evitar la exposición de información sensible.

- *MET00-J. Validate method arguments:* Los métodos que reciben argumentos deben validarlos por razones de seguridad. Esto sirve para comprobar que se encuentran dentro de los límites del diseño previsto. Si no se realiza la validación de los métodos puede implicar excepciones en tiempo de ejecución, cálculos incorrectos, etc.  *Solución:* Validar las variables pasadas por parámetro.

- *MSC59-J. Limit the lifetime of sensitive data:* La contraseña es guardada como un objeto String. Las credenciales permanecen expuestas hasta que el garbage collector recupere la memoria asociada con esta variable. *Solución:* Establecer la variable password como una cadena de chars. De esta manera, se puede limpiar la contraseña del array inmediatamente después de su uso.
```java 
char[] password = request.getParameter("password").toCharArray();
...
 Arrays.fill(password,' '); //vaciar la contraseña
```
- *Rec. 00. Input Validation and Data Sanitization (IDS)* Los datos no son sanitizados (ni name ni password). *Solución:* Añadir al código la sanitización de las variables mediante la función: ```sanitize(value)```

- En este código no se establece un tiempo limitado para que el servidor invalide la sesión, por lo que es vulnerable a un ataque XSS. *Solución:* Se mitigaría este riesgo si se pone un tiempo limitado a la cookie, por ejemplo de 15 minutos. Esto se haría mediante el método ``` setMaxAge(900)```

- *MSC03-J. Never hard code sensitive information:* La contraseña se comprueba en texto plano. *Solución:* Habría que usar funciones hash para minimizar la exposición de la contraseña. Para ello, se podría crear una nueva clase Password que contenga las funciones setPassword(byte[] pass) y checkPassword(byte[] pass), donde  setPassword alamacene el valor hash de la contraseña en un fichero .bin y checkPassword compruebe el hash de dicho fichero con el valor pasado por parámetro.

- *EXP02-J. Do not use the Object.equals() method to compare two arrays.* La contraseña es comparada con un equals. Esto hace que se compare solo las referencias de los arrays, no su contenido. *Solución:* Utilizar la función ```Arrays.equals(password, password2)``` donde password2 será la contraseña correcta. Hay que guardar la contraseña en un vector de chars mediante ```char[] password2 = "admin123".toCharArray();``` Si tenemos en cuenta la correción anterior, habría que almacenar el valor hash de la contraseña en el fichero .bin para luego hacer su comprobación con la contraseña que ingresa el usuario. En este caso, no es necesaria la línea ```Arrays.equals(password, password2)```, sino que se pondría ```java if(checkPassword(password)){ ... ```

- Según la programación seguida en el código la contraseña válida es "admin123". Dicha contraseña es correcta con cualquier nombre de usuario lo que podría ser una vulnerabilidad ya que la cookie se guarda con el nombre de usuario. *Solución:* Una posible solución a esto podría ser crear una base de datos con nombre/contraseña o almacenar los nombres en un vector de vectores chars y emparejarlo mediante un LoginService con una función map y crear una función isValidUser().

LogoutServlet.java y ProfileServlet.java:

- *ERR01-J. Do not allow exceptions to expose sensitive information:* Las excepciones revelan información que puede ser utilizada por los atacantes para conocer las vulnerabilidades del software. *Solución:* Se deben atrapar las excepciones y poner mensajes genéricos para evitar la exposición de información sensible.

- *MET00-J. Validate method arguments:* Los métodos que reciben argumentos deben validarlos por razones de seguridad. Esto sirve para comprobar que se encuentran dentro de los límites del diseño previsto. Si no se realiza la validación de los métodos puede implicar excepciones en tiempo de ejecución, cálculos incorrectos, etc.  *Solución:* Validar las variables pasadas por parámetro.

 
