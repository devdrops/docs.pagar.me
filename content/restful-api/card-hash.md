---
title: Gerando o card_hash
---

# Gerando o *card_hash*

Todos os dados de cartão de crédito devem ser enviados para o servidor utilizando o `card_hash`. O `card_hash` torna possível que todos os dados do cartão trafeguem de forma criptografada e que só pode ser compreendida pelo PagarMe, tornando impraticável qualquer tentativa de utilizá-los de forma indevida por terceiros.

O `card_hash` consiste em uma string gerada a partir dos dados do cartão de crédito. Essa string é encriptada por RSA usando uma chave pública enviada pelo servidor. Essa chave deve ser requisitada a cada `card_hash` gerado e é invalidada assim que o servidor o lê, ou seja, só pode ser utilizada uma única vez. A chave também é temporária, e por isso expira 5 minutos após ter sido gerada.

As bibliotecas do PagarMe sempre utilizam o `card_hash` para enviar os dados para o servidor, o que aumenta consideravelmente a segurança da transação.

Gerando o `card_hash`, passo a passo:

## Requisitando ao servidor uma chave para encriptar o `card_hash`

A URL a ser requisitada, conforme definido na [referência de métodos da API](/restful-api/methods), é:

	GET /transactions/card_hash

Caso o `card_hash` seja gerado do lado do cliente (exemplo: no browser, como faz nossa [biblioteca em Javascript](/apis/javascript)), o parâmetro `api_key` não deve ser utilizado para autenticar essa requisição, já que este deve ser mantido em segredo e, portanto, somente do lado do servidor.

Nesses casos, deve-se utilizar a `encryption_key`, que consiste em uma alternativa a `api_key` para as requisições feitas para obter a chave de encriptação do `card_hash` do lado do cliente. Essa chave está disponível em seu [dashboard](https://dashboard.pagar.me/).

Em casos onde o `card_hash` é gerado do lado do servidor, a `api_key` pode ser utilizada normalmente, já que não há risco de expô-la.

No exemplo de geração do `card_hash` a seguir, a `encryption_key` utilizada será `9741a03ea3a4f15f6fa8d9fe9d2c47c8`.

Realizando a requisição para obter a chave para encriptar o `card_hash`, com o cURL:

<pre><code data-language="shell">curl 'https://api.pagar.me/1/transactions/card_hash' \
	-d 'encryption_key=9741a03ea3a4f15f6fa8d9fe9d2c47c8' \
	-X GET 
</code></pre>

O servidor retornará: 

<pre><code data-language="javascript">{
    "date_created": "2013-04-13T21:42:03.886Z",
    "id": "5169d12b3da665f36e00000a",
    "public_key": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAv71aNn1njtjcSrPXW7LF\nZlxajpBht/jq/+pl77eiZEVyNnP1nHlmkM4ufZmZQF7Q8seTUEBjR2PjoocCrFsP\nsu9+ITFnqAqlYmAVXKFf/gCCQfPDfhsavQXVauDAHXyl/69ooWIMUrYmCmxpZfSU\ne9E/4dl7sUg1ywllU8EpMKIn8Zd7blk49pNZ8I2FlkLRLk3yS9JXDIe8dAZLHoZP\nyT1c/5p1czLoB7Q9k5ic2A4ZM3cwCVkbIKC4wEmFuQCQx4tu1J96kvXhVLYoZlvV\n6+u8apFpFQVpTAK71IVYJbTQjHHty1qtZMImw42YM0kFz0GqhfQk3LKziBDX/FHq\nRQIDAQAB\n-----END PUBLIC KEY-----\n"
}</code></pre>

A chave que será usada para encriptar os dados do cartão é a `public_key`.

## Encriptando os dados do cartão de crédito

Nesse exemplo, usaremos os seguintes dados do cartão de crédito:

- `card_number` - 4901720080344448
- `card_holder_name` - Usuario de Teste
- `card_expiracy_date` - 1213
- `card_cvv` - 314

Para mais informações sobre os parâmetros que contém os dados do cartão, [consulte a referência dos métodos da API](/restful-api/methods).

O conteúdo que deve ser encriptado pela chave retornada pelo servidor é uma string com os dados do cartão de crédito formatados como parâmetros HTTP:

<pre><code data-language="html">card_number=4901720080344448&card_holder_name=Usuario de Teste&card_expiracy_date=1213&card_cvv=314</code></pre>

Você deve, com essa string,

- Encriptá-la utilizando a chave retornada pelo servidor (`public_key`).
- Converter o resultado dessa encriptação para [base64](http://en.wikipedia.org/wiki/Base64).

Como resultado, temos:

	FFtwikzg/FC1mH7XLFU5fjPAzDsP0ogeAQh3qXRpHzkIrgDz64lITBUGwio67zm2CQXwbKRjGdRi5J1xFNpQLWnxQsUJAQELcTSGaGtF6RGSu6sq1stp8OLRSNG7wp+xGe8poqxw4S1gOL5JYO7XZp/Uz7rTpKXh3IcRshmX36hh66J6+7l5j0803cGIfMZu3T7nbMjQYIf+yLi8r0O6vL9DQPmqSZ9FBerqFGxWHrxScneaaMVzMpNX/5eneqveVBt88RccytyJG5+HYRHcRyKIbLfmX48L/C22HJeAm3PyzehGHdOmDcsxPtVB+Fgq7SDuB4tHWBT8j6wihOO7ww==

## Enviando o `card_hash` para o servidor

O `card_hash` deve conter o `id` retornado pelo servidor ao requisitar a chave para encriptação. Portanto, ele tem a seguinte forma:

	(id retornado pelo servidor)_(resultado da criptografia dos dados do cartão formatados em base64)

O `id` retornado pelo servidor nesse exemplo foi `5169d12b3da665f36e00000a`. Logo, o `card_hash` é:

	5169d12b3da665f36e00000a_FFtwikzg/FC1mH7XLFU5fjPAzDsP0ogeAQh3qXRpHzkIrgDz64lITBUGwio67zm2CQXwbKRjGdRi5J1xFNpQLWnxQsUJAQELcTSGaGtF6RGSu6sq1stp8OLRSNG7wp+xGe8poqxw4S1gOL5JYO7XZp/Uz7rTpKXh3IcRshmX36hh66J6+7l5j0803cGIfMZu3T7nbMjQYIf+yLi8r0O6vL9DQPmqSZ9FBerqFGxWHrxScneaaMVzMpNX/5eneqveVBt88RccytyJG5+HYRHcRyKIbLfmX48L/C22HJeAm3PyzehGHdOmDcsxPtVB+Fgq7SDuB4tHWBT8j6wihOO7ww==

Ele deve ser enviado para o seu servidor através de uma requisição HTTP ou HTTPS. O seu servidor se comunicará com a API do PagarMe novamente para realizar a transação com os dados de cartão criptografados presentes no `card_hash`.

O `card_hash` só pode ser utilizado uma vez e deve ser gerado novamente a cada transação.

## Utilizando o `card_hash` gerado

Uma vez com o `card_hash` em seu servidor, você deve utilizar nossas bibliotecas para realizar a transação com o PagarMe. Elas dispõem de métodos para utilizar o `card_hash` para realizar transações.

Caso você deseja realizar a transação com o `card_hash` manualmente, temos um exemplo de utilização do `card_hash` com o cURL:

<pre><code data-language="shell">curl 'https://api.pagar.me/1/transactions' \
	-d 'api_key=Jy1V5bJcGf8q4gHepttt' \
	-d 'card_hash=5169d12b3da665f36e00000a_FFtwikzg/FC1mH7XLFU5fjPAzDsP0ogeAQh3qXRpHzkIrgDz64lITBUGwio67zm2CQXwbKRjGdRi5J1xFNpQLWnxQsUJAQELcTSGaGtF6RGSu6sq1stp8OLRSNG7wp+xGe8poqxw4S1gOL5JYO7XZp/Uz7rTpKXh3IcRshmX36hh66J6+7l5j0803cGIfMZu3T7nbMjQYIf+yLi8r0O6vL9DQPmqSZ9FBerqFGxWHrxScneaaMVzMpNX/5eneqveVBt88RccytyJG5+HYRHcRyKIbLfmX48L/C22HJeAm3PyzehGHdOmDcsxPtVB+Fgq7SDuB4tHWBT8j6wihOO7ww==' \
	-d 'amount=1500' \
	-X POST 
</code></pre>

O servidor retornará:

<pre><code data-language="javascript">{
    "status": "approved",
    "date_created": "2013-04-08T01:01:56.672Z",
    "amount": "1500",
    "id": "516217040ef16fc9fc00000f",
    "live": true,
    "costumer_name": "Usuario de Teste"
}</code></pre>

A transação foi realizada utilizando os dados de cartão de crédito presentes no `card_hash`.