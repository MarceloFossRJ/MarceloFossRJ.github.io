---
layout: post
title:  "Rails in brazilian portuguese"
date:   2018-07-13 19:45:31 -0300
categories: [article]
tags: [architecture, rails, ror]
author: Marcelo Foss
---
https://pt.stackoverflow.com/questions/19512/como-programar-em-português-no-ruby-on-rails

## Mudando a interface para português

Crie o arquivo ```config/locales/pt-BR.yml``` e cole lá o seguinte conteúdo:

```ruby
pt-BR:
  # formatos de data e hora
  date:
    formats:
      default: "%d/%m/%Y"
      short: "%d de %B"
      long: "%d de %B de %Y"

    day_names: [Domingo, Segunda, Terça, Quarta, Quinta, Sexta, Sábado]
    abbr_day_names: [Dom, Seg, Ter, Qua, Qui, Sex, Sáb]
    month_names: [~, Janeiro, Fevereiro, Março, Abril, Maio, Junho, Julho, Agosto, Setembro, Outubro, Novembro, Dezembro]
    abbr_month_names: [~, Jan, Fev, Mar, Abr, Mai, Jun, Jul, Ago, Set, Out, Nov, Dez]
    order: [day, month, year]

  time:
    formats:
      default: "%A, %d de %B de %Y, %H:%M h"
      short: "%d/%m, %H:%M h"
      long: "%A, %d de %B de %Y, %H:%M h"
    am: ''
    pm: ''

  # distancia do tempo em palavras
  datetime:
    distance_in_words:
      half_a_minute: 'meio minuto'
      less_than_x_seconds:
        one: 'menos de 1 segundo'
        other: 'menos de %{count} segundos'

      x_seconds:
        one: '1 segundo'
        other: '%{count} segundos'

      less_than_x_minutes:
        one: 'menos de um minuto'
        other: 'menos de %{count} minutos'

      x_minutes:
        one: '1 minuto'
        other: '%{count} minutos'

      about_x_hours:
        one: 'aproximadamente 1 hora'
        other: 'aproximadamente %{count} horas'

      x_days:
        one: '1 dia'
        other: '%{count} dias'

      about_x_months:
        one: 'aproximadamente 1 mês'
        other: 'aproximadamente %{count} meses'

      x_months:
        one: '1 mês'
        other: '%{count} meses'

      about_x_years:
        one: 'aproximadamente 1 ano'
        other: 'aproximadamente %{count} anos'

      over_x_years:
        one: 'mais de 1 ano'
        other: 'mais de %{count} anos'

      almost_x_years:
        one: 'quase 1 ano'
        other: 'quase %{count} anos'

    prompts:
      year:   "Ano"
      month:  "Mês"
      day:    "Dia"
      hour:   "Hora"
      minute: "Minuto"
      second: "Segundos"

  # numeros
  number:
    format:
      precision: 3
      separator: ','
      delimiter: '.'
    currency:
      format:
        unit: 'R$'
        precision: 2
        format: '%u %n'
        separator: ','
        delimiter: '.'
    percentage:
      format:
        delimiter: '.'
    precision:
      format:
        delimiter: '.'
    human:
      format:
        precision: 2
        delimiter: '.'
        significant: true
        strip_unsignificant_zeros: true
      # number_to_human_size()
      storage_units:
        format: "%n %u"
        units:
          byte:
            one: "Byte"
            other: "Bytes"
          kb: "KB"
          mb: "MB"
          gb: "GB"
          tb: "TB"
      # number_to_human()
      # new in rails 3: please add to other locales
      decimal_units:
        format: "%n %u"
        units:
          unit: ""     
          thousand: "mil"
          million:
            one: milhão
            other: milhões
          billion:
            one: bilhão
            other: bilhões
          trillion:
            one: trilhão
            other: trilhões
          quadrillion:
            one: quatrilhão
            other: quatrilhões

  # Usado no Array.to_sentence
  support:
    array:
      words_connector: ", "
      two_words_connector: " e "
      last_word_connector: " e "

  # ActiveRecord
  activerecord:
    errors:
      template:
        header:
          one: "Não foi possível gravar %{model}: 1 erro"
          other: "Não foi possível gravar %{model}: %{count} erros."
        body: "Por favor, verifique o(s) seguinte(s) campo(s):"
      messages:
        inclusion: "não está incluído na lista"
        exclusion: "não está disponível"
        invalid: "não é válido"
        confirmation: "não está de acordo com a confirmação"
        accepted: "deve ser aceito"
        empty: "não pode ficar vazio"
        blank: "não pode ficar em branco"
        too_long: "é muito longo (máximo: %{count} caracteres)"
        too_short: "é muito curto (mínimo: %{count} caracteres)"
        wrong_length: "não possui o tamanho esperado (%{count} caracteres)"
        taken: "já está em uso"
        not_a_number: "não é um número"
        not_an_integer: "não é um número inteiro"
        greater_than: "deve ser maior do que %{count}"
        greater_than_or_equal_to: "deve ser maior ou igual a %{count}"
        equal_to: "deve ser igual a %{count}"
        less_than: "deve ser menor do que %{count}"
        less_than_or_equal_to: "deve ser menor ou igual a %{count}"
        odd: "deve ser ímpar"
        even: "deve ser par"
        record_invalid: "A validação falhou: %{errors}"
```

Depois vá em config/application.rb e adicione o conteúdo abaixo. Isso torna o português do Brasil a linguagem padrão da aplicação.

config.i18n.default_locale = :"pt-BR"
I18n.enforce_available_locales = false


## Alterando pluralização para português

O arquivo config/initializers/inflections.rb é o responsável em dizer para o Rails como ele deve pluralizar/singularizar os nomes. Altere-o para o seguinte conteúdo:

``` ruby
# encoding: utf-8
# Be sure to restart your server when you modify this file.

# Add new inflection rules using the following format
# (all these examples are active by default):
# ActiveSupport::Inflector.inflections do |inflect|
#   inflect.plural /^(ox)$/i, '\1en'
#   inflect.singular /^(ox)en/i, '\1'
#   inflect.irregular 'person', 'people'
#   inflect.uncountable %w( fish sheep )
# end
ActiveSupport::Inflector.inflections do |inflect|
  inflect.clear

  inflect.plural(/$/,  's')
  inflect.plural(/(s)$/i,  '\1')
  inflect.plural(/^(paí)s$/i, '\1ses')
  inflect.plural(/(z|r)$/i, '\1es')
  inflect.plural(/al$/i,  'ais')
  inflect.plural(/el$/i,  'eis')
  inflect.plural(/ol$/i,  'ois')
  inflect.plural(/ul$/i,  'uis')
  inflect.plural(/([^aeou])il$/i,  '\1is')
  inflect.plural(/m$/i,   'ns')
  inflect.plural(/^(japon|escoc|ingl|dinamarqu|fregu|portugu)ês$/i,  '\1eses')
  inflect.plural(/^(|g)ás$/i,  '\1ases')
  inflect.plural(/ão$/i,  'ões')
  inflect.plural(/^(irm|m)ão$/i,  '\1ãos')
  inflect.plural(/^(alem|c|p)ão$/i,  '\1ães')

  # Sem acentos...
  inflect.plural(/ao$/i,  'oes')
  inflect.plural(/^(irm|m)ao$/i,  '\1aos')
  inflect.plural(/^(alem|c|p)ao$/i,  '\1aes')

  inflect.singular(/([^ê])s$/i, '\1')
  inflect.singular(/^(á|gá|paí)s$/i, '\1s')
  inflect.singular(/(r|z)es$/i, '\1')
  inflect.singular(/([^p])ais$/i, '\1al')
  inflect.singular(/eis$/i, 'el')
  inflect.singular(/ois$/i, 'ol')
  inflect.singular(/uis$/i, 'ul')
  inflect.singular(/(r|t|f|v)is$/i, '\1il')
  inflect.singular(/ns$/i, 'm')
  inflect.singular(/sses$/i, 'sse')
  inflect.singular(/^(.*[^s]s)es$/i, '\1')
  inflect.singular(/ães$/i, 'ão')
  inflect.singular(/aes$/i, 'ao')
  inflect.singular(/ãos$/i, 'ão')    
  inflect.singular(/aos$/i, 'ao')
  inflect.singular(/ões$/i, 'ão')
  inflect.singular(/oes$/i, 'ao')
  inflect.singular(/(japon|escoc|ingl|dinamarqu|fregu|portugu)eses$/i, '\1ês')
  inflect.singular(/^(g|)ases$/i,  '\1ás')

  # Incontáveis
  inflect.uncountable %w( tórax tênis ônibus lápis fênix )

  # Irregulares
  inflect.irregular "país", "países"
end
```

Porém, ao criar tabelas e controladores com nomes em português, você precisa levar algumas coisas em consideração:

### Nem todas as palavras serão pluralizadas corretamente

Na verdade isso pode acontecer mesmo programando em inglês. O ideal é abrir o console do Rails na sua aplicação (rails console ou rails c) e conferir a pluralização.

Por exemplo: digite "usuario".pluralize e confira se a saída é "usuarios". Depois digite "usuarios".singularize e confira se a saída é "usuario".

Para evitar problemas, se o Rails estiver pluralizando errado, antes de criar as tabelas e controladores inclua a seguinte linha no inflections.rb para forçar a pluralização correta:

inflect.irregular "usuario", "usuarios"
Obs.: "usuario" é só um exemplo, na verdade ele já é pluralizado corretamente.

### Cuidado ao criar tabelas e controladores com mais de uma palavra no nome

A pluralização do Rails funciona levando o inglês em consideração, onde os adjetivos vêm antes dos substantivos. Portanto, ele só vai pluralizar a última palavra do nome.

Se você quiser criar uma tabela de notas do aluno, não crie o modelo como NotaDoAluno pois o Rails vai erradamente pluralizar como NotaDoAlunos. Ao invés disso crie o modelo como AlunoNota, assim ele vai pluralizar como AlunoNotas. Idem para os controladores.

Caso você saiba o que está fazendo, você pode sobrescrever as convenções de nomes de tabelas do Rails e configurar manualmente o nome da tabela daquele modelo:

```ruby
class Product < ActiveRecord::Base
  self.table_name = "notas_do_aluno"
end
```

### Tabelas many-to-many

Às vezes você pode querer criar um modelo para uma relação many-to-many, para poder adicionar mais campos ou métodos. É comum que nesse caso o nome das duas tabelas seja no plural, para isso use no inflections.rb:

```ruby
inflect.irregular "postagemtag", "postagenstags"
inflect.irregular "postagem tag", "postagens tags"
inflect.irregular "postagem_tag", "postagens_tags"
```

Assim, ao rodar rails generate, ele vai criar nomes de tabelas e controladores com ambas as palavras no plural, do contrário só a última palavra seria pluralizada.

## Alterando rotas para português

Isso é fácil, está explicado na documentação oficial. Vai ficar mais ou menos assim:

resources :usuarios, path_names: { new: "novo", edit: "editar" }
Ou se você quiser pode alterar vários de uma vez usando scope:

```ruby
scope path_names: { new: "novo", edit: "editar" } do
  # seus resources
end
```

As variáveis _path não são alteradas, continuam com new e edit. Por exemplo: new_usuario_path e edit_usuario_path(@usuario).

Bônus - Configurando Fuso Horário

O Rails por default exibe e guarda no banco as horas no horário UTC.

Se você quiser que o Rails continue guardando datas/horas no formato UTC, mas converta para o usuário em outro horário, como o de Brasília, use o código abaixo no config/application.rb:

```ruby
config.time_zone = "Brasilia"
```

Isso é uma boa ideia se você precisa suportar usuários de países diferentes.

Porém se você quiser que o Rails tanto guarde no banco como exiba ao usuário no horário de Brasília, use:

```ruby
config.time_zone = "Brasilia"
config.active_record.default_timezone = :local
```

Isso é necessário quando você está trabalhando em uma base legada, que usa o horário de Brasília em suas colunas de data e hora. Ou se a base é própria mas você importa dados de outra base direto via SQL, sem ser via ActiveRecord.
