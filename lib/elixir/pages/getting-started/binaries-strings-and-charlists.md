---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/lib/elixir/pages/getting-started/binaries-strings-and-charlists.md
revision: 980500551c67dd08303765764314d6321a7fcf42
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
-->

# Binários, strings e charlists

Em ["Tipos básicos"](basic-types.md), aprendemos um pouco sobre strings e usamos
a função `is_binary/1` para verificações:

```elixir
iex> string = "hello"
"hello"
iex> is_binary(string)
true
```

Neste capítulo, vamos esclarecer o que exatamente são binários e como eles se
relacionam com strings.
Também aprenderemos sobre charlists, `~c"como esta"`, que são frequentemente
usadas para interoperabilidade com Erlang.

Embora strings sejam um dos tipos de dados mais comuns em linguagens de
programação, elas são sutilmente complexas e frequentemente mal compreendidas.
Para entender strings em Elixir, vamos primeiro discutir o
[Unicode](https://en.wikipedia.org/wiki/Unicode) e as codificações de
caracteres, especificamente a codificação
[UTF-8](https://en.wikipedia.org/wiki/UTF-8).

## Unicode e pontos de código

Para facilitar a comunicação significativa entre computadores em várias
linguagens, é necessário um padrão para que os uns e zeros em uma máquina
signifiquem o mesmo quando transmitidos para outra.
O [Padrão Unicode](https://unicode.org/standard/standard.html) funciona como um
registro oficial de praticamente todos os caracteres que conhecemos: isso inclui
caracteres de textos clássicos e históricos, emojis, além de caracteres de
formatação e controle.

O Unicode organiza todos os caracteres do seu repertório em tabelas de código, e
cada caractere recebe um índice numérico único.
Esse índice numérico é conhecido como
[ponto de código](https://en.wikipedia.org/wiki/Code_point).

No Elixir, você pode usar um `?` antes de um caractere literal para revelar seu
ponto de código:

```elixir
iex> ?a
97
iex> ?ł
322
```

Note que a maioria das tabelas de códigos Unicode se refere a um ponto de código
por sua representação hexadecimal (hex), por exemplo, `97` corresponde a `0061`
em hexadecimal, e podemos representar qualquer caractere Unicode em uma string
Elixir usando a notação `\uXXXX` e a representação hexadecimal do número do seu
ponto de código:

```elixir
iex> "\u0061" == "a"
true
iex> 0x0061 = 97 = ?a
97
```

A representação hexadecimal também ajudará você a pesquisar informações sobre um
ponto de código.
Por exemplo, [https://codepoints.net/U+0061](https://codepoints.net/U+0061)
possui uma folha de dados completa sobre a letra minúscula `a`, também conhecida
como ponto de código 97.

## UTF-8 e codificações

Agora que entendemos o que é o padrão Unicode e o que são pontos de código,
podemos finalmente falar sobre codificações.
Enquanto o ponto de código é **o que** armazenamos, uma codificação lida com
**como** o armazenamos: a codificação é uma implementação.
Em outras palavras, precisamos de um mecanismo para converter os números dos
pontos de código em bytes para poderem ser armazenados na memória, gravados em
disco, etc.

Elixir usa UTF-8 para codificar suas strings, o que significa que os pontos de
código são codificados como uma série de bytes de 8 bits.
UTF-8 é uma codificação de caracteres de **largura variável** que usa de um a
quatro bytes para armazenar cada ponto de código.
É capaz de codificar todos os pontos de código Unicode válidos.
Vejamos um exemplo:

```elixir
iex> string = "héllo"
"héllo"
iex> String.length(string)
5
iex> byte_size(string)
6
```

Embora a string acima tenha 5 caracteres, ela usa 6 bytes, pois dois bytes são
usados para representar o caractere `é`.

> Nota: se você estiver usando o Windows, é possível que seu terminal não use
> UTF-8 por padrão.
> Você pode alterar a codificação da sua sessão atual executando `chcp 65001`
> antes de entrar no `iex` (`iex.bat`).

Além de definir caracteres, o UTF-8 também fornece o conceito de grafemas.
Grafemas podem consistir em múltiplos caracteres frequentemente percebidos como
um só.
Por exemplo, o [emoji da bombeira](https://emojipedia.org/woman-firefighter/) é
representado como a combinação de três caracteres: o emoji da mulher (👩), um
conector oculto de largura zero e o emoji do caminhão de bombeiros (🚒).

```elixir
iex> String.codepoints("👩‍🚒")
["👩", "‍", "🚒"]
iex> String.graphemes("👩‍🚒")
["👩‍🚒"]
```

No entanto, o Elixir é inteligente o suficiente para saber que eles são vistos
como um único caractere e, portanto, o comprimento continua sendo um:

```elixir
iex> String.length("👩‍🚒")
1
```

> Nota: se você não consegue ver os emojis acima no seu terminal, verifique se o
> seu terminal suporta emojis e se você está usando uma fonte que possa
> renderizá-los.

Embora essas regras possam parecer complicadas, documentos codificados em UTF-8
são onipresentes.
Esta página em si está codificada em UTF-8.
As informações de codificação são fornecidas ao seu navegador, que então sabe
como renderizar todos os bytes, caracteres e grafemas de acordo.

Se você quiser ver os bytes exatos que uma string ocuparia em um arquivo, um
truque comum é concatenar o byte nulo `<<0>>` a ela:

```elixir
iex> "hełło" <> <<0>>
<<104, 101, 197, 130, 197, 130, 111, 0>>
```

Alternativamente, você pode visualizar a representação binária de uma string
usando `IO.inspect/2`:

```elixir
iex> IO.inspect("hełło", binaries: :as_binaries)
<<104, 101, 197, 130, 197, 130, 111>>
```

Estamos nos adiantando um pouco.
Vamos falar sobre bitstrings para entender exatamente o que o construtor `<<>>`
significa.

## Bitstrings

Embora já tenhamos abordado pontos de código e codificação UTF-8, ainda
precisamos nos aprofundar um pouco mais em como armazenamos os bytes
codificados, e é aqui que introduzimos a **bitstring**.
Uma bitstring é um tipo de dado fundamental em Elixir, denotado pela sintaxe
[`<<>>`](`<<>>/1`).
**Uma bitstring é uma sequência contígua de bits na memória.**

Por padrão, 8 bits (ou seja, 1 byte) são usados para armazenar cada número em
uma bitstring, mas você pode especificar manualmente o número de bits por meio
de um modificador `::n` para denotar o tamanho em `n` bits, ou pode usar a
declaração mais detalhada `::size(n)`.

```elixir
iex> <<42>> == <<42::8>>
true
iex> <<3::4>>
<<3::size(4)>>
```

Por exemplo, o número decimal `3`, quando representado com 4 bits na base 2,
seria `0011`, o que é equivalente aos valores `0`, `0`, `1`, `1`, cada um
armazenado usando 1 bit:

```elixir
iex> <<0::1, 0::1, 1::1, 1::1>> == <<3::4>>
true
```

Qualquer valor que exceda a capacidade de armazenamento de acordo com o número
de bits disponíveis será truncado:

```elixir
iex> <<1>> == <<257>>
true
```

Aqui, 257 na base 2 seria representado como `100000001`, mas como reservamos
apenas 8 bits para sua representação (por padrão), o bit mais à esquerda é
ignorado e o valor é truncado para `00000001`, ou simplesmente `1` em decimal.

Uma referência completa para o construtor de bitstring pode ser encontrada na
documentação de [`<<>>`](`<<>>/1`).

## Binários

**Um binário é uma bitstring onde o número de bits é divisível por 8.**
Isso significa que todo binário é uma bitstring, mas nem toda bitstring é um
binário.
Podemos usar as funções `is_bitstring/1` e `is_binary/1` para demonstrar isso.

```elixir
iex> is_bitstring(<<3::4>>)
true
iex> is_binary(<<3::4>>)
false
iex> is_bitstring(<<0, 255, 42>>)
true
iex> is_binary(<<0, 255, 42>>)
true
iex> is_binary(<<42::16>>)
true
```

Podemos realizar correspondência de padrões em binários/bitstrings:

```elixir
iex> <<0, 1, x>> = <<0, 1, 2>>
<<0, 1, 2>>
iex> x
2
iex> <<0, 1, x>> = <<0, 1, 2, 3>>
** (MatchError) no match of right hand side value: <<0, 1, 2, 3>>
```

Observe que, a menos que você use explicitamente os modificadores `::`,
espera-se que cada entrada no padrão binário corresponda a um único byte
(exatamente 8 bits).
Se quisermos encontrar uma correspondência em um binário de tamanho
desconhecido, podemos usar o modificador `binary` no final do padrão:

```elixir
iex> <<0, 1, x::binary>> = <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> x
<<2, 3>>
```

Existem alguns outros modificadores que podem ser úteis ao realizar
correspondências de padrões em binários.
O modificador `binary-size(n)` corresponderá a `n` bytes em um binário:

```elixir
iex> <<head::binary-size(2), rest::binary>> = <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> head
<<0, 1>>
iex> rest
<<2, 3>>
```

**Uma string é um binário codificado em UTF-8**, onde o ponto de código para
cada caractere é codificado usando de 1 a 4 bytes.
Portanto, toda string é um binário, mas devido às regras de codificação do
padrão UTF-8, nem todo binário é uma string válida.

```elixir
iex> is_binary("hello")
true
iex> is_binary(<<239, 191, 19>>)
true
iex> String.valid?(<<239, 191, 19>>)
false
```

O operador de concatenação de strings [`<>`](`<>/2`) é, na verdade, um operador
de concatenação binária:

```elixir
iex> "a" <> "ha"
"aha"
iex> <<0, 1>> <> <<2, 3>>
<<0, 1, 2, 3>>
```

Dado que as strings são binárias, também podemos realizar correspondência de
padrões em strings:

```elixir
iex> <<head, rest::binary>> = "banana"
"banana"
iex> head == ?b
true
iex> rest
"anana"
```

No entanto, lembre-se de que a correspondência de padrões binários funciona com
*bytes*, portanto, a correspondência com uma string como "über" com caracteres
multibyte não corresponderá ao *caractere* em si, mas sim ao *primeiro byte
desse caractere*:

```elixir
iex> "ü" <> <<0>>
<<195, 188, 0>>
iex> <<x, rest::binary>> = "über"
"über"
iex> x == ?ü
false
iex> rest
<<188, 98, 101, 114>>
```

Acima, `x` correspondeu apenas ao primeiro byte do caractere multibyte `ü`.

Portanto, ao realizar correspondência de padrões em strings, é importante usar o
modificador `utf8`.

```elixir
iex> <<x::utf8, rest::binary>> = "über"
"über"
iex> x == ?ü
true
iex> rest
"ber"
```

## Charlists

Nossa exploração de bitstrings, binários e strings está quase completa, mas
ainda temos mais um tipo de dado para explicar: a charlist.

**Uma charlist é uma lista de inteiros onde todos os inteiros são pontos de
código válidos.**
Na prática, você não encontrará charlists com frequência, apenas em cenários
específicos, como a interação com bibliotecas Erlang mais antigas que não
aceitam binários como argumentos.

```elixir
iex> ~c"hello"
~c"hello"
iex> [?h, ?e, ?l, ?l, ?o]
~c"hello"
```

O símbolo [`~c`](`Kernel.sigil_c/2`) (abordaremos símbolos mais adiante no
capítulo ["Símbolos"](sigils.md)) indica que estamos lidando com uma lista de
caracteres e não com uma string comum.

Em vez de conter bytes, uma charlist contém pontos de código inteiros.
No entanto, a lista só é impressa como um símbolo se todos os pontos de código
estiverem dentro do intervalo ASCII:

```elixir
iex> ~c"hełło"
[104, 101, 322, 322, 111]
iex> is_list(~c"hełło")
true
```

Isso é feito para facilitar a interoperabilidade com Erlang, embora possa levar
a alguns comportamentos inesperados.
Por exemplo, se você estiver armazenando uma lista de números inteiros que
variam de 0 a 127, por padrão o IEx interpretará isso como uma lista de
caracteres e exibirá os caracteres ASCII correspondentes.

```elixir
iex> heartbeats_per_minute = [99, 97, 116]
~c"cat"
```

Você sempre pode forçar a impressão de charlists em sua representação de lista
chamando a função `inspect/2`:

```elixir
iex> inspect(heartbeats_per_minute, charlists: :as_list)
"[99, 97, 116]"
```

Além disso, você pode converter uma charlist em uma string e vice-versa usando
as funções `to_string/1` e `to_charlist/1`:

```elixir
iex> to_charlist("hełło")
[104, 101, 322, 322, 111]
iex> to_string(~c"hełło")
"hełło"
iex> to_string(:hello)
"hello"
iex> to_string(1)
"1"
```

As funções acima são polimórficas, ou seja, aceitam vários formatos: elas não
apenas convertem charlists em strings (e vice-versa), como também podem
converter inteiros, átomos e assim por diante.

A concatenação (binária) de strings usa o operador [`<>`](`<>/2`), mas
charlists, por serem listas, usam o operador de concatenação de listas
[`++`](`++/2`).

```elixir
iex> ~c"this " <> ~c"fails"
** (ArgumentError) expected binary argument in <> operator but got: ~c"this "
    (elixir) lib/kernel.ex:1821: Kernel.wrap_concatenation/3
    (elixir) lib/kernel.ex:1808: Kernel.extract_concatenations/2
    (elixir) expanding macro: Kernel.<>/2
    iex:1: (file)
iex> ~c"this " ++ ~c"works"
~c"this works"
iex> "he" ++ "llo"
** (ArgumentError) argument error
    :erlang.++("he", "llo")
iex> "he" <> "llo"
"hello"
```

Com os tipos binários, strings e charlists já explicados, é hora de falar sobre
estruturas de dados de chave-valor.
