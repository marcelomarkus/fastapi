# Incluindo WSGI - Flask, Django e outros

Você pode montar aplicativos WSGI como viu em [Sub Aplicações - Montagens](sub-applications.md){.internal-link target=_blank} e [Atrás de um Proxy](behind-a-proxy.md){.internal-link target=_blank}.

Para isso, você pode usar o `WSGIMiddleware` e usá-lo para envolver seu aplicativo WSGI, por exemplo, Flask, Django, etc.

## Usando `WSGIMiddleware`

Você precisa importar `WSGIMiddleware`.

Em seguida, envolva o aplicativo WSGI (por exemplo, Flask) com o middleware.

E então monte-o sob um caminho.

```Python hl_lines="2-3  23"
{!../../../docs_src/wsgi/tutorial001.py!}
```

## Verifique

Agora, todas as solicitações sob o caminho `/v1/` serão tratadas pelo aplicativo Flask.

E o restante será tratado pelo **FastAPI**.

Se você executá-lo e acessar <a href="http://localhost:8000/v1/" class="external-link" target="_blank">http://localhost:8000/v1/</a> verá a resposta do Flask:

```txt
Hello, World from Flask!
```

E se você acessar <a href="http://localhost:8000/v2" class="external-link" target="_blank">http://localhost:8000/v2</a> verá a resposta do FastAPI:

```JSON
{
    "message": "Hello World"
}
```
