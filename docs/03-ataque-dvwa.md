# 03 — Teste de autenticação web com DVWA

## Objetivo

Documentar a análise do formulário Brute Force do Damn Vulnerable Web Application (DVWA), em ambiente local e autorizado.

## Ambiente

- Aplicação: Damn Vulnerable Web Application (DVWA).
- Máquina: Metasploitable 2.
- Endereço: `http://192.168.100.10/dvwa/`.
- Origem: Kali Linux (`192.168.100.20`).
- Página: `/dvwa/vulnerabilities/brute/`.
- Nível de segurança: `Low`.

## Preparação

- [x] Acessar a página Brute Force.
- [x] Fazer uma tentativa manual com senha fictícia incorreta.
- [x] Observar os campos `username`, `password` e `Login` na URL (GET).
- [x] Registrar a mensagem de falha `Username and/or password incorrect.`.
- [x] Confirmar Medusa 2.3 e módulo `web-form` 2.1.
- [x] Identificar os cookies de sessão no Firefox (os valores não são armazenados neste repositório).
- [x] Criar a wordlist `passwords-dvwa.txt`.
- [x] Executar o teste automatizado com o caminho de formulário corrigido.
- [x] Confirmar manualmente a credencial encontrada pelo Medusa.

## Observação do formulário

Foi feita uma tentativa manual com o usuário de laboratório `admin` e uma senha fictícia incorreta. A URL exibiu parâmetros de consulta semelhantes a:

```text
/dvwa/vulnerabilities/brute/?username=admin&password=[valor-ficticio]&Login=Login
```

A aplicação apresentou a mensagem:

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

## Primeira execução e correção

Na primeira execução, o Medusa reportou `welcome1 [SUCCESS]`, mas a validação manual dessa combinação falhou. O resultado foi tratado como falso positivo.

A revisão do caminho do formulário identificou que o módulo `web-form` acrescenta a barra inicial ao montar a requisição. Portanto, o valor de `FORM` deve ser informado **sem** a barra inicial. O código-fonte do módulo mostra a montagem do caminho como `GET /%s`. <https://sources.debian.org/src/medusa/2.2-7/src/modsrc/web-form.c>

## Execução corrigida com Medusa

Com a barra inicial removida do parâmetro `FORM`, foi executado no Kali:

```bash
medusa -h 192.168.100.10 \
-u admin \
-P passwords-dvwa.txt \
-M web-form \
-m FORM:dvwa/vulnerabilities/brute/ \
-m FORM-DATA:"get?username=&password=&Login=Login" \
-m DENY-SIGNAL:"Username and/or password incorrect." \
-m CUSTOM-HEADER:"cookie: PHPSESSID=${PHPSESSID}; security=low" \
-t 1 -f -v 4
```

O valor do cookie foi mantido localmente no terminal e não é reproduzido neste documento.

**Saída observada:**

```text
ACCOUNT FOUND: [web-form] Host: 192.168.100.10 User: admin Password: password [SUCCESS]
```

## Validação manual

A combinação reportada pelo Medusa (`admin` / `password`) foi testada manualmente no formulário Brute Force do DVWA. **O login foi aceito**, confirmando o resultado automatizado.

## Resultado observado

- Data da execução corrigida: 24/11/2026.
- Aplicação: DVWA no Metasploitable 2.
- Origem: Kali Linux (`192.168.100.20`).
- Alvo: `192.168.100.10`.
- Página: Brute Force.
- Nível de segurança: `Low`.
- Usuário: `admin`.
- Medusa: versão `2.3`.
- Módulo: `web-form`, versão `2.1`.
- Wordlist: `passwords-dvwa.txt`, quatro candidatos.
- Resultado: `password [SUCCESS]`.
- Validação manual: login com `admin` / `password` bem-sucedido.

## Análise

O exercício demonstrou como uma senha fraca pode ser identificada por tentativas automatizadas em um formulário vulnerável. Também mostrou a importância de configurar corretamente o caminho e os campos do formulário, a mensagem de falha e os cookies da sessão para evitar falsos positivos.

Em aplicações reais, recomenda-se não colocar senhas na URL, utilizar HTTPS, limitar e monitorar tentativas de autenticação, aplicar políticas de senha e oferecer MFA.

## Evidências e apresentação

Esta entrega não inclui capturas de tela. Os passos realizados, os parâmetros utilizados e os resultados observados estão descritos neste relatório. Cookies de sessão não são publicados.
