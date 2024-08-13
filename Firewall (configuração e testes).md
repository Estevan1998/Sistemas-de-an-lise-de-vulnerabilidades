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

**Para autorizar acessos a pedido DNS da M #3 à Internet, criamos uma regra de acesso a esse pedido**

>Sugestão de regra de autorizção de acesso:

**![DNS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_7.png)**

>Resultado da regra criada:

**![DNS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_8.png)**

**Através dos logs da regra criada, vemos que a máquina 3 já tem acesso a pedidos DNS.**

>Confirmação a partir dos LOGS:

**![LOGS](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/firewall_9.png)**

***

# Autorize acesso a pedidos HTTP e HTTPS da M #3 à Internet (tcp / porta 80 e porta 443).


