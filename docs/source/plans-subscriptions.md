---
title: Planos e assinaturas

language_tabs:
  - shell
  - ruby
  - php

search: true
---

# Planos e assinaturas

Com o Pagar.me, você pode cobrar seus clientes de forma recorrente através de
planos e assinaturas.

Ao criar um plano, você define como, quando e quanto devemos cobrar seu
cliente. E para cobrar seu cliente, basta criar uma assinatura atrelada a um
plano.

O Pagar.me então se encarregará de todo o fluxo de cobrança e gestão da
inadimplência para você.

## Criando um plano

O primeiro passo é criar um plano.

Nesse exemplo, criaremos um plano de R$49,90 e 30 dias:

```shell
curl -X POST 'https://api.pagar.me/1/plans' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=4990' \
    -d 'days=30' \
	-d 'name=Plano Mensal'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

plan = PagarMe::Plan.new({
    :amount => 4990,
    :days => 30,
	:name => "Plano Mensal"
})

plan.create
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$plan = new PagarMe_Plan(array(
		'amount' => 4990,
		'days' => 30,
		'name' => "Plano Mensal"
	));

	$plan->create();
?>
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

### Configurações do plano

Tag | Padrão | Descrição
--- | ------ | ---------
name | --- | Nome do plano. Ex: `Plano Gold`
amount | --- | Valor a ser cobrado (em centavos). Ex: R$14,99 = `1499`
days| --- | Dias de duração do plano
trial_days | `0` | Dias de duração do período de trial
payment_methods | `credit_card,boleto` | Meios de pagamento aceitos para o plano
charges | `null` | Número de vezes que o usuário deverá ser cobrado, **sem contar a cobrança inicial da assinatura no caso de pagamento com cartão de crédito** (caso deva haver 3 cobranças, o valor passado deve ser `2` no caso de cartão). `null` irá cobrar o usuário indefinidamente, ou até que o plano seja cancelado.
installments | `1` | Número de parcelas a ser cobrado no cartão de crédito. Útil para planos anuais em que o usuário irá parcelar o valor ao longo dos `12` meses.

## Criando uma assinatura

Para criar uma assinatura de cartão de crédito atrelada ao plano criado
anteriormente, basta fornecer o ID do plano criado, o `card_hash` contendo os
dados de cartão e o e-mail do cliente.

Para mais informações sobre a geração do `card_hash`, veja como [realizar a
captura dos dados de cartão](/capturing-card-data).

Para criar uma assinatura com boleto bancário, basta passar `boleto` como o
`payment_method`.

```shell
curl -X POST 'https://api.pagar.me/1/subscriptions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'plan_id=1234' \
    -d 'card_hash={CARD_HASH}' \
	-d 'customer[email]=email.do.cliente@gmail.com'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

subscription = PagarMe::Subscription.new({
	:plan => PagarMe::Plan.find_by_id("1234"),
	:card_hash => "{CARD_HASH}",
	:customer => {
		:email => "email.do.cliente@gmail.com"
	}
})

subscription.create
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = new PagarMe_Subscription(array(
		'plan' => PagarMe_Plan::findById("1234"),
		'card_hash' => "{CARD_HASH}",
		'customer' => array(
			'email' => "email.do.cliente@gmail.com"
		)
	));

	$subscription->create();
?>
```

> Não se esqueça de substituir:<br/>
> - `1234` pelo ID do plano,<br/>
> - `{CARD_HASH}` pelo `card_hash` gerado no browser do cliente,<br/>
> - `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível
>   no seu [Dashboard](https://dashboard.pagar.me/).

## Fluxo de cobrança

Toda assinatura tem um `current_period_start` e um `current_period_end`,
indicando, respectivamente, o início e o fim do período de cobrança atual.

Dessa forma, uma assinatura com um plano de 30 dias criada hoje, terá
hoje como `current_period_start`, e daqui a 30 dias como `current_period_end`.

As cobranças serão sempre realizadas na data do `current_period_end`.

A última transação realizada dentro de uma assinatura está disponível na
variável `current_transaction`. Dessa forma, você pode acessar, por exemplo, os
dados do boleto bancário da assinatura sem precisar realizar outra requisição.

### Na criação da assinatura

#### Cartão de crédito

Ao criar uma assinatura de cartão de crédito, tentaremos na mesma hora realizar
a primeira cobrança no cartão do usuário. A assinatura passará a ter o status
`paid` caso a cobrança ocorra com sucesso.

Se o plano tiver um período de trial, iremos validar o cartão antes de
armazená-lo, e a primeira cobrança só será realizada ao fim do trial. Até lá, a
assinatura terá o status `trialing`.

Caso a primeira cobrança ou a validação do cartão de crédito falhe, a
assinatura não será criada e um erro será retornado.

#### Boleto bancário

Ao criar uma assinatura de boleto bancário, será emitido um boleto com 7 dias
de validade. A assinatura terá status `unpaid` até que o boleto bancário seja
pago. Ao detectarmos o pagamento, o status mudará para `paid`.

Se o plano tiver um período de trial, o boleto será emitido com data de
validade para o fim do período de trial e a assinatura terá status `trialing`.
Ao fim do trial, se o boleto for pago, a assinatura irá para o status `paid`.

Caso o boleto não tenha sido pago até o fim do trial, a assinatura passará para
`unpaid`.

### Ao fim do período de cobrança atual

#### Cartão de crédito

Ao fim do período atual (`current_period_end`), iremos realizar a próxima
cobrança na assinatura. Se a cobrança ocorrer com sucesso, a assinatura
permanecerá no status `paid`, e o `current_period_start` e `current_period_end`
serão atualizados.

Caso a cobrança falhe por algum motivo (falta de saldo, transação negada pelo
banco, etc), a assinatura entrará no status `pending_payment` e o usuário será
notificado por e-mail.

#### Boleto bancário 

Sempre que um boleto de uma assinatura é pago, o próximo boleto é emitido e já
está disponível para pagamento. Dessa forma, após o último pagamento, o boleto
correspondente ao período atual já poderá ser pago pelo cliente.

Ao detectarmos o pagamento do boleto do período atual, a assinatura continuará
com o status `paid` e o `current_period_start` e `current_period_end` serão
atualizados respeitando a data de pagamento do boleto. Dessa forma, se o
usuário efetuar o pagamento 5 dias antes do fim do período atual, o
`current_period_end` terá um acréscimo de 5 dias.

Caso o boleto não seja pago até o fim do período, a assinatura entrará no
status `pending_payment` e o usuário será notificado por e-mail.

### Durante o status `pending_payment`

Quando a assinatura atinge o fim do período atual sem ser paga, a mesma muda
para o status `pending_payment`. Durante esse status, tentaremos cobrar o
usuário todos os dias e iremos notificá-lo por e-mail caso o pagamento falhe ou
não seja detectado novamente.

Sempre que o usuário é notificado por e-mail, mandamos um link para uma página
onde ele poderá alterar a forma de pagamento ou o cartão de crédito utilizado
na assinatura.

O tempo que a assinatura ficará `pending_payment` caso o pagamento não seja
detectado é, por padrão, de 5 dias. Esse valor (`payment_deadline`) pode ser
configurado pelo seu Dashboard e é único para sua conta.

Caso após todos as 5 dias de tentativa de cobrança a assinatura continue não paga, a
mesma passará para o status `unpaid`.

### Durante o status `unpaid`

Quando a assinatura atinge o status `unpaid`, você deve cortar o acesso do
usuário ao seu serviço/sistema, pois o mesmo encontra-se inadimplente há 5
dias.

Durante o status `unpaid`, faremos mais `4` tentativas de cobrança com um
intervalo de `3` dias entre elas. Tanto o número de tentativas quanto o
intervalo entre elas pode ser configurada pelo seu Dashboard, e são únicos para
a sua conta.

Você pode, opcionalmente, optar por automaticamente cancelar a assinatura caso
após todas as tentativas de cobrança (do status `pending_payment` e `unpaid`)
ela continue não paga. Essa opção pode ser configurada pelo seu Dashboard e por
padrão está desabilitada.

### Durante o status `canceled`

Após a assinatura ser cancelada, nenhuma tentativa de cobrança será mais feita.

Uma assinatura cancelada não pode ser cobrada novamente.

## Recebendo notificações de mudança de status (POSTback)

Ao fornecer uma `postback_url` ao criar a assinatura, iremos notificar sua
aplicação sempre que o status da assinatura mudar. Dessa forma, você pode
cortar e reativar o serviço de um cliente baseado nas mudanças de status da
assinatura.

<aside class="notice">Quando a assinatura passa para o status `unpaid`, você
deve cortar o serviço do cliente, já que a mesma está inadimplente.</aside>

```shell
curl -X POST 'https://api.pagar.me/1/subscriptions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'plan_id=1234' \
    -d 'card_hash={CARD_HASH}' \
	-d 'customer[email]=email.do.cliente@gmail.com' \
	-d 'postback_url=http://seusite.com/subscriptions/2718'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

subscription = PagarMe::Subscription.new({
	:plan => PagarMe::Plan.find_by_id("1234"),
	:card_hash => "{CARD_HASH}",
	:customer => {
		:email => "email.do.cliente@gmail.com"
	},
	:postback_url => "http://seusite.com/subscriptions/2718"
})

subscription.create
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = new PagarMe_Subscription(array(
		'plan' => PagarMe_Plan::findById("1234"),
		'card_hash' => "{CARD_HASH}",
		'customer' => array(
			'email' => "email.do.cliente@gmail.com"
		),
		'postback_url' => "http://seusite.com/subscriptions/2718"
	));

	$subscription->create();
?>
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

<aside class="notice">Você pode utilizar o serviço
[RequestBin](http://requestb.in) para gerar URLs de POSTback de teste e
visualizar as requisições enviadas pelo Pagar.me.</aside>

Quando a `postback_url` é passada, as mudanças de status da transação serão
enviadas para o seu servidor na URL de POSTback através de um request `HTTP
POST` com os seguintes parâmetros:

Parâmetro | Descrição | Valor padrão 
--- | --- | ---------
object | Objeto que originou a notificação de POSTback | `subscription`
id | ID do objeto (assinatura) que originou a notificação de POSTback | ---
event | Evento que originou a notificação de POSTback | `subscription_status_changed`
current_status | Status da assinatura após o evento | ---
old_status | Status da assinatura antes do evento | ---
desired_status | Status desejado após o evento | `paid`

<aside class="notice">OBS: Você deve validar os postbacks para garantir que eles foram enviados pela Pagar.me.<br/>
  Para isso existe o cabeçalho HTTP `X-Hub-Signature`, [saiba mais](https://docs.pagar.me/advanced#validando-a-origem-de-um-postback)</aside>

## Cancelando uma assinatura

Para cancelar uma assinatura basta fazer o seguinte:

```shell
curl -X POST 'https://api.pagar.me/1/subscriptions/{ID da Assinatura}/cancel' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

subscription = PagarMe::Subscription.find_by_id({ID da Assinatura})

subscription.cancel # Subscription cancelada
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = PagarMe_Subscription::findById({ID da Assinatura});
	$subscription->cancel(); // Cancel

	if($subscription->getStatus() == 'canceled') {
		// cancelado
	}
?>
```

> Não se esqueça de substituir:<br/>
> - `{ID da Assinatura}` pelo ID da assinatura,<br/>
> - `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível
>   no seu [Dashboard](https://dashboard.pagar.me/).


##Perguntas frequentes


### O que muda no ato de upgrade ou downgrade de uma assinatura?

- No **upgrade**, a periodicidade não é alterada. O que acontece é uma cobrança calculada da seguinte maneira:

1. Se o status não é `paid`, o valor integral do novo plano é cobrado.  

2. Se o status é `paid`, o valor é calculado da seguinte maneira:

    2.1. É obtida a "proporção não usufruída" do plano com (Dias não usados do plano antigo) / (Dias do plano antigo). 
    Os dias não usados são simplesmente quantos dias faltam pra chegar no `current_period_end` da assinatura. 

    2.2. É obtido o "valor não usufruído" do plano antigo multiplicando essa 
    "proporção não usufruída" pelo valor do plano antigo. 

    2.3. É subtraído o "valor não usufruído" do valor do plano novo, e esse resultado é cobrado.

Isso ocorre pela razão de que não existe motivo para cobrar o valor "não usado" do plano antigo no plano novo.    


- No **downgrade**, não há nova cobrança e a periodicidade é alterada:

1. O `current_period_start` da assinatura passa a ser a data atual.

2. O `current_period_end` da assinatura passa é calculado da seguinte maneira:

    2.1. É obtida a "proporção não usufruída" do plano com (Dias não usados do plano antigo) / (Dias do plano antigo). Os dias 
    não usados são simplesmente quantos dias faltam pra chegar no `current_period_end` da assinatura.

    2.2. É multiplicada essa "proporção não usufruída" pelo número de dias do novo plano, e o `current_period_end` da 
    assinatura passa a ser esse número de dias a partir da data atual.

    Isso ocorre para que o tempo mais caro que já foi pago não seja "perdido" pelo cliente. O cliente só volta a ser cobrado 
    depois que a proporção de dias do plano mais caro já tiver sido usufruída pelo cliente, mesmo que no plano mais barato.
   
    Vale notar que o reajuste do período no downgrade **NÃO depende em nada do valor** – só dos períodos.




### Quando uma assinatura vira `unpaid`, o serviço não é cortado, quando o cliente volta a pagar, a periodicidade se altera, ou seja, cliente consegue "ganhar" estes dias que se encontra em `unpaid`. Isto está certo?

Existem três status de uma assinatura: `pending_payment`, `unpaid` (com prazos de retentativa diferentes) e `canceled`. 
O comportamento esperado é que durante os dias de `pending_payment` o serviço não seja cancelado e que o cliente "ganhe" 
esses dias. Já quando o status vira `unpaid`, um postback é mandado ao cliente. Nessa situação, é esperado que o serviço 
seja suspenso por parte do cliente, e não da Pagar.me, e que o cliente seja avisado que ele precisa ser cobrado. 

Ou seja, cabe ao cliente fazer com que o cliente não "ganhe" esses dias do status `unpaid`. Essa é a razão de existir um 
status `unpaid` separado do `pending_payment`. O status `canceled` é **irreversível**. Isso quer dizer que uma nova assinatura 
precisaria ser criada para que esse serviço fosse continuado. Assim, ele só deveria ser atingido quando o cliente 
definitivamente não pagar ou cancelar o serviço.

Maiores informações em: [Fluxo de Cobrança](https://docs.pagar.me/plans-subscriptions/#fluxo-de-cobranca)


### Após término do período de trial, qual a data efetiva de cobrança da assinatura?

Quando um plano é criado, são dados dois parâmetros: `trial_days` e `days`. Quando acaba o `trial_days`, a data de cobrança é definida a partir da contagem de dias definidos no parâmetro `days`, começando do dia posterior ao final de `trial_days`.


### O que acontece quando um usuário que já assinou um plano trial no passado, e depois assinar de novo o mesmo plano no futuro? Gera um erro, assina sem trial, ou libera novamente o trial?

Ele libera novamente o trial. Entretanto, o controle desse comportamento pode ser gerenciado em sua aplicação, no caso
de bloqueio do período de trial, pode ser criado um plano sem período de trial mas com os mesmos valores e periodicidade.



### O que acontece na mudança de um Plano padrão para um Plano com trial?

**O comportamento de upgrade/downgrade não se aplica**. Isso quer dizer que independentemente do acréscimo ou decréscimo no valor, será cobrado exatamente o amount da assinatura, e o período ao fim do trial não terá ajuste algum, e será baseado integralmente no `days` do plano.









