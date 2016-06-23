# Cartões

## Objeto `card`

> Objeto card

```json
{
    "object": "card",
    "id": "card_ci6y37hc00030a416wrxsmzyi",
    "date_created": "2015-03-06T21:21:25.000Z",
    "date_updated": "2015-03-06T21:21:26.000Z",
    "brand": "visa",
    "holder_name": "API CUSTOMER",
    "first_digits": "401872",
    "last_digits": "8048",
    "fingerprint": "Jl9oOIiDjAjR",
    "customer": null,
    "valid": true
}
```

Sempre que você faz uma requisição através da nossa API nós guardamos as informações do portador do cartão, para que, futuramente, você possa utilizar essas informações para novas cobranças, ou implementação de features como *one-click-buy*.

| Propriedade | Descrição |
|--:|:--|
| **object**<br> String | Nome do tipo do objeto criado/modificado. <br> **Valor retornado**: `subscription` |
| **id**<br> String | Identificador do cartão |
| **date_created**<br> String | Data de criação do objeto `card` |
| **date_updated**<br> String | Data de atualização do objeto `card` |
| **brand**<br> String | Marca da operadora do cartão |
| **holder_name**<br> String | Nome do portador do cartão |
| **first_digits**<br> String | Primeiros dígitos do cartão (6 dígitos) |
| **last_digits**<br> String | Últimos dígitos do cartão (4 dígitos) |
| **fingerprint**<br> String | Hash que permite comparar dois cartões através de seus fingerprints para saber se são o mesmo |
| **customer**<br> Object | Objeto com dados do comprador |
| **valid**<br> Boolean | Propriedade para verificar a validade do cartão |

## Criando um cartão

> POST https://api.pagar.me/1/cards

```shell
curl -X  POST https://api.pagar.me/1/cards \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
-d 'card_number=4018720572598048' \
-d 'holder_name=API Customer' \
-d 'card_expiration_date=0116'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"


card = PagarMe::Card.new({
	:card_number => '4111111111111111',
	:card_holder_name => 'Jose da Silva',
	:card_expiration_month => '10',
	:card_expiration_year => '15',
	:card_cvv => '314'
})

card.create
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$card = new PagarMe_Card(array(
		"card_number" => "4111111111111111",
		"card_holder_name" => "Jose da Silva",
		"card_expiration_month" => 10,
		"card_expiration_year" => 22,
		"card_cvv" => "123",
	));
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";
PagarMeService.DefaultEncryptionKey = "ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk";

Card card = new Card();
card.Number = "4111111111111111";
card.HolderName = "Jose da Silva";
card.ExpirationDate = "1022";
card.Cvv = "123";

card.Save();
```

Você pode armazenar os dados do cartão do seu cliente através da rota `/cards`, assim você poderá usar o `id` do objeto gerado para realizar futuras transações, no lugar do `card_hash`.

> JSON Retornado (Exemplo)

```json
{
    "object": "card",
    "id": "card_ci6y37h16wrxsmzyi",
    "date_created": "2015-03-06T21:21:25.000Z",
    "date_updated": "2015-03-06T21:21:26.000Z",
    "brand": "visa",
    "holder_name": "API CUSTOMER",
    "first_digits": "401872",
    "last_digits": "8048",
    "fingerprint": "Jl9oOIiDjAjR",
    "customer": null,
    "valid": true
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |
| **card_number** | Número do portador do cartão |
| **card_expiration_date** ou **expiration_date** | Data de expiração do cartão |
| **holder_name** | Nome no cartão do portador |
| **customer_id** | Você pode usar o `id` do objeto `customer` para associar mais informações do cliente ao `card` a ser gerado |
| **card_hash** | Você também pode criar um objeto `card` usando os dados do cartão criptografados no `card_hash` |

## Retornando um cartão salvo

> GET https://api.pagar.me/1/cards/:id

```shell
curl -X  GET https://api.pagar.me/1/cards/card_ci6y37h16wrxsmzyi \
-d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"

card = PagarMe::Card.find_by_id("1234")
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$card = PagarMe_Card::findById("card_ci6y37hc00030a416wrxsmzyi");
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

var card = PagarMeService.GetDefaultService().Cards.Find("card_ci6y37hc00030a416wrxsmzyi");
```

Use a rota `/cards/:id` para retornar os dados de um cartão previamente salvo.

> JSON Retornado (Exemplo)

```json
{
    "object": "card",
    "id": "card_ci6y37h16wrxsmzyi",
    "date_created": "2015-03-06T21:21:25.000Z",
    "date_updated": "2015-03-06T21:21:26.000Z",
    "brand": "visa",
    "holder_name": "API CUSTOMER",
    "first_digits": "401872",
    "last_digits": "8048",
    "fingerprint": "Jl9oOIiDjAjR",
    "customer": null,
    "valid": true
}
```

| Parâmetro | Descrição |
|--:|:--|
| **api_key**<br> <span class="required">obrigatório</span> | Chave da API (disponível no seu dashboard) |


