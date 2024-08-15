# IDS/IPS (Instalação, Configuração e Testes)

**A instalação de um IDS/IPS oferece muitas vantagens na segurança da rede. Para demonstrar essas vanatgens, foi instalado um sistema IDS/IPS numa máquina (PfSense) centralizada na rede. Desta forma todo o tráfego proveniente da internet que entre na rede interna com destino a outras máquinas na rede, tem de passar pelo sistema de deteção de intrusos instalado no PfSense**

**Desta forma, existe na rede um ponto único para monitorizar comportamentos suspeitos ou fora do comum, com base em assinaturas de ataques conhecidos padrão como força bruta ou varreduras, a fim de alertar o administrador de rede sobre um possivel ataque na rede.**

**Este sistema também atua como prevenção uma vez que pode atuar de forma automática no bloqueio de certo tráfego considerado suspeito. Para este laboratório, o IDS utilizado foi o Snort devido ao seu caracter Open Source e de fácil utilização para além de ser um sistema de deteção de intrusos gratuito.**

**Para essa demonstração foi montado um laboratório com o seguinte esquema:**

* A M# 1 será o vosso host físico (computador pessoal)
* A M# 2 será o PfSense
* A M#3 e M#4 poderão ser VMs Ubuntu ou Mint
* A M#5 será a vm “HackLAB: Vulnix”
* A M#6 será o VM Kali Linux
* Os IPs das máquinas 2,3,4 e 6 neste procedimento encontram-se na gama 10.1.20.0/24

**![Exemplo](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-11%20202244.png)**

**A fim de exemplificar, passo a passo, o processo de instalação e configuração do IDS/IPS, elaboramos este laboratório com os seguintes tópicos:**

* 1 - Instalação do IDS/IPS (Snort)
* 2 - Análise das assinaturas e dos alertas detetados
* 3 - Configuração de um alerta para o ataque do tipo DoS
* 4 - Configuração de um alerta para o acesso a uma página com referência à palavra “Moscovo” a partir das máquinas internas.

***

## 1 - Instalação do IDS/IPS (Snort)

**Para a realização da instalação do Snort é preciso entrar na interface WAN do PfSense. Após aceder á interface, através do Package Manager, vamos instalar o Snort.**

>Inatalação do Snort:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_1.png)**

**Depois de instalar, criamos conta no Snort, e puxamos o nosso Oinkcode para ter-mos acesso à API do snort e podemos apartir daí usufruir dos recursos.**

>Criação da conta no Snort:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_2.png)**

**De seguida passamos para a configuração do Snort**

>Configuração:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_3.png)**

>General Settings:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_4.png)**

**Realizamos o update das regras, este passo é muito importante.**

>Update das regras:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_5.png)**

**Bloquemos hosts que criarem um alerta e ativamos as regras.**

>Bloqueio:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_6.png)**

>Ativação das regras:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_7.png)**

**Por fim, ativamos o Snort na interface do PfSense.**

>Ativação do Snort:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_8.png)**

***

## 2 - Análise das assinaturas e dos alertas detetado

**Neste tópico, para realizar a análise das assinaturas e dos alertas, geramos algum tráfego para obter os resultados.**

**Primeiramente, utilizamos o nmap para fazer um scan na M #3 e na M #2 através do Kali Linux.**

>NMAP á máquina #3:

**![Snort](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/IDS_9.png)**
