# Views & Respostas

## Conteúdo

- [O Basico](#the-basics)
- [Anexando Dados As Views](#binding-data-to-views)
- [Aninhando Views](#nesting-views)
- [Views Nomeadas](#named-views)
- [Composers de Views](#view-composers)
- [Redirecionamento](#redirects)
- [Redirecionando com Flash Data](#redirecting-with-flash-data)
- [Downloads](#downloads)
- [Erros](#errors)

<a name="the-basics"></a>
## O Basico

Views contém o HTML que sera enviado para a pessoa usando a sua aplicação. Por separar sua view da logica de negocio da sua aplicação, seu código ficará mais limpo e fácil de manter.

Todas as views ficam no diretório **application/views** e tem PHP como extensão do arquivo. A classe **View** fornece uma forma simples de obter suas views e de envia-las ao usuário. Vamos ver um exemplo!

#### Criando a view:

	<html>
		Estou salva em views/home/index.php!
	</html>

#### Retornando a view através de uma rota:

	Route::get('/', function()
	{
		return View::make('home.index');
	});

#### Retornando a view através de um controller

	public function action_index()
	{
		return View::make('home.index');
	});

#### Determinando se uma view existe:

	$exists = View::exists('home.index');

As vezes você pode precisar de um pouco mais de controle sob as respostas enviadas ao navegador. Por exemplo, você pode precisar definir um header customizado para a resposta, ou mudar o código de estado HTTP. Para tal:

#### Retornando uma resposta customizada:

	Route::get('/', function()
	{
		$headers = array('foo' => 'bar');

		return Response::make('Hello World!', 200, $headers);
	});

#### Retornando uma resposta customizada, contendo uma view e dados anexados:

	return Response::view('home', array('foo' => 'bar'));

#### Retornando uma resposta em JSON:

	return Response::json(array('name' => 'Batman'));

#### Retornando models do Eloquent como JSON:

	return Response::eloquent(User::find(1));

<a name="binding-data-to-views"></a>
## Anexando Dados As Views

Tipicamente, uma rota ou controller vai requisitar dados de um model que a view precisa mostrar. Então nos precisamos de uma forma de enviar os dados para a view. Aqui estão algumas formas de fazer isso, escolha a forma que você preferir!

#### Anexando dados a view:

	Route::get('/', function()
	{
		return View::make('home')->with('name', 'James');
	});

#### Acessando os dados anexados na view:

	<html>
		Hello, <?php echo $name; ?>.
	</html>

#### Anexando dados a view utilizando encadeamento:

	View::make('home')
		->with('name', 'James')
		->with('votes', 25);

#### Passando uma array de dados para ser anexada:

	View::make('home', array('name' => 'James'));

#### Utilizando metódos magicos para anexar dados:

	$view->name  = 'James';
	$view->email = 'example@example.com';

#### Usando o acesso em forma de array para anexar dados:

	$view['name']  = 'James';
	$view['email'] = 'example@example.com';

<a name="nesting-views"></a>
## Aninhando Views

Comumente será preciso aninhar views dentro de outras views. As vezes views aninhadas são chamadas de "partials", e ajudam você a manter views pequenas e modulares.

> Nota de tradução: A tradução literal de "Partial" seria "Parcial". Porém por se tratar de uma expressão do desenvolvimento de interfaces web, vamos utiliza-lo sem tradução.

#### Anexando uma view aninhada usando o método "nest":

	View::make('home')->nest('footer', 'partials.footer');

#### Passando dados para uma view aninhada:

	$view = View::make('home');

	$view->nest('content', 'orders', array('orders' => $orders));

As vezes você pode querer incluir uma view diretamente em outra view. Você pode usar a função helper **render**:

#### Usando o helper "render" para exibir uma view:

	<div class="content">
		<?php echo render('user.profile'); ?>
	</div>

É muito comum ter uma view partial que é responsável por mostrar uma instancia de dados em uma lista. Por exemplo, você pode criar uma view partial responsável por mostrar os detalhes sobre um unico pedido. Depois, por exemplo, você pode rodar um loop por uma array de pedidos, renderizando a view parcial para cada um dos pedidos. Isso pode ser feito de forma simples usando o método helper **render_each**:

#### Renderizando uma view partial para cada item na array:

	<div class="orders">
		<?php echo render_each('partials.order', $orders, 'order');
	</div>

O primeiro argumento é o nome da view partial, o segundo é a array de dados e o terceiro é o nome da variável cada item da array quando este for passado para a view partial.

<a name="named-views"></a>
## Views Nomeadas

Views nomeadas podem ajudar a fazer seu código mais expressívo e organizado. Usa-las é simples:

#### Registrando uma view nomeada:

	View::name('layouts.default', 'layout');

#### Obtendo uma instancia de uma view nomeada:

	return View::of('layout');

#### Anexando dados a uma view nomeada:

	return View::of('layout', array('orders' => $orders));

<a name="view-composers"></a>
## Composers de Views

Sempre que uma view é criada, seu evento "composer" será disparado. Você pode esperar por esse evento e usa-lo para anexar assets e dados comuns para a view cada vez que essa for criada. Um caso de uso comum para essa funcionalidade é seria uma partial de *barra de navegação lateral* que mostra uma lista de posts de seu blog aleatoriamente. Você pode aninhar sua view partial na sua view layout, depois definir um composer para aquela partial. O composer pode então fazer uma query para obter os posts da tabela e todas as informações necessárias para a exibição na sua view. Sem código espaguete! Composers são tipicamente definidos em **application/routes.php**. Aqui está um exemplo:

#### Register a view composer for the "home" view:

	View::composer('home', function($view)
	{
		$view->nest('footer', 'partials.footer');
	});

Agora toda vez que a view "home" for criada, uma instancia da View será passada para a Closure registrada, permitindo você preparar a view da forma como você desejar.

#### Registrar um composer que trata multipla views:

	View::composer(array('home', 'profile'), function($view)
	{
		//
	});

> **Nota:** Uma view pode ter mais de um composer. Divirta-se!

<a name="redirects"></a>
## Redirecionamento

É importante notar que ambos, rotas e controllers, precisam que as respostas sejam retornadas com a diretriva "return". Ao invés de chamar "Redirect::to()" quando você quiser redirecionar o usuário, você deve usar "return Redirect::to()". Essa distinção é importante, já que é diferente da maioria dos outros frameworks PHP, o que pode causar uma confusão facilmente caso o desenvolvedor não preste atenção.

#### Redirecionando para outra URI:

	return Redirect::to('user/profile');

#### Redirecionando com um estado específico:

	return Redirect::to('user/profile', 301);

#### Redirecionando para uma URI segura:

	return Redirect::to_secure('user/profile');

#### Redirecionando para a raiz de sua aplicação:

	return Redirect::home();

#### Redirecionando de volta para a ação anterior:

	return Redirect::back();

#### Redirecionando para uma rota nomeada:

	return Redirect::to_route('profile');

#### Redirecionando para uma ação de um controller:

	return Redirect::to_action('home@index');

As vezes você pode precisar redirecionar para uma rota nomeada, mas também precisa especificar valores que serão usados como parametros (wildcards). É fácil substituir os parametros com os valores devidos:

#### Redirecionando para uma rota nomeada com parametros (wildcard):

	return Redirect::to_route('profile', array($username));

#### Redirecionando para uma ação com parametros:

	return Redirect::to_action('user@profile', array($username));

<a name="redirecting-with-flash-data"></a>
## Redirecionando com Flash Data

Depois que um usuário cria uma conta na sua aplicação, é comum mostrar um "bem vindo" ou mensagem informativa, por exemplo: "cadastro efetuado com sucesso". Como você pode definir uma mensagem informativa para estar disponível na proxima request? Use o método with() para enviar uma flash data junto com o seu redirecionamento.

> Nota de tradução: Flash data não possuí uma tradução exata. É uma forma de enviar dados de uma requisição a outra utilizando a sessão do usuário. Não faria muito sentido chamarmos de "dados flash".

	return Redirect::to('profile')->with('status', 'Welcome Back!');

Você pode acessar sua mensagem na view com o método Session get:

	$status = Session::get('status');

*Leitura complementar:*

- *[Sessões](/docs/session/config)*

<a name="downloads"></a>
## Downloads

#### Enviando o download de um arquivo como resposta:

	return Response::download('file/path.jpg');

#### Enviando um arquivo como download e definindo um nome para tal:

	return Response::download('file/path.jpg', 'photo.jpg');

<a name="errors"></a>
## Erros

Para gerar as devidas repostas de erro, simplismente especifique o código de resposta que você deseja retornar. As views correspondentes em **views/error** serão automáticamente retornadas.

#### Gerando uma resposta de erro 404:

	return Response::error('404');

#### Gerando uma resposta de erro 500:

	return Response::error('500');
