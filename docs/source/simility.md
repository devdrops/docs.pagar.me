# Integração com o antifraude
O Pagar.me utiliza as tecnologias mais avançadas em prevenção de fraude do mercado, criando modelos personalizados para o seu modelo de negócio que se adaptam aos padrões dos fraudadores em tempo real conforme eles adotam novas estratégias. Além disso, a integração com o script de reconhecimento de dispositivos e `fingerprinting` permite que sejamos capaz de tirar conclusões sobre o comportamento do comprador na hora da compra para detectar padrões suspeitos e perceber ataques de fraude, parando transações que vêm de dispositivos fraudulentos e utilizando informações de fraude de diversos sites diferentes. Assim, o fraudador que for reconhecido em um cliente do Pagar.me não conseguirá realizar fraudes em nenhum outro.

## Integração do _Device Recon_
O `Device Recon` é um script que está no seu site ou aplicativo e colecta características sobre os dispositivos dos seus usuários (computadores, tablets, telefones, etc), como por exemplo _browser_, sistema operacional, _hardware_, e comportamento do usuário. O antifraude usa essas características para determinar o grau de risco do dispositivo e do usuário. O guia a seguir foi extraído da documentação disponível [aqui](https://simility.com/developer/#devicerecon).

### JavaScript

Temos quatro tipos de integração JavaScript. Escolha aquela que melhor se encaixa às suas necessidades e cole o bloco de código JavaScript correspondente na seção `<body>` de todas as páginas do seu site.

#### JS1: Teste

Este setup é para testar a instalação correta, e não deve ser utilizado em produção.

```html

<script>
  var similityContext = {
    "customer_id": "microcorpinc1"  // obrigatório; fornecido pelo Pagar.me durante o setup
  };
</script>
<script type="application/javascript" src="https://cdn.simility.com/b.js"></script>

```

#### JS2: Padrão

Esta é a instalação padrão em produção.

```html

<script>
  var similityContext = {
    "customer_id": "microcorpinc1",  // obrigatório; fornecido pelo Pagar.me durante o setup
    "session_id": variavel.de.identificacao.de.sessao.da.sua.pagina,  // recomendado; único por sessão do usuário, gerado no seu backend
    "simility_lite": true  // opcional; true por padrão
  };
</script>
<script type="application/javascript" src="https://cdn.simility.com/b.js"></script>

```


> **session_id** - Nós recomendamos fortemente que você defina a variável session_id para o identificador de sessão gerado no seu backend (como por exemplo o **jsessionid**). Este session_id é o que persiste no seu backend durante a sessão do usuário e é a maneira padrão de unir as sessões do frontend com as transações no backend.

> **simility_lite** - Isto está marcado como **true** por padrão. Para que o script seja capaz de detectar informações mais detalhadas de comportamento do usuário (como geolocalização ou movimentos do mouse), precisa ser marcado como **false**, e outras informações precisarão ser passadas. Caso deseje utilizar este recurso, contacte nossa equipe de suporte.

### iOS

A API de iOS do antifraude permite coletar dados para detecção de fraude do app iOS. Uma vez que esse API é utilizado, ele vai coletar dados e enviar ao servidor do antifraude de maneira assíncrona sem afetar as operações da interface com o usuário. Dois artefatos serão fornecidos: o arquivo header **SimilityBeacon.h** e a biblioteca touch estática Cocoa **libSimilityBeacon.a**.

#### Parâmetros

* **customerId** (NSString) - Obrigatório e fornecido pelo Pagar.me durante o setup;
* **sessionId** (NSString) - Obrigatório e gerado pelo seu backend, conforme descrito acima (em JS2: Padrão);

> Você precisará linkar bibliotecas/frameworks Foundation, UIKit, CoreTelephony, Security e CommonCrypto se elas já não forem utilizadas no seu aplicativo. Você também precisará linkar o framework AdSupport se o app tem propagandas.

#### Declarações

```c

+ (void) initBeacon:(NSString *) customerId sessionId:(NSString *) sessionId;

+ (void) initBeacon:(NSString *) customerId sessionId:(NSString *) sessionId userId:(NSString *) userId;

```

#### Apps em Objective C

1. Baixe o arquivo em zip [neste link](https://storage.googleapis.com/simility-beacon-bins/ios/ios_beacon_v1.5.zip) (ou [neste](https://storage.googleapis.com/simility-beacon-bins/ios/ios_beacon_advertising_id_v1.5.zip) caso seu app tenha propagandas);
2. Crie um novo projeto Xcode e abra o projeto existente;
3. Arraste os arquivos extraídos para o grupo de "supporting files";
4. Dentro da caixa de diálogo de opções para abrir arquivo, escolha a opção de copiar itens se necessário e adicione aos alvos;
5. Importe o arquivo de header SimilityBeacon e adicione o código que chama a API Simility (escrito abaixo);
6. Compile e execute.

```c

#import "ViewController.h"
#import "SimilityBeacon.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
  [super viewDidLoad];
  // Do any additional setup after loading the view, typically from a nib.
  NSDictionary *metadata = @{@"orderId" : @"order_no_8765456"};
  NSString *customerId = @"customer_id_issued_by_simility";
  [SimilityBeacon initBeacon:customerId sessionId:@"cae1234567" userId:@"user1" metadata:metadata];
}

- (void)didReceiveMemoryWarning {
  [super didReceiveMemoryWarning];
  // Dispose of any resources that can be recreated.
}

@end

```

#### Apps em Swift
1. Baixe o arquivo em zip [neste link](https://storage.googleapis.com/simility-beacon-bins/ios/ios_beacon_v1.5.zip) (ou [neste](https://storage.googleapis.com/simility-beacon-bins/ios/ios_beacon_advertising_id_v1.5.zip) caso seu app tenha propagandas);
2. Se o seu projeto não tem um arquivo de header em Objective C, crie um arquivo em Objective C vazio e coloque-o junto dos arquivos extraídos;
3. Crie um novo projeto Xcode e abra o projeto existente;
4. Arraste os arquivos extraídos para o grupo de "supporting files";
5. Dentro da caixa de diálogo de opções para abrir arquivo, escolha a opção de copiar itens se necessário e adicione aos alvos;
6. Quando aparece a opção de criar um arquivo de header, opte por criá-lo;
7. Crie um novo grupo de "supporting files" e mova os arquivos para este grupo. Você pode deletar o arquivo vazio de Objective C se você o criou anteriormente;
8. Importe o arquivo de header SimilityBeacon e adicione o código que chama a API Simility (escrito abaixo);
9. Compile e execute.

```swift

import UIKit

class ViewController: UIViewController {

  override func viewDidLoad() {
      super.viewDidLoad()
      // Do any additional setup after loading the view, typically from a nib.
      let customerId = "customer_id_issued_by_simility"
      let metadata = ["order_id":"order_no_865432"]
      SimilityBeacon.initBeacon(customerId, sessionId: "cae876543", userId: "user1", metadata: metadata)
  }

  override func didReceiveMemoryWarning() {
      super.didReceiveMemoryWarning()
      // Dispose of any resources that can be recreated.
  }

}

```

### Android
A API de Android do antifraude permite coletar dados para detecção de fraude do app Android. O arquivo **beacon_sdk.jar** será fornecido. Assim que o cliente chamar a API, ela irá computar sinais no background sem afetar nenhuma operação da interface com o usuário. Sinais são coletados baseados nas permissões no Manifest do aplicativo.

#### Parâmetros
* **context** - Contexto do aplicativo chamando a API;
* **customerId** - Obrigatório e fornecido pelo Pagar.me durante o setup;
* **sessionId** - Obrigatório e gerado pelo seu backend, conforme descrito acima (em JS2: Padrão);

#### Declarações
```java

public static void initBeacon(Context context, String customerId, String sessionId)

public static void initBeacon(Context context, String customerId, String sessionId, String userId)

```

#### Apps usando Eclipse IDE
1. Baixe e extraia o [arquivo .jar](https://storage.googleapis.com/simility-beacon-bins/andorid/android_beacon_v1.53.zip);
2. Crie um novo projeto de Aplicativo Android e abra o projeto existente;
3. Crie uma nova pasta chamada libs na pasta ```raiz``` do seu projeto se ela já não existir;
4. Copie o arquivo JAR para esta pasta;
5. Importe ```com.simility.beacon.SimilityBeacon``` e chame a API do antifraude com os parâmetros aplicáveis (escrito abaixo);
6. Compile e execute.

```java

package com.example.androidintegratiodemo;

import java.util.HashMap;
import java.util.Map;

import com.simility.beacon.SimilityBeacon;

import android.app.Activity;
import android.os.Bundle;

public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        String customerId = "your_simility_customer_id";
        Map<String, String> metadata = new HashMap<String, String>();
        metadata.put("order_id", "order_no_8765542");
        SimilityBeacon.initBeacon(getApplicationContext(), customerId, "cae8765432", "user1", metadata);
    }
}

```

#### Apps usando Android Studio IDE
1. Baixe e extraia o [arquivo .jar](https://storage.googleapis.com/simility-beacon-bins/andorid/android_beacon_v1.53.zip);
2. Crie um novo projeto de Aplicativo Android e abra o projeto existente;
3. Vá para a estrutura do projeto na barra à esquerda e encontre a pasta ```libs``` dentro de ```app```;
4. Copie o arquivo JAR para esta pasta. Clique nele com o botão direito do mouse e escolha a opção de adicionar como biblioteca (Add as Library). Isso tomará conta de adicionar os arquivos de compilação em build.grade;
5. Importe ```com.simility.beacon.SimilityBeacon``` e chame a API do antifraude com os parâmetros aplicáveis (escrito abaixo);
6. Compile e execute.

```java

package com.example.androidintegratiodemo;

import java.util.HashMap;
import java.util.Map;

import com.simility.beacon.SimilityBeacon;

import android.app.Activity;
import android.os.Bundle;

public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        String customerId = "your_simility_customer_id";
        Map<String, String> metadata = new HashMap<String, String>();
        metadata.put("order_id", "order_no_8765542");
        SimilityBeacon.initBeacon(getApplicationContext(), customerId, "cae8765432", "user1", metadata);
    }
}

```

#### Apps usando PhoneGap/Cordava
1. Siga os passos para adicionar um arquivo JAR conforme descritos acima;
2. Para chamar a API do antifraude, adicione ```import``` na classe estendendo ```DroidGap```, chame ```super.init()``` e adicione ```JavascriptInterface``` com ```WebView``` de superclasse. Código exemplo para a classe estendendo ```DroidGap```:

```java

package com.simility.phonegaptestapp;

import org.apache.cordova.DroidGap;
import com.simility.beacon.SimilityBeacon;
import android.app.Activity;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.webkit.JavascriptInterface;
import android.webkit.WebView;

public class MainActivity extends DroidGap/*Activity*/ {

  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    super.init();
    super.appView.addJavascriptInterface(this, "MainActivity");
    super.loadUrl("file:///android_asset/www/index.html");
  }

  @JavascriptInterface
  public void callSimilityBeacon() {
        String customerId = "your_simility_customer_id";
        Map<String, String> metadata = new HashMap<String, String>();
        metadata.put("order_id", "order_no_8765542");
        SimilityBeacon.initBeacon(super.getActivity(), customerId, "cae8765432", "user1", metadata);
  }
}

```

3. Adicione um método com anotação ```@JavascriptInterface```, que permite espor os métodos ao JavaScript (isso chamará a API do antifraude com o contexto de super atividade com parâmetro);
4. No código fonte da página, adicione o código para que o JavaScript chame o SimilityBeacon. Código exemplo:

```html

<!DOCTYPE HTML>
<html>
  <head>
    <title>Sample</title>
    <script type="text/javascript" charset="utf-8" src="cordova-2.0.0.js"></script>
  </head>
  <body>
    <h1>PhoneGap Sample App</h1>
    <script type="text/javascript">
    (function(){
         window.MainActivity.callSimilityBeacon();
    })();
    </script>
  </body>
</html>

```

## Envio de Histórico de Transações
Para que o antifraude seja o mais assertivo possível, é ideal que tenhamos um histórico de todas as transações já realizadas por vocês, assim como uma gama de informações sobre cada uma delas. Uma vez que a integração com o _Device Recon_ estiver encaminhada, um arquivo .csv deve ser enviado ao email `compliance@pagar.me` contendo todas as transações que vocês já tiveram, assim como as seguintes colunas:

* **id** - O número identificador da transação;
* **status** - O estado da transação, entre ```paid```, ```refused```, ```refunded```, e ```chargedback```;
* **created_at** - Data e hora em que a transação ocorreu, formato ```0000-00-00T00:00:00-03:00```;
* **accrual_date** - Data e hora em que a transação virou _chargeback_, caso esse seja o caso, formato ```0000-00-00T00:00:00-03:00```;
* **refused_reason** - Caso a transação seja estornada, o motivo pelo estorno. Se for estornada a pedido do cliente, utilize ```user_request```; caso haja suspeita de fraude, utilize ```suspected_fraud```; caso seja outro motivo, descreva o motivo;
* **IP** - O IP do comprador;
* **platform** - Tipo de plataforma do site (```web```, ```ios```, ```android```, ```windows_phone```, ```other```);
* **user_id** - Identificador do usuário no seu sistema;
* **email** - Email do usuário;
* **registered_at** - Data de registro do usuário, formato ```0000-00-00T00:00:00-03:00```;
* **login_source** - De onde veio o login; ```guest``` caso seja visitante, ```registered``` caso seja registrado no seu site, ```facebok```, ```google```, ```twitter```, ```linkedin```, ```github```, ```bitbucket```, ```steam```, ```battlenet```, ```psn``` ou ```live``` caso o usuário tenha usado uma dessas para logar, ```other``` caso não seja nenhuma dessas opções.

As informações das colunas seguintes são sobre a pessoa que realizou o pagamento (geralmente portador do cartão).

* **billing_name** - Nome completo;
* **billing_document_number** - CPF ou CNPJ;
* **billing_born_at** - Data de nascimento;
* **billing_gender** - Gênero (M/F/O);
* **billing_country** - País;
* **billing_state** - Estado;
* **billing_city** - Cidade;
* **billing_zipcode** - CEP;
* **billing_neighborhood** - Bairro;
* **billing_street** - Rua;
* **billing_street_number** - Número;
* **billing_complementary** - Complemento;
* **billing_latitude** - Latitude do endereço;
* **billing_longitude** - Longitude do endereço;
* **billing_phone_numbers** - Lista de telefones, com formato ```{+55-11-99999-9999,+55-11-99999-9999}``` onde ```+55``` precisa ser substituído pelo código do país, ```11``` pelo DDD, e ```99999-9999``` pelo número de telefone.

As informações das colunas seguintes são sobre a pessoa que realizou a compra (e podem ser as mesmas que as anteriores).

* **buyer_name** - Nome completo;
* **buyer_document_number** - CPF ou CNPJ;
* **buyer_born_at** - Data de nascimento;
* **buyer_gender** - Gênero (M/F/O);
* **buyer_country** - País;
* **buyer_state** - Estado;
* **buyer_city** - Cidade;
* **buyer_zipcode** - CEP;
* **buyer_neighborhood** - Bairro;
* **buyer_street** - Rua;
* **buyer_street_number** - Número;
* **buyer_complementary** - Complemento;
* **buyer_latitude** - Latitude do endereço;
* **buyer_longitude** - Longitude do endereço;
* **buyer_phone_numbers** - Lista de telefones, com formato ```{+55-11-99999-9999,+55-11-99999-9999}``` onde ```+55``` precisa ser substituído pelo código do país, ```11``` pelo DDD, e ```99999-9999``` pelo número de telefone.

As informações das colunas seguintes são sobre a pessoa que receberá o produto, mesmo que seja eletrônico (e podem ser as mesmas que as anteriores).

* **shipping_name** - Nome completo;
* **shipping_document_number** - CPF ou CNPJ;
* **shipping_born_at** - Data de nascimento;
* **shipping_gender** - Gênero (M/F/O);
* **shipping_country** - País;
* **shipping_state** - Estado;
* **shipping_city** - Cidade;
* **shipping_zipcode** - CEP;
* **shipping_neighborhood** - Bairro;
* **shipping_street** - Rua;
* **shipping_street_number** - Número;
* **shipping_complementary** - Complemento;
* **shipping_latitude** - Latitude do endereço;
* **shipping_longitude** - Longitude do endereço;
* **shipping_phone_numbers** - Lista de telefones, com formato ```["+55-11-99999-9999", "+55-11-99999-9999"]``` onde ```+55``` precisa ser substituído pelo código do país, ```11``` pelo DDD, e ```99999-9999``` pelo número de telefone;
* **shipping_method** - Método de entrega ```virtual```, ```pac```, ```sedex```, ```e-sedex```, ```transportadora```, ```agendado```, ```expresso```, ```privado```, ```economico```, ```internacional``` (```virtual``` para bens adquiridos online, como ingressos);
* **shipping_fee** - Taxa de entrega;
* **shipping_favorite** - True se este endereço for marcado como favorito do usuário;
* **shipping_delivery_time** - Tempo estimado da entrega (em minutos para entrega de comida, em dias para entrega de produtos);
* **shipping_takeout_time** - (Para entrega de comida) tempo estimado de preparo da comida em minutos, quando for retirada no local;
* **shipping_scheduled** - True se a entrega tiver data/horário agendado;
* **shipping_schedule_time** - Se a coluna anterior for true, a data/horário de entrega.

As próximas colunas são JSONs contendo listas de várias informações sobre as transações.

* **shopping_cart**

```javascript
[
    { // todos os itens comprados nesta transação
        "name": "", // nome do item
        "type": "", // tipo de item - enum: goods/services/ticket/food_delivery/other
        "quantity": 0, // quantidade
        "unit_price": 0, // preço unitário
        "totalAdditions": 0, // total de adições ao produto
        "totalDiscounts": 0, // total de descontos neste produto

        // informação de tickets (quando type = ticket)
        "event_id": "0", // ID do evento dentro do objeto "events"
        "ticket_type_id": "0", // ID do tipo de ticket dentro do evento
        "ticket_owner_name": "", // nome do dono daquele ticket
        "ticket_owner_document_number": "", // documento do dono daquele ticket

        // informações de restaurante (quando type = food_delivery)
        "food_type_id": "" // ID do tipo de comida que este item é, conforme food_types abaixo
    }
]
```

* **events** - Apenas ticketerias têm esta coluna:

```javascript
[
    { // todos os eventos para os quais ingressos foram comprados nesta transação
        "id": "0", // ID do evento
        "name": "", // nome
        "type": "",  // tipo de evento - enum
        "date": "00-00-0000T00:00:00-03:00", // data do evento
        "venue_name": "", // nome da casa de eventos
        "address": { // endereço
            "country": "Brasil", // país
            "state": "SP", // estado
            "city": "Sao Paulo", // cidade
            "zipcode": "05414001", // CEP
            "neighborhood": "", // bairro
            "street": "", // rua
            "street_number": "00", // número
            "complementary": "" // complemento/referência
        },
        "ticket_types": [
            { // todos os tipos de ticket que existem para este evento
                "id": "0", // ID do tipo de ticket - precisa ser único para este evento
                "name": "", // nome do tipo de ticket
                "type": "", // entrada inteira ou parcial - enum: full/partial_price
                "batch": 1, // lote do ticket
                "price": 0, // preço unitário
                "available_number": 0, // número de tickets ainda disponíveis deste tipo no momento da compra
                "total_number": 0, // número total deste ticket que foram disponibilizados
                "identity_verified": false, // true se a identidade for verificada no evento
                "assigned_seats": false // true se o evento tiver local marcado
            }
        ]
    }
]
```

* **restaurant** - Apenas serviços de entrega de comida têm esta coluna:

```javascript
{
	"id": "", // ID do restaurante
	"company_group" : "", // em qual company o restaurante foi registrado inicialmente - enum
	"name": "", // nome do restaurante
	"registered_at": "0000-00-00T00:00:00-03:00", // quando o restaurante foi registrado
	"group_id": "", // ID do grupo ao qual este restaurante pertence
	"phone_numbers": [{ // Lista com todos os telefones disponíveis
		"ddi": "00",
		"ddd": "00",
		"number": "00000000",
		"customer_contact": false // true se este for o telefone pelo qual o comprador contacta o restaurante
	}],
	"address": { // endereço
		"country": "Brasil", // país
		"state": "SP", // estado
		"city": "Sao Paulo", // cidade
		"zipcode": "05414001", // CEP
		"neighborhood": "", // bairro
		"street": "", // rua
		"street_number": "00", // número
		"complementary": "", // complemento/referência
		"latitude": 0.0, // latitude
		"longitude": 0.0 // longitude
	},
	"opening_hours": [{ // horários de abertura e fechamento do restaurante
		"day_of_week": 0, // dia da semana - enum 0, 1, 2, 3, 4, 5, 6
		"opening_time": 0, // hora de abertura em UNIX time
		"closing_time": 0 // hora de fechamento em UNIX time
	}],
	"score": 0, // número que determina a ordem de aparecimento daquele restaurante na lista no aplicativo
	"average_price": 0, // preço médio do restaurante
	"payment_method": [{ // métodos de pagamento aceitos pelo restaurante (cartão de crédito, etc)
		"id": "", // ID do tipo de pagamento
		"description": "", // descrição do tipo de pagamento
		"payment_options": [{
			"id": "", // ID da opção de pagamento
			"description": "", // descrição da opção
			"accept_change": false // true se esta opção aceita troco
		}]
	}],
	"food_types": [{ // tipos de comida vendidos
		"id": "", // ID do tipo de comida
		"name": "", // nome do tipo de comida
		"main": false // true se este for o tipo de comida principal do restaurante
	}],
	"accept_online_payment": false, // true se este restaurante aceita pagamentos online
	"supports_delivery": false, // true se esse restaurante realiza entregas a domicilio
	"supports_to_go": false, // true se esse restaurante aceita que a comida seja retirada no local
	"supports_schedule": false, // true se esse restaurante aceita marcar horários de entrega de comida
	"supports_order_tracking": false // true se esse restaurante fornece dados de acompanhamento do pedido
}
```

<script type="text/javascript">
  // Workaround to bypass `rouge` false-positive error
  // MAY GOD BE WITH US
  Array.from(document.getElementsByClassName("err")).forEach( (element, index, array) => element.className = "p" );
</script>

## Antifraud Metadata
Uma vez que o modelo estiver construído e o script de reconhecimento de dispositivos estiver integrado, as transações podem começar a ser processadas pelo antifraude. Para isso, um JSON com as mesmas informações que as pedidas acima precisa ser enviado dentro do campo da integração ```antifraud_metadata```. Instruções para mandar informações neste campo na API se encontram neste link.
