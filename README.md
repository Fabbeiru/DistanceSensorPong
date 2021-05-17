# DistanceSensorPong
Blinking led: As the sinusoidal function reaches its maximum and minimum points the flashing frequency of the led, the one incorporated on the Arduino ONE, varies. Done by:
- José María Amusquívar Poppe. (<a href="https://github.com/JoseMAP-99">Link</a>) <br>
- Prashant Jeswani Tejwani. (<a href="https://github.com/Prashant-JT">Link</a>) <br>
- Fabián Alfonso Beirutti Pérez.

## Introducción
Se programa una interfaz que utiliza la información de distancia suministrada por el sensor Sharp GP2D12, leída a través del Arduino, para controlar el movimiento de la pala del Jugador 2 en el juego Pong implementado con Processing. Se ha reutilizado y refactorizado en clases el código del <a href="https://prashant-jt.github.io/My-Processing-Book/2021/02/09/pong.html">juego multijugador</a> implementado anteriormente. A continuación:
* Se describe el trabajo realizado argumentando las decisiones adoptadas para la solución propuesta
* Se incluye las referencias y herramientas utilizadas
* Se muestra el resultado con un gif animado

## Diseño y configuración 

El diseño y configuración ha sido el que se puede observar en la siguiente figura. Se adjunta el <a href="https://www.tinkercad.com/things/e77wAqxA3kJ">enlace de la configuración en Tinkercad</a> para visualizar la simulación y modificar el código si se desea. El conexionado de cada cable del sensor de distancia a las señales que corresponden en la tarjeta es el siguiente: rojo = 5v, negro = GND y amarillo = A0.

<p align="center"><img src="" alt="Diseño, configuración y simulación del Arduino en Tinkercad"/>

## Código implementado

A continuación se describe el trabajo realizado. Respecto al código de Processing, se crean e inicializan las variables necesarias para el correcto funcionamiento y establecimiento de la conexión con Arduino.
```C
Serial arduino;
String value = "0";
```
En la función *setup()* se obtiene una lista de todos los puertos serie disponibles y se escoge el puerto adecuado. A continuación, se crea objeto Serial para leer los datos que son enviados desde Arduino a través de la función *Serial.println(msg)* (véase la función *loop()* del programa en Arduino).

**Nota:** Se deberá escoger el mismo puerto que se esté utilizando en Arduino (en nuestro caso es el primero de la lista).
```C
void setup() {
  ...
  // Arduino port
  String portName = Serial.list()[0];
  arduino = new Serial(this, portName, 9600);
  ...
}
```
En la función *draw()* se llama la función *move()* de la clase Paddle para el movimiento de las palas de los jugadores. Como el movimiento de la pala del el jugador 2 se realiza mediante el sensor, se pasa como parámetro la distancia mediante la función *getSensorDistance()*.
```C
void draw() {
  if (start) {
    ...
    paddle.move(getSensorDistance());
    ...
  } else {
    ...
  }
}
```    
La función *getSensorDistance()* devuelve la distancia en cm obtenida de Arduino, en el caso de que el valor sea nulo se devuelve -1.
```C      
float getSensorDistance() {
  if (arduino.available() > 0) {
    value = arduino.readStringUntil('\n');
  }

  return (value != null) ? float(value) : -1;
}    
```      
La Clase Paddle representa las palas de ambos jugadores. Para el jugador 2 se inicializan las variables para establecer un rango mínimo y máximo de distancia.
```C
...
private float minDistance;
private float maxDistance;
private float posRemapped;

public Paddle() {
  ...
  this.minDistance = 7.0;
  this.maxDistance = 30.0;
}
```
La función *move(distance)* se encarga mover las palas de ambos jugadores. Para el movimiento de pala del jugador 2, la variable *posRemapped* mapea la distancia obtenida de la mano entre 0 y la parte inferior del tablero de juego.
```C
void move(float distance) {
  ...
  if (distance == -1) return;    
  if (distance > maxDistance) distance = maxDistance;
  if (distance < minDistance) distance = minDistance;

  // Map player 2 position 
  posRemapped = map(distance, minDistance, maxDistance, 0, height-89);
  this.posy2 = posRemapped;
}
```
Respecto al código de Arduino, se inicializan las variables que almacenarán los resultado obtenidos del sensor de distancia, conectando el sensor en el pin analógico A0. En la función *setup()* se abre el monitor de serie para visualizar las distancias que se obtienen.
```C
int IR_SENSOR = 0; // Sensor connected to the analog A0
int sensorResult = 0; // Sensor result
float sensorDistance = 0; // Calculated value

void setup() {
  // Setup communication with serial monitor
  Serial.begin(9600);
}
```
La función *loop()* se encarga de leer el valor del sensor, convertirlo en centímetros y enviarlo a Processing.
```C
void loop() {
  // Read the value from the ir sensor
  sensorResult = analogRead(IR_SENSOR);
  // Calculate distance in cm
  sensorDistance = (6787.0 / (sensorResult - 3.0)) - 4.0;
  // Send distance to Processing
  Serial.println(sensorDistance);
  delay(200); // Wait
}
```
A continuación, se muestra el resultado final mediante un gif animado. La pala de la derecha es del jugador 2, controlado mediante el sensor de distancia:

## Descarga y prueba
Para poder probar correctamente el código, descargar los ficheros (el .zip del repositorio) y en la carpeta llamada BlinkLed se encuentran los archivos de la aplicación listos para probar y ejecutar. El archivo "README.md" y aquellos fuera de la carpeta del proyecto (BlinkLed), son opcionales, si se descargan no deberían influir en el funcionamiento del código ya que, son usados para darle formato a la presentación y explicación del repositorio en la plataforma GitHub.

Por otra parte, si se desea comprobar y/o visitar los repositorios de los compañeros de este proyecto puede acceder a ellos a continuación:
- <a href="https://josemap-99.github.io/2021/05/16/sensor_pong.html">Repositorio del proyecto de José María Amusquívar Poppe.</a>
- <a href="https://prashant-jt.github.io/My-Processing-Book/2021/05/09/sensor-pong.html">Repositorio del proyecto de Prashant Jeswani Tejwani.</a>

## Referencias
Para la realización de este proyecto, se han consultado y/o utilizado los siguientes recursos:
* Guión de prácticas de la asignatura CIU
* <a href="https://www.arduino.cc/">Página oficial de Arduino</a>
