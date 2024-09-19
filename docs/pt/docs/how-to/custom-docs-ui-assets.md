# Personalizar Docs UI Static Assets (auto-hospedagem)

A documentação da API usa **Swagger UI** e **ReDoc**, e cada um deles precisa de alguns arquivos JavaScript e CSS.

Por padrão, esses arquivos são servidos de uma <abbr title="Content Delivery Network: Um serviço, normalmente composto de vários servidores, que fornece arquivos estáticos, como JavaScript e CSS. É comumente usado para servir esses arquivos do servidor mais próximo do cliente, melhorando o desempenho.">CDN</abbr>.

Mas é possível personalizá-lo, você pode definir um CDN específico ou servir os arquivos você mesmo.

## CDN personalizado para JavaScript e CSS

Digamos que você queira usar uma <abbr title="Content Delivery Network">CDN</abbr> diferente, por exemplo, você quer usar `https://unpkg.com/`.

Isso pode ser útil se, por exemplo, você mora em um país que restringe alguns URLs.

### Desabilitar os documentos automáticos

O primeiro passo é desabilitar os documentos automáticos, pois, por padrão, eles usam o CDN padrão.

Para desativá-los, defina seus URLs como `None` ao criar seu aplicativo `FastAPI`:

```Python hl_lines="8"
{!../../../docs_src/custom_docs_ui/tutorial001.py!}
```

### Incluir os documentos personalizados

Agora você pode criar as *operações de rotas* para a documentação personalizada.

Você pode reutilizar as funções internas do FastAPI para criar as páginas HTML para documentação e passar a elas os argumentos necessários:

* `openapi_url`: a URL onde a página HTML para documentação obter o esquema OpenAPI para sua API. Você pode usar aqui o atributo `app.openapi_url`.
* `title`: o título da sua API.
* `oauth2_redirect_url`: você pode usar `app.swagger_ui_oauth2_redirect_url` aqui para usar o padrão.
* `swagger_js_url`: a URL onde o HTML para seus documentos Swagger UI pode obter o arquivo **JavaScript**. Esta é a URL CDN personalizada.
* `swagger_css_url`: a URL onde o HTML para seus documentos Swagger UI pode obter o arquivo **CSS**. Esta é a URL CDN personalizada.

E da mesma forma para o ReDoc...

```Python hl_lines="2-6  11-19  22-24  27-33"
{!../../../docs_src/custom_docs_ui/tutorial001.py!}
```

/// tip | "Dica"

A *operação de rotas* para `swagger_ui_redirect` é um auxiliar para quando você usa OAuth2.

Se você integrar sua API com um provedor OAuth2, você poderá autenticar e retornar aos documentos da API com as credenciais adquiridas. E interagir com ela usando a autenticação OAuth2 real.

O Swagger UI cuidará disso nos bastidores para você, mas precisa desse auxiliar de "redirecionamento".

///

### Crie uma *operação de rota* para testá-lo

Agora, para poder testar se tudo funciona, crie uma *operação de rota*:

```Python hl_lines="36-38"
{!../../../docs_src/custom_docs_ui/tutorial001.py!}
```

### Teste-o

Agora, você deve conseguir ir para documentação em <a href="http://127.0.0.1:8000/docs" class="external-link" target="_blank">http://127.0.0.1:8000/docs</a> e recarregar a página. Ela carregará esses ativos do novo CDN.

## Auto-hospedagem de JavaScript e CSS para documentação

A auto-hospedagem do JavaScript e do CSS pode ser útil se, por exemplo, você precisar que seu aplicativo continue funcionando mesmo offline, sem acesso aberto à Internet ou em uma rede local.

Aqui você verá como servir esses arquivos você mesmo, no mesmo aplicativo FastAPI, e configurar os documentos para usá-lo.

### Estrutura do arquivo do projeto

Digamos que a estrutura do arquivo do seu projeto se pareça com isto:

```
.
├── app
│   ├── __init__.py
│   ├── main.py
```

Agora crie um diretório para armazenar esses arquivos estáticos.

Sua nova estrutura de arquivo poderia ser parecida com esta:

```
.
├── app
│   ├── __init__.py
│   ├── main.py
└── static/
```

### Baixe os arquivos

Baixe os arquivos estáticos necessários para o documentos e coloque-os no diretório `static/`.

Você provavelmente pode clicar com o botão direito em cada link e selecionar uma opção semelhante a `Salvar link como...`.

**Swagger UI** usa os arquivos:

* <a href="https://cdn.jsdelivr.net/npm/swagger-ui-dist@5/swagger-ui-bundle.js" class="external-link" target="_blank">`swagger-ui-bundle.js`</a>
* <a href="https://cdn.jsdelivr.net/npm/swagger-ui-dist@5/swagger-ui.css" class="external-link" target="_blank">`swagger-ui.css`</a>

E o **ReDoc** usa o arquivo:

* <a href="https://cdn.jsdelivr.net/npm/redoc@next/bundles/redoc.standalone.js" class="external-link" target="_blank">`redoc.standalone.js`</a>

Depois disso, a estrutura do seu arquivo poderá ficar assim:

```
.
├── app
│   ├── __init__.py
│   ├── main.py
└── static
    ├── redoc.standalone.js
    ├── swagger-ui-bundle.js
    └── swagger-ui.css
```

### Servir os arquivos estáticos

* Importar `StaticFiles`.
* "Mounte" uma `StaticFiles()` instancia em um rota específica.

```Python hl_lines="7  11"
{!../../../docs_src/custom_docs_ui/tutorial002.py!}
```

### Teste os arquivos estáticos

Inicie seu aplicativo e vá para <a href="http://127.0.0.1:8000/static/redoc.standalone.js" class="external-link" target="_blank">http://127.0.0.1:8000/static/redoc.standalone.js</a>.

Você deverá ver um arquivo JavaScript muito longo para **ReDoc**.

Poderia começar com algo como:

```JavaScript
/*!
 * ReDoc - OpenAPI/Swagger-generated API Reference Documentation
 * -------------------------------------------------------------
 *   Version: "2.0.0-rc.18"
 *   Repo: https://github.com/Redocly/redoc
 */
!function(e,t){"object"==typeof exports&&"object"==typeof m

...
```

Isso confirma que você está conseguindo fornecer arquivos estáticos do seu aplicativo e que colocou os arquivos estáticos dos documentos no lugar correto.

Agora podemos configurar o aplicativo para usar esses arquivos estáticos para os documentos.

### Desabilitar a documentação automática para arquivos estáticos

Da mesma forma que ao usar um CDN personalizado, o primeiro passo é desabilitar os documentos automáticos, pois eles usam o CDN por padrão.

Para desativá-los, defina seus URLs como `None` ao criar seu aplicativo `FastAPI`:

```Python hl_lines="9"
{!../../../docs_src/custom_docs_ui/tutorial002.py!}
```

### Incluir os documentos personalizados para arquivos estáticos

E da mesma forma que com um CDN personalizado, agora você pode criar as *operações de rotas* para os documentos personalizados.

Novamente, você pode reutilizar as funções internas do FastAPI para criar as páginas HTML para o docs e passar a elas os argumentos necessários:

* `openapi_url`: a URL onde a página HTML para os documentos pode obter o esquema OpenAPI para sua API. Você pode usar aqui o atributo `app.openapi_url`.
* `title`: o título da sua API.
* `oauth2_redirect_url`: você pode usar `app.swagger_ui_oauth2_redirect_url` aqui para usar o padrão.
* `swagger_js_url`: a URL onde o HTML para seus documentos Swagger UI pode obter o arquivo **JavaScript**. **Este é o que seu próprio aplicativo está servindo agora**.
* `swagger_css_url`: a URL onde o HTML para seus documentos Swagger UI pode obter o arquivo **CSS**. **Este é o que seu próprio aplicativo está servindo agora**.

E da mesma forma para o ReDoc...

```Python hl_lines="2-6  14-22  25-27  30-36"
{!../../../docs_src/custom_docs_ui/tutorial002.py!}
```

/// tip | "Dica"

A *operação de rota* para `swagger_ui_redirect` é um auxiliar para quando você usa OAuth2.

Se você integrar sua API com um provedor OAuth2, você poderá autenticar e retornar a documentação da API com as credenciais adquiridas. E interagir com ela usando a autenticação OAuth2 real.

O Swagger UI cuidará disso nos bastidores para você, mas precisa desse auxiliar de "redirecionamento".

///

### Crie uma *operação de rota* para testar arquivos estáticos

Agora, para poder testar se tudo funciona, crie uma *operação de rota*:

```Python hl_lines="39-41"
{!../../../docs_src/custom_docs_ui/tutorial002.py!}
```

### Testar a UI de arquivos estáticos

Agora, você deve conseguir desconectar seu WiFi, acessar sua documentação em <a href="http://127.0.0.1:8000/docs" class="external-link" target="_blank">http://127.0.0.1:8000/docs</a> e recarregar a página.

E mesmo sem Internet, você poderá ver a documentação da sua API e interagir com ela.
