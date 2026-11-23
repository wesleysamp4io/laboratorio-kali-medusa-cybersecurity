# 02 — Simulação controlada em FTP

## Objetivo

Registrar o cenário de auditoria de autenticação FTP realizado no ambiente vulnerável do laboratório, utilizando o Medusa conforme as orientações do desafio.

## Escopo e autorização

- Alvo: máquina Metasploitable 2 do laboratório local.
- Serviço: FTP.
- Origem do teste: Kali Linux.
- Os testes de autenticação serão limitados a contas e valores fictícios ou explicitamente autorizados para este laboratório.

## Preparação

- [x] Confirmar o endereço IP do alvo: `192.168.100.10`.
- [x] Confirmar que o serviço FTP está disponível no alvo.
- [ ] Preparar uma pequena wordlist exclusivamente para o exercício.
- [ ] Registrar a versão do Medusa utilizada.

## Reconhecimento do serviço

A partir do Kali Linux (`192.168.100.20`), foi executado:

```bash
sudo nmap -sV -p 21 192.168.100.10
```

**Resultado observado no terminal:**

| Campo | Resultado |
|---|---|
| Host | `192.168.100.10` |
| Estado do host | `up` |
| Porta | `21/tcp` |
| Estado da porta | `open` |
| Serviço | `ftp` |
| Versão identificada | `vsftpd 2.3.4` |
| Detecção do sistema | Unix |

O Nmap também exibiu um aviso de que a resolução DNS reversa estava desabilitada. A varredura foi concluída e identificou a porta e o serviço FTP.

**Interpretação:** o serviço FTP está acessível no alvo do laboratório. Esta etapa identificou o serviço e sua versão; ainda não houve tentativa de autenticação nem validação de credenciais.

## Próxima atividade

- [ ] Confirmar o acesso ao serviço com um cliente FTP, sem realizar tentativas automatizadas.
- [ ] Definir uma conta de teste autorizada e uma pequena lista artificial de senhas.
- [ ] Executar a simulação controlada com Medusa conforme o escopo do laboratório.
- [ ] Registrar os comandos, parâmetros e resultados reais.

## Procedimento

Documente aqui os comandos efetivamente utilizados, explicando os parâmetros e substituindo qualquer dado sensível por valores fictícios.

```bash
# Os comandos do teste com Medusa serão registrados após a execução.
```

## Evidências

A captura do reconhecimento Nmap deverá ser adicionada em `images/evidencias/` e referenciada aqui após o upload.

## Resultado observado

- Data: 23/11/2026
- Alvo de laboratório: `192.168.100.10`
- Serviço: FTP (`vsftpd 2.3.4`)
- Reconhecimento: porta `21/tcp` aberta
- Resultado de autenticação: ainda não executado

## Análise

O reconhecimento confirmou que o FTP está exposto na máquina de laboratório. Serviços FTP tradicionais não protegem as credenciais em trânsito com criptografia; em ambientes reais, prefira SFTP ou FTPS, restrinja o acesso por rede e monitore tentativas de autenticação. As conclusões sobre autenticação deste laboratório serão preenchidas somente após a execução do teste.
