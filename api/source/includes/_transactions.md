# Transações 

Através da rota `/transactions` e suas derivadas, você pode criar transações, estornar, capturar, dentre outras atividades relacionadas a estas.

## Objeto `transaction`

> Objeto transaction

```json
{
  "object": "transaction",
  "status": "processing",
  "refuse_reason": null,
  "status_reason": "acquirer",
  "acquirer_response_code": null,
  "authorization_code": null,
  "soft_descriptor": "testeDeAPI",
  "tid": null,
  "nsu": null,
  "date_created": "2015-02-25T21:54:56.000Z",
  "date_updated": "2015-02-25T21:54:56.000Z",
  "amount": 310000,
  "installments": 5,
  "id": 184220,
  "cost": 0,
  "postback_url": "http://requestb.in/pkt7pgpk",
  "payment_method": "credit_card",
  "antifraud_score": null,
  "boleto_url": null,
  "boleto_barcode": null,
  "boleto_expiration_date": null,
  "referer": "api_key",
  "ip": "189.8.94.42",
  "subscription_id": null,
  "phone": null,
  "address": null,
  "customer": null,
  "card": {
    "object": "card",
    "id": "card_ci6l9fx8f0042rt16rtb477gj",
    "date_created": "2015-02-25T21:54:56.000Z",
    "date_updated": "2015-02-25T21:54:56.000Z",
    "brand": "mastercard",
    "holder_name": "Api Customer",
    "first_digits": "548045",
    "last_digits": "3123",
    "fingerprint": "HSiLJan2nqwn",
    "valid": null
  },
  "metadata": {
    "nomeData": "API Doc Test",
    "idData": 13
  }
}
```

Ao criar ou atualizar uma transação, este será o objeto que você irá receber como resposta em cada etapa do processo de efetivação da transação.

| Propriedade | Descrição |
|--:|:--|
| **object**<br> String | Nome do tipo do objeto criado/modificado. <br> **Valor retornado**: `transaction` |
| **status**<br> String | Para cada atualização no processamento da transação, esta propriedade será alterada, e o objeto `transaction` retornado como resposta através da sua URL de *postback* ou após o término do processamento da ação atual. <br> **Valores possíveis**: `processing`, `authorized`, `paid`, `refunded`, `waiting_payment`, `pending_refund`, `refused` |
| **status_reason**<br> String | Motivo/agente responsável pela validação ou anulação da transação. <br> **Valores possíveis**: `acquirer`, `antifraud`, `internal_error`, `no_acquirer`, `acquirer_timeout` |
| **acquirer_name**<br> String | Adquirente responsável pelo processamento da transação. <br> **Valores possíveis**: `development` (em ambiente de testes), `pagarme` (adquirente Pagar.me), `stone`, `cielo`, `rede`, `mundipagg` |
| **acquirer_response_code**<br> String | Mensagem de resposta do adquirente referente ao status da transação.  |
| **authorization_code**<br> String | Código de autorização retornado pela bandeira. |
| **soft_descriptor**<br> String | Texto que irá aparecer na fatura do cliente depois do nome da loja. <br> **OBS**: Limite de 13 caracteres. |
| **tid**<br> String | Código que identifica a transação no adquirente. |
| **nsu**<br> String | Código que identifica a transação no adquirente. |
| **date_created**<br> String | Data de criação da transação no formato ISODate |
| **date_updated**<br> String | Data de atualização da transação no formato ISODate |
| **amount**<br> Number | Valor, em centavos, da transação |
| **installments**<br> Number | Número de parcelas/prestações a serem cobradas |
| **id**<br> Number | Número identificador da transação |
| **cost**<br> Number | Custo da transação para o lojista |
| **postback_url**<br> String | URL (endpoint) do sistema integrado a Pagar.me que receberá as respostas a cada atualização do processamento da transação |
| **payment_method**<br> String | Método de pagamento possíveis: `credit_card` e `boleto`  |
| **boleto_url**<br> String | URL do boleto para impressão |
| **boleto_barcode**<br> String | Código de barras do boleto gerado na transação |
| **boleto_expiration_date**<br> String | Data de expiração do boleto (em ISODate) |
| **referer**<br> String | Mostra se a transação foi criada utilizando a API Key ou Encryption Key. |
| **ip**<br> String | IP de origem que criou a transção, podendo ser ou do seu cliente (quando criado via checkout ou utilizando card_hash) ou do servidor. |
| **subscription_id**<br> Number | Caso essa transação tenha sido originada na cobrança de uma assinatura, o `id` desta será o valor dessa propriedade  |
| **phone**<br> Object | Objeto com dados do telefone do cliente |
| **address**<br> Object | Objeto com dados do endereço do cliente |
| **customer**<br> Object | Objeto com dados do cliente |
| **card**<br> Object | Objeto com dados do cartão do cliente |
| **metadata**<br> Object | Objeto com dados adicionais do cliente/produto/serviço vendido |

## Criando uma transação

> POST https://api.pagar.me/1/transactions

```shell
curl -X POST https://api.pagar.me/1/transactions \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'amount=3100' \
-d 'card_id=card_ci6l9fx8f0042rt16rtb477gj' \
-d 'postback_url=http://requestb.in/pkt7pgpk' \
-d 'metadata[idProduto]=13933139'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
    :amount => 3100,
    :card_id => "card_ci6l9fx8f0042rt16rtb477gj",
    :postback_url => "http://requestb.in/pkt7pgpk",
    :metadata[idProduto] => 13933139
})

transaction.charge
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $transaction = new PagarMe_Transaction(array(
        "amount" => 3100,
        "card_id" => "card_ci6l9fx8f0042rt16rtb477gj",
        "postback_url" => "http://requestb.in/pkt7pgpk",
        "metadata" => array(
            "idProduto" => 13933139
        )
    ));

    $transaction->charge();
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 3100;
transaction.CardId = "card_ci6l9fx8f0042rt16rtb477gj";
transaction.PostbackUrl = "http://requestb.in/pkt7pgpk";
transaction.Metadata = new Metadata() {
    IdProduto = 13933139
};

transaction.Save();
```

Para fazer uma cobrança, você deve usar a rota `/transactions` para criar sua transação, que pode ser feita por cartão de crédito ou por boleto bancário.

> JSON retornado (exemplo):

```json
{
  "object": "transaction",
  "status": "processing",
  "refuse_reason": null,
  "status_reason": "acquirer",
  "acquirer_response_code": null,
  "authorization_code": null,
  "soft_descriptor": "testeDeAPI",
  "tid": null,
  "nsu": null,
  "date_created": "2015-02-25T21:54:56.000Z",
  "date_updated": "2015-02-25T21:54:56.000Z",
  "amount": 310000,
  "installments": 5,
  "id": 184220,
  "cost": 0,
  "postback_url": "http://requestb.in/pkt7pgpk",
  "payment_method": "credit_card",
  "antifraud_score": null,
  "boleto_url": null,
  "boleto_barcode": null,
  "boleto_expiration_date": null,
  "referer": "api_key",
  "ip": "189.8.94.42",
  "subscription_id": null,
  "phone": null,
  "address": null,
  "customer": null,
  "card": {
    "object": "card",
    "id": "card_ci6l9fx8f0042rt16rtb477gj",
    "date_created": "2015-02-25T21:54:56.000Z",
    "date_updated": "2015-02-25T21:54:56.000Z",
    "brand": "mastercard",
    "holder_name": "Api Customer",
    "first_digits": "548045",
    "last_digits": "3123",
    "fingerprint": "HSiLJan2nqwn",
    "valid": null
  },
  "metadata": {
    "idProduto": "13933139"
  }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span>| Chave da API (disponível no seu dashboard) |
| **amount**<br> <span class="required">obrigatório</span> | Valor a ser cobrado. Deve ser passado em centavos. Ex: R$ 10.00 = `1000` |
| **card_hash**<br> <span class="required">obrigatório\*</span> | Informações do cartão do cliente criptografadas no navegador. <br>**OBS**: Apenas para transações de **cartão de crédito** você deve passar **ou** o  `card_hash` **ou** o  `card_id` |
| **card_id**<br> <span class="required">obrigatório\*</span> | Ao realizar uma transação, retornamos o `card_id` do cartão para que nas próximas transações desse cartão possa ser utilizado esse identificador ao invés do `card_hash` |
| **payment_method**<br> default: `credit_card` | Aceita dois tipos de pagamentos/valores: `credit_card` e `boleto` |
| **postback_url** | Endpoint do seu sistema que receberá informações a cada atualização da transação. Caso você defina este parâmetro, o processamento da transação se tornará assíncrono. |
| **async** <br> default: `false` ou `true` caso utilize `postback_url` | Utilize `false` caso queira utilizar POSTbacks e manter o processamento síncrono de uma transação. |
| **installments**<br> mínimo: 1, máximo: 12 | Se o pagamento for boleto, o padrão é 1 |
| **boleto_expiration_date**<br> default: data atual + 7 dias | Prazo limite para pagamento do boleto |
| **soft_descriptor** | Descrição que aparecerá na fatura depois do nome da loja. Máximo de 13 caracteres |
| **capture**<br> default: `true` | Após a autorização de uma transação, você pode escolher se irá capturar ou adiar a captura do valor. Caso opte por postergar a captura, atribuir o valor `false` |
| **metadata** | Você pode passar dados adicionais na criação da transação para posteriormente filtrar estas na nossa dashboard. Ex: `metadata[ idProduto ]=13933139` |
| **customer[name]**<br> <span class="required">obrigatório\* (com antifraude)</span> | Nome completo ou razão social do cliente que está realizando a transação |
| **customer[document_number]**<br> <span class="required">obrigatório\* (com antifraude)</span> | CPF ou CNPJ do cliente, sem separadores |
| **customer[email]**<br> <span class="required">obrigatório\* (com antifraude)</span> | email do cliente |
| **customer[address][street]**<br> <span class="required">obrigatório\* (com antifraude)</span> | logradouro (rua, avenida, etc) do cliente |
| **customer[address][street_number]**<br> <span class="required">obrigatório\* (com antifraude)</span> | Número da residência/estabelecimento do cliente |
| **customer[address][complementary]** | complemento do endereço do cliente |
| **customer[address][neighborhood]**<br> <span class="required">obrigatório\* (com antifraude)</span> | bairro de localização do cliente |
| **customer[address][zipcode]**<br> <span class="required">obrigatório\* (com antifraude)</span> | CEP do imóvel do cliente, sem separadores |
| **customer[phone][ddd]**<br> <span class="required">obrigatório\* (com antifraude)</span> | DDD do telefone do cliente |
| **customer[phone][number]**<br> <span class="required">obrigatório\* (com antifraude)</span> | número de telefone do cliente |
| **customer[sex]**<br> `M` ou `F` (letras maiúsculas) | sexo do cliente |
| **customer[born_at]**<br> Formato: `MM-DD-AAAA` | Data de nascimento do cliente. Ex: 11-02-1985 |
| **split_rules** | Esse parâmetro é um `Array` que irá conter as regras da divisão do valor transacionado. <br> **OBS**: Caso você deseje incluir mais regras, passe os parâmetros abaixo alterando o índice em `+1` para cada nova regra/recebedor |
| **split_rules[n][recipient_id]** | Identificador do [recebedor](/#recebedores) |
| **split_rules[n][charge_processing_fee]**<br> default: `true` | Indica se o recebedor vinculado a essa regra de divisão será cobrado pelas taxas da transação |
| **split_rules[n][liable]**<br> default: `true` | Indica se o recebedor vinculado a essa regra de divisão assumirá o risco da transação, ou seja, possíveis estornos (*chargeback*) |
| **split_rules[n][percentage]**<br> <span class="required">obrigatório\*</span> | Define a porcentagem a ser recebida pelo recebedor configurado na regra. <br> **OBS**: se for utilizado a propriedade `percentage`, a propriedade `amount` não será necessária |
| **split_rules[n][amount]**<br> <span class="required">obrigatório\*</span> | Define o valor a ser recebido pelo recebedor configurado na regra. <br> **OBS**: se for utilizado a propriedade `amount`, a propriedade `percentage` não será necessária |

<aside class="notice"> OBS: Caso você vá usar o recurso antifraude, é obrigatório passar os dados do cliente na hora da criação da transação, como explicado nesse link: https://pagar.me/docs/transactions/#customer-data.</aside>

## Retornando uma Transação

> GET https://api.pagar.me/1/transactions/:id

```shell
curl -X GET https://api.pagar.me/1/transactions/194351 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

transaction = PagarMe::Transaction.find_by_id("184270")
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $transaction = PagarMe_Transaction::findById("194351");
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var transaction = PagarMeService.GetDefaultService().Transactions.Find("194351");
```

Retorna os dados de uma transação realizada.

> JSON Retornado (exemplo):

```json
{
    "object": "transaction",
    "status": "paid",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": null,
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": null,
    "nsu": null,
    "date_created": "2015-02-26T15:35:32.000Z",
    "date_updated": "2015-02-26T15:35:47.000Z",
    "amount": 25000,
    "installments": 1,
    "id": 184270,
    "cost": 115,
    "postback_url": null,
    "payment_method": "boleto",
    "antifraud_score": null,
    "boleto_url": "https://pagar.me",
    "boleto_barcode": "1234 5678",
    "boleto_expiration_date": "2015-03-02T03:00:00.000Z",
    "referer": "session_id",
    "ip": "189.8.94.42",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": null,
    "metadata": {}
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | id da transação previamente criada |

## Retornando transações

> GET https://api.pagar.me/1/transactions

```shell
curl -X GET https://api.pagar.me/1/transactions \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'count=3' \
-d 'page=3'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

transactions = PagarMe::Transaction.all(3, 3)
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $transaction = PagarMe_Transaction::all(3, 3);
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var transaction = PagarMeService.GetDefaultService().Transactions.FindAll(new Transaction());
```

Retorna um `Array` contendo objetos de transações, ordenadas a partir da transação realizada mais recentemente.

> JSON Retornado (exemplo):

```json
[{
    "object": "transaction",
    "status": "refused",
    "refuse_reason": "acquirer",
    "status_reason": "acquirer",
    "acquirer_response_code": "51",
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": 1425933798340,
    "nsu": 1425933798340,
    "date_created": "2015-03-09T20:43:17.000Z",
    "date_updated": "2015-03-09T20:43:18.000Z",
    "amount": 54496,
    "installments": "10",
    "id": 185679,
    "cost": 0,
    "postback_url": null,
    "payment_method": "credit_card",
    "antifraud_score": null,
    "boleto_url": null,
    "boleto_barcode": null,
    "boleto_expiration_date": null,
    "referer": "encryption_key",
    "ip": "179.185.132.108",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": {
        "object": "card",
        "id": "card_ci1u3yidd00036t16wkzev8s8",
        "date_created": "2014-10-29T03:12:50.000Z",
        "date_updated": "2015-03-07T19:43:08.000Z",
        "brand": "visa",
        "holder_name": "murilo junqueira",
        "first_digits": "411111",
        "last_digits": "1111",
        "fingerprint": "HEiFgPIQJqXG",
        "valid": true
    },
    "metadata": {}
}, {
    "object": "transaction",
    "status": "authorized",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": null,
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": null,
    "nsu": null,
    "date_created": "2015-03-09T20:41:20.000Z",
    "date_updated": "2015-03-09T20:41:20.000Z",
    "amount": 50000,
    "installments": 1,
    "id": 185676,
    "cost": 0,
    "postback_url": null,
    "payment_method": "boleto",
    "antifraud_score": null,
    "boleto_url": null,
    "boleto_barcode": null,
    "boleto_expiration_date": "2015-03-16T03:00:00.126Z",
    "referer": "encryption_key",
    "ip": "177.157.206.15",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": null,
    "metadata": {}
}, {
    "object": "transaction",
    "status": "authorized",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": "00",
    "acquirer_name": "development",
    "authorization_code": "854653",
    "soft_descriptor": null,
    "tid": 1425933651790,
    "nsu": 1425933651790,
    "date_created": "2015-03-09T20:40:51.000Z",
    "date_updated": "2015-03-09T20:40:51.000Z",
    "amount": 50000,
    "installments": 1,
    "id": 185675,
    "cost": 0,
    "postback_url": null,
    "payment_method": "credit_card",
    "antifraud_score": null,
    "boleto_url": null,
    "boleto_barcode": null,
    "boleto_expiration_date": null,
    "referer": "encryption_key",
    "ip": "177.157.206.15",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": {
        "object": "card",
        "id": "card_ci6ttnn2y007n5616jhotcfof",
        "date_created": "2015-03-03T21:42:58.000Z",
        "date_updated": "2015-03-09T20:06:15.000Z",
        "brand": "mastercard",
        "holder_name": "John Appleseed",
        "first_digits": "590072",
        "last_digits": "4446",
        "fingerprint": "XHLU9UYzU3+x",
        "valid": true
    },
    "metadata": {}
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **count**<br> default: `10` | Retorna `n` objetos de transação |
| **page**<br> default: `1` | Útil para implementação de uma paginação de resultados |

<aside class="notice">OBS: Você pode passar qualquer propriedade e valor presentes nos objetos `transaction` como parâmetro de busca/filtro nesta rota. Ex: `card_last_digits=4242`</aside>

## Gerando uma nova chave para encriptação do `card_hash`

> GET https://api.pagar.me/1/transactions/card_hash_key

```shell
curl -X GET https://api.pagar.me/1/transactions/card_hash_key \
-d 'encryption_key=ek_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

key = PagarMe::Transaction.generate_card_hash()
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $t = new PagarMe_Transaction(array(
      "amount" => 3100,
      "card_id" => "card_ci6l9fx8f0042rt16rtb477gj",
  	  "postback_url" => "http://requestb.in/1ahq78t1",
  	  "metadata" => array(
        "idProduto" => 13933139
       )
    ));

    $key = $t->generateCardHash();
```

```cs
```

Caso você queira/precise criar o `card_hash` manualmente, essa rota deverá ser utilizada para obtenção de uma chave pública de encriptação dos dados do cartão de seu cliente.

Saiba mais sobre como criar um `card_hash` [aqui](https://pagar.me/docs/capturing-card-data/#capturando-os-dados-em-uma-pgina-web).

> JSON Retornado (Exemplo)

```json
{
    "date_created": "2015-02-27T15:44:26.000Z",
    "id": 111111,
    "ip": "000.0.00.00",
    "public_key": "-----BEGIN PUBLIC KEY-----\ -----END PUBLIC KEY-----\ "
}
```

| Parâmetro | Descrição |
|--:|:--|
| **encryption_key**<br> <span class="required">obrigatório</span> | Chave de encriptação (disponível no seu dashboard) |

## Retornando as regras de divisão de uma transação

> GET https://api.pagar.me/1/transactions/:transaction_id/split_rules

```shell
curl -X GET https://api.pagar.me/1/transactions/189164/split_rules \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna os dados das regras de divisão do valor transacionado.

> JSON Retornado (exemplo):

```json
[{
    "object": "split_rule",
    "id": "sr_ci7ntawl1001s2m164zrbp7tz",
    "recipient_id": "re_ci7nhf1ay0007n016wd5t22nl",
    "charge_processing_fee": true,
    "liable": true,
    "percentage": 30,
    "amount": null,
    "date_created": "2015-03-24T21:26:09.000Z",
    "date_updated": "2015-03-24T21:26:09.000Z"
}, {
    "object": "split_rule",
    "id": "sr_ci7ntawl1001t2m1606u3e0uw",
    "recipient_id": "re_ci7nheu0m0006n016o5sglg9t",
    "charge_processing_fee": true,
    "liable": false,
    "percentage": 70,
    "amount": null,
    "date_created": "2015-03-24T21:26:09.000Z",
    "date_updated": "2015-03-24T21:26:09.000Z"
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação previamente criada |

## Retornando uma regra de divisão específica

> GET https://api.pagar.me/1/transactions/:transaction_id/split_rules/:id

```shell
curl -X GET https://api.pagar.me/1/transactions/189164/split_rules/sr_ci7ntawl1001s2m164zrbp7tz \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna os dados de uma regra de divisão de uma determinada transaçào.

> JSON Retornado (exemplo):

```json
{
    "object": "split_rule",
    "id": "sr_ci7ntawl1001s2m164zrbp7tz",
    "recipient_id": "re_ci7nhf1ay0007n016wd5t22nl",
    "charge_processing_fee": true,
    "liable": true,
    "percentage": 30,
    "amount": null,
    "date_created": "2015-03-24T21:26:09.000Z",
    "date_updated": "2015-03-24T21:26:09.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | Identificador da transação previamente criada |
| **:id**<br> <span class="required">obrigatório</span> | Identificador da regra de divisão |

## Retornando pagamentos da transação

> GET https://api.pagar.me/1/transactions/:transaction_id/payables

```shell
curl -X GET https://api.pagar.me/1/transactions/192669/payables \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna um array com objetos `payable` informando os dados dos pagamentos referentes a uma transação.

> JSON Retornado (exemplo):

```json
[{
    "object": "payable",
    "id": 1485,
    "status": "paid",
    "amount": 39000,
    "fee": 115,
    "installment": null,
    "transaction_id": 192669,
    "split_rule_id": "sr_ci87hce8o00083016bkniqems",
    "payment_date": "2015-04-07T03:00:00.000Z",
    "type": "credit",
    "payment_method": "boleto",
    "date_created": "2015-04-07T15:47:48.000Z"
}, {
    "object": "payable",
    "id": 1486,
    "status": "paid",
    "amount": 91000,
    "fee": 0,
    "installment": null,
    "transaction_id": 192669,
    "split_rule_id": "sr_ci87hce8o00093016fin8p6ll",
    "payment_date": "2015-04-07T03:00:00.000Z",
    "type": "credit",
    "payment_method": "boleto",
    "date_created": "2015-04-07T15:47:48.000Z"
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | Identificador da transação previamente criada |

## Retornando um pagamento da transação

> GET https://api.pagar.me/1/transactions/:transaction_id/payables/:id

```shell
curl -X GET https://api.pagar.me/1/transactions/192669/payables/1485 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna um objeto `payable` informando os dados de um pagamento referente a uma determinada transação.

> JSON Retornado (exemplo):

```json
{
    "object": "payable",
    "id": 1485,
    "status": "paid",
    "amount": 39000,
    "fee": 115,
    "installment": null,
    "transaction_id": 192669,
    "split_rule_id": "sr_ci87hce8o00083016bkniqems",
    "payment_date": "2015-04-07T03:00:00.000Z",
    "type": "credit",
    "payment_method": "boleto",
    "date_created": "2015-04-07T15:47:48.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | Identificador da transação previamente criada |
| **:id**<br> <span class="required">obrigatório</span> | Identificador do objeto `payable` |

## Retornando uma análise antifraude

> GET https://api.pagar.me/1/transactions/:transaction_id/antifraud_analyses/:id

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/antifraud_analyses/913456 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna uma análise antifraude específica realizada em uma transação.

> JSON Retornado (Exemplo)

```json
{
	"object": "antifraud_analysis",
	"name": "name",
	"score": "score",
	"cost": "cost",
	"status": "status",
	"date_created": "date_created",
	"date_updated": "date_updated",
	"id": "id"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |
| **:id**<br> <span class="required">obrigatório</span> | id da análise previamente feita |

## Retornando todas as análises antifraude

> GET https://api.pagar.me/1/transactions/:transaction_id/antifraud_analyses

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/antifraud_analyses \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna todas as análises antifraude realizadas em uma transação.

> JSON Retornado (Exemplo)

```json
[{
	"object": "antifraud_analysis",
	"name": "name",
	"score": "score",
	"cost": "cost",
	"status": "status",
	"date_created": "date_created",
	"date_updated": "date_updated",
	"id": "id"
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |

## Retornando um POSTback

> GET https://api.pagar.me/1/transactions/:transaction_id/postbacks/:id

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/postbacks/po_ciat6ssga0022k06ng8vxg \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna um POSTback específico relacionado a transação.

> JSON Retornado (Exemplo)

```json
{
        "date_created": "2015-06-12T05:41:57.000Z", 
        "date_updated": "2015-06-12T05:42:07.000Z", 
        "deliveries": [
            {
                "date_created": "2015-06-12T05:42:06.000Z", 
                "date_updated": "2015-06-12T05:42:07.000Z", 
                "id": "pd_ciat6szv2002yk06nyhacqmr4", 
                "object": "postback_delivery", 
                "response_body": "", 
                "response_headers": "{\"cache-control\":\"no-cache\",\"pragma\":\"no-cache\",\"content-length\":\"0\",\"expires\":\"-1\",\"server\":\"Microsoft-IIS/8.0\",\"x-aspnet-version\":\"4.0.30319\",\"x-powered-by\":\"ASP.NET\",\"set-cookie\":[\"ARRAffinity=663d85223525d21e72aebd941082ca482841f5719c27124196939b3de6204504;Path=/;Domain=requestb.in\"],\"date\":\"Fri, 12 Jun 2015 05:42:06 GMT\",\"connection\":\"close\"}", 
                "response_time": 516, 
                "status": "success", 
                "status_code": "200", 
                "status_reason": "http_status_code"
            }
        ], 
        "headers": "{\"Content-Type\":\"application/x-www-form-urlencoded\",\"X-PagarMe-Event\":\"transaction_status_changed\",\"X-Hub-Signature\":\"sha1=d825b60eee7f3034484be584ccca20d3f7bb8c5b\",\"User-Agent\":\"PagarMe-Hookshot/1.0\"}", 
        "id": "po_ciat6ssga0022k06ng8vxg", 
        "model": "transaction", 
        "model_id": "674579", 
        "next_retry": null, 
        "object": "postback", 
        "payload": "id=674579&fingerprint=05112b2b5d756a1501d994027c95d3202c7b&event=transaction_status_changed&old_status=authorized&desired_status=paid&current_status=refused&object=transaction", 
        "request_url": "http://requestb.in/1azqnq81?inspect", 
        "retries": 0, 
        "signature": "d825b60eee7f3034484be584d3f7bb8c5b", 
        "status": "success"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |
| **:id**<br> <span class="required">obrigatório</span> | id do POSTback |

## Retornando todos os POSTbacks

> GET https://api.pagar.me/1/transactions/:transaction_id/postbacks

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/postbacks \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna todos os POSTbacks enviados relacionados a transação.

> JSON Retornado (Exemplo)

```json
[
    {
        "date_created": "2015-06-12T05:41:57.000Z", 
        "date_updated": "2015-06-12T05:42:07.000Z", 
        "deliveries": [
            {
                "date_created": "2015-06-12T05:42:06.000Z", 
                "date_updated": "2015-06-12T05:42:07.000Z", 
                "id": "pd_ciat6szv2002yk06nyhacqmr4", 
                "object": "postback_delivery", 
                "response_body": "", 
                "response_headers": "{\"cache-control\":\"no-cache\",\"pragma\":\"no-cache\",\"content-length\":\"0\",\"expires\":\"-1\",\"server\":\"Microsoft-IIS/8.0\",\"x-aspnet-version\":\"4.0.30319\",\"x-powered-by\":\"ASP.NET\",\"set-cookie\":[\"ARRAffinity=663d85223525d21e72aebd941082ca482841f5719c27124196939b3de6204504;Path=/;Domain=requestb.in\"],\"date\":\"Fri, 12 Jun 2015 05:42:06 GMT\",\"connection\":\"close\"}", 
                "response_time": 516, 
                "status": "success", 
                "status_code": "200", 
                "status_reason": "http_status_code"
            }
        ], 
        "headers": "{\"Content-Type\":\"application/x-www-form-urlencoded\",\"X-PagarMe-Event\":\"transaction_status_changed\",\"X-Hub-Signature\":\"sha1=d825b60eee7f3034484be584ccca20d3f7bb8c5b\",\"User-Agent\":\"PagarMe-Hookshot/1.0\"}", 
        "id": "po_ciat6ssga0022k06ng8vxg", 
        "model": "transaction", 
        "model_id": "674579", 
        "next_retry": null, 
        "object": "postback", 
        "payload": "id=674579&fingerprint=05112b2b5d756a1501d994027c95d3202c7b&event=transaction_status_changed&old_status=authorized&desired_status=paid&current_status=refused&object=transaction", 
        "request_url": "http://requestb.in/1azqnq81?inspect", 
        "retries": 0, 
        "signature": "d825b60eee7f3034484be584d3f7bb8c5b", 
        "status": "success"
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |

## Reenviando um POSTback

> POST https://api.pagar.me/1/transactions/:transaction_id/postbacks/:id/redeliver

```shell
curl -X POST https://api.pagar.me/1/transactions/314578/postbacks/po_ciat6ssga0022k06ng8vxg/redeliver \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Com essa rota você pode reenviar qualquer POSTback que já foi enviado de uma transação. Lembrando que caso o envio de um POSTback falhe ou seu servidor não o receba, nós o retentamos diversas vezes (com um total de 31 vezes). 

> JSON Retornado (Exemplo)

```json
{
        "date_created": "2015-06-12T05:41:57.000Z", 
        "date_updated": "2015-06-12T05:42:07.000Z", 
        "deliveries": [
            {
                "date_created": "2015-06-12T05:42:06.000Z", 
                "date_updated": "2015-06-12T05:42:07.000Z", 
                "id": "pd_ciat6szv2002yk06nyhacqmr4", 
                "object": "postback_delivery", 
                "response_body": "", 
                "response_headers": "{\"cache-control\":\"no-cache\",\"pragma\":\"no-cache\",\"content-length\":\"0\",\"expires\":\"-1\",\"server\":\"Microsoft-IIS/8.0\",\"x-aspnet-version\":\"4.0.30319\",\"x-powered-by\":\"ASP.NET\",\"set-cookie\":[\"ARRAffinity=663d85223525d21e72aebd941082ca482841f5719c27124196939b3de6204504;Path=/;Domain=requestb.in\"],\"date\":\"Fri, 12 Jun 2015 05:42:06 GMT\",\"connection\":\"close\"}", 
                "response_time": 516, 
                "status": "success", 
                "status_code": "200", 
                "status_reason": "http_status_code"
            },
			{
                "date_created": "2015-06-18T05:42:06.000Z", 
                "date_updated": "2015-06-18T05:42:07.000Z", 
                "id": "pd_ciat6szv2002yk06nyhasdasd5", 
                "object": "postback_delivery", 
                "response_body": "", 
                "response_headers": "{\"cache-control\":\"no-cache\",\"pragma\":\"no-cache\",\"content-length\":\"0\",\"expires\":\"-1\",\"server\":\"Microsoft-IIS/8.0\",\"x-aspnet-version\":\"4.0.30319\",\"x-powered-by\":\"ASP.NET\",\"set-cookie\":[\"ARRAffinity=663d85223525d21e72aebd941082ca482841f5719c27124196939b3de6204504;Path=/;Domain=requestb.in\"],\"date\":\"Fri, 18 Jun 2015 05:42:06 GMT\",\"connection\":\"close\"}", 
                "response_time": 510, 
                "status": "success", 
                "status_code": "200", 
                "status_reason": "http_status_code"
            }
        ], 
        "headers": "{\"Content-Type\":\"application/x-www-form-urlencoded\",\"X-PagarMe-Event\":\"transaction_status_changed\",\"X-Hub-Signature\":\"sha1=d825b60eee7f3034484be584ccca20d3f7bb8c5b\",\"User-Agent\":\"PagarMe-Hookshot/1.0\"}", 
        "id": "po_ciat6ssga0022k06ng8vxg", 
        "model": "transaction", 
        "model_id": "674579", 
        "next_retry": null, 
        "object": "postback", 
        "payload": "id=674579&fingerprint=05112b2b5d756a1501d994027c95d3202c7b&event=transaction_status_changed&old_status=authorized&desired_status=paid&current_status=refused&object=transaction", 
        "request_url": "http://requestb.in/1azqnq81?inspect", 
        "retries": 0, 
        "signature": "d825b60eee7f3034484be584d3f7bb8c5b", 
        "status": "success"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |
| **:id**<br> <span class="required">obrigatório</span> | id do POSTback |

## Retornando todos os eventos de uma transação

> GET https://api.pagar.me/1/transactions/:transaction_id/events

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/events \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna todos os eventos já criados dentro de uma transação. <br>**Ex**: mudanças de status.

> JSON Retornado (Exemplo)

```json
[
    {
        "id": "ev_cift4mmt800t7z55z343v6xto", 
        "model": "transaction", 
        "model_id": "314578", 
        "name": "transaction_status_changed", 
        "object": "event", 
        "payload": {
            "current_status": "paid", 
            "desired_status": "paid", 
            "old_status": "processing"
        }
    }, 
    {
        "id": "ev_cift4nz1200t8zda33zh7zilzkt", 
        "model": "transaction", 
        "model_id": "314578", 
        "name": "transaction_status_changed", 
        "object": "event", 
        "payload": {
            "current_status": "refunded", 
            "desired_status": "refunded", 
            "old_status": "paid"
        }
    }
]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |

## Retornando todo histórico de uma transação

> GET https://api.pagar.me/1/transactions/:transaction_id/operations

```shell
curl -X GET https://api.pagar.me/1/transactions/314578/operations \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna todo o histórico de uma transação, ou seja, toda e qualquer operação que já aconteceu com ela. <br><br>**Ex**: autorização, análise antifraude, captura, estorno, chargeback, emissão de boleto, conciliação, etc.

> JSON Retornado (Exemplo)

```json
[
    {
        "date_created": "2015-10-16T03:59:40.000Z", 
        "date_updated": "2015-10-16T03:59:42.000Z", 
        "ended_at": 1444967982134, 
        "fail_reason": null, 
        "group_id": "gog_cift4ml505xsfce3ff9o8xgyv", 
        "id": "go_cift4ml505xsece3fqltn2ok0", 
        "metadata": {
            "environment": {}
        }, 
        "model": "transaction", 
        "model_id": "314578", 
        "next_group_id": "gog_cift4nxyp5mj9on3ehfthtxiy", 
        "processor": "pagarme", 
        "processor_response_code": null, 
        "request_id": "gr_cift4ml3c5xsdce3fistg0u6q", 
        "rollbacked": false, 
        "started_at": 1444967981743, 
        "status": "success", 
        "type": "capture"
    }, 
    {
        "date_created": "2015-10-16T03:59:40.000Z", 
        "date_updated": "2015-10-16T03:59:41.000Z", 
        "ended_at": 1444967981722, 
        "fail_reason": null, 
        "group_id": "gog_cift4ml5k5xshce3fqqjaeijr", 
        "id": "go_cift4ml5k5xsgce3fjelkr3c6", 
        "metadata": {
            "environment": {
                "authorization_code": "07482", 
                "nsu": "314578", 
                "response_code": "0000", 
                "tid": "314578"
            }
        }, 
        "model": "transaction", 
        "model_id": "314578", 
        "next_group_id": "gog_cift4ml505xsfce3ff9o8xgyv", 
        "processor": "pagarme", 
        "processor_response_code": "0000", 
        "request_id": "gr_cift4ml3c5xsdce3fistg0u6q", 
        "rollbacked": false, 
        "started_at": 1444967980241, 
        "status": "success", 
        "type": "authorize"
    }, 
    {
        "date_created": "2015-10-16T04:00:43.000Z", 
        "date_updated": "2015-10-16T04:00:44.000Z", 
        "ended_at": 1444968044662, 
        "fail_reason": null, 
        "group_id": "gog_cift4nxyp5mj9on3ehfthtxiy", 
        "id": "go_cift4nxyp5mj8on3e6e2k1d6t", 
        "metadata": {
            "environment": {}
        }, 
        "model": "transaction", 
        "model_id": "314578", 
        "next_group_id": null, 
        "processor": "pagarme", 
        "processor_response_code": null, 
        "request_id": "gr_cift4nxy15mj7on3edu9sf4fj", 
        "rollbacked": false, 
        "started_at": 1444968043510, 
        "status": "success", 
        "type": "refund"
    }]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:transaction_id**<br> <span class="required">obrigatório</span> | id da transação |

## Notificando cliente sobre boleto a ser pago

> POST https://api.pagar.me/1/transactions/:id/collect_payment

```shell
curl -X POST https://api.pagar.me/1/transactions/314578/collect_payment \
-d 'api_key=ak_live_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'email=seu@email.com'
```

```ruby
```

```php
```

```cs
```

Envia o link de um boleto pendente para o cliente.

**OBS**: Essa rota não funciona em ambiente de testes.

> JSON Retornado (Exemplo)

```json
{ }
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | id da transação |
| **email**<br> <span class="required">obrigatório</span> | email a ser enviado o link do boleto |

## Capturando uma transação posteriormente

> POST https://api.pagar.me/1/transactions/:id/capture

```shell
curl -X POST https://api.pagar.me/1/transactions/314578/capture \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

transaction = PagarMe::Transaction.find_by_id("1234")

transaction.capture({:amount => 1000})
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$t = new PagarMe_Transaction(array(
	  "amount" => 3100,
	  "card_id" => "card_ci6l9fx8f0042rt16rtb477gj",
	  "postback_url" => "http://requestb.in/1ahq78t1",
	  "capture" => "false",
	  "metadata" => array(
		"idProduto" => 13933139
	  )
	));

	$t->charge();

	$t->capture(3100);
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 3100;
transaction.CardId = "card_ci6l9fx8f0042rt16rtb477gj";
transaction.PostbackUrl = "http://requestb.in/pkt7pgpk";
transaction.Metadata = new Metadata() {
    IdProduto = 13933139
};
transaction.Save();

transaction.Capture(3100);
```

Você pode capturar o valor de uma transação após a autorização desta, no prazo máximo de 5 dias após a autorização.

> JSON Retornado (Exemplo)

```json
{
    "object": "transaction",
    "status": "authorized",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": "00",
    "acquirer_name": "development",
    "authorization_code": "132534",
    "soft_descriptor": "testeDeApi",
    "tid": "1425302906112",
    "nsu": "1425302906112",
    "date_created": "2015-03-02T13:28:25.000Z",
    "date_updated": "2015-03-02T13:28:26.000Z",
    "amount": 130000,
    "installments": 1,
    "id": 184622,
    "cost": 0,
    "postback_url": "http://requestb.in/pkt7pgpk",
    "payment_method": "credit_card",
    "antifraud_score": null,
    "boleto_url": null,
    "boleto_barcode": null,
    "boleto_expiration_date": null,
    "referer": "api_key",
    "ip": "189.8.94.42",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": {
        "object": "card",
        "id": "card_ci6l9fx8f0042rt16rtb477gj",
        "date_created": "2015-02-25T21:54:56.000Z",
        "date_updated": "2015-02-25T21:54:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": {
        "nomeData": "API Doc test",
        "idData": "13"
    }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da transação a ser capturada |

## Estorno de transação 

> POST https://api.pagar.me/1/transactions/:id/refund

```shell
curl -X POST https://api.pagar.me/1/transactions/314578/refund \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```shell
# Estorno de transação paga com boleto bancário

curl -X POST https://api.pagar.me/1/transactions/314578/refund \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'bank_account[bank_code]=111' \
-d 'bank_account[agencia]=1234' \
-d 'bank_account[conta]=09876' \
-d 'bank_account[conta_dv]=1' \
-d 'bank_account[document_number]=12312312312' \
-d 'bank_account[legal_name]=joao miranda'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

transaction = PagarMe::Transaction.find_by_id("1234")

transaction.refund
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$t = new PagarMe_Transaction(array(
	  "amount" => 3100,
	  "card_id" => "card_ci6l9fx8f0042rt16rtb477gj",
	  "postback_url" => "http://requestb.in/1ahq78t1",
	  "capture" => "false",
	  "metadata" => array(
		"idProduto" => 13933139
	  )
	));

	$t->charge();

	$t->refund();
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 3100;
transaction.CardId = "card_ci6l9fx8f0042rt16rtb477gj";
transaction.PostbackUrl = "http://requestb.in/pkt7pgpk";
transaction.Metadata = new Metadata() {
    IdProduto = 13933139
};
transaction.Save();

transaction.Refund();
```

Essa rota é utilizada quando se deseja estornar uma transação, realizada por uma cobrança via cartão de crédito ou boleto bancário.

Em caso de estorno de uma transação realizada com cartão de crédito, apenas o `id` da transação é necessário para efetivação do estorno.

Caso a compra tenha sido feita por boleto bancário, você precisará passar os dados da conta bancária que irá receber o valor estornado, ou o id desta conta, que pode ser gerada através da rota `/bank_accounts`.  



> JSON Retornado - Estorno de Cartão de Crédito (Exemplo)


```json 
{
  "object": "transaction",
  "status": "refunded",
  "refuse_reason": null,
  "status_reason": "acquirer",
  "acquirer_response_code": "00",
  "acquirer_name": "pagarme",
  "authorization_code": "40777",
  "soft_descriptor": null,
  "tid": 545176,
  "nsu": 545176,
  "date_created": "2016-07-01T01:16:09.145Z",
  "date_updated": "2016-07-01T01:16:40.736Z",
  "amount": 10000,
  "authorized_amount": 10000,
  "paid_amount": 10000,
  "refunded_amount": 10000,
  "installments": 1,
  "id": 545176,
  "cost": 0,
  "card_holder_name": "Aardvark Silva",
  "card_last_digits": "4242",
  "card_first_digits": "424242",
  "card_brand": "visa",
  "postback_url": null,
  "payment_method": "credit_card",
  "capture_method": "ecommerce",
  "antifraud_score": null,
  "boleto_url": null,
  "boleto_barcode": null,
  "boleto_expiration_date": null,
  "referer": "session_id",
  "ip": "132.125.152.103",
  "subscription_id": null,
  "phone": {
    "object": "phone",
    "ddi": "55",
    "ddd": "11",
    "number": "15110808",
    "id": 36183
  },
  "address": {
    "object": "address",
    "street": "Avenida Brigadeiro Faria Lima",
    "complementary": null,
    "street_number": "1811",
    "neighborhood": "Jd. Paulistano",
    "city": "São Paulo",
    "state": "SP",
    "zipcode": "01451001",
    "country": "Brasil",
    "id": 37461
  },
  "customer": {
    "object": "customer",
    "document_number": "11122233389",
    "document_type": "cpf",
    "name": "Aardvark Silva",
    "email": "aardvark.silva@pagar.me",
    "born_at": null,
    "gender": null,
    "date_created": "2016-06-29T16:34:39.615Z",
    "id": 76762
  },
  "card": {
    "object": "card",
    "id": "card_ciq13rfsh00wxru6eq00ndqkf",
    "date_created": "2016-06-29T16:34:39.666Z",
    "date_updated": "2016-07-01T01:16:09.130Z",
    "brand": "visa",
    "holder_name": "Aardvark Silva",
    "first_digits": "424242",
    "last_digits": "4242",
    "country": "US",
    "fingerprint": "8Z6Lxj449c8M",
    "valid": true,
    "expiration_date": "1119"
  },
  "split_rules": null,
  "metadata": {},
  "antifraud_metadata": {}
}
```


> JSON Retornado - Estorno de Boleto Bancário (Exemplo)

```json
{
    "object": "transaction",
    "status": "pending_refund",
    "refuse_reason": null,
    "status_reason": "acquirer",
    "acquirer_response_code": null,
    "acquirer_name": "development",
    "authorization_code": null,
    "soft_descriptor": null,
    "tid": null,
    "nsu": null,
    "date_created": "2015-02-26T19:50:38.000Z",
    "date_updated": "2015-03-02T17:38:10.000Z",
    "amount": 3100000,
    "installments": 1,
    "id": 184306,
    "cost": 115,
    "postback_url": "http://requestb.in/pkt7pgpk",
    "payment_method": "boleto",
    "antifraud_score": null,
    "boleto_url": "https://pagar.me",
    "boleto_barcode": "1234 5678",
    "boleto_expiration_date": "2015-03-13T03:00:00.000Z",
    "referer": "api_key",
    "ip": "189.8.94.42",
    "subscription_id": null,
    "phone": null,
    "address": null,
    "customer": null,
    "card": {
        "object": "card",
        "id": "card_ci6l9fx8f0042rt16rtb477gj",
        "date_created": "2015-02-25T21:54:56.000Z",
        "date_updated": "2015-02-25T21:54:57.000Z",
        "brand": "mastercard",
        "holder_name": "Api Customer",
        "first_digits": "548045",
        "last_digits": "3123",
        "fingerprint": "HSiLJan2nqwn",
        "valid": true
    },
    "metadata": {}
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | id da transação |
| **bank_account_id**<br> <span class="required">obrigatório\*</span> | Se você tiver o id de uma conta previamente criada, você pode passar apenas seu id. Caso a conta ainda não exista, você pode [criar uma conta](/#criando-uma-conta-bancaria) ou passar os dados da conta via parâmetros |
| **bank_code**<br> <span class="required">obrigatório\*</span> | Dígitos que identificam cada banco. Confira a lista dos bancos [aqui](http://www.febraban.org.br/arquivo/bancos/sitebancos2-0.asp) |
| **agencia**<br> <span class="required">obrigatório\*</span> | Número da agência bancária |
| **agencia_dv** | Digito verificador da agência. Obrigatório caso o banco o utilize |
| **conta**<br> <span class="required">obrigatório\*</span> | Número da conta |
| **conta_dv**<br> <span class="required">obrigatório\*</span> | Dígito verificador da conta. Obrigatório caso o banco o utilize |
| **document_number**<br> <span class="required">obrigatório\*</span> | CPF ou CNPJ do favorecido |
| **legal_name**<br> <span class="required">obrigatório\*</span> | Nome/razão social do favorecido |


## Estorno Parcial de uma transação

> POST https://api.pagar.me/1/transactions/:id/refund

```shell
curl -X POST https://api.pagar.me/1/transactions/562797/refund \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'amount=1000'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

transaction = PagarMe::Transaction.find_by_id("1234")

transaction.refund
```

```php

<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");
	
	$t = PagarMe_Transaction::findById("562797");
	
	$params = array("amount" => 1000);

	$t->refund($params);

?>

```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var transaction = PagarMeService.GetDefaultService().Transactions.Find("562797");

transaction.Refund(1000);
```


O estorno parcial obedece as mesmas regras de um estorno total, e usa o parâmetro `amount` como referência para o valor 
a ser estornado. Entretanto, só pode ser usado para transações pagas por cartão de crédito, e com a observação de que
o `status` da transação vai permanecer `paid` até que o valor total da transação tenha sido estornado.

> JSON Retornado

```json
{
"object":"transaction",
	"status":"paid",
	"refuse_reason":null,
	"status_reason":"acquirer",
	"acquirer_response_code":"00",
	"acquirer_name":"pagarme",
	"authorization_code":"845088",
	"soft_descriptor":null,
	"tid":562797,
	"nsu":562797,
	"date_created":"2016-07-10T17:09:41.289Z",
	"date_updated":"2016-07-11T02:14:53.547Z",
	"amount":9900,
	"authorized_amount":9900,
	"paid_amount":9900,
	"refunded_amount":1000,
	"installments":1,
	"id":562797,
	"cost":50,
	"card_holder_name":"John Smith",
	"card_last_digits":"4242",
	"card_first_digits":"424242",
	"card_brand":"visa",
	"postback_url":null,
	"payment_method":"credit_card",
	"capture_method":"ecommerce",
	"antifraud_score":null,
	"boleto_url":null,
	"boleto_barcode":null,
	"boleto_expiration_date":null,
	"referer":"session_id",
	"ip":"162.15.6.27",
	"subscription_id":71865,
	"phone":null,
	"address":null,
	"customer":{
		"object":"customer",
		"document_number":null,
		"document_type":"cpf",
		"name":null,
		"email":"john.smith@emailg.com",
		"born_at":null,
		"gender":null,
		"date_created":"2016-07-10T17:09:41.236Z",
		"id":79118},
	"card":{
		"object":"card",
		"id":"card_ciqguuuqk001dkg6erhqzh4jf",
		"date_created":"2016-07-10T17:09:41.278Z",
		"date_updated":"2016-07-10T17:09:41.834Z",
		"brand":"visa",
		"holder_name":"John Smith",
		"first_digits":"424242",
		"last_digits":"4242",
		"country":"US",
		"fingerprint":"8Z6Lxj449c8M",
		"valid":true,
		"expiration_date":"1119"
	},
	"split_rules":null,
	"metadata":{},
	"antifraud_metadata":{}
}
```
| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | id da transação |
| **amount**<br><span class="required">obrigatório</span> | Valor desejado para o estorno da transação|

## Status das transações

Quando uma transação é criada, ela inicialmente é retornada com o status `processing`. Após ser processada, ela pode ter os seguintes status:

- `processing`: transação sendo processada.
- `authorized`: transação autorizada. Cliente possui saldo na conta e este valor foi reservado para futura captura, que deve acontecer em no máximo 5 dias. Caso a transação **não seja capturada**, a autorização é cancelada automaticamente.
- `paid`: transação paga (autorizada e capturada).
- `refunded`: transação estornada.
- `waiting_payment`: transação aguardando pagamento (status para transações criadas com boleto bancário).
- `pending_refund`: transação paga com boleto aguardando para ser estornada.
- `refused`: transação não autorizada.
- `chargedback`: transação sofreu chargeback.


## Calculando Pagamentos Parcelados

> GET https://api.pagar.me/1/transactions/calculate_installments_amount

```shell
curl -X GET https://api.pagar.me/1/transactions/calculate_installments_amount \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0 \
-d 'max_installments=3' \
-d 'free_installments=1' \
-d 'interest_rate=13' \
-d 'amount=10000'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

installments_result = PagarMe::Transaction.calculate_installments({
    amount: 10000,
    interest_rate: 13
})
```

```php
```

```cs
```

Usada para calcular o valor de cada uma das parcelas de uma compra.

> JSON retornado (exemplo):

```json
{
    "installments": {
        "1": {
            "installment": 1,
            "amount": 10000,
            "installment_amount": 10000
        },
        "2": {
            "installment": 2,
            "amount": 10598,
            "installment_amount": 5299
        },
        "3": {
            "installment": 3,
            "amount": 10897,
            "installment_amount": 3632
        }
    }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **max_installments**<br> default: `12` | Valor máximo de parcelas |
| **free_installments**<br> default: `1` | Número de parcelas isentas de juros |
| **interest_rate**<br> <span class="required">obrigatório</span> | Valor da taxa de juros |
| **amount**<br> <span class="required">obrigatório</span> | Valor do produto/serviço vendido |

## Testando pagamento de Boletos

> PUT https://api.pagar.me/1/transactions/:id

```shell
curl -X PUT https://api.pagar.me/1/transactions/260582 \
-d 'api_key=ak_test_3343DSY7DWVzOXSz3xjvzIpBME4afc' \
-d 'status=paid'
```

```ruby
```

```php
```

```cs
```

Usado **apenas em ambiente de Teste** para simular o pagamento de um Boleto.

> JSON retornado (exemplo):

```json
{
    "object":"transaction",
    "status":"paid",
    "refuse_reason":null,
    "status_reason":"acquirer",
    "acquirer_response_code":null,
    "acquirer_name":"development",
    "authorization_code":null,
    "soft_descriptor":null,
    "tid":null,
    "nsu":null,
    "date_created":"2015-08-27T17:53:56.000Z",
    "date_updated":"2015-08-27T18:01:24.000Z",
    "amount":25000,
    "installments":1,
    "id":260582,
    "cost":380,
    "card_holder_name":null,
    "card_last_digits":null,
    "card_first_digits":null,
    "card_brand":null,
    "postback_url":"",
    "payment_method":"boleto",
    "antifraud_score":null,
    "boleto_url":"https://pagar.me",
    "boleto_barcode":"1234 5678",
    "boleto_expiration_date":"2015-09-03T03:00:00.000Z",
    "referer":"api_key",
    "ip":"180.185.133.109",
    "subscription_id":null,
    "phone":null,
    "address":null,
    "customer":null,
    "card":null,
    "metadata":{
    },
    "antifraud_metadata":{
    }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **status**<br> <span class="required">obrigatório</span> | Utilize o valor **paid** para simular o pagamento |

## Gerando card_hash manualmente

O `card_hash` consiste em uma string gerada a partir dos dados do cartão de crédito. Essa string é encriptada por RSA usando uma chave pública que deve ser requisitada ao servidor a cada novo `card_hash` gerado. Essa chave é invalidada assim que o servidor lê as informações contidas no `card_hash`, e por isso só pode ser utilizada uma única vez. Ela também é temporária, expirando 5 minutos após ter sido gerada.

As bibliotecas do Pagar.me sempre utilizam o `card_hash` para enviar os dados para o servidor, o que aumenta consideravelmente a segurança da transação.

### Requisitando ao servidor uma chave para encriptar o `card_hash`

> GET https://api.pagar.me/1/transactions/card_hash_key

```shell
curl -X GET https://api.pagar.me/1/transactions/card_hash_key \
-d 'encryption_key=ek_test_9741a03ea3a4f15f6fa8d9fe9d2c47c8' \
```

```ruby
```

```php
```

```cs
```

Para gerarmos o `card_hash`, primeiramente devemos requisitar do servidor a chave pública para criptografarmos nossos dados.

> JSON retornado (exemplo):

```json
{
  "date_created": "2015-02-27T15:44:26.000Z",
  "id": 111111,
  "ip": "000.0.00.00",
  "public_key": "-----BEGIN PUBLIC KEY-----\ -----END PUBLIC KEY-----\ " 
}
```

| Propriedade | Descrição |
|--:|:--|
| **id**<br> Number | id retornado que será utilizado posterirormente para compor o `card_hash`, por isso é importante que você o salve |
| **public_key**<br> String | Chave pública utilizada para criptografar os dados do cartão de crédito |
| **ip**<br> String | IP de onde o request foi originado |

### Encriptando os dados do cartão de crédito

Agora você vai ter que criar uma QueryString com valores URLEncoded para os parâmetros do cartão de crédito. Vamos pegar os seguintes dados abaixo como exemplo:

- card_number = `4901720080344448`
- card_holder_name = `"Usuario de Teste"`
- card_expiration_date = `1213`
- card_cvv = `314`

A querystring será da seguinte forma:

`
card_number=4901720080344448&card_holder_name=Usuario%20de%20Teste&card_expiration_date=1213&card_cvv=314
`

Agora você vai fazer uma criptografia publica com `RSA` e o padding `PKCS1Padding` usando a public_key que você recebeu na request passando essa QueryString que você montou.

Após criptografar esses dados você deve converter o resultado para base64. Como resultado você terá:

`
FFtwikzg/FC1mH7XLFU5fjPAzDsP0ogeAQh3qXRpHzkIrgDz64lITBUGwio67zm2CQXwbKRjGdRi5J1xFNpQLWnxQsUJAQELcTSGaGtF6RGSu6sq1stp8OLRSNG7wp+xGe8poqxw4S1gOL5JYO7XZp/Uz7rTpKXh3IcRshmX36hh66J6+7l5j0803cGIfMZu3T7nbMjQYIf+yLi8r0O6vL9DQPmqSZ9FBerqFGxWHrxScneaaMVzMpNX/5eneqveVBt88RccytyJG5+HYRHcRyKIbLfmX48L/C22HJeAm3PyzehGHdOmDcsxPtVB+Fgq7SDuB4tHWBT8j6wihOO7ww==
`

Agora que você possui o id vindo do request e o os dados criptografados convertidos para base64, seu `card_hash` deverá ser formatado da seguinte maneira:

**`
card_hash = id + "_" + encrypted_string_base64
`**

