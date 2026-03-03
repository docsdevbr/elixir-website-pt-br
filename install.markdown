---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir-lang.github.com/blob/main/install.markdown
revision: 9d1fc67c74b62d910464cf56900b408a4ead6d78
status: ready

title: "Instalando o Elixir"
section: install
layout: default
image: /images/social/elixir-og-card.jpg
---

{% assign stable = site.data.elixir-versions[site.data.elixir-versions.stable] %}

# Instalação

{% include toc.html %}

A maneira mais rápida de instalar o Elixir é por meio de scripts de instalação,
do gerenciador de pacotes do sistema operacional ou usando um dos instaladores
disponíveis.
Se nenhuma dessas opções estiver disponível, recomendamos usar os pacotes
pré-compilados ou compilar a linguagem.
Todas essas opções são detalhadas a seguir.

Observe que o Elixir {{ stable.name }} requer o Erlang {{ stable.minimum_otp }}
ou posterior.
Muitas das instruções abaixo instalarão o Erlang automaticamente para você.
Caso contrário, a seção "Instalando o Erlang" explica como.

Se você não tiver certeza se o Elixir está instalado ou não, execute
`elixir --version` no seu terminal.

## Por sistema operacional

Instale o Elixir de acordo com o seu sistema operacional e a ferramenta de sua
escolha.

### macOS

- Usando [scripts de instalação](#scripts-de-instalação)

- Usando o [Homebrew](https://brew.sh/):
  - Run: `brew install elixir`

- Usando o [MacPorts](https://www.macports.org/):
  - Execute: `sudo port install elixir`

- Usando [gerenciadores de versão](#gerenciadores-de-versões)

### GNU/Linux

Abaixo, listamos os passos para instalar o Elixir em diferentes distribuições.
Se a sua distribuição não estiver listada ou se os passos abaixo não
funcionarem, você pode considerar o uso de
[gerenciadores de versão](#gerenciadores-de-versões).

#### Distribuições

- **Arch Linux** (repositório da comunidade)
  - Run: `pacman -S elixir`

- **Fedora**
  - O repositório Rawhide do Fedora mantém as versões mais recentes:
    `sudo dnf --repo=rawhide install elixir elixir-doc erlang erlang-doc`
  - Você pode usar a distribuição padrão, mas elas estão geralmente
    desatualizadas: `sudo dnf install elixir erlang`
  - A documentação está disponível em pacotes separados:
    `sudo dnf install elixir-doc erlang-doc`

- **Gentoo**
  - Execute: `emerge --ask dev-lang/elixir`

- **GNU Guix**
  - Execute: `guix package -i elixir`

- **Ubuntu**
  - Use [scripts de instalação](#scripts-de-instalação) (ou, alternativamente,
    [gerenciadores de versão](#gerenciadores-de-versões))
  - Os pacotes no `apt` costumam estar várias versões atrasados.
    Você pode usar os [pacotes do RabbitMQ](https://launchpad.net/~rabbitmq)
    descritos abaixo, que provavelmente são mais recentes que o `apt`:

    ```bash
    $ sudo add-apt-repository ppa:rabbitmq/rabbitmq-erlang
    $ sudo apt update
    $ sudo apt install git elixir erlang
    ```

#### Gerenciadores de versões

Existem muitas ferramentas que permitem às pessoas desenvolvedoras instalar e
gerenciar múltiplas versões de Erlang e Elixir.
Elas são úteis se você tiver vários projetos rodando em diferentes versões de
Elixir ou Erlang, se não puder instalar Erlang ou Elixir como mencionado acima
ou se a versão fornecida pelo seu gerenciador de pacotes estiver desatualizada.
Aqui estão algumas dessas ferramentas:

- [asdf](https://github.com/asdf-vm/asdf) - instala e gerencia diferentes
  versões do [Elixir](https://github.com/asdf-vm/asdf-elixir) e do
  [Erlang](https://github.com/asdf-vm/asdf-erlang).
- [kerl](https://github.com/kerl/kerl) - instala e gerencia diferentes versões
  do Erlang.

Lembre-se de que cada versão do Elixir suporta versões específicas do
Erlang/OTP.
[Consulte as versões suportadas em nossa documentação](/docs).

### BSD

- **FreeBSD**
  - A versão mais recente do Elixir é chamada
    [lang/elixir-devel](https://freshports.org/lang/elixir-devel).
    O Elixir padrão, [lang/elixir](https://freshports.org/lang/elixir), pode
    apresentar um pequeno atraso, pois os ports dependentes geralmente não
    conseguem ser atualizados para a versão mais recente do Elixir
    imediatamente.
  - Usando ports:
    - Execute: `cd /usr/ports/lang/elixir && make install clean`
  - Usando pkg:
    - Execute: `pkg install elixir` ou `pkg install elixir-devel`

- **OpenBSD**
  - Execute: `pkg_add elixir`

### Windows

- Usando instaladores do Windows:
  - [Baixe e execute o instalador do Erlang](https://www.erlang.org/downloads.html)
  - Baixe e execute o instalador do Elixir compatível com sua versão do
    Erlang/OTP:

    {% for otp_version in stable.otp_versions %}
    - [Elixir {{ stable.version }} no Erlang {{ otp_version }}](https://github.com/elixir-lang/elixir/releases/download/v{{ stable.version }}/elixir-otp-{{ otp_version }}.exe)
    {% endfor %}

    Execute `erl` no terminal se não tiver certeza da sua versão do Erlang/OTP.<br/>
    Versões anteriores do Elixir estão disponíveis em nossa página de
    [Releases](https://github.com/elixir-lang/elixir/releases).

- Usando [scripts de instalação](#scripts-de-instalação)

- Usando o [Scoop](https://scoop.sh/):
  - Instale o Erlang: `scoop install erlang`
  - Instale o Elixir: `scoop install elixir`

- Usando o [Chocolatey](https://community.chocolatey.org/):
  - Instale o Elixir (instala o Erlang como dependência): `choco install elixir`

### Raspberry Pi e dispositivos embarcados

Para compilar e empacotar uma aplicação Elixir, com todo o sistema operacional,
e gravá-lo em um disco ou implantá-lo em outro local,
[consulte o projeto Nerves](https://www.nerves-project.org).

Se você deseja instalar o Elixir como parte de um sistema operacional existente,
siga as etapas relevantes acima para o seu sistema operacional ou instale a
partir do pacote pré-compilado ou do código-fonte.

### Docker

Se você já conhece o Docker, pode usar a imagem oficial do Docker para começar
rapidamente com o Elixir.

- Entre no modo interativo.
  - Execute: `docker run -it --rm elixir`
- Entre no bash dentro do contêiner com o `elixir` instalado.
  - Execute: `docker run -it --rm elixir bash`

O comando acima apontará automaticamente para as versões mais recentes do Erlang
e do Elixir disponíveis.
Para uso em produção, recomendamos o uso das imagens Docker do
[Hex.pm](https://hub.docker.com/r/hexpm/elixir), que são imutáveis e apontam
para uma versão específica do Erlang e do Elixir.

## Scripts de instalação

O Elixir e o Erlang/OTP podem ser instalados rapidamente no macOS, Windows ou
Ubuntu usando um script `install.sh`/`install.bat`:

Se você estiver usando o bash (macOS/Ubuntu/Windows), execute:

```sh
curl -fsSO {{ site.url }}/install.sh
sh install.sh elixir@{{ stable.version }} otp@{{ stable.recommended_otp }}
installs_dir=$HOME/.elixir-install/installs
export PATH=$installs_dir/otp/{{ stable.recommended_otp }}/bin:$PATH
export PATH=$installs_dir/elixir/{{ stable.version }}-otp-{{ stable.otp_versions[0] }}/bin:$PATH
iex
```

Se você estiver usando o PowerShell (Windows), execute:

```pwsh
curl.exe -fsSO {{ site.url }}/install.bat
.\install.bat elixir@{{ stable.version }} otp@{{ stable.recommended_otp }}
$installs_dir = "$env:USERPROFILE\.elixir-install\installs"
$env:PATH = "$installs_dir\otp\{{ stable.recommended_otp }}\bin;$env:PATH"
$env:PATH = "$installs_dir\elixir\{{ stable.version }}-otp-{{ stable.otp_versions[0] }}\bin;$env:PATH"
iex.bat
```

Você pode querer
[definir a variável de ambiente $PATH](#configurando-a-variável-de-ambiente-path)
para todo o seu sistema.
Use `install.sh --help` ou `install.bat --help` para saber mais sobre os
argumentos e opções disponíveis.

## Pacote pré-compilado

O Elixir fornece um pacote pré-compilado para cada versão.
Primeiro, [instale o Erlang](#instalando-o-erlang) e, em seguida,
baixe o pacote pré-compilado do Elixir apropriado abaixo.
Você pode consultar sua versão do Erlang/OTP executando `erl -s halt`:

{% for otp_version in stable.otp_versions %}
- [Elixir {{ stable.version }} no Erlang/OTP {{ otp_version }}](https://github.com/elixir-lang/elixir/releases/download/v{{ stable.version }}/elixir-otp-{{ otp_version }}.zip)
{% endfor %}

Após baixar a versão, descompacte-a e você poderá executar os comandos `elixir`
e `iex` a partir do diretório `bin`.
No entanto, recomendamos que você
[adicione o caminho do diretório `bin` do Elixir à sua variável de ambiente PATH](#configurando-a-variável-de-ambiente-path)
para facilitar o desenvolvimento.

### Espelhos e versões noturnas

Os links acima direcionam diretamente para a versão do GitHub.
Também hospedamos e espelhamos pacotes pré-compilados e versões noturnas
globalmente via `builds.hex.pm` usando o seguinte esquema de URL:

    https://builds.hex.pm/builds/elixir/${versão-do-elixir}-otp-${versão-do-otp}.zip

Por exemplo, para usar o Elixir v1.13.3 com Erlang/OTP 24.x, use:

    https://builds.hex.pm/builds/elixir/v1.13.3-otp-24.zip

Para usar a versão noturna de uma determinada versão do Erlang/OTP (como a 25),
use:

    https://builds.hex.pm/builds/elixir/main-otp-25.zip

Para obter uma lista de todas as compilações, use:

    https://builds.hex.pm/builds/elixir/builds.txt

## Compilando a partir do código-fonte

Você pode baixar e compilar o Elixir em alguns passos.
O primeiro é [instalar o Erlang](#instalando-o-erlang).
Você também precisará ter o [make](https://www.gnu.org/software/make/)
instalado.

Em seguida, você deve baixar o código-fonte
([.zip](https://github.com/elixir-lang/elixir/archive/v{{ stable.version }}.zip),
[.tar.gz](https://github.com/elixir-lang/elixir/archive/v{{ stable.version }}.tar.gz))
da [versão mais recente](https://github.com/elixir-lang/elixir/releases/tag/v{{ stable.version }}),
descompactá-lo e executar `make` dentro do diretório descompactado (observação:
se você estiver usando o Windows,
[leia esta página sobre como configurar seu ambiente para compilar o Elixir](https://github.com/elixir-lang/elixir/wiki/Windows)).

Após a compilação, você estará pronto para executar os comandos `elixir` e `iex`
a partir do diretório `bin`.
É recomendável
[adicionar o caminho do diretório bin do Elixir à sua variável de ambiente PATH](#configurando-a-variável-de-ambiente-path)
para facilitar o desenvolvimento.

Caso você queira se aventurar, também é possível compilar a partir do branch
main:

```bash
$ git clone https://github.com/elixir-lang/elixir.git
$ cd elixir
$ make clean compile
```

## Instalando o Erlang

O único pré-requisito para o Elixir é o Erlang, versão {{ stable.minimum_otp }}
ou posterior.
Ao instalar o Elixir, o Erlang geralmente é instalado automaticamente.
No entanto, se você quiser instalar o Erlang manualmente, pode consultar:

- [Distribuição do código-fonte e instaladores para Windows no site oficial do Erlang](http://www.erlang.org/downloads.html)
- [Pacotes pré-compilados para algumas instalações do tipo Unix](https://www.erlang-solutions.com/resources/download.html)
- [Uma lista geral de métodos de instalação da documentação do Riak](https://docs.riak.com/riak/kv/latest/setup/installing/source/erlang/)

Após a instalação do Erlang, você poderá abrir a linha de comando (ou prompt de
comando) e verificar a versão do Erlang digitando `erl -s erlang halt`.
Você verá informações semelhantes a:

    Erlang/OTP {{ stable.minimum_otp }} [64-bit] [smp:2:2] [...]

Observe que, dependendo de como você instalou o Erlang, os binários do Erlang
podem não estar disponíveis no seu PATH.
Certifique-se de ter os binários do Erlang no seu
[PATH](https://en.wikipedia.org/wiki/Environment_variable), caso contrário, o
Elixir não funcionará!

## Configurando a variável de ambiente PATH

É altamente recomendável adicionar o caminho do diretório bin do Elixir à sua
variável de ambiente PATH para facilitar o desenvolvimento.

No Windows, existem
[instruções para diferentes versões](http://www.computerhope.com/issues/ch000549.htm)
explicando o processo.

Em sistemas Unix, você precisa
[encontrar seu arquivo de perfil do shell](https://unix.stackexchange.com/a/117470/101951)
e, em seguida, adicionar ao final desse arquivo a seguinte linha, refletindo o
caminho para sua instalação do Elixir:

```bash
export PATH="$PATH:/caminho/para/elixir/bin"
```

## Fazendo perguntas

Depois que o Elixir estiver instalado e funcionando, é comum ter dúvidas à
medida que você aprende e usa a linguagem.
Existem muitos lugares onde você pode fazer perguntas, aqui estão alguns deles:

- [#elixir no irc.libera.chat](irc://irc.libera.chat/elixir)
- [Fórum do Elixir](http://elixirforum.com)
- [Elixir no Slack](https://elixir-slack.community)
- [Elixir no Discord](https://discord.gg/elixir)
- [tag elixir no StackOverflow](https://stackoverflow.com/questions/tagged/elixir)

Ao fazer perguntas, lembre-se destas duas dicas:

- Em vez de perguntar "como fazer X em Elixir", pergunte "como resolver Y em
  Elixir".
  Em outras palavras, não pergunte como implementar uma solução específica, mas
  sim descreva o problema em questão.
  Apresentar o problema fornece mais contexto e reduz o viés de uma resposta
  correta.

- Caso algo não esteja funcionando como esperado, inclua o máximo de informações
  possível em seu relatório, por exemplo: sua versão do Elixir, o trecho de
  código e a mensagem de erro juntamente com o stacktrace do erro.

Aproveite!
