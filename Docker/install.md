Instalação do Docker
O primeiro passo para utilizar o Docker é realizar a sua instalação. Isso nos dará acesso à sua interface de linha de comando (CLI).

Curiosidade: o Docker é feito de três grandes programas: Docker Daemon, a API e o CLI. Neste conteúdo, vamos instalar os três de uma vez só, entretanto vamos interagir com o Docker apenas por meio da sua interface de linha de comando.

No Linux, o Docker não possui uma interface gráfica de utilização (GUI) oficial, mas existem várias opções não-oficiais disponíveis, bem como uma extensão oficial da Microsoft para a plataforma no VSCode.

Aqui, vamos fazer a instalação do Docker por meio do comando apt-get, considerando uma máquina com Ubuntu LTS 64-bit (amd64).

Caso você queira ver as demais opções de instalações ou esteja utilizando outro Sistema Operacional, veja a documentação de instalação completa.

⚠️Caso ocorra algum problema ou comportamento diferente em algum dos passos, consulte o guia oficial.

1. Desinstale versões anteriores
Caso você já possua alguma versão de Docker instalada na sua máquina e queira refazer o processo de instalação para atualizar ou para corrigir algum problema, primeiro você deve remover os pacotes da versão que está na sua máquina. Para isso, utilize o seguinte comando no terminal:

Copiar
sudo apt-get remove docker* containerd runc
Caso nenhum dos pacotes esteja instalado, esse comando retornará o erro E: Impossível encontrar o <nome-do-pacote>. Nesse caso, é só prosseguir com a instalação.

👀 De olho na dica: o Docker preserva informações sobre imagens, containers, volumes e redes na pasta /var/lib/docker/. Nesse processo, esses arquivos não são apagados. Para remoção completa do Docker no seu sistema, consulte a seção Desinstalando o Docker no final desse tópico.

Instalando as dependências iniciais
Para habilitar a obtenção dos repositórios via HTTPS pelo apt-get, instale os pacotes listados abaixo. Nós precisamos disso para prosseguir a instalação:

Copiar
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
Adicionando a chave pública do repositório Docker em nossa máquina
Para adicionar a chave GPG* oficial do repositório do Docker, execute o comando a seguir:

⚠️ Este passo é necessário pois precisamos obter uma chave pública dos servidores da empresa Docker Inc para garantir que qualquer pacote obtido através da Internet esteja assinado por esta chave, evitando assim possíveis ataques de segurança.

Copiar
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
Se tudo der certo, você não deve receber nenhum retorno visual.

Adicionando o repositório remoto na lista do apt
Para finalmente adicionar o repositório oficial do Docker no apt, execute o seguinte comando:

Copiar
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
Perceba que adicionamos o repositório stable (em $(lsb_release -cs) stable), isso significa que teremos somente o repositório com as versões estáveis do Docker.

Em distribuições baseadas no Ubuntu (como o Linux Mint), talvez você precise alterar o comando $(lsb_release -cs) para uma versão do Ubuntu que seja compatível com aquele sistema. Por exemplo: caso utilize o Linux Mint Tessa, você deve alterar o valor para bionic.

⚠️Importante: o Docker não garante seu funcionamento em sistemas fora dos requisitos de sistema operacional.

Instalando o Docker no Linux
Agora sim, vamos à instalação do Docker!

Primeiro, vamos garantir que os índices dos pacotes do apt estão atualizados, já que adicionamos um novo repositório:
Copiar
sudo apt-get update
Vamos instalar a última versão estável do Docker Engine - Community, que é uma versão mantida pela própria comunidade, já que o Docker é open source.
Para isso, execute no terminal:

Copiar
sudo apt-get install docker-ce docker-ce-cli containerd.io
Instalando o Docker no MacOs
Para instalação do Docker em um Mac basta seguir os passos de Instalação do Docker no Mac Desktop da documentação do Docker.

Adicionando seu usuário ao grupo de usuários Docker
Para evitar o uso de sudo em todos os comandos Docker que formos executar, vamos dar as devidas permissões ao nosso usuário.

Primeiro crie um grupo chamado docker:
Copiar
sudo groupadd docker
Caso ocorra uma mensagem: groupadd: grupo 'docker' já existe, é só prosseguir.

Então, use o comando abaixo para adicionar seu usuário a este novo grupo:
Copiar
sudo usermod -aG docker $USER
⚠️ Execute o comando exatamente como ele está acima, considerando as letras maiúsculas e minúsculas.

Para ativar as alterações realizadas nos grupos, você pode realizar logout e login de sua sessão ou executar o seguinte comando no terminal:
Copiar
newgrp docker
⚠️ Se após esse comando você tiver algum problema, reinicie sua máquina. Depois de reiniciar siga para os próximos passos

8. Inicie o Daemon do Docker
O Daemon é um serviço que fica no background, ou seja, é um serviço que sempre está em execução e aguarda por comandos feitos por meio do CLI. Entretanto, para que este serviço fique sempre disponível, precisamos ativá-lo, até mesmo após reiniciarmos nosso computador.

Para consultar o status atual do daemon do Docker, execute o seguinte comando:
Copiar
sudo systemctl status docker
O comando anterior deve retornar algo como um pequeno relatório sobre o serviço, onde consta seu status de funcionamento:
Copiar
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: inactive (dead) since Thu 2021-09-23 11:55:11 -03; 2s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
    Process: 2034 ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock (code=exited, status=0>
   Main PID: 2034 (code=exited, status=0/SUCCESS
Caso o parâmetro Active esteja como stop/waiting ou no nosso caso, como inactive, rode o comando start para iniciá-lo:
Copiar
sudo systemctl start docker
Ao consultar o status novamente, o processo deverá estar como start/running/active.

Agora, vamos habilitar o daemon do Docker para iniciar durante o boot:
Copiar
sudo systemctl enable docker
9. Valide a instalação
Para validar se tudo ocorreu como deveria na instalação, vamos executar o tão esperado hello world do Docker:

Copiar
docker run hello-world
O terminal deve retornar uma mensagem com dicas, conforme a seguir:

Mostrando um Olá do Docker no terminal
Mostrando um Olá do Docker no terminal
Quando executamos o comando docker run hello-world, estamos pedindo que ele busque em seu repositório oficial uma imagem chamada hello-world. Essa imagem é um exemplo simples de um container, que no final nos retorna uma mensagem de texto. Falaremos mais sobre isso adiante!

Pronto, temos o Docker instalado para utilizarmos! 🐋