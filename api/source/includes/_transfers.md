# Transferências

## Objeto `transfer`

> Objeto transfer

```json
{
    "object": "transfer",
    "id": 480,
    "amount": 13000,
    "type": "doc",
    "status": "pending_transfer",
    "fee": 367,
    "funding_estimated_date": "2015-03-21T15:44:14.417Z",
    "bank_account": {
        "object": "bank_account",
        "id": 4840,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:35:40.000Z"
    },
    "date_created": "2015-03-20T15:44:14.000Z"
}
```

Objeto retornado ao se criar uma transferência bancária.

| Propriedade | Descrição |
|--:|:--|
| **object**<br> String | Nome do tipo do objeto criado/modificado. <br> **Valor retornado**: `transfer` |
| **id**<br> Number | Número identificador da transação |
| **amount**<br> Number | Valor, em centavos, do valor transferido |
| **type**<br> String | Tipo da transação. <br> **Valores possíveis**: `ted`, `doc` ou `credito_em_conta` |
| **status**<br> String | Estado no qual a transação se encontra. <br> **Valores possíveis**: `pending_transfer`, `transferred`, `failed`, `processing` ou `canceled` |
| **fee**<br> Number | Taxa cobrada pela transferência, em centavos. |
| **funding_date**<br> String | Data da ocorrência da transferência |
| **funding_estimated_date**<br> String | Data estimada para efetivação da transferência (ISODate) |
| **transaction_id**<br> Number | Identificador da transação estornada |
| **bank_account**<br> Object | Objeto contendo os dados da [conta bancária](/?shell#objeto-bank_account) que irá receber a transferência |
| **date_created**<br> String | Data da criação da transferência (ISODate) |

## Criando uma transferência

> POST https://api.pagar.me/1/transfers

```shell
curl -X POST https://api.pagar.me/1/transfers \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'amount=13000' \
-d 'bank_account_id=4840'
```

```ruby
```

```php
```

```cs
```

Realiza uma transferência para uma conta bancária previamente criada.

> JSON Retornado (Exemplo)

```json
{
    "object": "transfer",
    "id": 480,
    "amount": 13000,
    "type": "doc",
    "status": "pending_transfer",
    "fee": 367,
    "funding_estimated_date": "2015-03-21T15:44:14.417Z",
    "bank_account": {
        "object": "bank_account",
        "id": 4840,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:35:40.000Z"
    },
    "date_created": "2015-03-20T15:44:14.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **amount**<br> <span class="required">obrigatório</span> | Valor, em centavos, a ser transferido para uma determinada conta bancária |
| **bank_account_id**<br> <span class="required">obrigatório\*</span> | Número identificador da conta bancária que irá receber a transferência. <br> **OBS**: Caso tenha sido passado um `recipient_id`, se o parâmetro `bank_account_id` for omitido, o valor será depositado na conta bancária definida no recebedor informado |
| **recipient_id**<br> <span class="required">obrigatório\*</span> | Indica que o valor da transferência sairá da conta do recebedor identificado por este parâmetro. <br> **OBS**: Caso o `recipient_id` seja passado, não é necessário passar `bank_account_id` |

## Transferência entre recebedores

> POST https://api.pagar.me/1/transfers

```shell
curl -X POST https://api.pagar.me/1/transfers \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'amount=13000' \
-d 'source_id=re_cimcpc2qc002za46d9dt4vfok' \
-d 'target_id=re_cimedz85n000xw56eor49cwk7'
```

```ruby
```

```php
```

```cs
```

Realiza uma transferência do saldo de um `source` para o saldo de um `target`, com a condição de que um deles **deve** ser o recebedor padrão de sua company.

> JSON Retornado (Exemplo)

```json
{
  "object": "transfer",
  "id": 6180,
  "amount": 50000,
  "type": "inter_recipient",
  "status": "transferred",
  "source_type": "recipient",
  "source_id": "re_cimcpc2qc002za46d9dt4vfok",
  "target_type": "recipient",
  "target_id": "re_cimedz85n000xw56eor49cwk7",
  "fee": 0,
  "funding_date": "2016-06-17T16:15:26.609Z",
  "funding_estimated_date": "2016-06-17T16:15:26.609Z",
  "transaction_id": null,
  "date_created": "2016-06-17T16:15:26.613Z",
  "recipient": {
    "object": "recipient",
    "id": "re_cimedz85n000xw56eor49cwk7",
    "bank_account": {
      "object": "bank_account",
      "id": 12304182,
      "bank_code": "232",
      "agencia": "0808",
      "agencia_dv": "4",
      "conta": "12367",
      "conta_dv": "2",
      "document_type": "cpf",
      "document_number": "11111111111",
      "legal_name": "TESTING",
      "charge_transfer_fees": true,
      "date_created": "2016-03-29T19:40:23.138Z"
    },
    "transfer_enabled": true,
    "last_transfer": null,
    "transfer_interval": "weekly",
    "transfer_day": 5,
    "automatic_anticipation_enabled": false,
    "anticipatable_volume_percentage": 0,
    "date_created": "2016-03-30T05:02:50.123Z",
    "date_updated": "2016-03-30T05:02:50.123Z"
  }
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **amount**<br> <span class="required">obrigatório</span> | Valor, em centavos, a ser transferido. |
| **source_id**<br> <span class="required">obrigatório\*</span> | Identificador de um recebedor que terá seu saldo **debitado**. |
| **target_id**<br> <span class="required">obrigatório\*</span> | Identificador de um recebedor que terá seu saldo **creditado**. |

## Vendo dados de uma transferência

> GET https://api.pagar.me/1/transfers/:id

```shell
curl -X GET https://api.pagar.me/1/transfers/484 \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna os dados de uma transferência previamente realizada.

> JSON Retornado (Exemplo)

```json
{
    "object": "transfer",
    "id": 480,
    "amount": 13000,
    "type": "doc",
    "status": "pending_transfer",
    "fee": 367,
    "funding_estimated_date": "2015-03-21T15:44:14.417Z",
    "bank_account": {
        "object": "bank_account",
        "id": 4840,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:35:40.000Z"
    },
    "date_created": "2015-03-20T15:44:14.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da transferência desejada |

## Vendo dados de várias transferências

> GET https://api.pagar.me/1/transfers

```shell
curl -X GET https://api.pagar.me/1/transfers \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Retorna os dados de todas as transferências previamente realizadas.

> JSON Retornado (Exemplo)

```json
{
    "object": "transfer",
    "id": 480,
    "amount": 13000,
    "type": "doc",
    "status": "pending_transfer",
    "fee": 367,
    "funding_estimated_date": "2015-03-21T15:44:14.417Z",
    "bank_account": {
        "object": "bank_account",
        "id": 4840,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:35:40.000Z"
    },
    "date_created": "2015-03-20T15:44:14.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **count**<br> default: `10` | Retorna `n` objetos de transferência |
| **page**<br> default: `1` | Útil para implementação de uma paginação de resultados |

## Cancelando uma transferência

> POST https://api.pagar.me/1/transfers/:id/cancel

```shell
curl -X POST https://api.pagar.me/1/transfers/480/cancel \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
```

```php
```

```cs
```

Cancela uma transferência previamente realizada.

> JSON Retornado (Exemplo)

```json
{
    "object": "transfer",
    "id": 480,
    "amount": 13000,
    "type": "doc",
    "status": "canceled",
    "fee": 367,
    "funding_date": null,
    "funding_estimated_date": "2015-03-21T15:44:14.000Z",
    "transaction_id": null,
    "bank_account": {
        "object": "bank_account",
        "id": 4840,
        "bank_code": "341",
        "agencia": "0932",
        "agencia_dv": "5",
        "conta": "58054",
        "conta_dv": "1",
        "document_type": "cpf",
        "document_number": "26268738888",
        "legal_name": "API BANK ACCOUNT",
        "charge_transfer_fees": false,
        "date_created": "2015-03-19T15:35:40.000Z"
    },
    "date_created": "2015-03-20T15:44:14.000Z"
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **:id**<br> <span class="required">obrigatório</span> | Id da transferência a ser cancelada |


