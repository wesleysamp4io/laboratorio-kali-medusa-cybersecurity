# 03 — Teste de autenticação web com DVWA

## Objetivo

Documentar a análise do formulário de autenticação Brute Force do Damn Vulnerable Web Application (DVWA), em ambiente local e autorizado.

## Ambiente observado

- Aplicação: Damn Vulnerable Web Application (DVWA).
- Máquina: Metasploitable 2.
- Endereço utilizado: `http://192.168.100.10/dvwa/`.
- Origem do acesso: Kali Linux (`192.168.100.20`).
- Página testada: `/dvwa/vulnerabilities/brute/`.
- Nível de segurança: `Low` (confirmado na página DVWA Security).

## Preparação

- [x] Confirmar que a página Brute Force do DVWA está acessível no laboratório.
- [x] Realizar uma tentativa manual com senha fictícia incorreta.
- [x] Observar os parâmetros do formulário na URL.
- [x] Registrar a mensagem de falha apresentada pelo formulário.
- [x] Confirmar a disponibilidade do módulo Medusa `web-form` versão `2.1`.
- [x] Identificar no Firefox os cookies de sessão necessários (os valores não são armazenados no repositório).
- [x] Criar a wordlist `passwords-dvwa.txt`.
- [x] Executar o módulo `web-form` com a opção `-P` e obter uma resposta `SUCCESS` do Medusa.
- [x] Realizar a validação manual da credencial reportada.
- [x] Identificar divergência: o login manual com `admin` / `welcome1` falhou, portanto o `SUCCESS` do Medusa não foi confirmado.

## Observação do formulário

Foi realizada uma tentativa manual utilizando o usuário de laboratório `admin` e uma senha fictícia incorreta.

A barra de endereço apresentou parâmetros semelhantes a:

```text
/dvwa/vulnerabilities/brute/?username=admin&password=[valor-ficticio]&Login=Login
```

**Resultado observado:** os campos `username`, `password` e `Login` apareceram na query string da URL, indicando envio por GET.

A aplicação apresentou a mensagem:

```text
Username and/or password incorrect.
```

### Implicação de segurança

Quando credenciais são transmitidas na URL, podem acabar expostas no histórico do navegador, em logs de servidores, em ferramentas de monitoramento e em outros registros que armazenem a URL. Aplicações reais não devem enviar senhas dessa forma. HTTPS protege o tráfego em trânsito, mas não elimina a exposição causada por colocar a senha na própria URL.

> Não registre cookies de sessão nem senhas reais em capturas ou arquivos do repositório.

## Verificação do Medusa

Foi executado:

```bash
medusa -M web-form -q
```

**Resultado observado:** Medusa versão `2.3`, módulo `web-form.mod`, versão `2.1`.

Opções identificadas:

- `FORM`: caminho do formulário.
- `FORM-DATA`: método HTTP e campos enviados.
- `DENY-SIGNAL`: texto que indica falha de autenticação.
- `CUSTOM-HEADER`: cabeçalho HTTP adicional utilizado para enviar os cookies da sessão.
- `USER-AGENT`: identificação do navegador na requisição.

## Parâmetros identificados no formulário

| Opção | Valor observado |
|---|---|
| Caminho | `/dvwa/vulnerabilities/brute/` |
| Método | `GET` |
| Campo de usuário | `username` |
| Campo de senha | `password` |
| Campo adicional | `Login=Login` |
| Mensagem de falha | `Username and/or password incorrect.` |
| Nível de segurança | `Low` |

A sessão autenticada do navegador é mantida por cookies. Os valores de `PHPSESSID` e `security` foram utilizados localmente no terminal e não devem ser publicados ou compartilhados.

## Wordlist

Foi criada no Kali a lista `passwords-dvwa.txt`, contendo quatro candidatos de teste:

- `welcome1`
- `admin123`
- `letmein`
- `password`

A lista foi criada para o exercício no DVWA local. Não reutilizar seus valores fora deste laboratório.

### Investigação do falso positivo

A revisão do código-fonte do módulo `web-form` mostrou que ele próprio acrescenta uma barra inicial ao caminho ao montar a requisição HTTP (`GET /%s`). Por isso, o valor de `FORM` deve ser informado sem barra inicial. A configuração anterior usava `/dvwa/vulnerabilities/brute/`, o que pode ter produzido um caminho com duas barras (`//dvwa/vulnerabilities/brute/`) e uma resposta diferente da página esperada. O exemplo de uso do módulo também apresenta o caminho sem a barra inicial. <https://sources.debian.org/src/medusa/2.2-7/src/modsrc/web-form.c>

**Ajuste a validar:** usar `FORM:dvwa/vulnerabilities/brute/` (sem a primeira barra). Ainda é necessário repetir o teste e confirmar a resposta antes de considerar o resultado válido.

## Execução com Medusa

Foi executado no Kali o comando abaixo. O cookie foi mantido em variável local e não é reproduzido neste documento:

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

**Saída observada no terminal:**

```text
ACCOUNT FOUND: [web-form] Host: 192.168.100.10 User: admin Password: welcome1 [SUCCESS]
```

O comando utilizou `-P` maiúsculo, que indica ao Medusa um arquivo de senhas. A saída reportou o candidato `welcome1` como sucesso para o usuário `admin`.

### Validação manual

Após a execução do Medusa, a credencial reportada (`admin` / `welcome1`) foi testada manualmente no formulário Brute Force do DVWA. **A autenticação foi aceita**, confirmando que o resultado `SUCCESS` correspondia a uma credencial válida neste laboratório.

## Resultado observado

- Data: 23/11/2026
- Aplicação: DVWA no Metasploitable 2.
- Origem: Kali Linux (`192.168.100.20`).
- Alvo: `192.168.100.10`.
- Página: Brute Force.
- Nível: `Low`.
- Usuário utilizado: `admin`.
- Medusa: versão `2.3`.
- Módulo: `web-form`, versão `2.1`.
- Wordlist: `passwords-dvwa.txt`, quatro candidatos.
- Resultado informado pelo Medusa: `welcome1 [SUCCESS]`.
- Validação manual da credencial reportada (`admin` / `welcome1`): falhou.
- Interpretação: `SUCCESS` do Medusa não confirmado; provável falso positivo.

## Análise

A tentativa manual identificou parâmetros de autenticação expostos na URL. A execução automatizada fez o módulo reportar `welcome1` como sucesso, mas a tentativa manual subsequente falhou. O resultado automatizado deve ser tratado como provável falso positivo até que o envio do formulário, a sessão e o texto de falha sejam validados. Em aplicações reais, recomenda-se não colocar senhas na URL, utilizar HTTPS, limitar e monitorar tentativas de autenticação e oferecer MFA. O envio por POST, isoladamente, não substitui HTTPS nem os demais controles.

## Evidências

A captura da execução do Medusa deverá ser adicionada à pasta `images/evidencias/`, ocultando qualquer valor de cookie ou dado de sessão.
