# ProjetoIntegrador-VII
## Encontros:
* terça Feira das 20h30 às 22h00min
* Google Meet


## Material Base:
*
*
*

## Avaliações:
* 1ª Avaliação:
* 2º Avaliação:
* Segunda Chamada:
* Exame:

## Material Complementar:
*
*
*

## primeira avaliação 

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


