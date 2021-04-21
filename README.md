# ProjetoIntegrador-VII
## Encontros:
* terça Feira das 20h30 às 22h00min
* Google Meet

## Avaliações:
* 1ª Avaliação:
* 2º Avaliação:
* Segunda Chamada:
* Exame:

## primeira avaliação:
 ### Objetivo:
 * Trabalhando com a Plataforma [Konker](http://www.konkerlabs.com/)
  * 1a. Etapa: 
    * Objetivo: Publicar dados a partir do computador ou de uma ESP32
    * Deadline: 20/04/2021
    * Criar uma conta no Konker
    * Postar informações sensoriadas por uma biblioteca em Python e/ou Bash
    * Computador - CPython: exemplo no [Tutorial do Konker](https://konker.atlassian.net/wiki/spaces/DEV/pages/28180518/Guia+de+Uso+da+Plataforma+Konker)
    * ESP32 - MicroPython: utilizar o exemplo deste [site](https://mjrobot.org/2018/06/13/iot-feito-facil-esp-micropython-mqtt-thingspeak/) (umqtt.simple) - 
 
 ### Resultado:
* Código Python:
~~~python
import time
import psutil
import paho.mqtt.client as mqtt
import json


client = mqtt.Client()
client.username_pw_set("4ddl8eqaqd7s", "Qvy7j5eVEqt9")
client.connect("mqtt.demo.konkerlabs.net", 1883)

while True:
    cpu_percent = psutil.cpu_percent(interval=1)
    cpu_p = float(cpu_percent)

    try:
        client.publish("data/4ddl8eqaqd7s/pub/<Channel>",
                        json.dumps({"Cpu": cpu_p, "unit": "%"}))

    except:
        print("connection failed")  # Em caso de erro de conexão
        time.sleep(5)
~~~
*  Plataforma [Konker](https://demo.konkerlabs.net/registry/devices/p1ck2t84@p1ck2t84/15271e9a-d7f4-40a4-906d-cc546a124b41/events) com as informações enviadas:

