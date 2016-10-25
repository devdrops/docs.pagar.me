---
title: Opções avançadas

language_tabs:
  - shell
  - ruby
  - php

search: true
---

# Opções avançadas

## Validando a origem de um POSTback

Você deve validar a origem do POSTback, isto é, se ele foi realmente enviado
pelo Pagar.me.

Para fazer isso, você deve calcular o HMAC-SHA1 ([wikipedia HMAC](pt.wikipedia.org/wiki/HMAC))
do body da requisição HTTP e compará-lo com o header `X-Hub-Signature`.

<aside class="notice">A `X-Hub-Signature` é o hash `HMAC-SHA1` calculado a partir do body
  da requisição HTTP com a sua API Key do Pagar.me como chave.</aside>

```shell
EXPECTED_SIGNATURE=`cat postback_body | openssl dgst -sha1 -hmac "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0"`
SIGNATURE=1213e67a3b34c2848f8317d29bcb8cbc9e0979b8
if [ "$SIGNATURE" = "$EXPECTED_SIGNATURE" ]; then
	echo "Valid Signature"
fi
```

```ruby
require 'pagarme'

PagarMe.api_key = 'ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0';

if PagarMe::PostBack.valid_request_signature?(postback_body, '1213e67a3b34c2848f8317d29bcb8cbc9e0979b8')
	puts "Valid Signature"
end
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	if(PagarMe::validateRequestSignature(postback_body, "1213e67a3b34c2848f8317d29bcb8cbc9e0979b8")) {
		echo "Valid Signature";
	}
?>
```

> Não se esqueça de:<br/>
> - `postback_body` pelo conteúdo do body da requisição HTTP do postback,<br/>
> - `1213e67a3b34c2848f8317d29bcb8cbc9e0979b8` pela `signature` recebido em X-Hub-Signature,<br/>
> - `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível<br/>
>   no seu [Dashboard](https://dashboard.pagar.me/).

## Enviando dados adicionais (metadata)

Você pode também enviar dados adicionais para o Pagar.me (ID do pedido em seu
sistema, carrinho de compras, características do produto/serviço, etc.) através
do campo `metadata`.

Dessa forma, você poderá visualizar, exportar e efetuar buscas pelos dados do
seu negócio através do seu [Dashboard](https://dashboard.pagar.me).

Para enviar esses dados para o Pagar.me, basta configurar as chaves/valores das
variáveis que você deseja enviar dentro do campo `metadata`.

O `metadata` pode ser enviado em transações e assinaturas.

```shell
curl -X POST 'https://api.pagar.me/1/transactions' \
    -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
    -d 'amount=1000' \
    -d 'card_hash={CARD_HASH}' \
	-d 'metadata[id_pedido]=12345' \
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.new({
	:amount => 1000,
    :card_hash => "{CARD_HASH}",
	:metadata => {
		:id_pedido => 12345
	}
})

transaction.charge
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = new PagarMe_Transaction(array(
		'amount' => 1000,
		'card_hash' => "{CARD_HASH}"
		'metadata' => array(
			'id_pedido' => 12345
		)
	));

	$transaction->charge();
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

AbstractModel metadata = new AbstractModel();

metadata["id_pedido"] = 12345;

Transaction transaction = new Transaction();

transaction.Amount = 1000;
transaction.CardHash = "{CARD HASH}";
transaction.Metadata = metadata;

transaction.Save();
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu [Dashboard](https://dashboard.pagar.me/).
