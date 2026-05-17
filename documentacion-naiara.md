# Actividad - Herramientas de Sistemas de Construcción: Maven naiara

Esta actividad es la base para probar las herramientas de automatización en la construcción, es decir cómo podemos automatizar todo el proceso de construcción que vamos a ver en esta actividad.

---

## Preparación del laboratorio
1. Comienzo con la preparación del laboratorio, poniendo los comandos que se ven a continuación para:
   + Crear una varable con mi nombre
   + Crear una carpeta
   + Colocarme en dicha carpeta
```bash
# crea variable con tu nombre
TuNombre=naiara
# Creamnos carpeta
mkdir -p Unidad5/Actividad-Maven
# Nos colocamos en ella
cd Unidad5/Actividad-Maven
```
![](/images/img1.png)

2. Compruebo si está instalado java11 con el comando:
```bash
update-alternatives --list java
```
Y veo que efectivamente tengo instalado java11
![](/images/img2.png)

3. Descargo la aplicación store-app que brinda el profesor en el enlace [store-app](https://github.com/jmmedinac03vjp/PuestaProduccionSegura/blob/main/Unidad5-SistemasDesplegadoAutomatizadoSoftware/Actividad-SistemasConstruccionMaven/files/store-app.zip) y la pongo en la tarea en la que estoy trabajando. Seguidamente ejecuto los comandos
```bash
# coloca store-app.zip en la carpeta
# descomprime 
unzip store-app.zip 
# comprueba que se ha descomprimido
ls -l store-app
```
para descomprimir la aplicación y comprobar que se ha hecho correctamente.
![](/images/img3.png)
4. A continuación compruebo la versión de Maven usando
```bash
mvn --version
```
![](/images/img4.png)

---

## ESTRUCTURA DE UN PROYECTO MAVEN
Observo brevemente la estructura del proyecto, tiene que salir
algo así:
```bash
cd store-app
tree
```
Hay varios elementos importantes como por ejemplo 
+ pom.xml: archivo principal de maven, define el proyecto, las dependencias, plugins y el ciclo de construcción
+ src/main/java: tiene el código fuente de la aplicación organizado por pquetes de java
+ src/test/java: contiene el código de pruebas
+ target: es la carpeta que maven genera al compilarse.
![](/images/img5.png)

---

## Ciclo de vida <default> de maven
### VALIDATE
Valido que el proyecto sea correcto y que la información necesaria está disponible. Para ello reviso la estructura del archivo  <pom.xml>, configuración básica y disponibilidad de elementos necesarios para el build.
Al ejecutar el comando 
```bash
mvn validate
```
veo que avisa de que ha encontrado problemas, gracias a dios solo son <warnings> asociado que no se encuentran disponibles los plugins <org.codehaus.mojo:sql-maven-plugin>
![](/images/img6.png)

### COMPILE
Para compilar ejecuto:
```bash
# compilamos
mvn compile
# vemos las clases generadas
ls target/classes
# o si queremos verlas en forma de árbol
tree target/classes
```
lo que harán estos comandos es compilar el código fuente principal de <src/main/java>. Intervienen también los recursos que se encuentran en >src/main/resources/> y <target/classes/>
![](/images/img7.png)

### TEST
1. Ejecuto las pruebas unitarias del proyecto.
    + Compila los test que se encuentran en src/test/java.
   + Se construyen los test compilados y se guardan en target/test-classes/
   + La aplicación comprimida store-app no tiene test unitarios de prueba. Vamos a crear un archivo de pruebas para probar mvn test.
El archivo de pruebas es el siguiente, el cual colocaré en src/test/java/es/storeapp/business/entities/OrderTest.java
```java
package es.storeapp.business.entities;

import java.util.ArrayList;
import java.util.List;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertTrue;
import org.junit.Before;
import org.junit.Test;

public class OrderTest {

    private Order order;

    @Before
    public void setUp() {
        order = new Order();
    }

    @Test
    public void testSetAndGetOrderId() {
        order.setOrderId(1L);
        assertEquals(Long.valueOf(1L), order.getOrderId());
    }

    @Test
    public void testSetAndGetName() {
        order.setName("Pedido 1");
        assertEquals("Pedido 1", order.getName());
    }

    @Test
    public void testSetAndGetTimestamp() {
        Long timestamp = System.currentTimeMillis();
        order.setTimestamp(timestamp);
        assertEquals(timestamp, order.getTimestamp());
    }

    @Test
    public void testSetAndGetPrice() {
        order.setPrice(100);
        assertEquals(Integer.valueOf(100), order.getPrice());
    }

    @Test
    public void testSetAndGetAddress() {
        order.setAddress("Calle Falsa 123");
        assertEquals("Calle Falsa 123", order.getAddress());
    }

    @Test
    public void testSetAndGetState() {
        OrderState state = OrderState.PENDING; // Ajusta si cambia el enum
        order.setState(state);
        assertEquals(state, order.getState());
    }

    @Test
    public void testSetAndGetUser() {
        User user = new User();
        order.setUser(user);
        assertEquals(user, order.getUser());
    }

    @Test
    public void testOrderLinesInitialization() {
        assertNotNull(order.getOrderLines());
        assertTrue(order.getOrderLines().isEmpty());
    }

    @Test
    public void testSetAndGetOrderLines() {
        List<OrderLine> lines = new ArrayList<>();
        lines.add(new OrderLine());

        order.setOrderLines(lines);

        assertEquals(1, order.getOrderLines().size());
        assertEquals(lines, order.getOrderLines());
    }

    @Test
    public void testToStringNotNull() {
        order.setOrderId(1L);
        order.setName("Pedido");
        order.setTimestamp(123456L);
        order.setPrice(50);
        order.setAddress("Dirección");
        order.setState(OrderState.PENDING);
        order.setUser(new User());

        String result = order.toString();

        assertNotNull(result);
        assertTrue(result.contains("Pedido"));
        assertTrue(result.contains("50"));
    }
}
```
![](/images/img8.png)

2. Seguidamente ejecuto el test de maven en el terminal y veo las clases de test que ha generado
```bash
# ejecutar mvn test
mvn test
# vemos las clases de test generados
tree target/classes
```
![](/images/img9.png)

3. Puedo observar la descarga de las dependencias necesarias ejecutando las pruebas unitarias que se ejecutan sin errores
![](/images/img10.png)

### PACKAGE
Empaqueta el proyecto en su formato distribuible, por ejemplo un .jar o .war.
Para ello ejecuto:
```bash
# ejecutar package
mvn package
# comprobar archivo store-app-1.0.0.jar creado
ls -l target/
```
Debo aclarar que es curioso como se realizan todo lo que he ejecutado anteriormente: validate, compile y test y como poco a poco va tardando un poquito más porque tiene que ir haciendo todos los pasos.
![](/images/img11.png)

### VERIFY
Ejecuta comprobaciones adicionales para validar que el paquete generado es correcto y cumple ciertos criterios de calidad.
Para ejecutar este paso pongo:
```bash
# ejecutar verify
mvn verify
# ver carpeta de maven
ls -la /home/$TuNombre/.m2/repository
```
![](/images/img12.png)
![](/images/img13.png)

### INSTALL
Lo que hacen los siguientes comandos es copiar el artefacto empaquetado al repositorio local de Maven para que otros proyectos del mismo equipo o equipo local puedan usarlo como dependencia.
```bash
# ejecuta install
mvn install
# comprobamos estructura creada 
tree ls -la /home/$TuNombre/.m2/repository/es/store-app
```
![](/images/img14.png)
![](/images/img15.png)

### DEPLOY
No ejecuto nada puesto que no tengo acceso a ninguno.

### EJECUCIÓN DE LA APLICACIÓN
Ejuto la aplicación eligiendo la **Opción 3: ejecutar el .jar con un contenedor docker*** que para ello solo tengo que ejecutar lo siguiente:
```bash
docker run --rm -p 8888:8888 \
  -v "$(pwd)/target/store-app-1.0.0.jar:/app/app.jar" \
  -v "$(pwd)/work:/app/work" \
  -w /app \
  eclipse-temurin:11-jre \
  java -jar /app/app.jar
```
![](/images/img16.png)

### ACCEDER A LA APLICACIÓN
Para acceder a la aplicación pongo en el navegador http://localhost:8888
Finalizo la aplicación pulsando <Ctrl + c> en el terminal
![](/images/img17.png)

### CICLO DE VIDA <Site> DE MAVEN
Ejecuto la construcción de la documentación con el comando 
```bash
# ejecuta el ciclo site para generación de documentación
mvn site
# nos cambiamos a la carpeta de la web generada y comprobamos documentación generada en target/site
cd target/site
tree 
# levantamos un servidor php para visualizarla
php -S 0:4444
# Para finalizar el servidor php pulsar Ctrl + C
```
![](/images/img18.png)
Cuando termine accedo mediante firefox poniendo http://localhost:4444 en la barra de la url
 y yasta
 ![](/images/img19.png)
