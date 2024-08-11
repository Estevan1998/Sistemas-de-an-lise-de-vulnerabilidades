# Reconhecimento - Deteção de sistemas oprativos
***
**Para a realização da deteção de sistemas operativos colocou-se á prova 3 desafios:**

* Deverá ser possível, a partir da M #2 a deteção dos sistemas operativos nas M #3, M #4 e 
M#5;
* Documente os sistemas operativos e serviços em cada uma das máquinas;
* Efetue capturas de tráfego, detetando e identificando protocolos seguros e protocolos não 
seguros.


**Na fase de reconhecimento, a deteção de sistemas operacionais é uma etapa crucial para 
entender a topologia da rede alvo e identificar potenciais vulnerabilidades específicas do 
sistema operacional.** 

**Vamos também recorrer á enumeração de serviços que é uma etapa do processo de teste 
de segurança de sistemas de computadores e redes. Consiste em identificar e listar os 
serviços em execução num sistema ou rede, juntamente com detalhes sobre as suas 
configurações e versões. Esses serviços podem incluir servidores web, bancos de dados, 
serviços de e-mail, serviços de arquivos, entre outros.**

**O objetivo da enumeração de serviços é entender melhor a superfície de ataque de um 
sistema ou rede, identificar possíveis pontos de entrada para ataques e avaliar o nível de 
exposição a potenciais vulnerabilidades. Isso é feito através de técnicas como varredura 
de portas, análise de banners, consulta a diretórios padrão de serviços e interação com 
serviços para obter informações adicionais.**

**As informações obtidas durante a enumeração de serviços são essenciais para os 
profissionais de segurança informática desenvolverem estratégias de proteção e mitigação 
de riscos, como aplicação de patches, configuração adequada de serviços e 
implementação de políticas de segurança.**

**No decorrer deste projeto, é proposto utilizar alguns serviços de fraca segurança para 
demonstrações educacionais de como podemos explorar sistemas através desses serviços, 
criando assim um ambiente de estudos para praticar as nossas competências em 
segurança informática.**


**![Exemplo](https://github.com/Estevan1998/Sistemas-de-an-lise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-05%20172709.png)**

* A M# 1 será o vosso host físico (computador pessoal)
* A M#5 será a vm “HackLAB: Vulnix”
* A M#3 e M#4 poderão ser VMs ubuntu
* Os IPs das máquinas 2,3 e 4 neste procedimento encontram-se na gama 10.1.20.0/24 

***

## 1- N MAP

**Conforme ilustrado na figura abaixo,  usamos a ferramenta NMAP com o comando "nmap sP".**

````html
sudo nmap -sP 10.1.20.0/24
````

**É utilizado para realizar um ping sweep(é um método que pode estabelecer um intervalo de endereços IP que são mapeados para hosts ativos) na rede específica,onde "IP" é o endereço IP da rede que desejamos verificar. Esse comando envia um pacote ICMP Echo Request para cada IP na faixa especificada e aguarda pelas respostas para determinar quais hosts estão ativos na rede.**

**No entanto, é importante ressaltar que esse 
comando pode ser considerado intrusivo, pois ele envia pacotes para os dispositivos na rede, o que pode ser percebido como um ataque e esse tráfego ser intercetado com algumas ferramentas de análise tráfego de pacotes, regras de firewall, IDS e IPS.**


**![N MAP](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160350.png)**
***

## 2- ARP e MAC

**Na figura abaixo, após utilizar o nmap, iremos utilizar o recurso da tabela ARP.** 

**O comando "arp -a" é utilizado para mostrar a tabela ARP (Address Resolution Protocol) do sistema operacional. A tabela mapeia endereços IP para endereços MAC (Media Access Control) na rede local. Ao executar esse comando, veremos uma lista de todos os dispositivos que o sistema tem conhecimento, incluindo o nosso própriodispositivo, bem como outros dispositivos na rede local.**

**Esta tabela é útil para identificar quais dispositivos estão presentes na rede local e para associar endereços IP a endereços MAC. No entanto, é importante ressaltar que essa tabela pode não incluir todos os dispositivos da rede, especialmente se eles não tiverem interagido recentemente com o nosso sistema.**

**![ARP e MAC](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160415.png)**

**Encontramos as máquinas com endereços 10.1.20.5, 10.1.20.6, 10.1.20.7, vamos prosseguir com a fase de reconhecimento, e reunir o máximo de informações possíveis sobre estes hosts.** 

**Utilizaremos de novo a ferramenta nmap, com o comando ‘nmap -O -sV -sC’. 

**Em resumo, ao usar o comando "nmap -O -sV -sC", fazemos uma varredura completa que inclui a deteção de sistemas operacionais, identificação de versões de serviços e execução de scripts para deteção de vulnerabilidades e enumeração de informações nos hosts alvos.**

**![Opções](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20162226.png)**
**
**![Opções](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160415.png)**

***

## 3 - Enumeração de serviços na máquina 10.1.20.6 

**O resultado do scan á máquina com o endereço 10.1.20.6 com sistema Ubuntu.**  

**No resultado do nmap vamos prestar atenção aos serviços e versões e ver se as versões estão actualizadas para versão mais recente. Caso sejam versões desatualizadas, podemos procurar por exploits públicos para tentar comprometer o sistema. Outra alternativa, seria procurar por más configurações nos serviços listados e usá-los como vetor de ataque.**

**Na máquina em questão, a porta 21 está associada ao serviço FTP (File Transfer Protocol), utilizando o servidor vsftpd Figura 4 – nmap scan às máquinas 
versão 3.0.5, e seria de considerar o uso de FTPS (FTP seguro) para criptografar o tráfego 
entre o cliente e o servidor FTP.**

**A porta 22 está associada ao serviço SSH (Secure Shell), utilizando o servidor OpenSSH versão 9.6, uma boa prática seria configurar o SSH para utilizar chaves de autenticação em vez de senhas, se possível, e implementar medidas de segurança adicionais, como autenticação de dois fatores e controlo de tentativas de login.** 

**A porta 80 está associada ao serviço HTTP, utilizando o servidor Apache HTTP versão 2.4.58, seria de considerar implementar práticas de segurança, como o uso de HTTPS em vez de HTTP para criptografar o tráfego e configurar adequadamente as permissões de arquivo e diretório para evitar a exposição de informações sensíveis. Algumas recomendações de boas práticas seria, atualizar para as versões mais recentes dos serviços, implementar regras de firewall para restringir o acesso às portas apenas a endereços IP autorizados.**
**
**![nmap scan às máquinas](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160442.png)**
***
## 4 - Enumeração de serviços na máquina 10.1.20.7 

**Neste resultado, vamos seguir a mesma metodologia acima mencionada. 
Aqui podemos verificar dois serviços  na máquina 10.1.20.7, diferentes da máquina 
anterior.**

````html
sudo nmap -sP 10.1.20.7/24
````

**Neste resultado, vamos seguir a mesma metodologia acima mencionada. 
Aqui podemos verificar dois serviços  na máquina 10.1.20.7, diferentes da máquina 
anterior.**

**O SimpleHTTPServer é uma ferramenta de desenvolvimento simples e não deve ser usada 
em ambientes de produção devido a preocupações com segurança. Para hospedagem de 
sites reais, é recomendável usar servidores HTTP mais robustos e seguros, como o Apache 
HTTP Server ou o Nginx.**
</br>
**![nmap scan às máquinas](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160506.png)**
***

## 5 - Enumeração de serviços na máquina 10.1.20.5 

**O resultado do nmap á maquina 10.1.20.5 foi gigante, do qual não iremos colocar imagens 
e sim em texto numa tabela para facilitar a leitura e organização para usar futuramente.**

**![Enumeração de serviços na máquina 10.1.20.5 ](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160552.png)**
**![Enumeração de serviços na máquina 10.1.20.5 ](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160603.png)**

**Com tudo, o resultado do nmap apresentou outras portas e serviços. Iremos abaixo, 
documentar as portas e serviços encontrados.**

**![Enumeração de serviços na máquina 10.1.20.5 ](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160612.png)**
**![Enumeração de serviços na máquina 10.1.20.5 ](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160623.png)**
***

## 6- OS FingerPrinting 

**A técnica de utilizar a conexão Telnet para verificar o sistema operacional é chamada de 
"fingerprinting" ou "OS fingerprinting".** 

````html
telnet 10.1.20.7
````

**Essa técnica envolve a análise do comportamento 
do sistema durante uma conexão Telnet para determinar características específicas do 
sistema operacional, como a versão, o tipo e outras informações relevantes.** 

**Essas informações podem ser úteis para identificar vulnerabilidades específicas do sistema 
operacional ou para fins de auditoria e segurança de rede. Na figura abaixo, constatámos 
que se trata de um Linux Mint.**

**![telnet](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160518.png)**
***

## 7 - Captura de tráfego, detetando e identificando protocolos seguros e não seguros 

**Os objetivos da captura de tráfego, deteção e identificação de protocolos seguros e não 
seguros consiste em capturar o tráfego de rede que permite analisar o comportamento da 
rede em tempo real. Isso pode ajudar a identificar atividades suspeitas, como 
comunicações não autorizadas, tráfego malicioso ou tentativas de intrusão.**

**Com isso conseguimos fazer uma análise de segurança e com captura de tráfego permite 
analisar padrões de tráfego para identificar possíveis vulnerabilidades ou ameaças à 
segurança da rede. Isso pode incluir a deteção de ataques de negação de serviço (DDoS), 
tentativas de invasão, malware ou comunicações não criptografadas, entre muitas outras 
funções.** 

**Através da análise do tráfego capturado, é possível identificar os protocolos de 
comunicação utilizados na rede e determinar se estão sendo implementadas práticas de 
segurança adequadas. Isso inclui a identificação de protocolos seguros, como HTTPS, SSH 
e VPNs, em contraste com protocolos não seguros, como HTTP, Telnet, FTP e outros.**

**Em resumo, a captura de tráfego, deteção e identificação de protocolos seguros e não 
seguros são componentes essenciais de uma estratégia de segurança de rede abrangente, 
ajudando a proteger contra ameaças e garantir a integridade, confidencialidade e 
disponibilidade dos dados na rede.**
***

## 8 - Telnet 

**Neste primeiro teste, iremos realizar através da máquina Ubuntu (10.1.20.6) uma conexão 
Telnet na máquina Linux Mint (10.1.20.7), e após a conexão, irá executar o comando “ls” 
para listar os diretórios.**

**Então vamos prosseguir e verificar se o Telnet é um protocolo 
seguro ou inseguro, através da ferramenta WireShark no kali Linux (10.1.20.4 - M#2), que 
irá ficar à escuta do tráfego como se fosse um atacante na rede.**

**![telnet](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160726.png)**

**Na máquina Kali Linux, podemos confirmar no wireshark com o filtro de telnet, 
conseguimos capturar o tráfego entre a máquina 4 e 5 no Telnet.** 

**Na máquina LinuxMint podemos ver também a ferramenta TCPDUMP a capturar o tráfego 
da conexão Telnet, mas vamos continuar no wireshark para inspecionar de forma 
minuciosa o conteúdo dos pacotes.**

**Ao escolher um pacote listado no wireshark, ao utilizar a função de “Follow TCP Stream”, 
podemos ver o conteúdo do pacote selecionado.**

**E o que verificamos é que podemos “ver a Shell” da máquina Ubuntu ao efetuar login no 
telnet e a password do sistema do LinuxMint. Neste cenário constámos que “mint” é o 
utilizador e a password é “mint”. Podemos também perceber a execução do comando “ls” 
escrito anteriormente.** 

**![telnet](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160715.png)**

**Com este teste, concluímos que o serviço Telnet utiliza protocolos inseguros de 
transmissão de dados.**

**O Telnet transmite dados, incluindo senhas, de forma não criptografada pela rede, o que 
significa que qualquer pessoa que tenha acesso ao tráfego de rede pode intercetar e ler as 
informações transmitidas. Isso torna o Telnet altamente vulnerável a ataques de 
espionagem e intercetação de dados.**

**Por esse motivo, é altamente recomendável evitar o uso do Telnet em favor de protocolos 
mais seguros, como SSH (Secure Shell), que oferece criptografia de ponta a ponta para 
comunicações remotas, garantindo a confidencialidade e a integridade dos dados 
transmitidos pela rede.**

**![telnet](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-10%20160726.png)**
***












