---
layout: post
title:  "Rails in brazilian portuguese"
date:   2018-07-12 19:45:31 -0300
categories: [article]
tags: [architecture, rails, ror]
author: Marcelo Foss
---

This article is about the process of setting up Rails to use localization to brazilian-portuguese (pt-br), i.e., have  ActiveRecord error messsages, hours and dates to brazilian-pt; and to configures the development to use brazilian-pt, i.e., use Rails pluralization/singularization so that models, controllers and views can be creates in pt-br.

## Changing the interface to brazilian-pt

Create the file ```config/locales/pt-BR.yml``` and insert the following:

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

## Making brazilian-pt the default language to your application
Go to ```config/application.rb``` and add the following:

```ruby
#config/application.rb
config.i18n.default_locale = :"pt-BR"
I18n.enforce_available_locales = false
```

## Changing pluralization to brazilian-pt

The file ```config/initializers/inflections.rb``` has the responsibility to tell Rails how to pluralize or sigularize names. Add the following:

```ruby
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

### Pluralization for non regular words/names

When creating models, controllers or views in brazilian-pt, it is a good practice to see how Rails will pluralize it, to prevent the case of non regular names.   
Actually that happens in any language, even english.  
Open Rails console to check it:  
```
$ rails c
```
For example checkin for the word "Usuario", type the following
```
2.3.1 :001 > "usuario".pluralize
```
the result should be
```
=> "usuarios"
```
then type
```
2.3.1 :002 > "usuarios".singularize
```
the result should be
```
 => "usuario"
```
To avoid problems and force Rails to make the correct pluralization/singularization
edit the inflections.rb file like this:

```ruby
inflect.irregular "usuario", "usuarios"
```

### Creating models and controllers with more than one word

Rails pluralization was designed with the English language in mind, where adjectives are placed before nouns, so it will pluralize only the last word of the name.  
If you want to create a model to store student grades like "NotaDoAluno", Rails will generate the plural to "NotaDoAlunos", which is not really what is wanted.  
To avoid that it is better than crete the model as "AlunoNota", which Rails will pluralize to "AlunoNotas", which is preferable. The same applies to controllers.  

You can also verrule the naming conventions of Rails models, and manually configure the table name of that particular model:

```ruby
class Product < ActiveRecord::Base
  self.table_name = "notas_do_aluno"
end
```

### Many-to-many tables

When you need to create a many-to-many relationship, it is common to have the names of the two tables in the plural, to do that edit the ```inflections.rb``` file, so that when executing rails generate, it will create models and controllers with both words in plural.

```ruby
inflect.irregular "postagemtag", "postagenstags"
inflect.irregular "postagem tag", "postagens tags"
inflect.irregular "postagem_tag", "postagens_tags"
```

## Changing routes to portuguese

Edite the routes.rb file:
```ruby
resources :usuarios, path_names: { new: "novo", edit: "editar" }
```
Or using scopes:
```ruby
scope path_names: { new: "novo", edit: "editar" } do
  # seus resources

end
```

The path variables are not changed, they are kept as "new" and "edit", for example "new_usuario_path" e "edit_usuario_path(@usuario)" .


## Configuring timezone

By default Rails shows and store time int the databse using UTC.

If you want that Rails keep on storing time with UTC format, but convert it to the user in another timezone, like Rio de Janeiro, use the following on ```config/application.rb```:

```ruby
config.time_zone = "Brasilia"
```

The is the best if you need to support users from different countries/timezones

However, if you wish that Rails stores the timezone like it shows to the user, use:

```ruby
config.time_zone = "Brasilia"
config.active_record.default_timezone = :local
```
That is better when you are using a lagacy database, that uses Brasília timezone, or if you import data from another database directly via SQL and not via ActiveRecord.

# Reference
[http://vaidehijoshi.github.io/blog/2015/09/01/inflections-everywhere-using-activesupport-inflector/](http://vaidehijoshi.github.io/blog/2015/09/01/inflections-everywhere-using-activesupport-inflector/)

[https://pt.stackoverflow.com/questions/19512/como-programar-em-português-no-ruby-on-rails](https://pt.stackoverflow.com/questions/19512/como-programar-em-português-no-ruby-on-rails)
