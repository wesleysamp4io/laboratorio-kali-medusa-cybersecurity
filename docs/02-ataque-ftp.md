# 02 — Simulação controlada em FTP

## Objetivo

Registrar o cenário de auditoria de autenticação FTP realizado no ambiente vulnerável do laboratório, utilizando o Medusa conforme as orientações do desafio.

## Escopo e autorização

- Alvo: máquina Metasploitable 2 do laboratório local.
- Serviço: FTP.
- Origem do teste: Kali Linux.
- Os testes automatizados serão restritos ao alvo local do laboratório e a uma conta de teste autorizada.

## Preparação

- [x] Confirmar o endereço IP do alvo: `192.168.100.10`.
- [x] Confirmar que o serviço FTP está disponível no alvo.
- [x] Validar uma autenticação manual de teste.
- [ ] Preparar uma pequena wordlist exclusivamente para o exercício.
- [ ] Registrar a versão do Medusa utilizada.
- [ ] Executar e documentar a simulação controlada com Medusa.

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

## Validação manual de autenticação

Foi iniciada uma conexão FTP a partir do Kali:

```bash
ftp 192.168.100.10
```

Foi utilizada a conta de laboratório `msfadmin`. O servidor respondeu com:

```text
220 (vsFTPd 2.3.4)
331 Please specify the password.
230 Login successful.
```

**Resultado observado:** autenticação manual concluída com sucesso. Isso confirma que o serviço aceita a conta de teste utilizada. Esta etapa não representa, por si só, um ataque de força bruta.

## Próxima atividade

- [ ] Verificar a instalação e o módulo FTP disponíveis na versão local do Medusa.
- [ ] Preparar uma lista pequena de senhas fictícias e autorizadas para o teste.
- [ ] Executar a simulação controlada com uma única conta de laboratório e baixa concorrência.
- [ ] Registrar a saída real do Medusa e analisar o resultado.

## Procedimento com Medusa

Os comandos e parâmetros efetivamente utilizados serão registrados após a execução. Não utilizar listas extensas ou credenciais de terceiros.

## Evidências

As capturas do reconhecimento Nmap e da autenticação manual deverão ser adicionadas em `images/evidencias/` após a organização dos arquivos.

## Resultado observado

- Data: 23/11/2026
- Alvo de laboratório: `192.168.100.10`
- Serviço: FTP (`vsftpd 2.3.4`)
- Reconhecimento: porta `21/tcp` aberta
- Autenticação manual: bem-sucedida com a conta de laboratório `msfadmin`
- Teste automatizado com Medusa: ainda não executado

## Análise

O reconhecimento confirmou que o FTP está exposto na máquina de laboratório e a autenticação manual confirmou que a conta de teste funciona. O FTP tradicional não protege credenciais em trânsito com criptografia; em ambientes reais, prefira SFTP ou FTPS, restrinja o acesso por rede e monitore tentativas de autenticação. As conclusões sobre a simulação automatizada serão preenchidas somente após o teste.
