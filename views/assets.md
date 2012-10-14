# Gerenciando Assets

## Conteúdo

- [Registering Assets](#registering-assets)
- [Despejando Assets](#dumping-assets)
- [Dependencia de Assets](#asset-dependencies)
- [Asset Containers](#asset-containers)
- [Bundle Assets](#bundle-assets)

<a name="registering-assets"></a>
## Registrando Assets

> Nota de tradução: Assets em tradução literal significa "ativos". No contexto de desenvolvimento web significa os recursos que serão utilizados na pagina, como arquivos CSS, JavaScript e outros.

A classe **Asset** fornece uma forma simples de gerenciar o CSS e JavaScript usado por sua aplicação. Para registrar uma asset simplismente use o método **add** da classe **Asset**:

#### Registrando um asset:

	Asset::add('jquery', 'js/jquery.js');

O método **add** aceita tres parametros. O primeiro é o nome da asset, o segundo é o caminho para o asset, relativo ao diretório **public** e o terceito é uma lista das dependencias desse asset (veremos isso mais tarde).
Note que nos não dissemos ao método se estavamos registrando um arquivo JavaScript ou CSS. O método **add** usa a extensão do arquivo para determinar o tipo do asset que estão registrando.

<a name="dumping-assets"></a>
## Despejando Assets

Quando você estiver pronto para incluir os links para os assets registrados na sua view você deve usar os métodos **styles** e **scripts**:

#### Despejando assets na sua view:

	<head>
		<?php echo Asset::styles(); ?>
		<?php echo Asset::scripts(); ?>
	</head>

<a name="asset-dependencies"></a>
## Dependencia de Assets

As vezes você vai precisar especificar que um asset possui dependencias. Isso significa que aquele asset requer que outros assets tenham sidos declarados na sua view antes dele ser declarado. Gerenciar a dependencia entre assets no Laravel não poderia ser mais fácil. Lembra dos "nomes" que você deu aos seus assets? Você pode passa-los como o terceiro parametro para o método **add** para declarar as dependencias.

#### Registrando um assets que tem dependencias:

	Asset::add('jquery-ui', 'js/jquery-ui.js', 'jquery');

In this example, we are registering the **jquery-ui** asset, as well as specifying that it is dependent on the **jquery** asset. Now, when you place the asset links on your views, the jQuery asset will always be declared before the jQuery UI asset. Need to declare more than one dependency? No problem:

#### Registering an asset that has multiple dependencies:

	Asset::add('jquery-ui', 'js/jquery-ui.js', array('first', 'second'));

<a name="asset-containers"></a>
## Containers de Assets

Para aumentar o tempo de resposta, é comum posicionar o JavaScript no final dos documentos HTML. Mas e se você precisar posicionar alguns deles no head do seu documento? Sem problema. A classe **Asset** permite, de uma forma simples, gerenciar os **containers** de assets. Simplismente chame o método **container** na classe Asset e mencione o nome do container. Assim que você tiver uma instancia do container, você está livre para adicionar qualquer asset que desejar ao container usando a mesma syntaxe que você ja conhece:

#### Retornando uma instancia de um container de assets:

	Asset::container('footer')->add('example', 'js/example.js');

#### Despejando os assets de um container determinado:

	echo Asset::container('footer')->scripts();

<a name="bundle-assets"></a>
## Assets de Bundles

Antes de aprender como convenientemente adicionar e despejar assets de bundles você pode querer ler a documentação: [criando e publicando assets de bundles](/docs/bundles#bundle-assets).

Quando registrando assets, os caminhos são normalmente relativos ao diretório **public**. No entanto, isso é inconveniente quando estamos lidando com assets de uma bundle, ja que eles residem no diretório **public/bundles**. Mas lembre-se, o Laravel está aqui para facilitar a sua vida. Então, é simples especificar a bundle qual o container deve tratar.

#### Especificando a bundle que o container de assets esta gerenciando:

	Asset::container('foo')->bundle('admin');

Agora, quando você adicionar uma asset, você pode usar caminhos relativos ao diretório daquela bundle no diretório public. Laravel vai automáticamente gerar os caminhos corretos.
