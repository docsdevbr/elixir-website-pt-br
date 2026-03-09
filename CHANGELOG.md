---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir/blob/main/CHANGELOG.md
revision: 0df45fdba16e6ab8f59a30e9f0c1e281a24c7ef2
status: ready
---

<!--
  SPDX-License-Identifier: Apache-2.0
  SPDX-FileCopyrightText: 2021 The Elixir Team
  SPDX-FileCopyrightText: 2012 Plataformatec
-->

# Changelog do Elixir v1.20

## Melhorias no sistema de tipos

Esta versão inclui inferência de tipos para todas as construções.

### Inferência de tipos das definições de funções

O Elixir agora realiza inferência de tipos para funções inteiras.
A melhor maneira de demonstrar as novas funcionalidades é com exemplos.
Observe o código a seguir:

```elixir
def add_foo_and_bar(data) do
  data.foo + data.bar
end
```

O Elixir agora infere que a função espera um `map` como primeiro argumento, e
que o mapa deve ter as chaves `.foo` e `.bar` cujos valores sejam `integer()`
ou `float()`.
O tipo de retorno será `integer()` ou `float()`.

Aqui está outro exemplo:

```elixir
def sum_to_string(a, b) do
  Integer.to_string(a + b)
end
```

Embora o operador `+` funcione tanto com inteiros quanto com números de ponto
flutuante, o Elixir infere que `a` e `b` devem ser ambos inteiros, já que o
resultado de `+` é passado para uma função que espera um inteiro.
A informação de tipo inferida é então usada durante a verificação de tipos para
encontrar possíveis erros de tipagem.

### Inferência de tipo de guardas

Esta versão também realiza inferência de guardas!
Vejamos alguns exemplos:

```elixir
def example(x, y) when is_list(x) and is_integer(y)
```

O código acima infere corretamente que `x` é uma lista e `y` é um número
inteiro.

```elixir
def example({:ok, x} = y) when is_binary(x) or is_integer(x)
```

O código acima infere que x é um binário ou um número inteiro, e `y` é uma tupla
de dois elementos com `:ok` como primeiro elemento e um binário ou número
inteiro como segundo.

```elixir
def example(x) when is_map_key(x, :foo)
```

O código acima infere que `x` é um mapa que tem a chave `:foo`, representada
como `%{..., foo: dynamic()}`.
Lembre-se que o `...` inicial indica que o mapa pode ter outras chaves.

```elixir
def example(x) when not is_map_key(x, :foo)
```

E o código acima infere que `x` não possui a chave `:foo` (portanto, `x.foo`
resultará em uma violação de tipagem), que tem o tipo: `%{..., foo: not_set()}`.

Você também pode ter expressões que verificam o tamanho das estruturas de dados:

```elixir
def example(x) when tuple_size(x) < 3
```

O Elixir rastreará corretamente que a tupla tem no máximo dois elementos e,
portanto, acessar `elem(x, 3)` resultará em uma violação de tipo.
Em outras palavras, o Elixir pode analisar guardas complexas, inferir tipos e
usar essas informações para encontrar erros em nosso código, sem a necessidade
de introduzir assinaturas de tipo (ainda).

### Tipagem entre cláusulas

O Elixir agora infere o tipo de uma determinada cláusula com base em cláusulas
anteriores.
Vejamos um exemplo:

```elixir
case System.get_env("SOME_VAR") do
  nil -> :not_found
  value -> {:ok, String.upcase(value)}
end
```

`System.get_env("SOME_VAR")` retorna `nil` ou um `binary()`.
Como a primeira cláusula corresponde a `nil`, o sistema de tipos agora sabe que
`value` não pode mais ser `nil` e, portanto, deve ser apenas um `binary()`, o
que permite que a segunda cláusula também seja verificada quanto ao tipo sem
violações.

Essa inferência de tipo entre cláusulas também ajuda o sistema de tipos a
encontrar cláusulas redundantes e código morto em bases de código existentes.

### Tipagem completa de chaves de mapas

Mapas foram uma das primeiras estruturas de dados que implementamos no sistema
de tipos do Elixir; no entanto, até então, eles suportavam apenas chaves de
átomos.
Se tivessem chaves adicionais, essas chaves eram simplesmente marcadas como
`dynamic()`.

A partir do Elixir v1.20, podemos rastrear todos os domínios possíveis como
chaves de mapa.
Por exemplo, o mapa:

```elixir
%{123 => "hello", 456.0 => :ok}
```

terá o tipo:

```elixir
%{integer() => binary(), float() => :ok}
```

Também é possível combinar chaves de domínio, como acima, com chaves de átomos,
resultando no seguinte:

```elixir
%{integer() => integer(), root: integer()}
```

Este sistema é uma implementação de
[Typing Records, Maps, and Structs, de Giuseppe Castagna (2023)](https://www.irif.fr/~gc/papers/icfp23.pdf).

### Tipagem de operações de mapa

Tipamos a maioria das funções no módulo `Map`, permitindo que o sistema de tipos
rastreie como as chaves são adicionadas, atualizadas e removidas em todos os
tipos de chave possíveis.

Por exemplo, imagine que estamos chamando as seguintes funções de `Map` com uma
variável `map`, cujo formato exato desconhecemos, e uma chave do tipo átomo:

```elixir
Map.put(map, :key, 123)
#=> returns type %{..., key: integer()}

Map.delete(map, :key)
#=> returns type %{..., key: not_set()}
```

Como você pode ver, rastreamos quando as chaves são definidas e também quando
são removidas.

Algumas operações, como `Map.replace/3`, substituem a chave apenas se ela
existir, e isso também é propagado pelo sistema de tipos:

```elixir
Map.replace(map, :key, 123)
#=> returns type %{..., key: if_set(integer())}
```

Em outras palavras, se a chave existisse, ela teria sido substituída por um
valor inteiro.
Além disso, sempre que uma função no módulo `Map` é chamada e a chave fornecida
é comprovadamente inexistente no mapa, um erro é emitido.

Ao combinar inferência de tipo completa com operações de exclamação como
`Map.fetch!/2`, `Map.pop!/2`, `Map.replace!/3` e `Map.update!/3`, o Elixir
consegue propagar informações sobre as chaves desejadas.
Observe este módulo:

```elixir
defmodule User do
  def name(map), do: Map.fetch!(map, :name)
end

defmodule CallsUser do
  def calls_name do
    User.name(%{})
  end
end
```

O código acima apresenta uma violação de tipo, que agora foi detectada pelo
sistema de tipos:

```text
    warning: incompatible types given to User.name/1:

        User.name(%{})

    given types:

        %{name: not_set()}

    but expected one of:

        dynamic(%{..., name: term()})

    type warning found at:
    │
 16 │     User.name(%{})
    │         ~
    │
    └─ lib/calls_user.ex:7:5: CallsUser.calls_name/0
```

### Agradecimentos

O sistema de tipos foi possível graças a uma parceria entre o
[CNRS](https://www.cnrs.fr/) e a [Remote](https://remote.com/).
O trabalho de desenvolvimento é atualmente patrocinado pela
[Fresha](https://www.fresha.com/) e pela [Tidewave](https://tidewave.ai/).

## v1.20.0-rc.2 (04/03/2026)

### 1. Melhorias

#### Elixir

- [Code] Adiciona a opção `module_definition: :interpreted` ao `Code`, que
  permite que as definições de módulos sejam avaliadas em vez de compiladas.
  Em algumas aplicações/arquiteturas, isso pode levar a melhorias drásticas nos
  tempos de compilação.
  Observe que isso não afeta o arquivo `.beam` gerado, que terá o mesmo
  desempenho/comportamento de antes.
- [Code] Torna a remoção de módulos opcional e move a exclusão temporária de
  módulos para segundo plano para acelerar os tempos de compilação.
- [Integer] Adiciona `Integer.popcount/1`.
- [Kernel] Move a validação de structs em padrões e atualizações para o
  verificador de tipos.
  Isso significa que adicionar e remover campos de structs fará com que menos
  arquivos sejam recompilados.
- [Kernel] Adiciona a inferência de tipos entre cláusulas.
  Por exemplo, se uma cláusula diz `x when is_integer(x)`, então a próxima
  cláusula pode não ser mais um inteiro.
- [Kernel] Detecta e alerta sobre cláusulas redundantes.
- [List] Adiciona `List.first!/1` e `List.last!/1`.
- Adiciona a guia Software Bill of Materials à documentação.

#### Mix

- [mix compile] Adiciona a opção `module_definition: :interpreted` ao `Code`,
  permitindo que as definições de módulos sejam avaliadas em vez de compiladas.
  Em algumas aplicações/arquiteturas, isso pode levar a melhorias drásticas nos
  tempos de compilação.
  Observe que isso não afeta o arquivo `.beam` gerado, que terá o mesmo
  desempenho/comportamento de antes.
- [mix deps] Paraleliza as verificações de status de bloqueio de dependências
  durante o `deps.loadpaths`, melhorando os tempos de inicialização em projetos
  com muitas dependências do git.

### 2. Possíveis alterações que quebram a compatibilidade

#### Elixir

- `map.foo()` (acessando um campo de mapa com parênteses) e `mod.foo` (invocando
  uma função sem parênteses) agora lançarão exceções em vez de emitir alertas em
  tempo de execução, alinhando-se ao comportamento do sistema de tipos.

### 3. Correções de falhas

#### IEx

- [IEx] Garante que os alertas emitidos durante a análise do IEx sejam
  exibidos/impressos corretamente.
- [IEx] Garante que pry funcione em nós remotos.

#### Mix

- [mix compile.erlang] Ordena os módulos Erlang por ordem superior antes da
  compilação para a resolução adequada de dependências.

## v1.20.0-rc.1 (13/01/2026)

### 1. Correções de falhas

#### Elixir

- [Kernel] Melhora o desempenho do sistema de tipos ao trabalhar com grandes
  uniões de mapas abertos.
- [Kernel] Não falha em tipos de mapa com chaves de struct ao realizar operações
  de tipo.
- [Kernel] Marca o resultado de tipos bitstring como dinâmico.
- [Kernel] `<<expr::bitstring>>` terá o tipo `binary` em vez de `bitstring` se
  `expr` for um binário.
- [Kernel] Não falha em variáveis condicionais ao chamar uma função em um módulo
  representado por uma variável.

## v1.20.0-rc.0 (09/01/2026)

### 1. Melhorias

#### Elixir

- [Calendar] Otimiza `date_from_iso_days` utilizando o algoritmo de
  Neri-Schneider.
- [Enum] Adiciona o classificador `Enum.min_max`
- [Integer] Adiciona `Integer.ceil_div/2`
- [IO] Adiciona `IO.iodata_empty?/1`
- [File] Ignora dispositivos, pipes nomeados, etc., em `File.cp_r/3` em vez de
  gerar um erro com o motivo `:eio`.
- [Kernel] Imprime resultados intermediários de `dbg` para pipes.
- [Kernel] Avisa sobre requires não usados.
- [Regex] Adiciona `Regex.import/1` para importar expressões regulares definidas
  com `/E`.

#### ExUnit

- [ExUnit.CaptureLog] Adiciona a opção `:formatter` para formatação
  personalizada de logs.

#### Mix

- [mix deps] Suporta a filtragem da saída de `mix deps`.
- [mix compile] Força `:elixirc_paths` a ser uma lista de strings para evitar
  que caminhos sejam descartados (o único tipo documentado eram listas de
  strings).
- [mix test] Adiciona `mix test --dry-run`.

### 2. Possíveis alterações que podem quebrar a compatibilidade

#### Elixir

- `require SomeModule` não se expande mais para o módulo fornecido em tempo de
  compilação, mas ainda retorna o módulo em tempo de execução.
  Observe que, embora o Elixir não garanta que as macros se expandirão para
  determinadas construções, como isso pode quebrar o código que depende do
  comportamento anterior, como `require(SomeMod).some_macro()`, estamos
  adicionando esta nota ao CHANGELOG.

### 3. Descontinuações definitivas

#### Elixir

- [File] `File.stream!(path, modes, lines_or_bytes)` foi descontinuado em favor
  de `File.stream!(path, lines_or_bytes, modes)`.
- [Kernel] A correspondência de tamanho em um padrão de bits agora requer o
  operador de fixação para consistência, como `<<x::size(^existing_var)>>`
- [Kernel.ParallelCompiler] `Kernel.ParallelCompiler.async/1` foi descontinuado
  em favor de `Kernel.ParallelCompiler.pmap/2`, que é mais eficiente e resolve
  limitações conhecidas.

#### Logger

- [Logger] As funções `Logger.*_backend` foram descontinuadas em favor de
  handlers.
  Se você realmente deseja continuar usando backends, consulte o pacote
  `:logger_backends`.
- [Logger] `Logger.enable/1` e `Logger.disable/1` foram descontinuadas em favor
  de `Logger.put_process_level/2` e `Logger.delete_process_level/1`.

## v1.19

O CHANGELOG para as versões v1.19 pode ser encontrado
[na branch v1.19](https://github.com/elixir-lang/elixir/blob/v1.19/CHANGELOG.md).
