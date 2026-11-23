# 03 — Teste de autenticação web com DVWA

## Objetivo

Documentar a análise do formulário de autenticação Brute Force do Damn Vulnerable Web Application (DVWA), em ambiente local e autorizado.

## Ambiente observado

- Aplicação: Damn Vulnerable Web Application (DVWA).
- Máquina: Metasploitable 2.
- Endereço utilizado: `http://192.168.100.10/dvwa/`.
- Origem do acesso: Kali Linux (`192.168.100.20`).
- Página testada: `/dvwa/vulnerabilities/brute/`.
- Nível de segurança: ainda precisa ser confirmado na página DVWA Security.

## Preparação

- [x] Confirmar que a página de login do DVWA está acessível no laboratório.
- [x] Acessar a seção **Brute Force**.
- [x] Realizar uma tentativa manual com dados fictícios.
- [x] Observar como os parâmetros do formulário aparecem na URL.
- [x] Registrar a mensagem exibida após a tentativa inválida.
- [x] Confirmar que o Medusa possui o módulo `web-form` versão `2.1`.
- [x] Consultar as opções do módulo `web-form`.
- [ ] Identificar o cookie da sessão ativa do DVWA, sem publicá-lo no repositório.
- [ ] Executar a simulação automatizada, somente no DVWA local.
- [ ] Registrar a ferramenta, os parâmetros e os resultados reais.

## Observação do formulário

Foi realizada uma tentativa manual utilizando o usuário de laboratório `admin` e uma senha fictícia incorreta.

Após o envio, a barra de endereço do navegador apresentou parâmetros semelhantes a:

```text
/dvwa/vulnerabilities/brute/?username=admin&password=[valor-ficticio]&Login=Login
```

**Resultado observado:** os campos `username`, `password` e `Login` apareceram na query string da URL. Isso indica que os dados dessa submissão foram enviados em parâmetros de consulta (GET).

A aplicação também apresentou a mensagem:

```text
Username and/or password incorrect.
```

A mensagem confirma que a tentativa manual com a senha fictícia incorreta foi rejeitada.

### Implicação de segurança

Quando credenciais são transmitidas na URL, podem acabar expostas no histórico do navegador, em logs de servidores, em ferramentas de monitoramento e em outros registros que armazenem a URL. Aplicações reais não devem enviar senhas dessa forma. O uso de HTTPS é necessário para proteger o tráfego em trânsito, mas não elimina a exposição causada por colocar a senha na própria URL.

> Não registre cookies de sessão nem senhas reais em capturas ou arquivos do repositório.

## Verificação do Medusa

No Kali Linux, foi executado:

```bash
medusa -M web-form -q
```

**Resultado observado:** o Medusa exibiu o módulo `web-form.mod`, versão `2.1`, destinado a formulários web.

As opções consultadas incluem:

- `FORM`: caminho do formulário.
- `FORM-DATA`: método HTTP e campos enviados.
- `DENY-SIGNAL`: texto que indica falha de autenticação.
- `CUSTOM-HEADER`: cabeçalhos HTTP adicionais, necessários neste laboratório para enviar o cookie da sessão do DVWA.
- `USER-AGENT`: identificação do navegador na requisição.

## Parâmetros identificados no formulário

Com base na URL observada durante a tentativa manual, o formulário utiliza:

| Opção | Valor observado |
|---|---|
| Caminho | `/dvwa/vulnerabilities/brute/` |
| Método | `GET` |
| Campo de usuário | `username` |
| Campo de senha | `password` |
| Campo adicional | `Login=Login` |
| Mensagem de falha | `Username and/or password incorrect.` |

A sessão autenticada do navegador é mantida por cookies. Para reproduzir a requisição no laboratório, será necessário consultar no Firefox os cookies ativos do endereço `192.168.100.10` e utilizá-los localmente como cabeçalho HTTP. **O valor do cookie é um segredo de sessão: não o compartilhe e não o adicione ao GitHub.**

## Procedimento

A automação ainda não foi executada. O comando, os parâmetros efetivos e o resultado serão registrados depois da execução e validação no DVWA local.

## Evidências

- [ ] Captura da página Brute Force.
- [ ] Captura da URL com os valores de senha omitidos ou ocultados.
- [ ] Captura da mensagem de tentativa inválida.
- [ ] Evidência da disponibilidade do módulo `web-form`.
- [ ] Evidência do teste automatizado, quando realizado, sem expor cookies ou credenciais.

Armazenar as imagens em `images/evidencias/`.

## Resultado observado

- Data: 23/11/2026
- Aplicação: DVWA no Metasploitable 2.
- Página: Brute Force.
- Usuário de teste: `admin`.
- Tentativa manual: realizada com senha fictícia incorreta.
- Comportamento observado: parâmetros de usuário, senha e botão aparecem na URL (GET).
- Resposta da aplicação: `Username and/or password incorrect.`
- Medusa: versão `2.3`.
- Módulo web: `web-form.mod`, versão `2.1`.
- Teste automatizado: ainda não executado.

## Análise

A observação inicial identificou exposição de parâmetros de autenticação na URL. Esse comportamento aumenta o risco de divulgação acidental de credenciais por meio de históricos e registros. A mensagem de erro observada informa que a autenticação falhou, mas não revela qual campo estava incorreto. Para aplicações reais, enviar credenciais no corpo de uma requisição POST, utilizar HTTPS, aplicar limitação de tentativas, monitorar eventos de autenticação e oferecer MFA são controles importantes. O envio por POST, isoladamente, não substitui HTTPS nem os demais controles.
