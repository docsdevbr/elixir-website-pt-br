---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/introduction.md
revision: c710b2afb7e06f67c5fe70bb8bbf9fe17cdd9945
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Introdução

Boas vindas!

Este guia ensinará os fundamentos do Elixir: a sintaxe da linguagem, como
definir módulos, as estruturas de dados comuns e muito mais.
Este capítulo se concentrará em garantir que o Elixir esteja instalado e que
você possa executar com sucesso o shell interativo do Elixir, chamado IEx.

Vamos começar.

## Instalação

Se você ainda não instalou o Elixir, visite nossa
[página de instalação](https://elixir-lang.org/install.html).
Após a instalação, você pode executar `elixir --version` para obter a versão
atual do Elixir.
Os requisitos para este guia são:

- Elixir 1.15.0 ou superior.
- Erlang/OTP 26 ou superior.

Se você estiver procurando outros recursos para aprender Elixir, também pode
consultar a [página de aprendizado](https://elixir-lang.org/learning.html) do
site oficial.

## Modo interativo

Ao instalar o Elixir, você terá três novos executáveis de linha de comando:
`iex`, `elixir` e `elixirc`.

Por enquanto, vamos começar executando `iex` (ou `iex.bat` se você estiver
usando o Windows PowerShell, onde `iex` é um comando do PowerShell), que
significa Elixir Interativo.
No modo interativo, podemos digitar qualquer expressão Elixir e obter seu
resultado.
Vamos começar com algumas expressões básicas.

Abra o `iex` e digite as seguintes expressões:

```elixir
Erlang/OTP 26 [64-bit] [smp:2:2] [...]

Interactive Elixir - press Ctrl+C to exit
iex(1)> 40 + 2
42
iex(2)> "olá" <> " mundo"
"olá mundo"
```

Observe que alguns detalhes, como números de versão, podem variar um pouco na
sua sessão; isso não é importante.
Ao executar o código acima, você deverá avaliar as expressões e ver seus
resultados.
Para sair do `iex`, pressione `Ctrl+C` duas vezes.

Parece que podemos começar!
Usaremos bastante o shell interativo nos próximos capítulos para nos
familiarizarmos um pouco mais com as construções da linguagem e os tipos
básicos, a partir do próximo capítulo.

## Executando scripts

Após se familiarizar com os conceitos básicos da linguagem, você pode tentar
escrever programas simples.
Isso pode ser feito colocando o seguinte código Elixir em um arquivo:

```elixir
IO.puts("Olá mundo em Elixir")
```

Salve o arquivo como `simple.exs` e execute-o com `elixir`:

```console
$ elixir simple.exs
Olá mundo em Elixir
```

`iex` e `elixir` são tudo o que precisamos para aprender os principais conceitos
da linguagem.
Existe um guia separado chamado
["Guia Mix e OTP"](../mix-and-otp/introduction-to-mix.md) que explora como
criar, gerenciar e testar projetos Elixir completos.
Por enquanto, vamos aprender os tipos de dados básicos da linguagem.
