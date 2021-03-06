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
#Declarando as bibliotecas necessárias:
import time
import psutil
import paho.mqtt.client as mqtt
import json

#informações necessárias para enviar dados para plataforma
client = mqtt.Client()
client.username_pw_set("t86mto8vulfm", "Nk2DsZHiWpjz")
client.connect("mqtt.demo.konkerlabs.net", 1883)

while True:
    #leitura e tratamento dos dados da CPU
    cpu_percent = psutil.cpu_percent(interval=1)
    cpu_freq = psutil.cpu_freq(percpu=False)
    cpu_p = float(cpu_percent)
    cpu_f = float(round(cpu_freq.current, 2))

    # leitura e tratamento dos dados da memória:
    mem = psutil.virtual_memory()
    mem_available = float(round(mem.available / 1000000000, 2))

    # leitura e tratamento dos dados do disco:
    discoC = psutil.disk_usage('C://')
    discoD = psutil.disk_usage('D://')
    discoC_free = float(round(discoC.free / 1000000000, 2))
    discoD_free = float(round(discoD.free / 1000000000, 2))

    try:
        #Enviando informações de CPU
        client.publish("data/t86mto8vulfm/pub/<CPU>",
                        json.dumps({"Porcentagem de CPU ": cpu_p, "Unidade": "%"}))
        client.publish("data/t86mto8vulfm/pub/<CPU>",
                       json.dumps({"Frequência de CPU": cpu_f, "Unidade": "Hz"}))

        #Enviando informações de Memória:
        client.publish("data/t86mto8vulfm/pub/<Memoria>",
                       json.dumps({"Memória disponivel ": mem_available, "Unidade": "GB"}))

        #enviando Informações do disco:
        client.publish("data/t86mto8vulfm/pub/<Disco>",
                       json.dumps({"Disco C livre ": discoC_free, "Unidade": "GB"}))
        client.publish("data/t86mto8vulfm/pub/<Disco>",
                       json.dumps({"Disco D livre ": discoD_free, "Unidade": "GB"}))

    except:
        print("connection failed")  # Em caso de erro de conexão
        time.sleep(5)
~~~
*  Plataforma [Konker](https://demo.konkerlabs.net/registry/devices/p1ck2t84@p1ck2t84/0e0e588e-6fb4-49a1-813f-2b39f01dc71d/events) com as informações enviadas:

## Segunda parte:
* Código python
~~~python
import paho.mqtt.client as mqtt
import json

def on_connect(client, userdata, flags, rc):
    print("Connected!")
    client.subscribe("sub/t86mto8vulfm/out")

def on_message(client, data, msg):
    print(msg.topic + " " + str(msg.payload))


client = mqtt.Client()
client.on_message = on_message
client.on_connect = on_connect
client.username_pw_set("t86mto8vulfm", "Nk2DsZHiWpjz")
client.connect("mqtt.demo.konkerlabs.net", 1883)
client.loop_forever()

~~~

## Código python Grafana
~~~Python
# Importando bibliotecas:
import datetime
import time
import psutil
import paho.mqtt.client as mqtt
import json
import requests

#declarando os identificadores das cidades:
id_Jaguarao = "456277"
id_Rio_Grande = "455906"
id_Caxias = "12581299"

#Função de busca de temperatura:
def buscar_dados(id):
    request = requests.get(f"https://api.hgbrasil.com/weather?woeid={id}")
    todo = json.loads(request.content)


    temp = todo["results"]["temp"]

    return (temp)

#fazer a conexão com o broker:
client = mqtt.Client()
client.username_pw_set("####", "###########")
client.connect("142.47.103.158", 1883)

while True:
    #leitura e tratamento de dados da CPU:
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

    # execução da função para ler as informações de temperatura de Jaguarão, Rio Grande e Caxias:
    tempJ = buscar_dados(id_Jaguarao)
    tempR = buscar_dados(id_Rio_Grande)
    tempC = buscar_dados(id_Caxias)
    
    try:
        #Enviando informações da CPU
        client.publish("PI7", json.dumps({"id": "Gabriel_cpu", "data": "%f" %cpu_p}))

        #Enviando informações da Memória:
        client.publish("PI7", json.dumps({"id": "Gabriel_memory", "data": "%f" %mem_available}))

        #enviando Informações do disco:
        client.publish("PI7", json.dumps({"id": "Gabriel_DiscoC", "data": "%f" %discoC_free}))
        client.publish("PI7", json.dumps({"id": "Gabriel_DiscoD", "data": "%f" %discoD_free}))

        #enviando Informações da temperatura de Jaguarão:
        client.publish("PI7", json.dumps({"id": "Gabriel_tempJaguarao", "data": "%d" % tempJ}))

        # enviando Informações da temperatura de Rio Grande:
        client.publish("PI7", json.dumps({"id": "Gabriel_tempRioGrande", "data": "%d" % tempR}))

        # enviando Informações da temperatura de Caxias:
        client.publish("PI7", json.dumps({"id": "Gabriel_tempCaxias", "data": "%d" % tempC}))

        #impressão dos dados do computador
        print('"id": "Gabriel_cpu", "data": "%f"' %cpu_p)
        print('"id": "Gabriel_memory", "data": "%f"' %mem_available)
        print('"id": "Gabriel_DiscoC", "data": "%f"' %discoC_free)
        print('"id": "Gabriel_DiscoD", "data": "%f"' %discoD_free)

        #impressão dos dados das temperaturas
        print('"id": "Gabriel_temp_Jaguarao", "data": "%d"' % tempJ)
        print('"id": "Gabriel_temp_RioGrande", "data": "%d"' % tempR)
        print('"id": "Gabriel_temp_Caxias", "data": "%d"' % tempC)
        time.sleep(60) #mandar as informações a cada minuto/evitar spam de dados
    except:
        print("connection failed")  # Em caso de erro de conexão
        time.sleep(5)
~~~
