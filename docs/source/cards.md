---
title: Cobrando um cartão posteriormente

language_tabs:
  - shell
  - ruby
  - php

search: true
---

# Cobrando um cartão posteriormente

Com o Pagar.me, você pode armazenar os dados do cartão de um usuário em nosso
ambiente seguro e decidir quando, quanto e como deseja cobrá-lo.

Dessa forma, você pode implementar funcionalidades como o one-click-buy (compra
por um clique) e realizar transações sem que o usuário precise reentrar ou
lembrar seus dados de cartão.

## Armazenando um cartão

Para armazenar um cartão, basta passar um `card_hash` contendo os dados do
cartão. Para mais informações sobre a geração do `card_hash`, veja como
[realizar a captura dos dados de cartão](/capturing-card-data).

Você deve armazenar o `id` do objeto `card` para realizar as cobranças futuras
(veja abaixo).

```shell
curl -X POST 'https://api.pagar.me/1/cards' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'card_hash={CARD_HASH}' > card.json

CARD_ID=parse('card.json')['id']
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

card = PagarMe::Card.new({
    :card_hash => "{CARD_HASH}"
})

card.create

card_id = card.id
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$card = new PagarMe_Card(array(
		'card_hash' => "{CARD_HASH}"
	));

	$card->create();

	$card_id = $card->id;
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Card card = new Card();

card.CardHash = "{CARD_HASH}";

card.Save();

string cardId = card.Id;
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

## Recuperando o cartão de uma transação anterior

Você pode também recuperar um cartão de uma transação feita anteriormente e
utilizá-lo para realizar novas cobranças. Para isso, basta armazenar o `id` do
objeto `card` retornado na transação.

```shell
curl -X GET 'https://api.pagar.me/1/transactions/{ID DA TRANSAÇÃO}' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' > transaction.json

CARD_ID=parse('transaction.json')['card']['id']
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.find_by_id("{ID DA TRANSAÇÃO")
card_id = transaction.card.id
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = PagarMe_Transaction::findById("{ID DA TRANSAÇÃO}"),
	$card_id = $transaction->card->id;
?>
```


```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = PagarMeService.GetDefaultService().Cards.Find("{ID DA TRANSAÇÃO}");
string cardId = transaction.Card.Id;
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).

## Realizando uma transação usando um cartão 

Agora que você tem o `id` do `card` em mãos, basta [criar uma
transação](/transactions) passando o `card_id`. Os demais parâmetros da
transação (valor, número de parcelas, etc.) também podem ser passados
normalmente.

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_id={ID DO CARTÃO}'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
	:card => PagarMe::Card.find_by_id("{ID DO CARTÃO}")
})

transaction.charge
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		'amount' => 1000,
		'card' => PagarMe_Card::findById("{ID DO CARTÃO}"),
	));

	$transaction->charge();
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.Card = PagarMeService.GetDefaultService().Cards.Find("{ID DO CARTÃO}");

transaction.Save();
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).
