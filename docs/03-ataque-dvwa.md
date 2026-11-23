# 03 — Teste de autenticação web com DVWA

## Objetivo

Documentar a análise do formulário Brute Force do Damn Vulnerable Web Application (DVWA), em ambiente local e autorizado.

## Ambiente observado

- Aplicação: Damn Vulnerable Web Application (DVWA).
- Máquina: Metasploitable 2.
- Endereço: `http://192.168.100.10/dvwa/`.
- Origem: Kali Linux (`192.168.100.20`).
- Página: `/dvwa/vulnerabilities/brute/`.
- Nível de segurança: `Low`.

## Preparação concluída

- [x] Acessar a página Brute Force.
- [x] Fazer uma tentativa manual com senha fictícia incorreta.
- [x] Observar os campos `username`, `password` e `Login` na URL (GET).
- [x] Registrar a mensagem de falha `Username and/or password incorrect.`.
- [x] Confirmar Medusa 2.3 e módulo `web-form` 2.1.
- [x] Identificar os cookies de sessão no Firefox (os valores não são armazenados neste repositório).
- [x] Criar a wordlist `passwords-dvwa.txt`.

## Observação do formulário

Foi feita uma tentativa manual com o usuário de laboratório `admin` e uma senha fictícia incorreta. A URL exibiu parâmetros de consulta semelhantes a:

```text
/dvwa/vulnerabilities/brute/?username=admin&password=[valor-ficticio]&Login=Login
```

A aplicação apresentou:

```text
Username and/or password incorrect.
```

Como os dados aparecem na URL, podem ficar expostos em histórico do navegador, logs e outros registros que armazenem a URL. Aplicações reais não devem transmitir senhas dessa forma. HTTPS protege o tráfego, mas não remove a exposição causada por inserir credenciais na query string.

## Medusa e módulo web-form

Foi executado:

```bash
medusa -M web-form -q
```

Resultado observado: Medusa `2.3`, módulo `web-form.mod` versão `2.1`.

Parâmetros identificados:

| Opção | Finalidade |
|---|---|
| `FORM` | Caminho do formulário |
| `FORM-DATA` | Método HTTP e campos enviados |
| `DENY-SIGNAL` | Texto que identifica falha de autenticação |
| `CUSTOM-HEADER` | Cabeçalhos HTTP, incluindo cookie da sessão |

## Wordlist

Foi criada no Kali a lista `passwords-dvwa.txt`, com quatro candidatos artificiais:

- `welcome1`
- `admin123`
- `letmein`
- `password`

A lista foi preparada exclusivamente para o DVWA local.

## Primeira execução automatizada

A primeira execução usou o módulo `web-form`, o usuário `admin`, a wordlist `passwords-dvwa.txt`, o método GET e o cookie de sessão do DVWA. O valor do cookie não é reproduzido neste documento.

O parâmetro `FORM` foi informado com barra inicial:

```text
FORM:/dvwa/vulnerabilities/brute/
```

A saída observada foi:

```text
ACCOUNT FOUND: [web-form] Host: 192.168.100.10 User: admin Password: welcome1 [SUCCESS]
```

A senha reportada pelo Medusa (`welcome1`) foi testada manualmente no formulário Brute Force do DVWA, mas o login **falhou**. Portanto, o `SUCCESS` não confirmou uma credencial válida e deve ser tratado como provável falso positivo.

## Investigação e correção pendente

A revisão do código-fonte do módulo mostra que ele monta a requisição como `GET /%s`, acrescentando uma barra inicial ao caminho fornecido em `FORM`. O valor de `FORM` deve, portanto, ser informado sem barra inicial; a configuração anterior pode ter gerado o caminho duplicado `//dvwa/...`. O exemplo de uso do módulo também apresenta o caminho sem barra inicial. <https://sources.debian.org/src/medusa/2.2-7/src/modsrc/web-form.c>

**Próximo ajuste a validar:** usar `FORM:dvwa/vulnerabilities/brute/` e repetir a verificação somente depois de confirmar que o cookie de sessão corresponde à sessão atual do DVWA.

### Diagnóstico HTTP com cURL

Foi enviada uma única requisição de teste com senha deliberadamente incorreta, utilizando a sessão local. O terminal exibiu `HTTP/1.1 200 OK`, mas o filtro não encontrou no corpo da resposta nem a mensagem `Username and/or password incorrect.` nem o texto `Welcome`.

**Interpretação:** o servidor respondeu com HTTP 200, mas a resposta ainda não correspondeu a nenhum dos dois marcadores esperados. Isso não comprova login válido nem inválido; a resposta precisa ser inspecionada para identificar se é a página Brute Force, uma página de login/sessão ou outro conteúdo. O cookie não deve ser exibido ou publicado.

## Próxima verificação

Antes de repetir a lista inteira, validar com uma única requisição controlada que o cookie e o caminho corretos retornam a mensagem de falha conhecida quando é enviada uma senha deliberadamente incorreta. Só depois disso será repetido o teste automatizado, mantendo baixa concorrência e o alvo restrito a `192.168.100.10`.

## Resultado observado

- Data: 23/11/2026
- Aplicação: DVWA no Metasploitable 2.
- Página: Brute Force.
- Nível de segurança: `Low`.
- Usuário testado: `admin`.
- Medusa: `2.3`.
- Módulo: `web-form` 2.1.
- Wordlist: `passwords-dvwa.txt`, quatro candidatos.
- Resultado do Medusa na primeira execução: `welcome1 [SUCCESS]`.
- Validação manual de `admin` / `welcome1`: falhou.
- Estado do cenário: em investigação; automação ainda não validada.

## Análise

O formulário demonstrou exposição de parâmetros de autenticação na URL. A primeira execução do Medusa apresentou um falso positivo provável, pois a credencial reportada foi rejeitada no teste manual. O resultado automatizado só será considerado válido depois de confirmar a requisição e o sinal de falha. Em aplicações reais, recomenda-se não colocar senhas na URL, utilizar HTTPS, limitar e monitorar tentativas de autenticação e oferecer MFA.

## Evidências

Adicionar as capturas em `images/evidencias/`, ocultando todos os valores de cookie, dados de sessão e senhas.
