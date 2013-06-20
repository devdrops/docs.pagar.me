---
title: URL de POSTback
---

# URL de POSTback

O POSTback permite que o seu servidor seja notificado sempre que o `status` de uma transação mudar. Isso é possível definindo uma URL de POSTback para a transação ao criá-la. Dessa forma, quando ocorrerem modificações no `status` da transação, você receberá uma requisição HTTP na URL definida, notificando-o da mudança ocorrida.

A principal vantagem desse método é que ao realizar a transação com o PagarMe, não será necessário esperar a resposta do servidor para saber se a transação foi aprovada ou não. Ao criar a transação, será inicialmente retornado na requisição o status `processing`, indicando que ela ainda está sendo processada. Quando ela for aprovada/recusada, o PagarMe fará uma requisição a URL de POSTback para notificar seu servidor da mudança de `status`.

## Definindo uma URL de POSTback

Conforme descrito nas [referências dos métodos da API](/restful-api/methods), para definir uma URL de POSTback para a transação, basta enviar o parâmetro `postback_url` na requisição para criar uma nova transação:

	POST /transactions

Nota: a URL deve seguir o formato padrão. Exemplo: 

	http://www.meusite.com.br/transactions_postback

## Recebendo o POSTback

Quando o status da transação mudar, o PagarMe fará uma requisição a URL de POSTback com os seguintes parâmetros:

- `id` - id da transação (retornado ao realizá-la)
- `old_status` - status da transação antes de ter mudado
- `desired_status` - status desejado. Caso se deseja aprovar a transação, o status desejado seria `approved`. Caso se deseja cancelá-la, o status desejado seria `chargebacked`.
- `current_status` - status atual (modificado) da transação.

O seu servidor deverá interpretar esses parâmetros e realizar as ações/modificações necessárias de acordo com a mudança de status.

## Exemplo de uso da URL de POSTback