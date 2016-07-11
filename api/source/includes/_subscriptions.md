# Assinaturas

## Objeto `subscription`

> Objeto `subscription`

```json
{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12830,
        "amount": 1300,
        "days": 15,
        "name": "Plano Prata",
        "trial_days": 0,
        "date_created": "2015-03-06T17:25:25.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 14858,
    "current_transaction": {
        "object": "transaction",
        "status": "waiting_payment",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": null,
        "acquirer_name": "development",
        "authorization_code": null,
        "soft_descriptor": null,
        "tid": null,
        "nsu": null,
        "date_created": "2015-03-06T18:15:12.000Z",
        "date_updated": "2015-03-06T18:15:12.000Z",
        "amount": 1300,
        "installments": 1,
        "id": 185486,
        "cost": 0,
        "postback_url": null,
        "payment_method": "boleto",
        "antifraud_score": null,
        "boleto_url": "https://pagar.me",
        "boleto_barcode": "1234 5678",
        "boleto_expiration_date": "2015-03-13T18:15:12.000Z",
        "referer": null,
        "ip": null,
        "subscription_id": 14858,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "boleto",
    "current_period_start": null,
    "current_period_end": null,
    "charges": 0,
    "status": "canceled",
    "date_created": "2015-03-04T18:41:57.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": null
}
```

Esse objeto contém os dados das assinaturas geradas pelo seu sistema, que são atreladas a um **plano**.

| Propriedade | Descrição |
|--:|:--|
| **object**<br> String | Nome do tipo do objeto criado/modificado. <br> **Valor retornado**: `subscription` |
| **plan**<br> Object | Objeto com os dados do plano que a assinatura está associada |
| **id**<br> Number | Número identificador do plano |
| **current_transaction**<br> Object | Objeto com os dados da última transação realizada nessa assinatura |
| **postback_url**<br> String | Endpoint da aplicação integrada ao Pagar.me que irá receber os jsons de resposta a cada atualização dos processos |
| **payment_method**<br> String | Método de pagamento associado a essa assinatura |
| **current_period_start**<br> String | Início do período de cobrança da assinatura |
| **current_period_end**<br> String | Término do período de cobrança da assinatura |
| **charges**<br> Number | Número de cobranças que foram efetuadas na assinatura, **sem contar a cobrança inicial da assinatura no caso de cartão de crédito**. |
| **status**<br> String | Possíveis estados da transação/assinatura <br> **Valores possíveis**: `trialing`, `paid`, `pending_payment`, `unpaid`, `canceled`, `ended` |
| **date_created**<br> String | Data da criação da assinatura |
| **phone**<br> Object | Objeto com dados do telefone do cliente |
| **address**<br> Object | Objeto com dados do endereço do cliente |
| **customer**<br> Object | Objeto com dados do cliente |
| **card**<br> Object | Objeto com dados do cartão do cliente |
| **metadata**<br> Object | Objeto com dados adicionais do cliente/produto/serviço vendido |

## Criando assinaturas

> POST https://api.pagar.me/1/subscriptions

```shell
curl -X POST https://api.pagar.me/1/subscriptions \
-d 'api_key=ak_test_TSgC3nrsdidfHAas24shu43HUhurw9' \
-d 'customer[email]=api@test.com' \
-d 'plan_id=12783' \
-d 'card_id=card_ci234fx8rr649rt16rtb11132' \
-d 'postback_url=http://requestb.in/zyn5obzy' \
-d 'payment_method=boleto'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

plan = PagarMe::Plan.find_by_id("12783")

subscription = PagarMe::Subscription.new({
    :payment_method => 'boleto',
    :card_number => "4901720080344448",
    :card_holder_name => "Jose da Silva",
    :card_expiration_month => "10",
    :card_expiration_year => "15",
    :card_cvv => "314",
    :postback_url => "http://test.com/postback",
    :customer => {
        email: 'api@test.com'
})
subscription.plan = plan

subscription.create
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");
	
	$plan = PagarMe_Plan::findById("13850");

	$subscription = new PagarMe_Subscription(array(
		"plan" => $plan,
		"card_number" => "4901720080344448",
		"card_holder_name" => "Jose da Silva",
		"card_expiration_month" => 12,
		"card_expiration_year" => 15,
		"card_cvv" => "123",
		'customer' => array(
			'email' => 'api@test.com'
		)));

	$subscription->create();

?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Subscription subscription = new Subscription();
subscription.CardNumber = "4901720080344448";
subscription.CardHolderName = "Jose da Silva";
subscription.CardExpirationDate = "1215";
subscription.CardCvv = "123";

Customer customer = new Customer();
customer.Email = "api@test.com";
subscription.Customer = customer;

subscription.Save();
```

Para efetivamente cobrar seu cliente de forma recorrente, você deve criar uma **assinatura**, que atrelada a um **plano**, conterá os dados de cobrança.

A criação de uma `subscription` (assinatura) é parecida com a criação de uma transação. Veja mais detalhes sobre como cobrar seu cliente de forma recorrente [aqui](https://pagar.me/docs/plans-subscriptions/#criando-uma-assinatura).

**OBS**: Você pode passar os objetos `customer` e `metadata` na criação de uma assinatura, assim como feito na criação de uma transação. A diferença é que a propriedade `customer[email]` é obrigatória na criação da **assinatura**. 

**OBS**: As transações criadas pelas assinaturas não passam pelo antifraude, devido a ocorrência de fraudes nesse tipo de serviço serem praticamente nulas.

> JSON Retornado (Exemplo)

```json
{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12783,
        "amount": 31000,
        "days": 30,
        "name": "Plano Ouro",
        "trial_days": 0,
        "date_created": "2015-03-03T16:56:32.000Z",
        "payment_methods": ["boleto", "credit_card"],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 16892,
    "current_transaction": {
        "object": "transaction",
        "status": "waiting_payment",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": null,
        "acquirer_name": "development",
        "authorization_code": null,
        "soft_descriptor": null,
        "tid": null,
        "nsu": null,
        "date_created": "2015-04-14T20:17:18.000Z",
        "date_updated": "2015-04-14T20:17:19.000Z",
        "amount": 31000,
        "installments": 1,
        "id": 194402,
        "cost": 0,
        "card_holder_name": null,
        "card_last_digits": null,
        "card_first_digits": null,
        "card_brand": null,
        "postback_url": null,
        "payment_method": "boleto",
        "antifraud_score": null,
        "boleto_url": "https://pagar.me",
        "boleto_barcode": "1234 5678",
        "boleto_expiration_date": "2015-04-21T20:17:18.000Z",
        "referer": "api_key",
        "ip": "179.185.132.108",
        "subscription_id": 16892,
        "metadata": {}
    },
    "postback_url": "http://requestb.in/zyn5obzy",
    "payment_method": "boleto",
    "current_period_start": null,
    "current_period_end": null,
    "charges": 0,
    "status": "unpaid",
    "date_created": "2015-04-14T20:17:19.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": null,
    "metadata": null
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **plan_id**<br> <span class="required">obrigatório</span> | id do plano a ser associado a uma assinatura |
| **card_hash**<br> <span class="required">obrigatório</span> | Dados encriptados do cartão do cliente. Você também pode usar o `card_id` ao invés do `card_hash` |
| **postback_url** | URL onde nosso sistema irá enviar requisições informando a cada alteração de status da assinatura em questão |
| **customer[email]**<br> <span class="required">obrigatório</span> | Email do cliente |
| **customer[name]**<br> <span class="required">obrigatório (com antifraude)</span> | Nome completo ou razão social do cliente que está realizando a transação |
| **customer[document_number]**<br> <span class="required">obrigatório (com antifraude)</span> | CPF ou CNPJ do cliente, sem separadores |
| **customer[address][street]**<br> <span class="required">obrigatório (com antifraude)</span> | logradouro (rua, avenida, etc) do cliente |
| **customer[address][street_number]**<br> <span class="required">obrigatório (com antifraude)</span> | Número da residência/estabelecimento do cliente |
| **customer[address][complementary]** | completo do endereço do cliente |
| **customer[address][neighborhood]**<br> <span class="required">obrigatório (com antifraude)</span> | bairro de localização do cliente |
| **customer[address][zipcode]**<br> <span class="required">obrigatório (com antifraude)</span> | CEP do imóvel do cliente, sem separadores |
| **customer[phone][ddd]**<br> <span class="required">obrigatório (com antifraude)</span> | DDD do telefone do cliente |
| **customer[phone][number]**<br> <span class="required">obrigatório (com antifraude)</span> | número de telefone do cliente |
| **customer[sex]**<br> `M` ou `F` (letras maiúsculas) | sexo do cliente |
| **customer[born_at]**<br> Formato: `MM-DD-AAAA` | Data de nascimento do cliente.<br>Ex: 11-02-1985 |
| **metadata** | Você pode passar dados adicionais na criação da transação para posteriormente filtrar estas na nossa dashboard. Ex: `metadata[ idProduto ]=13933139` |

## Retornando uma assinatura

> GET https://api.pagar.me/1/subscriptions/:id

```shell
curl -X GET https://api.pagar.me/1/subscriptions/14858 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

subscription = PagarMe::Subscription.find_by_id("14858")
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = PagarMe_Subscription::findById(14858);
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var subscription = PagarMeService.GetDefaultService().Subscriptions.Find("14858");
```

Essa rota é utilizada para retornar os dados de uma determinada assinatura.

> JSON Retornado (Exemplo)

```json
{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12783,
        "amount": 31000,
        "days": 30,
        "name": "Plano Ouro",
        "trial_days": 0,
        "date_created": "2015-03-03T16:56:32.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 14858,
    "current_transaction": {
        "object": "transaction",
        "status": "paid",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": "00",
        "acquirer_name": "development",
        "authorization_code": "11344",
        "soft_descriptor": null,
        "tid": "1425494517057",
        "nsu": "1425494517057",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "amount": 31000,
        "installments": 1,
        "id": 185122,
        "cost": 515,
        "postback_url": null,
        "payment_method": "credit_card",
        "antifraud_score": null,
        "boleto_url": null,
        "boleto_barcode": null,
        "boleto_expiration_date": null,
        "referer": "api_key",
        "ip": "189.8.94.42",
        "subscription_id": 14858,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "credit_card",
    "current_period_start": "2015-03-04T18:41:56.746Z",
    "current_period_end": "2015-04-03T18:41:56.746Z",
    "charges": 0,
    "status": "paid",
    "date_created": "2015-03-04T18:41:57.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": null
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da assinatura |

## Retornando assinaturas

> GET https://api.pagar.me/1/subscriptions

```shell
curl -X GET https://api.pagar.me/1/subscriptions \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'page=1' \
-d 'count=2'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

subscriptions = PagarMe::Subscription.all(1, 2)
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = PagarMe_Subscription::all(1, 2);
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var subscriptions = PagarMeService.GetDefaultService().Subscriptions.FindAll(new Subscription());
```

Essa rota é utilizada para retornar os dados de todas assinaturas.

> JSON Retornado (Exemplo)

```json
[{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12830,
        "amount": 1300,
        "days": 15,
        "name": "Plano Prata",
        "trial_days": 0,
        "date_created": "2015-03-06T17:25:25.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 15186,
    "current_transaction": {
        "object": "transaction",
        "status": "paid",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": "00",
        "acquirer_name": "development",
        "authorization_code": "934740",
        "soft_descriptor": null,
        "tid": "1427840452918",
        "nsu": "1427840452918",
        "date_created": "2015-03-31T22:20:52.000Z",
        "date_updated": "2015-03-31T22:20:53.000Z",
        "amount": 62000,
        "installments": 1,
        "id": 191522,
        "cost": 980,
        "card_holder_name": "API CUSTOMER",
        "card_last_digits": "8048",
        "card_first_digits": "401872",
        "card_brand": "visa",
        "postback_url": null,
        "payment_method": "credit_card",
        "antifraud_score": null,
        "boleto_url": null,
        "boleto_barcode": null,
        "boleto_expiration_date": null,
        "referer": "api_key",
        "ip": "189.8.94.42",
        "subscription_id": 15186,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "credit_card",
    "card_brand": "visa",
    "card_last_digits": "8048",
    "current_period_start": "2015-03-31T22:20:53.320Z",
    "current_period_end": "2015-04-15T22:20:53.320Z",
    "charges": 2,
    "status": "paid",
    "date_created": "2015-03-13T20:56:31.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6y37hc00030a416wrxsmzyi",
        "date_created": "2015-03-06T21:21:25.000Z",
        "date_updated": "2015-03-06T21:21:26.000Z",
        "brand": "visa",
        "holder_name": "API CUSTOMER",
        "first_digits": "401872",
        "last_digits": "8048",
        "fingerprint": "Jl9oOIiDjAjR",
        "valid": true
    },
    "metadata": null
}, {
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 14335,
        "amount": 15590,
        "days": 180,
        "name": "Plano Semestral",
        "trial_days": 0,
        "date_created": "2015-03-13T21:04:18.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 15185,
    "current_transaction": {
        "object": "transaction",
        "status": "waiting_payment",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": null,
        "acquirer_name": "development",
        "authorization_code": null,
        "soft_descriptor": null,
        "tid": null,
        "nsu": null,
        "date_created": "2015-03-13T21:05:07.000Z",
        "date_updated": "2015-03-13T21:05:07.000Z",
        "amount": 3630,
        "installments": 1,
        "id": 186443,
        "cost": 0,
        "card_holder_name": null,
        "card_last_digits": null,
        "card_first_digits": null,
        "card_brand": null,
        "postback_url": null,
        "payment_method": "boleto",
        "antifraud_score": null,
        "boleto_url": "https://pagar.me",
        "boleto_barcode": "1234 5678",
        "boleto_expiration_date": "2015-03-20T21:05:07.000Z",
        "referer": null,
        "ip": null,
        "subscription_id": 15185,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "boleto",
    "card_brand": null,
    "card_last_digits": null,
    "current_period_start": null,
    "current_period_end": null,
    "charges": 4,
    "status": "unpaid",
    "date_created": "2015-03-13T20:54:30.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": null,
    "metadata": null
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |

## Atualizando uma assinatura

> PUT https://api.pagar.me/1/subscriptions/:id

```shell
curl -X PUT https://api.pagar.me/1/subscriptions/14858 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'plan_id=12830' \
-d 'payment_method=credit_card' \
-d 'card_id=card_ci6y37hc00030a416wrxsmzyi'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

subscription = PagarMe::Subscription.find_by_id(14858)

subscription.payment_method = 'boleto'

subscription.save
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = PagarMe_Subscription::findById(14858);
	$subscription->setPaymentMethod("boleto");

	$subscription->save();
```

```cs
```

Após criar uma assinatura, você pode atualizar os dados do **método do pagamento** e o **plano** que essa assinatura está atrelada.

> JSON Retornado (Exemplo)

```json
{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12830,
        "amount": 1300,
        "days": 15,
        "name": "Plano Prata",
        "trial_days": 0,
        "date_created": "2015-03-06T17:25:25.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 14858,
    "current_transaction": {
        "object": "transaction",
        "status": "waiting_payment",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": null,
        "acquirer_name": "development",
        "authorization_code": null,
        "soft_descriptor": null,
        "tid": null,
        "nsu": null,
        "date_created": "2015-03-06T18:15:12.000Z",
        "date_updated": "2015-03-06T18:15:12.000Z",
        "amount": 1300,
        "installments": 1,
        "id": 185486,
        "cost": 0,
        "postback_url": null,
        "payment_method": "boleto",
        "antifraud_score": null,
        "boleto_url": "https://pagar.me",
        "boleto_barcode": "1234 5678",
        "boleto_expiration_date": "2015-03-13T18:15:12.000Z",
        "referer": null,
        "ip": null,
        "subscription_id": 14858,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "boleto",
    "current_period_start": null,
    "current_period_end": null,
    "charges": 0,
    "status": "unpaid",
    "date_created": "2015-03-04T18:41:57.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": null
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **plan_id** | id do novo plano atrelado a assinatura |
| **payment_method**<br> <span class="required">obrigatório</span> | método de pagamento utilizado na assinatura. <br> **Valores possíveis**: `credit_card`, `boleto`  |
| **card_id** | Identificador dos dados de um cartão previamente salvo na nossa base de dados |
| **card_hash** | Dados encriptados de um cartão de crédito |
| **card_number** | Número de um cartão de crédito. Usado quando o cartão a ser configurado na assinatura ainda não está salvo no nosso banco de dados |
| **card_holder_name** | Nome do portador do cartão. Usado quando o cartão a ser configurado na assinatura ainda não está salvo no nosso banco de dados |
| **card_expiration_date**<br> Formato: `MMYY` | Data de expiração do cartão. <br> **Ex**: `0518` (Maio de 2018) |


## Cancelando uma assinatura

> POST https://api.pagar.me/1/subscriptions/:id/cancel

```shell
curl -X POST https://api.pagar.me/1/subscriptions/14858/cancel \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

subscription = PagarMe::Subscription.find_by_id("1234")

subscription.cancel
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$subscription = PagarMe_Subscription::findById(14858);
	$subscription->cancel();
```

```cs
```

Para cancelar uma assinatura você deve utilizar a rota `/subscriptions/:id/cancel`

> JSON Retornado (Exemplo)

```json
{
    "object": "subscription",
    "plan": {
        "object": "plan",
        "id": 12830,
        "amount": 1300,
        "days": 15,
        "name": "Plano Prata",
        "trial_days": 0,
        "date_created": "2015-03-06T17:25:25.000Z",
        "payment_methods": [
            "boleto",
            "credit_card"
        ],
        "color": null,
        "charges": null,
        "installments": 1
    },
    "id": 14858,
    "current_transaction": {
        "object": "transaction",
        "status": "waiting_payment",
        "refuse_reason": null,
        "status_reason": "acquirer",
        "acquirer_response_code": null,
        "acquirer_name": "development",
        "authorization_code": null,
        "soft_descriptor": null,
        "tid": null,
        "nsu": null,
        "date_created": "2015-03-06T18:15:12.000Z",
        "date_updated": "2015-03-06T18:15:12.000Z",
        "amount": 1300,
        "installments": 1,
        "id": 185486,
        "cost": 0,
        "postback_url": null,
        "payment_method": "boleto",
        "antifraud_score": null,
        "boleto_url": "https://pagar.me",
        "boleto_barcode": "1234 5678",
        "boleto_expiration_date": "2015-03-13T18:15:12.000Z",
        "referer": null,
        "ip": null,
        "subscription_id": 14858,
        "metadata": {}
    },
    "postback_url": null,
    "payment_method": "boleto",
    "current_period_start": null,
    "current_period_end": null,
    "charges": 0,
    "status": "canceled",
    "date_created": "2015-03-04T18:41:57.000Z",
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": null
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da assinatura a ser cancelada |

## Transações em uma assinatura

> GET https://api.pagar.me/1/subscriptions/:id/transactions

```shell
curl -X GET https://api.pagar.me/1/subscriptions/14858/transactions \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna um array de objetos `transaction` contendo as transações feitas a partir de uma assinatura.

> JSON Retornado (Exemplo)

```json
[{
    "object": "transaction",
    "status": "waiting_payment",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": null,
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": null,
    "nsu": null,
    "date_created": "2015-03-06T18:15:12.000Z",
    "date_updated": "2015-03-06T18:15:12.000Z",
    "amount": 1300,
    "installments": 1,
    "id": 185486,
    "cost": 0,
    "postback_url": null,
    "payment_method": "boleto",
    "antifraud_score": null,
    "boleto_url": "https://pagar.me",
    "boleto_barcode": "1234 5678",
    "boleto_expiration_date": "2015-03-13T18:15:12.261Z",
    "referer": null,
    "ip": null,
    "subscription_id": 14858,
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": {}
}, {
    "object": "transaction",
    "status": "waiting_payment",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": null,
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": null,
    "nsu": null,
    "date_created": "2015-03-06T18:02:25.000Z",
    "date_updated": "2015-03-06T18:02:26.000Z",
    "amount": 31000,
    "installments": 1,
    "id": 185481,
    "cost": 0,
    "postback_url": null,
    "payment_method": "boleto",
    "antifraud_score": null,
    "boleto_url": "https://pagar.me",
    "boleto_barcode": "1234 5678",
    "boleto_expiration_date": "2015-03-13T18:02:25.904Z",
    "referer": null,
    "ip": null,
    "subscription_id": 14858,
    "phone": null,
    "address": null,
    "customer": null,
    "card": null,
    "metadata": {}
}, {
    "object": "transaction",
    "status": "paid",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": "00",
    "acquirer_name": "development",
    "authorization_code": "11344",
    "soft_descriptor": null,
    "tid": 1425494517057,
    "nsu": 1425494517057,
    "date_created": "2015-03-04T18:41:56.000Z",
    "date_updated": "2015-03-04T18:41:57.000Z",
    "amount": 31000,
    "installments": 1,
    "id": 185122,
    "cost": 515,
    "postback_url": null,
    "payment_method": "credit_card",
    "antifraud_score": null,
    "boleto_url": null,
    "boleto_barcode": null,
    "boleto_expiration_date": null,
    "referer": "api_key",
    "ip": "189.8.94.42",
    "subscription_id": 14858,
    "phone": null,
    "address": null,
    "customer": {
        "object": "customer",
        "document_number": null,
        "document_type": "cpf",
        "name": null,
        "email": "api@test.com",
        "born_at": null,
        "gender": null,
        "date_created": "2015-03-04T18:40:03.000Z",
        "id": 14437
    },
    "card": {
        "object": "card",
        "id": "card_ci6v2mom200br5616ln4vg10q",
        "date_created": "2015-03-04T18:41:56.000Z",
        "date_updated": "2015-03-04T18:41:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": {}
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da assinatura que possui as transações desejadas |
