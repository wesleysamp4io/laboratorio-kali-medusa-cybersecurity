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

- [x] Confirmar que a página de login do DVWA está acessível no laboratório.
- [x] Acessar a seção **Brute Force**.
- [x] Realizar uma tentativa manual com dados fictícios.
- [x] Observar como os parâmetros do formulário aparecem na URL.
- [x] Registrar a mensagem exibida após a tentativa inválida.
- [x] Confirmar que o Medusa possui o módulo `web-form` versão `2.1`.
- [x] Consultar as opções do módulo `web-form`.
- [x] Identificar no Firefox os cookies de sessão `PHPSESSID` e `security` (os valores não são publicados nem compartilhados).
- [x] Realizar uma primeira execução experimental do módulo `web-form` no DVWA local.
- [ ] Corrigir e validar a interpretação do resultado antes de considerar o teste concluído.

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

## Primeira execução experimental

Foi feita uma tentativa inicial com o módulo `web-form`, utilizando o host local, o usuário `admin`, a wordlist `passwords-dvwa.txt`, o caminho do formulário Brute Force, o método GET, a mensagem de falha observada e o cookie de sessão do DVWA.

A saída exibida pelo terminal foi:

```text
ACCOUNT FOUND: [web-form] Host: 192.168.100.10 User: admin Password: passwords-dvwa.txt [SUCCESS]
```

**Causa identificada:** na execução, foi utilizado `-p` minúsculo. No Medusa, `-p` recebe uma senha literal; para ler uma lista de senhas, a opção correta é `-P` maiúsculo. Por isso, o programa interpretou `passwords-dvwa.txt` como a própria senha, em vez de abrir o arquivo. A saída é inconclusiva e não comprova autenticação bem-sucedida. A referência do manual do Medusa confirma essa diferença. (https://manpages.ubuntu.com/manpages/resolute/man1/medusa.1.html) Além disso, o módulo `web-form` classifica uma tentativa como sucesso quando a mensagem definida em `DENY-SIGNAL` não aparece na resposta HTTP; uma resposta inesperada, sessão inválida ou sinal de falha incorreto pode causar falso positivo. A documentação do código do módulo descreve esse critério de classificação. <https://sources.debian.org/src/medusa/2.2-7/src/modsrc/web-form.c>

Portanto, nenhuma credencial válida foi confirmada por esta primeira execução. A sintaxe e o conteúdo da lista precisam ser conferidos antes de repetir o teste, mantendo a execução restrita ao DVWA local.

## Procedimento

A execução experimental foi registrada, mas a validação automatizada ainda não foi concluída. O procedimento corrigido, os parâmetros efetivos e qualquer resultado confirmado serão documentados após a conferência.

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
- Primeira execução do Medusa: retornou `ACCOUNT FOUND`, mas reportou `passwords-dvwa.txt` como senha; resultado inconclusivo e não validado.

## Análise

A observação inicial identificou exposição de parâmetros de autenticação na URL. Esse comportamento aumenta o risco de divulgação acidental de credenciais por meio de históricos e registros. A mensagem de erro observada informa que a autenticação falhou, mas não revela qual campo estava incorreto. Para aplicações reais, enviar credenciais no corpo de uma requisição POST, utilizar HTTPS, aplicar limitação de tentativas, monitorar eventos de autenticação e oferecer MFA são controles importantes. O envio por POST, isoladamente, não substitui HTTPS nem os demais controles.
