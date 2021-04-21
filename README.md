# ProjetoIntegrador-VII
## Encontros:
* terça Feira das 20h30 às 22h00min
* Google Meet

## Avaliações:
* 1ª Avaliação:
* 2º Avaliação:
* Segunda Chamada:
* Exame:

## Primeira avaliação:
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
client.username_pw_set("9qe75fqo530o", "8W9sp2NptKNZ")
client.connect("mqtt.demo.konkerlabs.net", 1883)

while True:
    #leitura e tratamento de dados da CPU
    cpu_percent = psutil.cpu_percent(interval=1)
    cpu_freq = psutil.cpu_freq(percpu=False)
    cpu_p = float(cpu_percent)
    cpu_f = float(round(cpu_freq.current, 2))

    # leitura de memória:
    mem = psutil.virtual_memory()
    mem_available = float(round(mem.available / 1000000000, 2))

    # leitura de disco:
    discoC = psutil.disk_usage('C://')
    discoD = psutil.disk_usage('D://')
    discoC_free = float(round(discoC.free / 1000000000, 2))
    discoD_free = float(round(discoD.free / 1000000000, 2))

    try:
        client.publish("data/9qe75fqo530o/pub/<CPU porcentagem>",
                        json.dumps({"Cpu porcentagem ": cpu_p, "Unidade": "%"}))
        client.publish("data/9qe75fqo530o/pub/<CPU frequencia>",
                       json.dumps({"CPU frequencia": cpu_f, "Unidade": "Hz"}))
        client.publish("data/9qe75fqo530o/pub/<Memoria>",
                       json.dumps({"Memória ": mem_available, "Unidade": "GB"}))
        client.publish("data/9qe75fqo530o/pub/<DiscoC>",
                       json.dumps({"Disco C livre ": discoC_free, "Unidade": "GB"}))
        client.publish("data/9qe75fqo530o/pub/<DiscoD>",
                       json.dumps({"Disco D livre ": discoD_free, "Unidade": "GB"}))

    except:
        print("connection failed")  # Em caso de erro de conexão
        time.sleep(5)


~~~
*  Plataforma [Konker](https://demo.konkerlabs.net/registry/devices/p1ck2t84@p1ck2t84/a48b4adc-0af3-4370-9dda-629bc938cb21/events) com as informações enviadas:

