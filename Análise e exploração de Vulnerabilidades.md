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

**Primeiramenete, com o objetivo de identificar o número de máquinas na rede e o seu respetivo sistema operativo, realizamos um scan da rede com auxílio da ferramnta NMAP. Neste passo já nos é revelado no enunciado que o endereço de rede é o 10.1.20.0/24.**

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

````html
smtp-user-enum -M VRFY -U /usr/share/metasploit-framework/dat/wordlists/unix_users.txt -t 10.1.20.5
````

>Resultado enumeração:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_4.png)**

**Após obter a lista de utilizadores, voltamos a repetir o comando, mas com auxílio do comando grep e cut para filtrar nos resultados apenas o nome dos utilizadores e armazenar num ficheiro de texto, para criar uma automação para ser mais fácil de utilizar posteriormente.**

* “| grep exists”
* “|”: O operador pipe redireciona a saída do comando anterior para o próximo comando.
* “grep exists”: Filtra a saída para mostrar apenas as linhas que contêm a palavra "exists".
* “| cut -d " " -f2”
* “|”: Outro operador pipe que redireciona a saída do comando anterior para o próximo comando.
* “cut -d " " -f2”: Utiliza o comando cut para dividir a linha em campos delimitados por espaço (-d " ") e seleciona o segundo campo (-f2), que é o nome de utilizador.

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_5.png)**

***

## 3 - Finger 

**Através do metasploit, procuramos pelo nome do serviço.**

>Nome do serviço (Metasploit-framework):

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_6.png)**

**O modulo número 10, Finger Service User Enumerator, é o que utilizamos para enumerar utilizadores.**

````html
use 10
````

>Utilização do modulo número 10

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_7.png)**

**Encontramos os mesmos utilizadores, vistos anteriormente no serviço SMTP.**

>Utilizadores encontrados:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_8.png)**

**O script finger-user-enum.pl utiliza o protocolo Finger para consultar o servidor e verificar a existência de utilizadores listados no arquivo. O protocolo Finger é uma ferramenta de rede que pode ser usada para obter informações sobre os utilizadores de um sistema.**

**A enumeração de utilizadores usando o protocolo Finger pode revelar informações 
valiosas para um atacante, como a existência de contas de utilizadores, informações de 
login e outros detalhes pessoais. Embora o protocolo Finger seja antigo e raramente usado 
em sistemas modernos, alguns sistemas legados ainda podem ter o serviço Finger 
habilitado.**

>perl finger-user-enum.pl -U:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_9.png)**

***

4 - HYDRA

**Prosseguimos para força bruta com a ferramenta HYDRA.**

>O qué o HYDRA:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_10.png)**

**Utilizamos o ficheiro vulnixUsers.txt, onde foi armazenado os utilizadores do scanner anterior de SMTP, e vamos utilizar uma lista de passwords chamada “rockyou” bastante conhecida e completa, para tentar quebrar as credenciais dos utilizadores.** 

````html
hydra -l user /usr/share/wordlists/rockyou.txt 10.1.20.5 ssh -f -t4 -I
````

>Utilização da lista de passwords "rockyou":

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_11.png)**

**Confirmamos as credenciais do utilizador “user“com password “letmein”.**

**Na figura abaixo verificamos a  conexão via SSH com as credenciais do utilizador.**

````html
ssh use@10.1.20.5
````

>Conexão SSH:

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_12.png)**

***

## 5 - Escalar previlégios

**Escalar privilégios é o processo de obter acesso a níveis mais altos de autoridade ou 
permissões num sistema, permitindo ao invasor realizar ações que normalmente não 
seriam possíveis com suas credenciais iniciais.** 

**Isso geralmente é feito explorando 
vulnerabilidades ou falhas de segurança no sistema para ganhar controle sobre recursos 
ou executar comandos com privilégios elevados.** 

***

### /etc/passwd 

**O comando cat /etc/passwd é usado para visualizar o conteúdo do arquivo /etc/passwd em sistemas Unix e Linux. Este arquivo é fundamental para gerir utilizadores no sistema.** 

**![Metasploit](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_13.png)**

**A linha vulnix:x:2008:2008::/home/vulnix:/bin/bash no arquivo /etc/passwd contém todas as informações necessárias para o sistema identificar e autenticar o utilizador vulnix, além de definir o seu ambiente de trabalho (diretório home e shell de login).**

````html
vulnix:x:2008:2008::/home/vulnix:/bin/bash
````

**Cada linha do arquivo /etc/passwd contém os seguintes campos:**

````html
username:x:1000:1000:User Name,,,:/home/username:/bin/bash 
````

* username: O nome do utilizador.
* password: Uma marcação de senha. Tradicionalmente, este campo continha a senha criptografada do usuário, mas atualmente a maioria dos sistemas usa x ou *, e a senha real é armazenada no arquivo /etc/shadow, que é mais seguro.
* UID: O User ID, um número único que identifica o usuário no sistema.
* GID: O Group ID, um número que identifica o grupo principal do usuário.
* GECOS: Um campo de comentários que pode conter o nome completo do utilizador e outras informações.
* home directory: O diretório home do utilizador.
* shell: O shell de login do utilizador, que é o programa executado após o login (geralmente /bin/bash ou /bin/sh).

***

## 6 - LinEnum 

**Na figura abaixo fizemos a transferência do LinEnum.sh.**

**O LinEnum.sh é um script usado por profissionais de segurança e pentesters para avaliar a segurança de sistemas Linux. Ele fornece uma análise detalhada do sistema, identificando vulnerabilidades e pontos fracos que podem ser explorados.**

**Ele oferece uma ampla gama de funcionalidades, incluindo informações sobre o sistema, como a versão do kernel e a distribuição do sistema operacional, detalhes sobre hardware e configurações de rede.**

**Além disso, o LinEnum.sh verifica informações de utilizadores e grupos, como contas de utilizador, senhas criptografadas e permissões de arquivos críticos. Ele também analisa configurações de serviços e processos, como serviços em execução, processos ativos e configurações de serviços como SSH.**

**Em termos de segurança, o script realiza verificações de segurança, incluindo a análise de SUID/SGID, configurações de sudo e capacidades de binários.**

**Por fim, o LinEnum.sh também identifica arquivos de interesse, como arquivos com permissões inadequadas, arquivos de configuração que podem conter informações sensíveis e logs históricos que podem revelar atividades suspeitas.**

>LinEnum:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_14.png)**

**Após execução do script, vamos obter imensa informação relevante.**

**Colocamos as imagens o mais próximas possíveis pois são várias em função da 
informação encontrada no sistema.**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_15.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_16.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_17.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_18.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_19.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_20.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_21.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_22.png)**

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_23.png)**

**Montamos depois no nosso sistema a pasta partilhada no diretório /home/vulnix no kali para ter acesso a pasta /mnt/vulnix para conseguir acesso ao sistema.**

>Montagem da pasta partilhada:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_24.png)**

**Entramos no utilizador vulnix para aceder ao sistema.**

>Acesso ao sistema:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_25.png)**

**De seguida, geramos uma chave publica de SSH.**

>Chave SSH:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_26.png)**

**Movemos essa chave para dentro da pasta .ssh e colocamos como autorized_keys.**

>Colocação da chave dentro da pasta .ssh:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_27.png)**

**Na figura abaixo, tentamos fazer conexão via SSH e tirar proveito das chaves que criamos anteriormente.**

>Conexão via SSH:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_28.png)**

**Conexão com sucesso, com o comando “sudo -ll” que é utilizado para listar as permissões 
de sudo de um utilizador.**

````html
sudo -ll 
````

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_29.png)**

**Com base na saída fornecida, podemos concluir que o utilizador vulnix tem permissões 
específicas de sudo.**

**Pode executar o comando sudoedit no arquivo /etc/exports com privilégios de root.**

**Não precisa fornecer a senha ao executar sudoedit /etc/exports.**

**O arquivo /etc/exports é usado para configurar exportações de sistemas de arquivos via NFS. Ter acesso para editar este arquivo pode permitir ao utilizador vulnix modificar as configurações de exportação NFS, o que pode afetar a segurança e a configuração da rede.**

**Permitir a execução do comando sudoedit /etc/exports sem senha (NOPASSWD) reduz a segurança, pois qualquer comprometimento da conta vulnix poderia resultar em mudanças não autorizadas no arquivo /etc/exports.**

>/etc/exports:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_30.png)**

**Visto que não precisamos de senha para alterar este ficheiro, vamos acrescentar /root para ter acesso ao root através do sistema de exportação e gravamos o ficheiro.** 

>/root :

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_31.png)**

**Após gravar o ficheiro necessitamos de dar reboot à máquina para as alterações surtir efeito.**

**Contudo, para fazer reboot á máquina é necessário ter permissões root do qual ainda não temos.**

>reboot:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_32.png)**

**Então vamos tornar este cenário um pouco real e esgotar os recursos da máquina através de uma fork bomb obrigando o administrador a reiniciar a máquina.**

>fork bomb:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_33.png)**

**Este script, na figura abaixo representado, maximiza o consumo de recursos do sistema.**

>fork bomb em funcionamento:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_34.png)**

**Uma fork bomb funciona criando processos rapidamente, e cada novo processo cria mais processos, resultando num crescimento exponencial do número de processos.**

**Isso rapidamente consome todos os recursos disponíveis do sistema. Em sistemas Linux, o OOM (Out of Memory) Killer pode entrar em ação quando a memória é consumida de forma total, terminando processos para libertar memória.**

>Reinício da máquina:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_35.png)**

**Como pretendido, figura abaixo, a máquina vulnix ficou sem recursos, obrigando o 
administrador a reiniciar a máquina.**

**O comando “showmount -e” é utilizado para exibir as exportações de sistemas de arquivos NFS (Network File System) disponíveis num servidor. Ele lista os diretórios que são compartilhados e quais clientes têm permissão para montá-los.**

````html
sudo showmount -e 
````

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_36.png)**

**Vemos que o diretório “/root” está exportado e pode ser montado por qualquer máquina.**

**Com isso, vamos explorar vulnerabilidades a partir dos arquivos encontrados no diretório /root para obter privilégios administrativos.**

**De seguida, criamos e montamos um novo sistema de exportação.**

>Criação e montagem de um novo sistema de exportação:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_37.png)**

**Na figura abaixo, utilizamos o comando para listar os diretórios e ficheiros no caminho que criamos anteriormente.**

>Diretórios e ficheiros:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_38.png)**

**Conseguimos perceber que existe o ficheiro trophy.txt com a nossa flag descrita pelo autor da máquina Vulnix.  Na figura abaixo, ligamo-nos via SSH no utilizador root.**

>Ligação SSH (user root):

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_39.png)**

**Por fim, na figura abixo,é visível que conseguimos explorar a máquina e obter a flag.

>Obtenção da flag:

**![LinEnum](https://github.com/Estevan1998/Sistemas-de-analise-de-vulnerabilidades/blob/main/images/Vulnix_40.png)**

***


