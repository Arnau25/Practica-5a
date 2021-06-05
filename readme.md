# Practica 5 Escaner i2c

### Código
````
#include <Arduino.h>
#include <Wire.h>
#include "MAX30105.h"
#include <Adafruit_I2CDevice.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

void setup()
{
  Wire.begin();
 
  Serial.begin(127300);
  while (!Serial);             // Leonardo: wait for serial monitor
  Serial.println("\nI2C Scanner");
}
 
 
void loop()
{
  byte error, address;
  int nDevices;
 
  Serial.println("Scanning...");
 
  nDevices = 0;
  for(address = 1; address < 127; address++ )
  {
    // The i2c_scanner
    // the Write.endTransmisstion to see if
    // a device did acknowledge to the address.
    Wire.beginTransmission(address);
    error = Wire.endTransmission();
 
    if (error == 0)
    {
      Serial.print("I2C device found at address 0x");
      if (address<16)
        Serial.print("0");
      Serial.print(address,HEX);
      Serial.println("  !");
 
      nDevices++;
    }
    else if (error==4)
    {
      Serial.print("Unknown error at address 0x");
      if (address<16)
        Serial.
        Serial.print("0");
      Serial.println(address,HEX);
    }    
  }
  if (nDevices == 0)
    Serial.println("No I2C devices found\n");
  else
    Serial.println("done\n");
 
  delay(5000);           // wait 5 seconds for next scan
}
````

### Funcionamiento

Para esta practica era necesario que añadieramos unas librerias extra, estas son:
````
#include <Arduino.h>
#include <Wire.h>
#include "MAX30105.h"
#include <Adafruit_I2CDevice.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
````
Seguidamente por lo que hace el void setup() tendremos que inicializar una de las bibliotecas, en este caso la biblioteca WIRE con la funcion Wire.begin(), una vez hecho esto estableceremos la velocidad de datos, en nuestro caso sera de 127300 bits por segundo, para la transmisión de datos en serie. A continuación con la línea de código while(!Serial) lo que estamos diciendo es que la placa espere a que el PC establezca una conexión serie activa. Finalmente imprimiremos un mensaje por pantalla.

````
void setup()
{
  Wire.begin();
 
  Serial.begin(127300);
  while (!Serial);             // Leonardo: wait for serial monitor
  Serial.println("\nI2C Scanner");
}
````
Por lo que hace al void loop(), se fijara en que dirección tenemos nuestro dispositivo. Por lo tanto empezaremos definiendo algunas variables e imprimiremos por pantalla "Scanning...", seguidamente crearemos un bucle que hara que se repite 127 veces, este nos ayudara a detectar cualquier dispositivo I2C, mediante la funcion Wire.beginTransmission(address) iniciaremos una transmisión al dispositivo esclavo I2C con la dirección dada. seguidamente con la función Wire.endTransmission finaliza la transmisión iniciada por Wire.beginTransmission(address) y transmite los bytes puestos en cola por write(), estos bytes pueden tener 5 distintos valores. El 0 que determina que se a realizado con éxito, el 1 que determina que los datos son demasiado largos para caber en el búfer de transmisión, el 2 que es el que se encarga de transmitir la dirección y recibe el NACK, el 3 que es el que transmite datos y recibe el NACK, y por ultimo el 4 que indica cualquier otro error.

Esto nos provocara que si el error es igual a 0 se mostrará por el terminal la dirección, en cambio si el error es igual a 4 se mostrará por el terminal que hay un error en la conexión, y si no es igual a 0 ni a 4 se mostrara que no se ha encontrado ningñun dispositivo I2C.