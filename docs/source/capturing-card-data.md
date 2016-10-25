---
title: Capturando os dados de cartão

language_tabs:
  - shell
  - ruby
  - php

search: true
---

# Capturando os dados de cartão

Com o Pagar.me, você pode realizar a captura dos dados de cartão sem que o seu
cliente precise sair da sua página/aplicativo. Para que o processo aconteça de
forma segura, você precisará incluir nossa biblioteca em sua página/aplicativo
para realizar a captura desses dados.

Nossa biblioteca irá realizar a geração do `card_hash`, que é uma representação
segura dos dados de cartão e o único dado de cartão que deverá ser enviado para
o seu servidor.

Para boletos bancários, não é necessário utilizar o `card_hash`, já que não há
a transmissão de dados sensíveis entre o cliente, o seu servidor e o
Pagar.me.

## Capturando os dados em uma página web

Para realizar a geração do `card_hash` dentro de uma página web, você precisará
incluir o `Pagarme.js`, nosso JavaScript, na sua página. Para isso, insira o
seguinte código antes do final da seção `head` da sua página HTML:

```html
<script src="https://assets.pagar.me/js/pagarme.min.js"></script>
```

Depois, insira um formulário para digitar os dados de cartão.

```html
<form id="payment_form" action="https://seusite.com.br/transactions/new" method="POST">
	Número do cartão: <input type="text" id="card_number"/>
	<br/>
	Nome (como escrito no cartão): <input type="text" id="card_holder_name"/>
	<br/>
	Mês de expiração: <input type="text" id="card_expiration_month"/>
	<br/>
	Ano de expiração: <input type="text" id="card_expiration_year"/>
	<br/>
	Código de segurança: <input type="text" id="card_cvv"/>
	<br/>
	<div id="field_errors">
	</div>
	<br/>
	<input type="submit"></input>
</form>
```

Nesse exemplo, usaremos o jQuery para gerar e inserir o `card_hash` no seu formulário, 
então incluiremos esse script antes do final da seção `head` da sua página HTML:

```html
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
```

Agora, no seu JavaScript, você precisará detectar quando o botão do formulário
for pressionado e, a partir daí, chamar o `Pagarme.js` para gerar o `card_hash`
e substituir os outros campos dos dados de cartão por ele:

```javascript
$(document).ready(function() { // quando o jQuery estiver carregado...
	PagarMe.encryption_key = "ek_test_Ec8KhxISQ1tug1b8bCGxC2nXfxqRmk";

	var form = $("#payment_form");

	form.submit(function(event) { // quando o form for enviado...
		// inicializa um objeto de cartão de crédito e completa
		// com os dados do form
		var creditCard = new PagarMe.creditCard();
		creditCard.cardHolderName = $("#payment_form #card_holder_name").val();
		creditCard.cardExpirationMonth = $("#payment_form #card_expiration_month").val();
		creditCard.cardExpirationYear = $("#payment_form #card_expiration_year").val();
		creditCard.cardNumber = $("#payment_form #card_number").val();
		creditCard.cardCVV = $("#payment_form #card_cvv").val();

		// pega os erros de validação nos campos do form
		var fieldErrors = creditCard.fieldErrors();

		//Verifica se há erros
		var hasErrors = false;
		for(var field in fieldErrors) { hasErrors = true; break; }

		if(hasErrors) {
			// realiza o tratamento de errors
			alert(fieldErrors);
		} else {
			// se não há erros, gera o card_hash...
			creditCard.generateHash(function(cardHash) {
				// ...coloca-o no form...
				form.append($('<input type="hidden" name="card_hash">').val(cardHash));
				// e envia o form
				form.get(0).submit();
			});
		}

		return false;
	});
});
```

> Não se esqueça de substituir `ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk` pela
> sua [chave de encriptação](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).

## Capturando os dados em um aplicativo para iOS

Para realizar a geração do `card_hash` dentro de um aplicativo para iOS, você
precisará incluir nossa biblioteca dentro dele. Para isso, [baixe a biblioca
para iOS no GitHub do
Pagar.me](https://github.com/PagarMe/pagarme-ios/archive/master.zip) e adicione
a pasta `PagarMeAPI` e o framework `Security.framework` ao seu projeto.

Para utilizar a biblitoca, primeiro importe os headers:

```objective_c
#import "PagarMe.h"
```

E para gerar o `card_hash` a partir dos dados de cartão:

```objective_c
[PagarMe sharedInstance].encryptionKey = @"ek_test_Ec8KhxISQ1tug1b8bCGxC2nXfxqRmk";

PagarMeCreditCard *creditCard = [[PagarMeCreditCard alloc] init];
creditCard.cardNumber = @"4111111111111111";
creditCard.cardHolderName = @"Test User";
creditCard.cardExpiracyMonth = 12;
creditCard.cardExpiracyYear = 15;
creditCard.cardCvv = @"123";

// Valida os dados do cartão de crédito antes de gerar o card_hash...
NSDictionary *errors = [creditCard fieldErrors];

if([errors count] != 0) {
    NSLog(@"Foram encontrados erros nos dados do cartão de crédito: ");
    NSLog(@"%@", errors);
} else {
    [creditCard generateHash:^(NSError *error, NSString *cardHash) {
        if(error) {
            NSLog(@"Erro gerando o card_hash: %@", error);
            return;
        }

        NSLog(@"card_hash gerado: %@", cardHash);
        // Agora envie a string cardHash para o seu servidor.
    }];
}
```

> Não se esqueça de substituir `ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk` pela
> sua [chave de encriptação](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).

## Capturando os dados em um aplicativo para Windows ou Windows Phone (.NET)

Para realizar a geração do `card_hash` dentro de um aplicativo para Windows ou Windows Phone (plataforma .NET)
você precisará incluir nossa biblioteca dentro dele. Para isso, instale a [biblioteca .NET](https://www.nuget.org/packages/Pagar.me/) ou a [biblioteca portável para .NET](https://www.nuget.org/packages/Portable.Pagar.me/).

Para utilizar a biblitoeca, primeiro importe o namespace:

```cs
using PagarMe;
```

E para gerar o `card_hash` a partir dos dados de cartão:

```cs
PagarMeService.DefaultEncryptionKey = "ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk";

CardHash card = new CardHash();

card.CardNumber = "4111111111111111";
card.CardHolderName = "Test User";
card.CardExpirationDate = "1017";
card.CardCvv = "123";

string cardhash = card.Generate();
```

> Não se esqueça de substituir `ek_test_Ec8KhxISQ1tug1b8bCcxC2nXfxqRnk` pela
> sua [chave de encriptação](https://dashboard.pagar.me/#/myaccount/apikeys) disponível no seu
> [Dashboard](https://dashboard.pagar.me/).

## Próximos passos

Com o `card_hash` em mãos no seu servidor, você pode [realizar uma
transação](/transactions), [criar uma assinatura](/plans-subscriptions) ou
[armazenar esse cartão e cobrá-lo posteriormente](/cards).
