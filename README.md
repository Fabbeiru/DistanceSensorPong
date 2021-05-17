# DistanceSensorPong
Blinking led: As the sinusoidal function reaches its maximum and minimum points the flashing frequency of the led, the one incorporated on the Arduino ONE, varies. Done by:
- José María Amusquívar Poppe. (<a href="https://github.com/JoseMAP-99">Link</a>) <br>
- Prashant Jeswani Tejwani. (<a href="https://github.com/Prashant-JT">Link</a>) <br>
- Fabián Alfonso Beirutti Pérez.

## Introducción
Se programa un Arduino de manera que se genere una pulsación de frecuencia variable en el LED integrado en la placa. 
Se produce una señal senoidal que define la envolvente, de manera que cuando dicha señal alcance su valor máximo el  LED  parpadeará  a  una cierta frecuencia *freqMax*, mientras que cuando alcance el valor mínimo parpadeará a una frecuencia mínima *freqMin*.  A continuación:

* Se describe el trabajo realizado argumentando las decisiones adoptadas para la solución propuesta
* Se incluye las referencias y herramientas utilizadas
* Se muestra el resultado con un gif animado

## Diseño y configuración 

El diseño y configuración ha sido el que se puede observar en la siguiente figura. Se adjunta el <a href=" https://www.tinkercad.com/things/cknacAsoMJE">enlace de la configuración en Tinkercad</a> para visualizar la simulación y modificar el código si se desea.

<p align="center"><img src="/images/blink-led-tinkercad-demo.gif" alt="Diseño, configuración y simulación del Arduino en Tinkercad"/>

## Código implementado

A continuación se describe el trabajo realizado. Se crean e inicializan las variables necesarias, como la frequencia mínima, máxima y normal la cual se establecen a 600, 60 y 250 respectivamente. Se inicializan las variables *jump* (incremento), *value* (valor en radianes del seno con rango entre -PI/2 y PI/2) y *senFreq* (resultado del seno con rango entre -1 y 1). 
```C
    // En milisegundos
    const float threshold = 0.75;
    const int freqMax = 60;
    const int freqMin = 600;
    const int freqNormal = 250;
    
    float jump = 0.1;
    float value = 0;
    float senFreq = 0;
```
En la función **setup()** se habilita el monitor serie y se establece el led incorporado en el Arduino como salida.
```C
    void setup() {  
      Serial.begin(9600);
      pinMode(LED_BUILTIN, OUTPUT);
    }
```
En la función **loop()** se calcula el seno de la variable *value* y se actualiza la variable. A continuación, se comprueba que esta variable esté en el rango -PI/2 y PI/2. Se cambia la frecuencia del parpadeo según el valor del seno calculado llamando la función **blinkLed()**.
```C
    void loop() {
      senFreq = sin(value);
      value += jump;

      if (value >= PI/2 || value <= -PI/2) jump = -jump;

      Serial.println(senFreq);

      if (senFreq > threshold) {
        blinkLed(freqMax);
      } else if (senFreq < -threshold) {
        blinkLed(freqMin);                       
      } else {
        blinkLed(freqNormal);
      }
    }
```    
La función **blinkLed(freq)** se encarga de encender y apagar el led acorde a una frecuencia que es pasada como parámetro. 
```C      
    void blinkLed (int freq) {
      digitalWrite(LED_BUILTIN, HIGH);  
      delay(freq);
      digitalWrite(LED_BUILTIN, LOW);    
      delay(freq);  
    }    
```      
A continuación, se muestra el resultado final mediante un gif animado de la salida del monitor serie y la ejecución del código en el arduino:
<p align="center">
<img src="/images/blink-led-serial-demo.gif" alt="Salida del monitor serie"></img><br>
<a href="https://media.giphy.com/media/xx9DkkDZIqvtpPQFNa/giphy.gif" alt ="Prueba del código en vivo">Prueba del código en vivo.</a>
</p>

## Descarga y prueba
Para poder probar correctamente el código, descargar los ficheros (el .zip del repositorio) y en la carpeta llamada BlinkLed se encuentran los archivos de la aplicación listos para probar y ejecutar. El archivo "README.md" y aquellos fuera de la carpeta del proyecto (BlinkLed), son opcionales, si se descargan no deberían influir en el funcionamiento del código ya que, son usados para darle formato a la presentación y explicación del repositorio en la plataforma GitHub.

Por otra parte, si se desea comprobar y/o visitar los repositorios de los compañeros de este proyecto puede acceder a ellos a continuación:
- <a href="https://josemap-99.github.io/2021/05/08/blink_led.html">Repositorio del proyecto de José María Amusquívar Poppe.</a>
- <a href="https://prashant-jt.github.io/My-Processing-Book/2021/05/04/blink-led.html">Repositorio del proyecto de Prashant Jeswani Tejwani.</a>

## Referencias
Para la realización de este proyecto, se han consultado y/o utilizado los siguientes recursos:
* Guión de prácticas de la asignatura CIU
* <a href="https://www.arduino.cc/">Página oficial de Arduino</a>
