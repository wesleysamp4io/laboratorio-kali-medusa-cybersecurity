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
- [ ] Confirmar e registrar o nível de segurança selecionado no DVWA.
- [ ] Registrar a mensagem exibida pelo formulário após a tentativa.
- [ ] Preparar e executar a atividade automatizada conforme o roteiro da aula, somente no DVWA local.
- [ ] Registrar a ferramenta, os parâmetros e os resultados reais.

## Observação do formulário

Foi realizada uma tentativa manual utilizando o usuário de laboratório `admin` e uma senha fictícia incorreta.

Após o envio, a barra de endereço do navegador apresentou parâmetros semelhantes a:

```text
/dvwa/vulnerabilities/brute/?username=admin&password=[valor-ficticio]&Login=Login
```

**Resultado observado:** os campos `username`, `password` e `Login` apareceram na query string da URL. Isso indica que os dados dessa submissão foram enviados em parâmetros de consulta (GET).

### Implicação de segurança

Quando credenciais são transmitidas na URL, podem acabar expostas no histórico do navegador, em logs de servidores, em ferramentas de monitoramento e em outros registros que armazenem a URL. Aplicações reais não devem enviar senhas dessa forma. O uso de HTTPS é necessário para proteger o tráfego em trânsito, mas não elimina a exposição causada por colocar a senha na própria URL.

> A captura utilizada para esta observação deve ser adicionada ao repositório após ocultar qualquer dado que não deva ser publicado. Não registrar senhas reais no relatório.

## Procedimento

Os próximos passos e comandos serão documentados somente após a execução no ambiente local. O teste deverá permanecer restrito à aplicação DVWA do laboratório.

```text
# Registrar aqui os passos efetivamente realizados na atividade.
```

## Evidências

- [ ] Captura da página Brute Force.
- [ ] Captura da URL com os parâmetros, com valores de senha omitidos ou ocultados.
- [ ] Captura do resultado da tentativa manual.
- [ ] Evidências do teste automatizado, quando realizado.

Armazenar as imagens em `images/evidencias/`.

## Resultado observado

- Data: 23/11/2026
- Aplicação: DVWA no Metasploitable 2.
- Página: Brute Force.
- Usuário de teste: `admin`.
- Tentativa manual: realizada com senha fictícia incorreta.
- Comportamento observado: parâmetros de usuário, senha e botão aparecem na URL.
- Teste automatizado: ainda não executado.

## Análise

A observação inicial identificou exposição de parâmetros de autenticação na URL. Esse comportamento aumenta o risco de divulgação acidental de credenciais por meio de históricos e registros. Para aplicações reais, enviar credenciais no corpo de uma requisição POST, utilizar HTTPS, aplicar limitação de tentativas, monitorar eventos de autenticação e oferecer MFA são controles importantes. O envio por POST, isoladamente, não substitui HTTPS nem os demais controles.

