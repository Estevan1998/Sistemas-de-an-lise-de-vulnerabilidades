# Firewall (configuração e testes)

**Neste laboratório optou-se por colocar a máquina #2 (PfSense) como barreira na rede. Assim as máquinas na rede que queiram utilzar a internet, tem o seu tráfego todo a transitar pela a máquina #2. Esta opção foi decidida a fim de controlar todo o tráfego entre a rede externa e rede interna.**

**Deste modo, no PfSense, utilizamos o sistema mais adequado de análise de vulnerabilidades e que também controla e permite verificar os protolcolos do tráfego. Esse sistema trata-se da Firewall.**

**A configuração da firewall oferece enumeras vantagens como o controlo e definição de regras especificas afim de controlar o tráfego da rede. Também permite restringir e até bloquear tráfego indesejado**

**Também permite a monitorização em tempo real do tráfego da rede. Uma mais valia para identificar comportamentos suspeitos ou for do comum no tráfegoi de rede**

**A fim de demonstrar as possiblidades e opções oferecidas neste sistema de análise de vulnerabilidades (Firewall), criamos os seguintes desafios e demonstramos, passo a passo separado em tópicos, as seguintes propostas:**

* 1 - Bloqueie o acesso SSH da M #3 à M #4 (tcp / porta 22). 
* 2 - Autorize todo o tráfego ICMP de toda a Rede externa à M #2. 
* 3 - Autorize acessos a pedidos DNS da M #3 à Internet (tcp e udp / porta 53).
* 4 - Autorize acesso a pedidos HTTP e HTTPS da M #3 à Internet (tcp / porta 80 e porta 443). 
* 5 - Autorize acesso a pedido SSH da M #1 à M #3 (tcp / porta 22).
* 6 - Instale um serviço web na máquina 3. Autorize o acesso da rede externa a esse servidor web.
* 7 - Configure uma regra que redirecione os pedidos à porta 80 da máquina 3 para a porta 80 da máquina 4. 
* 8 - Crie uma DMZ. Escolha a localização e instale uma máquina virtual na DMZ com um serviço web. Documente, explicando a localização da DMZ.
* 9 - Crie uma regra na firewall que, se tentarmos aceder à mesma na porta 80, o tráfego seja redirecionado para o web server localizado na DMZ. 

*** 

**Neste laboratório exprimental, para demonstrar a configuração e testes de análise de vulnerabilidades, foi montado o seguinte esquema laboratorial:**

* A M# 1 será o vosso host físico (computador pessoal)
* A M# 2 será o PfSense
* A M#3 e M#4 poderão ser VMs Ubuntu ou Mint
* A M#5 será a vm “HackLAB: Vulnix”
* A M#6 será o VM Kali Linux
* Os IPs das máquinas 2,3,4 e 6 neste procedimento encontram-se na gama 10.1.20.0/24 


**![Exemplo](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-11%20202244.png)**

***

# 1 - Bloqueie o acesso SSH da M #3 à M #4 (tcp / porta 22). 

**Neste primeiro exemplo, dá-mos uma proposta de bloqueio do acesso SSH da M #3 á M #4 com o seguinte comando apresentado:**

````html
sudo ufw deny from 10.1.20.3 to any port 22
````

>**Como exemplificado na figura abaixo, criamos uma regra da firewall UFW, de origem do endereço IP 10.1.20.3 (mint) que bloqueia tráfego para a porta 22 (ssh).**

**![Bloquear trafego porta 22 SSH](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_1.png)**

>**Na figura abaixo, o tcpdump declara o tráfego proveniente da máquina 10.1.20.3 mas que não é realizado e não permite estabelecer conexão.**

**![tcpdump](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_2.png)**

***

# 2 - Autorize todo o tráfego ICMP de toda a Rede externa à M #2. 

**Neste exemplo, criamos uma regra na interface WAN do PfSense que autoriza o tráfego ICMP de toda a rede externa à M #2**

>Resumo da regra que permite o tráfego ICMP na interface WAN do pfsense:

**![Tráfego ICMP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_3.png)**

>O endereço IP de origem do Host, que pode ser considerado rede externa, 192.168.1.89, com destino ao endereço WAN do pfsense. E habilitamos a opção de LOG, com o nome de ICMP externo.

**![Tráfego ICMP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_4.png)**

**De seguida, realizamos ping no powershell do HOST, com o endereço de IP do pfsense na interface WAN.**

>Resultado da execução do Echo Request (ping):

**![Tráfego ICMP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_5.png)**

**Por fim, na figura abaixo, podemos verificar o resultado dos logs da regra criada anteriormente.**

>Resultado dos Logs:

**![Logs](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_6.png)**

***

# 3 - Autorize acessos a pedidos DNS da M #3 à Internet (tcp e udp / porta 53).

**Para autorizar acessos a pedido DNS da M #3 à Internet, criamos uma regra de acesso a esse pedido.**

>Sugestão de regra de autorizção de acesso:

**![DNS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_7.png)**

>Resultado da regra criada:

**![DNS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_8.png)**

**Através dos logs da regra criada, vemos que a máquina 3 já tem acesso a pedidos DNS.**

>Confirmação a partir dos LOGS:

**![LOGS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_9.png)**

***

# 4 - Autorize acesso a pedidos HTTP e HTTPS da M #3 à Internet (tcp / porta 80 e porta 443).

**Para autorizar acesso a pedidos HTTP e HTTPS da M #3 à internet, criamos uma regra no WAN do PfSense.

>Regra criada:

**![HTTP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_10.png)**

>Resultado da regra criada:

**![HTTP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_11.png)**

**De seguida, através do navegador, criamos tráfego para o website nasa.gov.**

>Exemplo do tráfego:

**![HTTP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_12.png)**

**Com recurso ao comando ping, no website nasa.gov, vemos o endereço IP da mesma, e confirmamos nos LOGS da regra criada o tráfego gerado.**

>Confirmação dos LOGS:

**![LOGS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_13.png)**

***

# 5 - Autorize acesso a pedido SSH da M #1 à M #3 (tcp / porta 22).

**Neste exemplo, para autorizar o acesso a pedido SSH da M #1 à M #3, criamos a seguinte regra no WAN do PfSense para possibilitar esse acesso.**

>Criamos a regra de Port Forward:

**![SSH](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_14.png)**

**Com recurso ao PuTTY colocamos o IP da interface WAN do pfsense.**

>IP da WAN do PfSense:

**![SSH](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_15.png)**

>E conseguimos acesso via ssh à M#3:

**![SSH](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_16.png)**

**Por fim, com a implementação desta regra, foi possivel demonstrar a autorização do acesso a pedido SSH.**

>Vizualização e confirmação da prova conceito:

**![SSH](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_17.png)**

***

# 6 - Instale um serviço web na máquina 3. Autorize o acesso da rede externa a esse servidor web.

**Neste exemplo instalamos o serviço web apache2 na máquina M #3 para demonstração.**

>Instalação do apache2:

**![apache2](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_20.png)**

**Criamos a regra na ufw para permitir tráfego na porta 80 que é onde o apache vai ser servido.**

````html
sudo ufw allow 80/tcp
````

**![ufw](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_21.png)**

**E por fim iniciamos o serviço  do apache.**

````html
sudo service apache2 start
````

**![apache2 start](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_22.png)**

>Podemos vizualizar que temos acesso local ao apache.

**![apache2 start](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_23.png)**

**De seguida criamos uma regra Firewall/Nat/Port Forward**

>Sugestão de regra:

**![Regra](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_24.png)**

>Colocamos o endereço IP da interface WAN do pfsense que redireciona para a M#3:

**![Regra](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_25.png)**

**Por fim, verificamos o tráfego do servidor apache.**

>Tráfego do servidor apache:

**![Tráfego](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_26.png)**

***

# 7 - Configure uma regra que redirecione os pedidos à porta 80 da máquina 3 para a porta 80 da máquina 4.

**Para este exemplo, criamos uma regra para redirecionar o tráfego para a M#4.**

````html
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destiantion 10.1.20.4:80
````

**Na figura abaixo, através do tcpdump podemos ver que recebemos o tráfego na M#4 através da M#3.**

>Vizualização do tcpdump:

**![tcpdump](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_26.png)**
