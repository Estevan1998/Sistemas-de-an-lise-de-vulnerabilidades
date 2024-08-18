# Análise e exploração de Vulnerabilidades (Vulnix)

**Para essa demonstração foi montado um laboratório com o seguinte esquema:**

* A M# 1 será o vosso host físico (computador pessoal)
* A M# 2 será o PfSense
* A M#3 e M#4 poderão ser VMs Ubuntu ou Mint
* A M#5 será a vm “HackLAB: Vulnix”
* A M#6 será o VM Kali Linux
* Os IPs das máquinas 2,3,4 e 6 neste procedimento encontram-se na gama 10.1.20.0/24

**![Exemplo](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-11%20202244.png)**

***

## Scan da rede (N MAP)

**Primeiramnete, a fim de identificar o número de máquinas na rede e o seu respetivo sistema operativo, realizamos um scan da rede com auxílio da ferramnta NMAP. Neste passo já nos é revelado no enunciado que o endereço de rede é o 10.1.20.0/24.**

````html
sudo nmap -sP 10.1.20.0/24
````

>Resultado do scan da rede:

**![N MAP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160350.png)**

**Na figura abaixo, após utilizar o nmap, iremos utilizar o recurso da tabela ARP.**

**Esta tabela é útil para identificar quais dispositivos estão presentes na rede local e para associar endereços IP a endereços MAC. No entanto, é importante ressaltar que essa tabela pode não incluir todos os dispositivos da rede, especialmente se eles não tiverem interagido recentemente com o nosso sistema.** 

````html
arp -a
````

>Resultado da execusão do comando arp -a:

**![ARP e MAC](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160415.png)**

**Encontrámos as máquinas com endereços 10.1.20.5, 10.1.20.6 e 10.1.20.7. O endereço da máquina pretendida é o IP 10.1.20.5 (Vulnix).**

**Após descoberto o IP da máquina Vulnix,  realizamos um scan mais profundo a esse endereço IP.**

````html
sudo nmap -O -sV -sC 10.1.20.5
````

**A lista de vulnerabilidades dessa máquina virtual foi imensa por isso para a realização de um pentest, utilizamos apena a vulnerabilidade encontrada na porta 25.**

**![Porta 25](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-18%20151756.png)**

***

## 2- SMTP

**Primeiro, confirmou-se que o serviço SMTP está em execução conforme o resultado do NMAP detalhado neste relatório anteriormente, na porta 25.** 

>Vereficação do serviço SMTP:

**![SMTP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_1.png)**

**Podemos também utilizar o metasploit-framework com o scanner de smtp_version para confirmar se o serviço está em execução.**

>Utilização do Metasploit:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_2.png)**

**Confirmou-se que o serviço está em execução, vamos prosseguir para a enumeração de utilizadores do serviço, com recurso ao comando VRFY.**

**É um comando SMTP (Simple Mail Transfer Protocol) usado para verificar a existência de um endereço de e-mail num servidor de e-mail. Quando enviamos o comando VRFY seguido de um endereço de email, o servidor SMTP responde informando se o endereço de e-mail é válido e existe no servidor.**

> smtp-user-enum

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_3.png)**

**Após obter a lista de utilizadores, voltamos a repetir o comando, mas com auxílio do comando grep e cut para filtrar nos resultados apenas o nome dos utilizadores e armazenar num ficheiro de texto, para criar uma automação para ser mais fácil de utilizar posteriormente.**

* “| grep exists”
* “|”: O operador pipe redireciona a saída do comando anterior para o próximo comando.
* “grep exists”: Filtra a saída para mostrar apenas as linhas que contêm a palavra "exists".
* “| cut -d " " -f2”
* “|”: Outro operador pipe que redireciona a saída do comando anterior para o próximo comando.
* “cut -d " " -f2”: Utiliza o comando cut para dividir a linha em campos delimitados por espaço (-d " ") e seleciona o segundo campo (-f2), que é o nome de utilizador.

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_4.png)**

***

## 3 - Finger 

**Através do metasploit, procuramos pelo nome do serviço.**

>Nome do serviço (Metasploit-framework):

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_5.png)**

**O modulo número 10, Finger Service User Enumerator, é o que utilizamos para enumerar utilizadores.**

````html
use 10
````

>Utilização do modulo número 10

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_6.png)**

**Encontramos os mesmos utilizadores, vistos anteriormente no serviço SMTP.**

>Utilizadores encontrados:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_7.png)**

**O script finger-user-enum.pl utiliza o protocolo Finger para consultar o servidor e verificar a existência de utilizadores listados no arquivo. O protocolo Finger é uma ferramenta de rede que pode ser usada para obter informações sobre os utilizadores de um sistema.**

**A enumeração de utilizadores usando o protocolo Finger pode revelar informações 
valiosas para um atacante, como a existência de contas de utilizadores, informações de 
login e outros detalhes pessoais. Embora o protocolo Finger seja antigo e raramente usado 
em sistemas modernos, alguns sistemas legados ainda podem ter o serviço Finger 
habilitado.**

>perl finger-user-enum.pl -U:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_8.png)**

***

4 - HYDRA

**Prosseguimos para força bruta com a ferramenta HYDRA.**

>O qué o HYDRA:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_9.png)**

**Utilizamos o ficheiro vulnixUsers.txt, onde foi armazenado os utilizadores do scanner anterior de SMTP, e vamos utilizar uma lista de passwords chamada “rockyou” bastante conhecida e completa, para tentar quebrar as credenciais dos utilizadores.** 

````html
hydra -l user /usr/share/wordlists/rockyou.txt 10.1.20.5 ssh -f -t4 -I
````

>Utilização da lista de passwords "rockyou":

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_9.png)**
