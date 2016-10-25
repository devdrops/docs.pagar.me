---
title: Realizando uma transação

language_tabs:
  - shell
  - ruby
  - php

search: true
---

# Realizando uma transação

Após [capturar os dados do cartão, gerar o `card_hash` e enviá-lo para o seu
servidor](/capturing-card-data), você deve realizar a transação junto ao
Pagar.me, que efetuará a transação no cartão do cliente.

Para transações de boleto bancário, a transação pode ser realizada totalmente
dentro do seu servidor, já que não há a transmissão de dados sensíveis e
portanto o `card_hash` não é utilizado.

**Atenção**: após ser gerado, o `card_hash` tem validade de 5 minutos e só pode ser utilizado uma única vez. Caso você deseje fazer diversas cobranças, cobranças posteriores, ou utilizar mais de uma feature (ex: transações + assinaturas), você deve primeiramente [criar um `card`](https://docs.pagar.me/cards/#storing-a-card).

## Realizando uma transação de cartão de crédito

Com o `card_hash` no seu servidor, agora basta realizar a transação:

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_hash={CARD_HASH}'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
    :card_hash => "{CARD_HASH}"
})

transaction.charge

status = transaction.status # status da transação
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		'amount' => 1000,
		'card_hash' => "{CARD_HASH}"
	));

	$transaction->charge();

	$status = $transaction->status; // status da transação
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.CardHash = "{CARD HASH}";

transaction.Save();

TransactionStatus status = transaction.Status;
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

Após realizar uma transação de cartão de crédito, a mesma terá status
`paid`, indicando que o cartão do usuário foi debitado com sucesso.

Se a transação for recusada pelas operadoras de cartão, a mesma terá status
`refused`.

<aside class="notice">No ambiente de testes, você pode simular uma transação
recusada pela operadora de cartão passando um `card_cvv` começando com
`6`.</aside>

### Configurações da transação

Tag | Padrão | Descrição
--- | ------ | ---------
amount | --- | Valor total a ser cobrado (em centavos). Ex: R$14,99 = `1499`
card_hash | --- | Representação segura dos dados de cartão de crédito gerada pelo `Pagarme.js`
installments | `1` | Número de parcelas a ser cobrado no cartão de crédito.
payment_method | `credit_card` | Meio de pagamento a ser utilizado
postback_url | --- | URL para receber notificações sobre alterações da transação ([saiba mais](#postback))
soft_descriptor | --- | Texto (de até 13 caracteres) que será aparecerá na fatura do cartão do cliente ao lado do nome da sua loja
capture | `true` | Passar `false` para apenas autorizar a transação e realizar a captura posteriormente

## Realizando uma transação de boleto bancário

Uma transação de boleto bancária deve ser realizada diretamente do seu
servidor, sem a necessidade de utilizar o `card_hash`, já que não há
transmissão de dados sensíveis para o seu servidor e para o Pagar.me.

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'payment_method=boleto'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
    :payment_method => "boleto"
})

transaction.charge

boleto_url = transaction.boleto_url # URL do boleto bancário
boleto_barcode = transaction.boleto_barcode # código de barras do boleto bancário
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		'amount' => 1000,
		'payment_method' => "boleto"
	));

	$transaction->charge();

	$boleto_url = $transaction->boleto_url; // URL do boleto bancário
	$boleto_barcode = $transaction->boleto_barcode; // código de barras do boleto bancário
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.PaymentMethod = PaymentMethod.Boleto;

transaction.Save();

string boletoUrl = transaction.BoletoUrl;
string boletoBarcode = transaction.BoletoBarcode;
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

A transação terá status `waiting_payment` até o pagamento do boleto
bancário. A URL do boleto bancário para pagamento estará disponível na variável
`boleto_url`.

Quando o boleto bancário for detectado como pago, a transação passará a ter o
status `paid`.

### Configurações da transação

Tag | Padrão | Descrição
--- | ------ | ---------
amount | --- | Valor total a ser cobrado (em centavos). Ex: R$14,99 = `1499`
payment_method | `boleto` | Meio de pagamento a ser utilizado
postback_url | --- | URL para receber notificações sobre alterações da transação ([saiba mais](#postback))
boleto_expiration_date | data atual + 7 dias | Data de vencimento do boleto bancário

## Recebendo notificações de mudança de status da transação (POSTback) 

Ao realizar uma transação, diversos serviços externos (como empresas de
antifraude, operadoras de cartão e bancos) são consultados. Por isso, esse
processo pode demorar um pouco mais do que uma requisição HTTP normal, que
costuma durar menos de um segundo.

Para evitar que o tempo de resposta da requisição prejudique sua aplicação, ao
criar uma transação, você pode fornecer uma `postback_url`. Dessa forma, quando
a transação mudar de status, o Pagar.me notificará sua aplicação e o fluxo de
compra pode ser finalizado.

As URLs de POSTback também são úteis ao criar transações de boleto bancário.
Dessa forma, quando o pagamento do boleto for detectado junto ao banco, sua
aplicação será notificada e o fluxo de compra pode ser finalizado.

Caso a transação seja estornada, a URL de POSTback também será notificada sobre
a mudança de status.

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_hash={CARD_HASH}' \
	-d 'postback_url=http://seusite.com/payments/2718'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
    :card_hash => "{CARD_HASH}",
	:postback_url => "http://seusite.com/payments/2718"
})

transaction.charge
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		'amount' => 1000,
		'card_hash' => "{CARD_HASH}",
		'postback_url' => "http://seusite.com/payments/2718"
	));

	$transaction->charge();
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.CardHash = "{CARD HASH}";
transaction.PostbackUrl = "http://seusite.com/payments/2718";

transaction.Save();
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

<aside class="notice">Você pode utilizar o serviço
[RequestBin](http://requestb.in) para gerar URLs de POSTback de teste e
visualizar as requisições enviadas pelo Pagar.me.</aside>

Quando a `postback_url` é passada, a transação será retornada com status
`processing`, e as mudanças de status serão enviadas para o seu servidor na URL
de POSTback, através de um request `HTTP POST` com os seguintes parâmetros:

Parâmetro | Descrição | Valor padrão 
--- | --- | ---------
object | Objeto que originou a notificação de POSTback | `transaction`
id | ID do objeto (transação) que originou a notificação de POSTback | ---
event | Evento que originou a notificação de POSTback | `transaction_status_changed`
current_status | Status da transação após o evento | ---
old_status | Status da transação antes do evento | ---
desired_status | Status desejado após o evento | Ao criar a transação: `paid`. Ao estornar a transação: `refunded` |

<aside class="notice">OBS: Você deve validar os postbacks para garantir que eles foram enviados pela Pagar.me.<br/>
  Para isso existe o cabeçalho HTTP `X-Hub-Signature`, [saiba mais](https://docs.pagar.me/advanced#validando-a-origem-de-um-postback)</aside>


## Dividindo uma transação 

Ao criar uma transação, você poderá definir quem irá receber e quanto do valor referente a esta transação será recebido. Essas regras são definidas através das `split_rules`, ou **regras de split**.

Para criar uma regra de divisão, você precisa primeiro [criar um recebedor](/recipients).

Após criado seus recebedores, você deve adicionar os parâmetros `split_rules` na hora de criar a transação, como no exemplo seguinte:

 ```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_hash={CARD_HASH}' \
	-d 'postback_url=http://seusite.com/payments/2718' \
    -d 'split_rules[0][recipient_id]=re_ci7nhf1ay0007n016wd5t22nl'
    -d 'split_rules[0][charge_processing_fee]=true' \
    -d 'split_rules[0][liable]=true' \
    -d 'split_rules[0][percentage]=30' \
    -d 'split_rules[1][recipient_id]=re_ci7nheu0m0006n016o5sglg9t' \
    -d 'split_rules[1][charge_processing_fee]=true' \
    -d 'split_rules[1][liable]=false' \
    -d 'split_rules[1][percentage]=70'
```

```ruby
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $transaction = new PagarMe_Transaction(array(
        'amount' => 1000,
        'card_hash' => "{CARD_HASH}",
        'split_rules' => array(
		array(
			'recipient_id' => 're_ci7nheu0m0006n016o5sglg9t',
			'charge_processing_fee' => true,
			'liable' => true,
			'percentage' => '60',
        
		),

		array(
			'recipient_id' => 're_ci7nhf1ay0007n016wd5t22nl',
			'charge_processing_fee' => true,
			'liable' => false,
			'percentage' => '40',
		)
        )
    ));

    $transaction->charge();

    $status = $transaction->status; // status da transação
?>
```

```cs
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

## Enviando dados do cliente para o Pagar.me (antifraude) 

Para ter um maior controle dos seus clientes, principalmente através do
Dashboard do Pagar.me, recomendamos que você também envie os dados do cliente
ao realizar a transação.

Para utilizar o recurso antifraude, é obrigatório o envio
dos dados do cliente para o Pagar.me.

<aside class="notice">No ambiente de testes, você pode simular uma transação
recusada pelo antifraude passando o `customer[document_number]` como
`11111111111`.</aside>

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_hash={CARD_HASH}' \
	-d 'customer[name]=John Appleseed' \
	-d 'customer[document_number]=92545278157' \
	-d 'customer[email]=jappleseed@apple.com' \
	-d 'customer[address][street]=Av. Brigadeiro Faria Lima' \
	-d 'customer[address][neighborhood]=Jardim Paulistano' \
	-d 'customer[address][zipcode]=01452000' \
	-d 'customer[address][street_number]=2941' \
	-d 'customer[address][complementary]=8º andar' \
	-d 'customer[phone][ddd]=11' \
	-d 'customer[phone][number]=30713261' \
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
    :card_hash => "{CARD_HASH}",
	:customer => {
		:name => "John Appleseed",
		:document_number => "92545278157",
		:email => "jappleseed@apple.com",
		:address => {
			:street => "Av. Brigadeiro Faria Lima",
			:neighborhood => "Jardim Paulistano",
			:zipcode => "01452000",
			:street_number => "2941",
			:complementary => "8º andar"
		},
		:phone => {
			:ddd => "11"
			:number => "30713261"
		}
	}
})

transaction.charge
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		"amount" => 1000,
		"card_hash" => "{CARD_HASH}",
		"customer" => array(
			"name" => "John Appleseed",
			"document_number" => "92545278157",
			"email" => "jappleseed@apple.com",
			"address" => array(
				"street" => "Av. Brigadeiro Faria Lima",
				"neighborhood" => "Jardim Paulistano",
				"zipcode" => "01452000",
				"street_number" => "2941",
				"complementary" => "8º andar"
			),
			"phone" => array(
				"ddd" => "11",
				"number" => "30713261"
			)
		}
	));

	$transaction->charge();
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.CardHash = "{CARD HASH}";
transaction.Customer = new Customer() {
	Name = "John Appleseed",
	DocumentNumber = "92545278157",
	Email = "jappleseed@apple.com",
	Address = new Address() {
		Street = "Av. Brigadeiro Faria Lima",
		Neighborhood = "Jardim Paulistano",
		Zipcode = "01452000",
		StreetNumber = "2941",
		Complementary = "8º andar"
	},
	Phone = new Phone() {
		Ddd => "11",
		Number => "30713261"
	}
};

transaction.Save();
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

## Cancelando uma Transação 

Para cancelar uma transação, ou seja, fazer um estorno, você deve enviar uma requisição `POST` para a rota `/transactions/:id/refund`, onde `:id` é o `id` da transação.

```shell
curl -X POST 'https://api.pagar.me/1/transactions/:id/refund' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
transaction = PagarMe::Transaction.find_by_id("{ID}")
transaction.refund

transaction.status
# refunded
```

```php
<?php

$transaction = PagarMe_Transaction::findById("{ID}");
$transaction->refund();

$transaction->getStatus();
// "refunded"

?>
```

```cs
var transaction = PagarMeService.GetDefaultService().Transactions.Find();

transaction.Refund();

TransactionStatus status = transaction.Status;
// TransactionStatus.Refunded
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).
