# Recebedores

## Objeto `recipient`

> Objeto recipient

```json
{
    "object": "recipient",
    "id": "re_ci9bucss300h1zt6dvywufeqc",
    "bank_account": {
        "object": "bank_account",
        "id": 4841,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:40:51.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "automatic_anticipation_enabled": true,
    "anticipatable_volume_percentage": 85,
    "date_created": "2015-05-05T21:41:48.000Z",
    "date_updated": "2015-05-05T21:41:48.000Z"
}
```

Objeto contendo os dados de um recebedor.

| Propriedade | Descrição |
|--:|:--|
| **object**<br> String | Nome do tipo do objeto criado/modificado. <br> **Valor retornado**: `recipient` |
| **id**<br> String | Identificador do recebedor |
| **bank_account**<br> Object | Objeto contendo os dados bancários do recebedor |
| **transfer_enabled**<br> Boolean | Identifica se o recebedor está habilitado para receber automaticamente ou não |
| **last_transfer**<br> String | Data da última transferência (ISODate) |
| **transfer_interval**<br> String | Frequência na qual o recebedor irá ser pago. <br> **Valores possíveis**: `daily`, `weekly`, `monthly` |
| **transfer_day**<br> Number | Dia no qual o recebedor vai ser pago. Para cada `transfer_day`, existe uma faixa de pagamento possível. <br> `weekly`: 1 a 5, onde 1 é segunda-feira e 5 é sexta-feira <br> `monthly`: 1 a 31 |
| **automatic_anticipation_enabled**<br> Boolean | Identifica se o recebedor está habilitado para receber automaticamente ou não o valor disponível para antecipação |
| **anticipatable_volume_percentage**<br> Number | Porcentagem do valor passível de antecipação que será antecipado automaticamente |
| **date_created**<br> String | Data de criação do recebedor (ISODate) |
| **date_updated**<br> String | Data de atualização do recebedor |

## Criando um recebedor

> POST https://api.pagar.me/1/recipients

```shell
curl -X POST https://api.pagar.me/1/recipients \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'transfer_interval=weekly' \
-d 'transfer_day=5' \
-d 'transfer_enabled=true' \
-d 'automatic_anticipation_enabled=true' \
-d 'anticipatable_volume_percentage=85'
-d 'bank_account_id=4841'
```

```ruby
```

```php
<?php
	require("pagarme-php/Pagarme.php");
	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");
	
	$recipient = new PagarMe_Recipient(array(
		"transfer_interval" => "weekly",
		"transfer_day" => 5,
		"transfer_enabled" => true,
		"automatic_anticipation_enabled" => true,
		"anticipatable_volume_percentage" => 85,
		"bank_account_id" => 4841
	));
	$recipient->create();
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Recipient recipient = new Recipient();
recipient.TransferInterval = TransferInterval.Weekly;
recipient.TransferDay = 5;
recipient.TransferEnabled = true;
recipient.BankAccount = PagarMeService.GetDefaultService().BankAccounts.Find("4840");
recipient.Save();
```

Com essa rota você consegue criar um recebedor, definindo o período que ele irá receber os pagamentos e qual a conta bancária que será utilizada para envio dos pagamentos.

> JSON Retornado (Exemplo)

```json
{
    "object": "recipient",
    "id": "re_ci9bucss300h1zt6dvywufeqc",
    "bank_account": {
        "object": "bank_account",
        "id": 4841,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:40:51.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "automatic_anticipation_enabled": true,
    "anticipatable_volume_percentage": 85,
    "date_created": "2015-05-05T21:41:48.000Z",
    "date_updated": "2015-05-05T21:41:48.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **transfer_interval**<br> <span class="required">obrigatório</span> | Frequência na qual o recebedor irá ser pago. <br> **Valores possíveis**: `daily`, `weekly`, `monthly` |
| **transfer_day**<br> <span class="required">obrigatório</span> | Dia no qual o recebedor vai ser pago. |
| **transfer_enabled**<br> <span class="required">obrigatório</span> | Variável que indica se o recebedor pode receber os pagamentos automaticamente |
| **bank_account_id**<br> <span class="required">obrigatório\*</span> | Identificador de uma conta bancária previamente criada. Caso você não tenha essa informação, você pode passar os parâmetros necessários para [criação de uma conta bancária](/#criando-uma-conta-bancaria) | 
| **bank_account**<br> <span class="required">obrigatório\*</span> | Objeto contendo os dados bancários do recebedor. Este objeto, e as suas respectivas propriedades, serão obrigatórios caso não seja informado um `bank_account_id` |
| **bank_account[bank_code]** | Código do banco do recebedor |
| **bank_account[agencia]** | Agência da conta do recebedor |
| **bank_account[conta]** | Número da conta bancária do recebedor |
| **bank_account[conta_dv]** | Dígito verificador da conta do recebedor |
| **bank_account[document_number]** | CPF ou CNPJ do recebedor |
| **bank_account[legal_name]** | Nome completo ou razão social do recebedor |

## Retornando todos os recebedores

> GET https://api.pagar.me/1/recipients

```shell
curl -X GET https://api.pagar.me/1/recipients \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
<?php
	require("pagarme-php/Pagarme.php");
	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$recipients = PagarMe_Recipient::all(1, 10);
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";


var pagarmeRequest = PagarMeService.GetDefaultService().Recipients.BuildFindQuery(new Recipient ());

// Mude/Adicione a query String o que você deseja, e.g: count = 50
var newTupleForQueryString = Tuple.Create("count", "50");

pagarmeRequest.Query.Add(newTupleForQueryString);

var recipients = PagarMeService.GetDefaultService ().Recipients.FinishFindQuery (pagarmeRequest.Execute ());




```

Retorna um `Array` de objetos com todos os recebedores criados pela sua companhia.

> JSON Retornado (Exemplo)

```json
[{
    "object": "recipient",
    "id": "re_ci7nhf1ay0007n016wd5t22nl",
    "bank_account": {
        "object": "bank_account",
        "id": 4901,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": null,
        "conta": "58999",
        "conta_dv": "3",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "RECIPIENT TEST",
        "charge_transfer_fees": true,
        "date_created": "2015-03-24T15:53:17.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "date_created": "2015-03-24T15:53:27.000Z",
    "date_updated": "2015-03-24T15:53:27.000Z"
}, {
    "object": "recipient",
    "id": "re_ci7nheu0m0006n016o5sglg9t",
    "bank_account": {
        "object": "bank_account",
        "id": 4901,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": null,
        "conta": "58999",
        "conta_dv": "3",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "RECIPIENT TEST",
        "charge_transfer_fees": true,
        "date_created": "2015-03-24T15:53:17.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "date_created": "2015-03-24T15:53:17.000Z",
    "date_updated": "2015-03-24T15:53:17.000Z"
}, {
    "object": "recipient",
    "id": "re_ci7ng63iv00bdp8164c05ggi9",
    "bank_account": {
        "object": "bank_account",
        "id": 4841,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:40:51.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "date_created": "2015-03-24T15:18:30.000Z",
    "date_updated": "2015-03-24T15:18:30.000Z"
}, {
    "object": "recipient",
    "id": "re_ci76hxnym00b8dw16y3hdxb21",
    "bank_account": {
        "object": "bank_account",
        "id": 1774,
        "bank_code": "000",
        "agencia": "0000",
        "agencia_dv": null,
        "conta": "00000",
        "conta_dv": "0",
        "document_type": "cnpj",
        "document_number": "00000000000000",
        "legal_name": "CONTA BANCARIA DE TESTES",
        "charge_transfer_fees": true,
        "date_created": "2015-03-12T18:35:51.000Z"
    },
    "transfer_enabled": false,
    "last_transfer": null,
    "transfer_interval": null,
    "transfer_day": null,
    "date_created": "2015-03-12T18:35:51.000Z",
    "date_updated": "2015-03-12T18:35:51.000Z"
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **count**<br> default: `10` | Retorna `n` objetos recebedores |
| **page**<br> default: `1` | Útil para implementação de uma paginação de resultados |

## Retornando um recebedor

> GET https://api.pagar.me/1/recipients/:id

```shell
curl -X GET https://api.pagar.me/1/recipients/re_ci7nhf1ay0007n016wd5t22nl \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby


```

```php
<?php
	require("pagarme-php/Pagarme.php");
	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");
	
	$recipient = PagarMe_Recipient::findById("re_ci7nhf1ay0007n016wd5t22nl");
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var recipient = PagarMeService.GetDefaultService().Recipients.Find("re_ci7nhf1ay0007n016wd5t22nl");
```

Retorna um objeto com os dados de um recebedor criado pela sua companhia.

> JSON Retornado (Exemplo)

```json
{
    "object": "recipient",
    "id": "re_ci7nhf1ay0007n016wd5t22nl",
    "bank_account": {
        "object": "bank_account",
        "id": 4901,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": null,
        "conta": "58999",
        "conta_dv": "3",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "RECIPIENT TEST",
        "charge_transfer_fees": true,
        "date_created": "2015-03-24T15:53:17.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "date_created": "2015-03-24T15:53:27.000Z",
    "date_updated": "2015-03-24T15:53:27.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id de recebedor desejado |

## Atualizando um recebedor

> PUT https://api.pagar.me/1/recipients/:id

```shell
curl -X PUT https://api.pagar.me/1/recipients/re_ci7nhf1ay0007n016wd5t22nl \
-d 'anticipatable_volume_percentage=80' \
-d 'bank_account_id=5233'
```

```ruby
```

```php
```

```cs
```

Após criar um recebedor, você pode atualizar todas as configurações do mesmo.

> JSON Retornado (Exemplo)

```json
{
    "object": "recipient",
    "id": "re_ci7nhf1ay0007n016wd5t22nl",
    "bank_account": {
        "object": "bank_account",
        "id": 5233,
        "bank_code": "341",
        "agencia": "0721",
        "agencia_dv": null,
        "conta": "02733",
        "conta_dv": "3",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "RECIPIENT TEST",
        "charge_transfer_fees": true,
        "date_created": "2015-03-24T15:53:17.000Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
	"anticipatable_volume_percentage": 80,
	"automatic_anticipation_enabled": true,
    "date_created": "2015-03-24T15:53:27.000Z",
    "date_updated": "2015-03-29T15:53:27.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **transfer_interval**<br> <span class="required">obrigatório</span> | Frequência na qual o recebedor irá ser pago. <br> **Valores possíveis**: `daily`, `weekly`, `monthly` |
| **transfer_day**<br> <span class="required">obrigatório</span> | Dia no qual o recebedor vai ser pago. |
| **transfer_enabled**<br> <span class="required">obrigatório</span> | Variável que indica se o recebedor pode receber os pagamentos automaticamente |
| **anticipatable_volume_percentage** | Limite de volume que pode ser antecipado pelo recebedor |
| **bank_account_id**<br> <span class="required">obrigatório\*</span> | Identificador de uma conta bancária previamente criada. Caso você não tenha essa informação, você pode passar os parâmetros necessários para [criação de uma conta bancária](/#criando-uma-conta-bancaria). <br>**OBS:** Para atualizar a conta bancária, ela deve **obrigatóriamente** possuir o mesmo CPF ou CNPJ da conta bancária anterior | 
| **bank_account**<br> <span class="required">obrigatório\*</span> | Objeto contendo os dados bancários do recebedor. Este objeto, e as suas respectivas propriedades, serão obrigatórios caso não seja informado um `bank_account_id` <br>**OBS:** Para atualizar a conta bancária, ela deve **obrigatóriamente** possuir o mesmo CPF ou CNPJ da conta bancária anterior |
| **bank_account[bank_code]** | Código do banco do recebedor |
| **bank_account[agencia]** | Agência da conta do recebedor |
| **bank_account[conta]** | Número da conta bancária do recebedor |
| **bank_account[conta_dv]** | Dígito verificador da conta do recebedor |
| **bank_account[document_number]** | CPF ou CNPJ do recebedor |
| **bank_account[legal_name]** | Nome completo ou razão social do recebedor |

## Saldo de um recebedor

> GET https://api.pagar.me/1/recipients/:id/balance

```shell
curl -X GET https://api.pagar.me/1/recipients/re_ci7nhf1ay0007n016wd5t22nl/balance \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna o balanço de saldo de um determinado recebedor.

> JSON Retornado (Exemplo)

```json
{
    "object": "balance",
    "waiting_funds": {
        "amount": 0
    },
    "available": {
        "amount": 0
    },
    "transferred": {
        "amount": 0
    }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id de recebedor desejado |

## Operações de saldo de um recebedor

> GET https://api.pagar.me/1/recipients/:recipient_id/balance/operations

```shell
curl -X GET https://api.pagar.me/1/recipients/re_ci7nhf1ay0007n016wd5t22nl/balance/operations \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'count=3' \
-d 'page=1'
```

```ruby
```

```php
```

```cs
```

Retorna as movimentações que aconteceram na conta do recebedor.

> JSON Retornado (Exemplo)

```json
[{
    "object": "balance_operation",
    "id": 4861,
    "status": "available",
    "balance_amount": 3019898,
    "balance_old_amount": 2920013,
    "movement_type": "payable",
    "amount": 100000,
    "fee": 115,
    "date_created": "2015-03-06T21:00:31.000Z",
    "movement_object": {
        "object": "payable",
        "id": 1297,
        "status": "paid",
        "amount": 100000,
        "fee": 115,
        "installment": 1,
        "transaction_id": 185537,
        "payment_date": "2015-03-06T03:00:00.000Z",
        "date_created": "2015-03-06T21:00:31.000Z"
    }
}, {
    "object": "balance_operation",
    "id": 4852,
    "status": "available",
    "balance_amount": 2920013,
    "balance_old_amount": 2910128,
    "movement_type": "payable",
    "amount": 10000,
    "fee": 115,
    "date_created": "2015-03-06T18:44:42.000Z",
    "movement_object": {
        "object": "payable",
        "id": 1294,
        "status": "paid",
        "amount": 10000,
        "fee": 115,
        "installment": 1,
        "transaction_id": 185507,
        "payment_date": "2015-03-06T03:00:00.000Z",
        "date_created": "2015-03-06T18:44:42.000Z"
    }
}, {
    "object": "balance_operation",
    "id": 4840,
    "status": "available",
    "balance_amount": 2910128,
    "balance_old_amount": 2880243,
    "movement_type": "payable",
    "amount": 30000,
    "fee": 115,
    "date_created": "2015-03-05T19:32:36.000Z",
    "movement_object": {
        "object": "payable",
        "id": 1290,
        "status": "paid",
        "amount": 30000,
        "fee": 115,
        "installment": 1,
        "transaction_id": 185273,
        "payment_date": "2015-03-05T03:00:00.000Z",
        "date_created": "2015-03-05T19:32:35.000Z"
    }
}]
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **count**<br> default: `10` | Retorna `n` objetos de operações de saldo |
| **page**<br> default: `1` | Útil para implementação de uma paginação de resultados |

## Operação de saldo específica de um recebedor

> GET https://api.pagar.me/1/recipients/:recipient_id/balance/operations/:id

```shell
curl -X GET https://api.pagar.me/1/recipients/re_ci7nhf1ay0007n016wd5t22nl/balance/operations \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna uma movimentação de saldo que aconteceu na conta do recebedor.

> JSON Retornado (Exemplo)

```json
{
    "object": "balance_operation",
    "id": 4861,
    "status": "available",
    "balance_amount": 3019898,
    "balance_old_amount": 2920013,
    "movement_type": "payable",
    "amount": 100000,
    "fee": 115,
    "date_created": "2015-03-06T21:00:31.000Z",
    "movement_object": {
        "object": "payable",
        "id": 1297,
        "status": "paid",
        "amount": 100000,
        "fee": 115,
        "installment": 1,
        "transaction_id": 185537,
        "payment_date": "2015-03-06T03:00:00.000Z",
        "date_created": "2015-03-06T21:00:31.000Z"
    }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:recipient_id**<br> <span class="required">obrigatório</span> | Id de recebedor desejado |
| **:id**<br> <span class="required">obrigatório</span> | Id da operação de saldo desejada |

