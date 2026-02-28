---
# Copyright (c) 2012–2026 The Elixir Team.
# Elixir and the Elixir logo are registered trademarks of The Elixir Team.
#
# Documentation licensed under the Apache License, Version 2.0.
# The original work was translated from English into Brazilian Portuguese.
# https://github.com/elixir-lang/elixir/blob/-/LICENSE

source_url: https://github.com/elixir-lang/elixir-lang.github.com/blob/main/docs.markdown
revision: f31ccdaca3a26e39d4bcb4189871c695da4aba95
status: ready

title: Documentação do Elixir
section: docs
layout: default
image: /images/social/elixir-og-card.jpg
---

# Documentação

A linguagem de programação Elixir é dividida em 6 aplicações.
Os links abaixo se referem à documentação dos módulos e funções em cada uma
dessas aplicações.
Veja também
[nosso guia de Introdução](https://hexdocs.pm/elixir/introduction.html) e
[a página de Aprendizagem](/learning.html) para livros, cursos, vídeos e muito
mais.

{% assign stable = site.data.elixir-versions[site.data.elixir-versions.stable] %}

{% for version in site.data.elixir-versions %}
  {% if version[0] == 'stable' %}
    {% continue %}
  {% endif %}

<h3 id="{{ version[1].name }}">
  {{ version[1].name }}
  {% if version[1].version == stable.version %}<small>(estável)</small>{% endif %}
  <small>(<a href="https://github.com/elixir-lang/elixir/releases/download/v{{ version[1].version }}/Docs.zip">baixar</a>)</small>
</h3>

{% if version[1].otp_versions %}
Versões Erlang/OTP suportadas: {% for otp in version[1].otp_versions reversed %}{{ otp }}{% if forloop.last %}{% else %}, {% endif %}{% endfor %}.
{% endif %}

- [Elixir](https://hexdocs.pm/elixir/{{ version[1].version }}/) - biblioteca
  padrão
- [EEx](https://hexdocs.pm/eex/{{ version[1].version }}/) - biblioteca de
  templates
- [ExUnit](https://hexdocs.pm/ex_unit/{{ version[1].version }}/) - biblioteca de
  testes unitários
- [IEx](https://hexdocs.pm/iex/{{ version[1].version }}/) - shell interativo
- [Logger](https://hexdocs.pm/logger/{{ version[1].version }}/) - Logger
  integrado
- [Mix](https://hexdocs.pm/mix/{{ version[1].version }}/) - ferramenta de
  compilação

<div style="margin-top: 40px"></div>
{% endfor %}

#### Desenvolvimento

- [Elixir](https://hexdocs.pm/elixir/main/) - biblioteca padrão
- [EEx](https://hexdocs.pm/eex/main/) - biblioteca de templates
- [ExUnit](https://hexdocs.pm/ex_unit/main/) - biblioteca de testes unitários
- [IEx](https://hexdocs.pm/iex/main/) - shell interativo
- [Logger](https://hexdocs.pm/logger/main/) - Logger integrado
- [Mix](https://hexdocs.pm/mix/main/) - ferramenta de compilação
