#### Onboard Data Logger

<img src="./img/logo_eesc.png" align="right"
     alt="Logo EESC" width="200" height="175">

Trabalho da disciplina SAA0356, realizada durante o segundo semestre de 2020.

#### Introdução e descrição inicial do sistema

Dentro da aeronautica, a determinação e controle de atitude (Attitude Detemination and Control - ADC) de uma aeronave é de fundamental importância para a existência do voo. Com a crescente utilização de Veículos Aéreos Não Tripulados (VANT's) para as mais variadas aplicações, iniciou-se um processo de barateamento da tecnologia de ADC, visando disseminar ainda mais a utilização desses veículos e tornar possível aplicações que não tenham capacidade de prover grandes investimentos. 


Este trabalho consiste na implementação de um sistema de fusão sensorial simplificado, o qual permite aquisitar sinais de uma unidade de medida inercial (Inertial Measurement Unit - IMU), calcular a atitude desse sensor - módulo que estaria presente na aeronave - e enviar esses sinais para um computador de monitoramento (Ground Station). Para isso, será utilizada uma BeagleBone Blue como dispositivo embarcado, uma placa de desenvolvimento com inúmeros recursos voltados para robótica, inclusive uma IMU e um computador de propósito geral, que servirá como Ground Station. Na Ground Station, os dados de atitude serão visualizados através de um modelo 3D de uma aeronave, a qual seguirá a atitude da placa de desenvolvimento, assim como seria caso a placa estivesse presente em um VANT, além de exibir os gráficos de rolagem, arfagem e guinada em função do tempo. A parte embarcada do sistema é fundamental para o voo de qualquer aeronave autônoma, já a parte desenvolvida para a Ground Station, imagina-se como exemplo de utilização no desenvolvimento de aeronaves de pequeno porte, para monitorar as variáveis principais de atitude, conseguindo avaliar o funcionamento do modelo assim como o desempenho do projeto em manobras. 

## Pré-requisitos

<p align="center">
  <img src="./img/hardware_diagram.png" alt="Logo EESC" width="400">
</p>


1. Escolha do IMU: 
O IMU é um sensor que, geralmente, combina as funcionalidades de acelerômetros, giroscópios e magnômetro, e permite medir a atitude de um objeto. 
Os acelerômetros são responsáveis por medir a variação de velocidade de um corpo. Os giroscópios medem as velocidades angulares. Por fim, os magnômetros medem a direção do campo magnético, funcionando como bússolas. 
Nosso projeto consistirá, portanto, no uso de um IMU embarcado em uma aeronave, cuja atitude medida será utilizada para plotagem de gráficos e visualização das rotações em um modelo tridimensional.

2. Protocolo de integração
SPI (Serial Peripheral Interface) é um protocolo de integração que possibilita que um microcontroador se comunique com outros componentes, de forma a criar uma rede. É utilizada para comunicações em pequenas distâncias, de forma síncrona, e funciona na forma "full duplex", de maneira "master-slave" com um master e múltiplos slaves.
Quando em modo master, o microcontrolador gera sinal de clock e, os que estão na posição de slaves, recebem o este sinal.

3. Protocolo de comunicação
Utiliza-se, neste projeto, o protocolo de comunicação TCP (Transmission Control Protocol), que garante comunicações a partir das quais diferentes aplicações podem trocar informações,e o qual utiliza IP (Internet Protocol). Nesse contexto, a comunicação entre a placa e a máquina host é feita por rede Wi-fi, via socket, e na máquina host é feita a integração entre os dados e a interface gráfica.

4. A comunicação é feita via sockets, que transmite dados na forma peer-to-peer, garantindo que o cliente envie informações para o servidor, e obtenha respostas deste.

5. Utiliza-se 10 ms como tempo de amostragem para o sistema de captação de atitude.


	Para que pudéssemos realizar o objetivo de estabelecer uma comunicação entre placa e computador, a fim de receber os dados da placa e conseguirmos, no computador, visualizar a atitude a qual está submetida a placa, por meio de uma interface gráfica, era de extrema importância conseguirmos entender a comunicação via socket.
	
	Neste trabalho, pudemos aprender como isso é feito, e uma das dificuldades foi entender tudo o que é preciso, nas implementações, para que cada parte da conexão exerça sua função de forma adequada. 
	
	Ao utilizarmos o protocolo de comunicação TCP, que funciona como se, de forma abstrata, houvesse uma conexão entre computador e placa, a comunicação é feita via socket. Nesse contexto, uma vez que temos, nesse tipo de comunicação, o servidor e o cliente, é necessário implementar o código para ambas as partes. 
	
	Para o servidor, é necessário que esse seja capaz de deixar aberta uma “porta”, de forma que esteja “aberto” à comunicação com o cliente. Tendo feito isso, é necessário que o servidor aguarde um cliente se conectar e, assim que isso é feito, esteja apto a receber dados do cliente, podendo também dar-lhe respostas. 
	
	O cliente, por sua vez, deve ser capaz de estabelecer a conexão com o servidor, via socket, a partir do IP do servidor e do número da porta a qual foi destinada para a conexão desejada. Ao se conectar, o cliente deve ser capaz de enviar informações e receber as respostas do servidor. 
	 


#### Instalação

Comandos no terminal:

#### 1. Cliente

##### 1.1. Comunicação entre host e placa: 

```sh
sudo sftp debian@(IP da comunicação)
```

##### 1.2. Localiza o diretório do arquivo server

##### 1.3. Cópia do código do server para a placa:
```sh
put socket_client 
```
##### 1.4. Estando o arquivo na placa, faz-se sua compilação:
```sh
gcc socket_client.c -o socket_client
```
##### 1.5. Execução do código na placa:
```sh
./socket_client
```
#### 2. Servidor

##### 2.1 Na máquina host, compila-se o arquivo do código:
```sh
gcc socket_server.c -o socket_server
```
##### 2.2. Executa-se o cófigo do servidor no host:
```sh
./socket_server
```
## Interface gráfica

A interface gráfica foi implementada em liguagem Python e permite acompanhar em tempo real a orientação da aeronave e gráficos de row, pitch e yaw. A comunicação entre a placa e a interface ocorre por meio de uma conexão cliente/servidor utilizando sockets e comunicação TCP/IP.  No caso, a máquina que executa a interface é o servidor e a placa é o cliente. Para que a comunicação funcione é necessário que a interface e a placa estejam na mesma rede local, caso estejam em redes diferentes, torna-se necessário realizar um redirecionamento de portas no roteador (isso ocorre devido a questões de segurança, é necessário configurar o roteador  da rede onde a interface está rodando para permitir que o  IP da placa acesse a porta previamente determinada na aplicação).

### CAD utilizado para a visualização

O modelo de avião utilizado na interface foi retirado e adapatado de [Free3D](https://free3d.com/3d-model/airplane-v2--549103.html). Foi necessário simplificar o modelo original pois este apresentava um nível elevado de detalhes e uma alta resolução de sua superfície, o que deixava todo o processamento de visualização mais lento.

<p align="center">
 <img src="./img/cad - imagem.png" 
     alt="CAD" width="256" height="130">
</p>


### Bibliotecas necessárias em Python (instale antes de rodar)

> [pyvista](https://docs.pyvista.org/) <br/>
> [numpy](https://numpy.org/install/) <br/>
> [vpython](https://www.glowscript.org/docs/VPythonDocs/index.html) <br/>
> [numpy-stl](https://pypi.org/project/numpy-stl/)

### Como rodar?


	$ python Interface-Servidor.py

### Explicação do Código

Inicialmente é feita a importação das bibliotecas necessárias para a execução da interface:


```python
from vpython import *
from time import *
import numpy as np
import math
import socket
import sys
import time
from stl import mesh 
import pyvista
```

É realizado a inicialização dos gráficos e a importação do modelo CAD para análise de seus parâmetros:

```python
s = 'Gráficos de <b><i>Row</i></b>, <b><i>Pitch</i></b>, <b><i>Yaw</i></b> em função do tempo.'

grafico = graph(title=s, xtitle='Tempo (s)', ytitle='Ângulo', fast=False, width=800)
funct1 = gcurve(color=color.blue, width=4, marker_color=color.orange, label='Row')
funct2 = gcurve( color=color.green, label='Pitch')
funct3 = gcurve(color=color.red, size=6, label='Yaw')

your_mesh = mesh.Mesh.from_file('airplane.stl') 
```

São exibidos no terminal informações como o volume e o centro de massa do CAD utilizado na visualização:

```python
volume, cog, inertia = your_mesh.get_mass_properties() 
print("Volume = {0}".format(volume)) 
print("Position of the center of gravity (COG) = {0}".format(cog)) 
print("Inertia matrix at expressed at the COG = {0}".format(inertia[0,:])) 
print(" {0}".format(inertia[1,:])) 
print(" {0}".format(inertia[2,:]))
```

É feita a importação e a manipulação do CAD para o ambiente de forma a garantir uma melhor visualização. Também é iniciado o ambiente de visualização do modelo 3D:

```python
sgrid = pyvista.PolyData('airplane.stl')
sgrid.translate([-cog[0], -1.15*cog[1], -cog[2]])

sgrid.rotate_z(90)
sgrid.rotate_x(-90)

# Get pointer to points
points = sgrid.points.copy()

cent = [0,0,0]
direction = [1,1,1]

# Start a plotter object and set the scalars to the Z height
plotter = pyvista.Plotter(off_screen=off_screen, notebook=notebook)
plotter.add_axes()
plotter.add_axes_at_origin(labels_off = True)
# plotter.add_mesh(sgrid, scalars=Z.ravel())
plotter.add_mesh(sgrid)
# plotter.camera_position = cpos
plotter.show(title='Airplane', window_size=[800, 600],
                 auto_close=False, interactive_update=True)

```

Inicia-se o servidor em "localhost" utilizando para conexão a porta 10000. O servidor passa então a esperar a conexão de algum cliente.

```python
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Bind the socket to the port
server_address = ("localhost", 10000)
print('Servidor: Iniciando servidor no endereco:', server_address)
sock.bind(server_address)

# Listen for incoming connections
sock.listen(1)
```

Inicia-se um loop com o servidor aguardando conexão. Quando a conexão é realizada, o cliente passa a enviar *strings* com os dados da seguinte forma: **time/roll/pitch/yaw**. 

O programa então faz a leitura, separação e conversão dessas *strings* para *float*. São plotados os respectivos gráficos e o corpo no ambiente de visualização é rotacionado. Após isso, o servidor responde o cliente com **R:time/roll/pitch/yaw** e passa a aguardar novos dados.

````python
while True:
    # Wait for a connection
    print('Servidor: Esperando conexao... ')
    connection, client_address = sock.accept()
    try:
        print('Servidor: Conexão feita com:', client_address)

        # Receive the data in small chunks and retransmit it
        while True:
            data = connection.recv(16).decode("utf-8")
            if data:
                str_time, str_roll, str_pitch, str_yaw =  data.split("/") #Le a string e a converte
                time = float(str_time)
                roll = float(str_roll)
                pitch = float(str_pitch)
                yaw = float(str_yaw)
     
                funct1.plot(time, roll)
                funct2.plot( time, pitch )
                funct3.plot( time, yaw )
                print("Roll=",roll," Pitch=",pitch,"Yaw=",yaw)
               
                sgrid.rotate_z(yaw)
                sgrid.rotate_y(pitch)
                sgrid.rotate_x(roll)

                plotter.update()
		
                connection.sendall(("R:" + data).encode()) #responde o cliente
            
    finally:
        print("")	
``````



## Funcionamento

TO DO

## Alunos

* **João Matheus Siqueira Souza** - *EESC/USP* - [GitHub](https://github.com/jmssouza)
* **Marina Gabriela Alves** - *EESC/USP* - [GitHub](https://github.com/MarinaGabrielaAlves)
* **Vinicius Aquilante Policarpo** - *EESC/USP* - [GitHub](https://github.com/viniciusapolicarpo)
* **William Zaniboni Silva** - *EESC/USP* - [GitHub](https://github.com/WilliamZaniboni)


## Agradecimentos

Este trabalho compõe os critérios de avaliação da matéria Sistemas Embarcados, e só foi possível a partir dos conceitos ensinados nas aulas do Professor Doutor Glauco Augusto de Paula Caurin e de sua equipe.

