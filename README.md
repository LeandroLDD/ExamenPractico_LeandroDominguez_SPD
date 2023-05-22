# Funcionamiento integral: Montacarga
![MontacargaArduino](https://github.com/LeandroLDD/ExamenPractico_LeandroDominguez_SPD/assets/104798622/5c8e7a29-1d5b-4874-96c2-0b73b528d956)

## Descripcion
Sistema de montacarga que implementa un algoritmo que permite recibir ordener para controlarlo hasta en 9 pisos. Las ordenes que recibe son:
- Bajar
- Subir
- Detener

El algoritmo muestra en un Display 7 segmentos el piso en el que se encuentra el montacarga, pudiendo controlarlo con tres pulsadores desde cualquiera de los pisos mientras el aviso de disponibilidad para presionar este encendido y avisa con dos leds si el sistema del montacarga se encuentra encendido o apagado.

## Variables importantes
Vectores que representan un numero en el 7 segmentos. Almacenan los pines del 7 segmentos que se encenderan finalizados con un -1 como limitador.
-     int cero[8] = {A,B,C,D,E,F,-1};
      int uno[8] = {B,C,-1};
      int dos[8] = {B,A,G,E,D,-1};
      int tres[8] = {A,B,G,C,D,-1};
      int cuatro[8] = {F,G,B,C,-1};
      int cinco[8] = {A,F,G,C,D,-1};
      int seis[8] = {A,F,G,C,D,E,-1};
      int siete[8] = {A,B,C,-1};
      int ocho[8] = {A,B,C,D,E,F,G,-1};
      int nueve[8] = {A,B,C,G,F,-1};

Arreglo de punteros para poder manipular los numeros que se encenderan en el 7 segmentos. Almacena todos los vectores que representan un numero en el Display.
-     int* numeroDisplay[] = {cero,uno,dos,tres,cuatro,cinco,seis,siete,ocho,nueve};

<a id="estadoMontacarga"></a>
Indica en que estado se encuentra el montacarga, SUBIR(1), BAJAR(-1) o DETENER(0).
-     int estadoMontacarga;

## Funciones

Enciende un vector de tipo int que represente un numero en el 7 segmentos y retorna un valor booleano, exito(true) o error(false).
Recibe como parametros, **int num[]** el vector de numeros y **bool estado** que indica si debe encender(true) o apagar(false) el numero que va a representar en el display.
-     bool digitalWriteSegmento(int num[], bool estado);

Enciende un pin.
Recibe como parametro **int led** el pin a encender.
-     void prenderLed(int led);

Apaga un pin.
Recibe como parametro **int led** el pin a apagar.
-     void apagarLed(int led);

Prende y apaga un pin segun un valor booleano.
Recibe como parametros, **bool valor** valor boleano que determina el pin a encender y a apagar, **int ledTrue** pin a encender, **int ledFalse** pin a apagar.
-     void prenderLedBooleano(bool valor, int ledTrue, int ledFalse);

## Funcion loop()
Contiene la logical principal del programa.

Lo primero que hace el algoritmo mientras el sistema del montacarga esté activo o apagado es enviar un mensaje por Serial indicando el piso actual en el se encuentra.

      Serial.println("\nPiso actual");
      Serial.println(pisoMontacarga);

Antes de entrar a la deteccion de botones se enciende un led con la función **prenderLed()** que indicara que el montacarga no está en movimiento y puede presionar algun boton controlador.
Para poder detectar la presión de los botones controladores del montacarga se uso un bucle, que se repetira hasta 15 veces con un tiempo de espera total de 1,5 segundos para dar tiempo a presionar alguno mientras el montacarga está subiendo o bajando. Cada condicional detecta un boton controlador, para luego establecer [estadoMontacarga](#estadoMontacarga) con su correspondiente valor.

        prenderLed(AVISODISPONIBLE);
        for(int i = 0; i < 15; i++){
          delay(100);

          if(!digitalRead(BTNSUBIR) && pisoMontacarga < PISOMAX){
            estadoMontacarga = SUBIR;
            sisMontacarga = true;
          }
          if(!digitalRead(BTNBAJAR) && pisoMontacarga > PISOMIN){
            estadoMontacarga = BAJAR;
            sisMontacarga = true;
          }
          if(!digitalRead(BTNDETENER) || (estadoMontacarga == BAJAR && pisoMontacarga == PISOMIN) || (estadoMontacarga == SUBIR && pisoMontacarga == PISOMAX)){
            estadoMontacarga = DETENER;
            sisMontacarga = false;
          }
        }

