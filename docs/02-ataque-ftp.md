# 02 — Simulação controlada em FTP

## Objetivo

Registrar o cenário de auditoria de autenticação FTP realizado no ambiente vulnerável do laboratório, utilizando o Medusa conforme as orientações do desafio.

## Escopo e autorização

- Alvo: máquina Metasploitable 2 do laboratório local.
- Serviço: FTP.
- Origem do teste: Kali Linux.
- Os testes automatizados foram restritos ao host local do laboratório e à conta de teste usada na atividade.

## Preparação

- [x] Confirmar o endereço IP do alvo: `192.168.100.10`.
- [x] Confirmar que o serviço FTP está disponível no alvo.
- [x] Validar uma autenticação manual de teste.
- [x] Verificar a instalação do Medusa e o módulo FTP.
- [x] Consultar as opções específicas do módulo FTP.
- [x] Criar uma wordlist curta e artificial para o exercício.
- [x] Executar a simulação controlada com Medusa.
- [x] Registrar a saída observada no terminal.

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

**Resultado observado:** autenticação manual concluída com sucesso. Isso confirmou que o serviço aceita a conta de teste utilizada. Esta etapa, isoladamente, não representa um teste automatizado de força bruta.

## Verificação do Medusa

No Kali Linux, foram executados:

```bash
medusa -V
medusa -d | grep -i ftp
```

**Resultado observado no terminal:**

| Componente | Versão / resultado |
|---|---|
| Medusa | `v2.3` |
| Módulo | `ftp.mod` |
| Versão do módulo | `2.1` |
| Descrição | Módulo de força bruta para sessões FTP/FTPS |

Isso confirmou que o Medusa estava instalado e que o módulo necessário para o cenário FTP estava disponível.

## Opções do módulo FTP

Foi executado:

```bash
medusa -M ftp -q
```

O módulo informa três modos de conexão:

| Modo | Descrição |
|---|---|
| `NORMAL` (padrão) | Comportamento FTP padrão; autenticação sem TLS, a menos que o servidor solicite criptografia |
| `EXPLICIT` | Negocia TLS explícito por meio de `AUTH TLS` |
| `IMPLICIT` | FTP sobre SSL/TLS implícito, geralmente associado à porta 990 |

**Escolha para este laboratório:** foi utilizado o modo padrão `NORMAL`, pois o reconhecimento identificou o FTP na porta 21 e a conexão manual foi estabelecida sem negociação TLS.

## Wordlist de laboratório

Foi criada a lista local `passwords-ftp.txt` e ela também foi publicada no repositório como [`wordlists/passwords-ftp.txt`](../wordlists/passwords-ftp.txt). A lista contém quatro candidatos artificiais, um por linha:

- `lab2026`
- `kali2026`
- `metasploitable`
- `msfadmin`

A lista foi preparada exclusivamente para o teste no Metasploitable 2 local. Não deve ser reutilizada fora deste laboratório.

## Execução com Medusa

No Kali Linux, foi executado o seguinte comando:

```bash
medusa -h 192.168.100.10 -u msfadmin -P passwords-ftp.txt -M ftp -t 1 -f -v 4
```

**Parâmetros utilizados:**

| Parâmetro | Finalidade |
|---|---|
| `-h 192.168.100.10` | Define o host do laboratório |
| `-u msfadmin` | Define a única conta de teste utilizada |
| `-P passwords-ftp.txt` | Informa a lista local de senhas |
| `-M ftp` | Seleciona o módulo FTP |
| `-t 1` | Limita a uma tarefa concorrente |
| `-f` | Interrompe a execução após encontrar uma combinação válida |
| `-v 4` | Define o nível de detalhes da saída |

**Saída observada no terminal:**

```text
ACCOUNT FOUND: [ftp] Host: 192.168.100.10 User: msfadmin Password: msfadmin [SUCCESS]
```

**Resultado:** o Medusa encontrou uma combinação válida para a conta de teste `msfadmin` no serviço FTP do Metasploitable 2. A saída identificou o host, o usuário, a senha correspondente e o estado `SUCCESS`.

O teste foi executado contra uma única máquina de laboratório, com uma única conta e uma lista curta criada para esta atividade.

## Resultado observado

- Data: 23/11/2026
- Origem: Kali Linux (`192.168.100.20`)
- Alvo: Metasploitable 2 (`192.168.100.10`)
- Serviço: FTP (`vsftpd 2.3.4`)
- Porta: `21/tcp` aberta
- Usuário testado: `msfadmin`
- Medusa: versão `2.3`
- Módulo FTP: `ftp.mod`, versão `2.1`
- Wordlist local: `passwords-ftp.txt`, quatro candidatos
- Resultado automatizado: combinação válida encontrada (`SUCCESS`)

## Análise

O exercício demonstrou que uma senha fraca ou previsível pode ser descoberta por tentativas automatizadas quando o serviço permite tentativas repetidas sem controles suficientes. Em ambientes reais, recomenda-se utilizar senhas fortes e únicas, limitar e monitorar tentativas de autenticação, restringir o acesso ao serviço e preferir protocolos seguros, como SFTP ou FTPS, em vez de FTP sem criptografia.

## Evidências e apresentação

Esta entrega não inclui capturas de tela. O reconhecimento, a validação manual e a saída do Medusa estão transcritos neste relatório.
