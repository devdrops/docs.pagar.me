---
title: Integração via Checkout

language_tabs:
  - shell
  - ruby
  - php
  - csharp

search: true
---

# Integração via Checkout

O Checkout é a forma mais simples de realizar a integração com o Pagar.me.

Com ele, você não precisa se preocupar com validações, segurança no envio dos
dados, tratamento de erros das operadoras de cartão, otimização da conversão,
etc.

Basta inserir nosso JavaScript na sua página e depois realizar a captura da
transação pelo seu servidor. Nós cuidaremos de todo o resto! :)

[Clique aqui](https://pagar.me/checkout) para testar o Checkout.

## Inserindo o formulário

```html
<form method="POST" action="/comprar">
	<script type="text/javascript"
		src="https://assets.pagar.me/checkout/checkout.js"
		data-button-text="Pagar"
		data-encryption-key="ek_test_Ec8KhxISQ1tug1b8bCGxC2nXfxqRmk"
		data-amount="1000">
	</script>
</form>
```

> Não se esqueça de substituir `ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk` pela
> sua [chave de encriptação](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).

O código acima irá inserir o botão de pagar na sua página. Você pode alterar
o texto do botão pela tag `data-button-text` e o valor da transação pela tag
`data-amount`.

Ao clicar no botão, o Checkout do Pagar.me abrirá dentro do seu site e
o usuário digitará os dados necessários para realizar a transação. 

Quando a transação for concluída, um `token` referente a transação realizada
será adicionado ao `form`. O `form`, então, será submetido para o seu servidor,
onde a transação será capturada.

### Configurações do Checkout

Tag | Padrão | Descrição
--- | ------ | ---------
data-encryption-key | --- | Chave de encriptação disponível no seu dashboard.
data-amount | --- | Valor da transação (em centavos) a ser capturada pelo Checkout. Ex: R$14,79 = `1479`
data-button-text | `Pagar` | Texto do botão de pagamento.
data-button-class | --- | Classe CSS a ser adicionada no botão de pagamento.
data-customer-data | `true` | Caso não deseje capturar dados do cliente pelo Checkout, setar como `false`.
data-boleto-discount-amount | --- | Valor, em centavos, do desconto caso o meio de pagamento seja boleto. Ex: desconto de R$10,00 = `1000`. OBS: você não pode adicionar essa tag caso a tag de desconto percentual já esteja presente.
data-boleto-discount-percentage | --- | Percentual de desconto caso o meio de pagamento seja boleto. Ex: desconto de 25% = `25`. OBS: você não pode adicionar essa tag caso a tag de desconto por valor já esteja presente.
data-boleto-helper-text | --- | Mensagem opcional que aparecerá embaixo do botão de pagamento Boleto.
data-credit-card-helper-text | --- | Mensagem opcional que aparecerá embaixo do botão de pagamento Cartão de Crédito.
data-payment-methods | `credit_card,boleto` | Meios de pagamento disponíveis no Checkout.
data-card-brands | `visa,mastercard,amex,aura,jcb,diners,elo` | Bandeiras aceitas pelo Checkout.
data-max-installments | `1` | Número máximo de parcelas aceitas.
data-ui-color | `#1a6ee1` | Cor primária da interface de Checkout.
data-postback-url | --- | Endereço da URL de POSTback do seu sistema, que receberá as notificações das alterações de status das transações |
data-create-token | `true` | Habilita a geração do token para autorização da transação. <br> **OBS**: Caso você queira apenas pegar os dados do cliente, deixe esse atributo com o valor `false`, e realize a transação normalmente no seu backend, com os dados informados no formulário do checkout.
| data-customer-name | - | Nome do cliente |
| data-customer-document-number | - | CPF ou CNPJ do cliente |
| data-customer-email | - | E-mail do cliente |
| data-customer-address-street | - | Nome do logradouro do cliente |
| data-customer-address-street-number | - | Número do imóvel do cliente |
| data-customer-address-complementary | - | Complemente do endereço do cliente |
| data-customer-address-neighborhood | - | Bairro do cliente |
| data-customer-address-city | - | Cidade do cliente |
| data-customer-address-state | - | Estado (unidade federativa) do cliente |
| data-customer-address-zipcode | - | Código de endereçmento postal (CEP) da cidade do cliente |
| data-customer-phone-ddd | - | DDD do telefone do cliente |
| data-customer-phone-number | - | Número do telefone do cliente |
| data-interest-rate | - | Taxa de juros a ser cobrada na transação |
| data-free-installments | - | Número de parcelas que não terão juros cobrados |
| data-default-installment | `1` | Define a parcela padrão selecionada ao abrir o checkout | 
| data-header-text | `Total a pagar {price_info}` | Define o texto do cabeçalho. <br>**OBS**: Você poderá utilizar a variável `{price_info}` para injetar o valor do pagamento no texto. |
| data-payment-button-text | `Pagar` | Define o texto do botão final de pagamento. |

## Inserindo o Checkout via API

Caso você deseje ter um controle maior sobre a inicialização checkout, ou se você possui um `single-page app`, você pode gerar o formulário e abrir o checkout diretamente via API.

**Atenção:** não esqueça de substituir a string `{{ ENCRYPTION KEY }}` pela `encryption_key` de sua conta.

```html
<html>
	<head>
		<!-- SCRIPT PAGAR.ME -->
	    <script src="https://assets.pagar.me/checkout/checkout.js"></script>
		<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
	</head>
	<body>
		<button id="pay-button">Abrir modal de pagamento</button>

		<script>
			$(document).ready(function() {
				var button = $('#pay-button');

				button.click(function() {

					// INICIAR A INSTÂNCIA DO CHECKOUT
					// declarando um callback de sucesso
					var checkout = new PagarMeCheckout.Checkout({"encryption_key":"ENCRYPTION KEY", success: function(data) {
						console.log(data);
						//Tratar aqui as ações de callback do checkout, como exibição de mensagem ou envio de token para captura da transação
					}});

					// DEFINIR AS OPÇÕES
					// e abrir o modal
					// É necessário passar os valores boolean em "var params" como string
					var params = {"customerData":"false", "amount":"100000", "createToken": "true", "interestRate": 10 };
					checkout.open(params);
				});
			});
		</script>
	</body>
</html>
```

### Parâmetros para abertura do Checkout

Tag | Padrão | Descrição
--- | ------ | ---------
amount | --- | Valor da transação (em centavos) a ser capturada pelo Checkout. Ex: R$14,79 = `1479`
buttonText | `Pagar` | Texto do botão de pagamento.
buttonClass | --- | Classe CSS a ser adicionada no botão de pagamento.
customerData | `true` | Caso não deseje capturar dados do cliente pelo Checkout, setar como `false`.
paymentMethods | `credit_card,boleto` | Meios de pagamento disponíveis no Checkout.
cardBrands | `visa,mastercard,amex,aura,jcb,diners,elo` | Bandeiras aceitas pelo Checkout.
maxInstallments | `1` | Número máximo de parcelas aceitas.
uiColor | `#1a6ee1` | Cor primária da interface de Checkout.
postbackUrl | --- | Endereço da URL de POSTback do seu sistema, que receberá as notificações das alterações de status das transações |
createToken | `true` | Habilita a geração do token para autorização da transação. <br> **OBS**: Caso você queira apenas pegar os dados do cliente, deixe esse atributo com o valor `false`, e realize a transação normalmente no seu backend, com os dados informados no formulário do checkout.
customerName | - | Nome do cliente 
customerDocumentNumber | - | CPF ou CNPJ do cliente 
customerEmail | - | E-mail do cliente 
customerAddressStreet | - | Nome do logradouro do cliente 
customerAddressStreetNumber | - | Número do imóvel do cliente 
customerAddressComplementary | - | Complemente do endereço do cliente 
customerAddressNeighborhood | - | Bairro do cliente 
customerAddressCity | - | Cidade do cliente 
customerAddress-State | - | Estado (unidade federativa) do cliente 
customerAddressZipcode | - | Código de endereçmento postal (CEP) da cidade do cliente 
customerPhoneDdd | - | DDD do telefone do cliente 
customerPhoneNumber | - | Número do telefone do cliente 
interestRate | - | Taxa de juros a ser cobrada na transação 
freeInstallments | - | Número de parcelas que não terão juros cobrados 
defaultInstallment | `1` | Define a parcela padrão selecionada ao abrir o checkout 
headerText | `Total a pagar {price_info}` | Define o texto do cabeçalho. <br>**OBS**: Você poderá utilizar a variável `{price_info}` para injetar o valor do pagamento no texto. |
paymentButtonText | `Pagar` | Define o texto do botão final de pagamento. |


## Capturando a transação

Com o `token` em mãos, agora basta realizar a captura da transação no seu
servidor. Por motivos de segurança, você precisará fornecer novamente o valor a
ser capturado.

```shell
curl -X POST "https://api.pagar.me/1/transactions/{TOKEN}/capture"
  -d 'amount=1000'
  -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0'
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.find_by_id("{TOKEN}")
transaction.capture({:amount => 1000})
```

```php
<?php
	require("pagarme-php/Pagarme.php");

	Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = PagarMe_Transaction::findById("{TOKEN}");
	$transaction->capture(1000);
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Transaction transaction = PagarMeService.GetDefaultService().Transactions.Find("{TOKEN}");
transaction.Capture(1000);
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).

Após realizar a captura de uma transação de cartão de crédito, a transação
terá status `paid`, indicando que o cartão do usuário foi cobrado com sucesso.

Caso a transação seja um boleto bancário, a transação terá status
`waiting_payment` e a URL do boleto bancário para pagamento estará disponível na
variável `boleto_url`.

<aside class="notice">Após a finalização da transação em sua página, você terá
5 horas para capturá-la no seu servidor. Após esse período, a transação será
recusada pelo motivo `capture_timeout`.</aside>

## Enviando metadata com Checkout

Com o checkout Pagar.me também é possível enviar metadata em uma transação. Para o caso do checkout, o envio de metadata é feito no momento de captura da transação. Basta passar a metada como parâmetros do request de captura.

Qualquer dúvida sobre metadata, basta checar a seção _metadata_ na [documentação para metadata](https://docs.pagar.me/advanced/#enviando-dados-adicionais-metadata).

```shell
curl -X POST "https://api.pagar.me/1/transactions/{TOKEN}/capture"
  -d 'amount=1000' \
  -d 'api_key=ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0' \
  -d 'metadata[id_pedido]=12345' \
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

transaction = PagarMe::Transaction.find_by_id("{TOKEN}")
transaction.capture({
	:amount => 1000,
	:metadata => {
		:id_pedido => 12345
	}
})
```

```php
<?php

    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

	$transaction = PagarMe_Transaction::findById($transaction_token);

	$transaction->capture( array('amount' => 1000, 'metadata' => array('id_pedido' => 1234 )));

?>
```

```cs
```

## Checkout em Assinaturas

Para usar o Checkout Pagar.me com nosso sistema de assinaturas você precisa passar uma opção adicional para o formulário.

Veja o exemplo abaixo:

```html
<form method="POST" action="/comprar">
	<script type="text/javascript"
		src="https://assets.pagar.me/checkout/checkout.js"
		data-button-text="Pagar"
		data-encryption-key="ek_test_Ec8KhxISQ1tug1b8bCGxC2nXfxqRmk"
		data-amount="1000"
		data-create-token="false">
	</script>
</form>
```

Usando essa configuração o Checkout passa a não gerar um `token` para ser usado para capturar a transação, em vez disso ele gera o `card_hash` dos dados do cartão e faz uma requisição POST para a url informada no formulário junto com os outros campos do formulário.

## Acessando os dados do Checkout

Após o Checkout enviar os dados para o seu servidor, você vai receber vários parâmetros da requisição enviada pelo Checkout, todas as chaves para acessar os valores começam com `pagarme`.

Exemplo de uso com PHP:

```php
$card_hash = $_POST['pagarme']['card_hash'];
```

## Criando assinaturas

Você só precisar pegar os dados da requisição POST e passar eles para a lib que você usar.

Abaixo temos exemplos da implementação:

```shell
Não é possível fazer essa implementação em Shell script 
```

```ruby
require 'pagarme'

PagarMe.api_key = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

subscription = PagarMe::Subscription.new({
    :payment_method => 'credit_card',
	:plan_id: 'ID_DO_PLANO_AQUI',
    :card_hash => "CARD_HASH_AQUI",
    :postback_url => "POSTBACK_URL_AQUI",
    :customer => {
        email: 'EMAIL_DO_CLIENTE_AQUI'
})

subscription.create
```

```php
<?php
    require("pagarme-php/Pagarme.php");

    Pagarme::setApiKey("ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0");

    $subscription = new PagarMe_Subscription(array(
		"payment_method" => "credit_card",
        "plan_id" => "ID_DO_PLANO_AQUI",
        "card_hash" => "CARD_HASH_AQUI",
		"postback_url" => "POSTBACK_URL_AQUI",
        'customer' => array(
            'email' => 'EMAIL_DO_CLIENTE_AQUI'
        )));

    $subscription->create();
?>
```

```cs
PagarMeService.DefaultApiKey = "ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0";

Plan plan = PagarMeService.GetDefaultInstance().Plans.Find("ID_DO_PLANO_AQUI");

Subscription subscription = new Subscription {
	PaymentMethod = PaymentMethod.CreditCard,
	Plan = plan,
	CardHash = "CARD_HASH_AQUI",
	Customer = new Customer {
		Email = "EMAIL_DO_CLIENTE_AQUI"
	}
};

subscription.Save();
```

> Não se esqueça de substituir `ak_test_grXijQ4GicOa2BLGZrDRTR5qNQxJW0` pela
> sua [chave de API](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).


