## VPN (Instalação, Configuração e Testes)

**A Instalação e configuração de um serviço VPN garante muitas vantagens. Desde a nivel de configuração e preferencias na rede até á segurança a nivel de rede.**

**Ao garantir a intalação e configuração da VPN, é garantido uma segurança avançada a nivel criptográfico e a nível de autenticação. Garante o anonimato na rede sendo substituido (ocultado) o endereço IP do utilizador por um endereço IP próprio da rede VPN.**

**Outra das vantagens oferecidas pela VPN é o acesso remoto de forma segura. Isto permite que um utilizador consiga ter acesso remoto seguro á red interna sem que seja preciso este estar no local da rede interna fisicamente.**

**Para demonstrar a utilidade deste sistema de segurança e análise de vulnerabilidades, realizamos testes em um laborátorio exprimental com o intuito de demonstrar, passo a passo, a intalação e configuração de um serviço VPN em uma máquina virtual (PfSense).**

**Para essa demonstração foi montado um laboratório com o seguinte esquema:**

* A M# 1 será o vosso host físico (computador pessoal)
* A M# 2 será o PfSense
* A M#3 e M#4 poderão ser VMs Ubuntu ou Mint
* A M#5 será a vm “HackLAB: Vulnix”
* A M#6 será o VM Kali Linux
* Os IPs das máquinas 2,3,4 e 6 neste procedimento encontram-se na gama 10.1.20.0/24

**![Exemplo](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Captura%20de%20ecr%C3%A3%202024-08-11%20202244.png)**

**A demonstração da instalação e configuração do serviço VPN encontra-se dividido nos seguintes tópicos:**

* 1 - Instalação na máquina 2 um servidor de VPN.
* 2 - Configuração do cliente Windows (M #1) para aceder ao servidor VPN.

**No final ainda foram realizados os seguintes testes para comprovar a efetividade das funcionalidades oferecidas pela VPN:**
* Teste 1 - Externamente deveremos ter acesso utilizando a VPN somente ao serviço ssh da máquina 3 e 4;
* Teste 2 - Externamente deveremos ter acesso utilizando a VPN somente ao serviço http na máquina 4; 

***



