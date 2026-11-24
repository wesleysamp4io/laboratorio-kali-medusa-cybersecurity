# 04 — Password Spraying e enumeração em SMB

## Objetivo

Documentar a enumeração de usuários e a simulação controlada de Password Spraying no serviço SMB do ambiente local autorizado.

## Conceitos

- **Enumeração de usuários:** identificação de nomes de contas expostos pelo serviço ou ambiente.
- **Password Spraying:** avaliação controlada de uma senha de teste contra um conjunto pequeno de contas de laboratório, evitando tentativas indiscriminadas.

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

O Nmap também exibiu um aviso sobre a resolução DNS reversa, sem impedir a identificação das portas e do serviço.

## Preparação

- [x] Confirmar o IP do alvo: `192.168.100.10`.
- [x] Confirmar que as portas SMB 139 e 445 estão abertas.
- [x] Enumerar usuários disponibilizados pelo serviço.
- [x] Preparar uma lista curta com três contas enumeradas: `msfadmin`, `user` e `root`.
- [x] Confirmar a disponibilidade do módulo SMB no Medusa (`smbnt.mod`, versão `3.0`).
- [ ] Executar o cenário de Password Spraying somente após identificar as contas e definir uma senha de teste autorizada.
- [ ] Registrar os resultados e evidências.

## Verificação do módulo SMB no Medusa

No Kali Linux, foi executado:

```bash
medusa -d | grep -i smb
```

**Resultado observado:**

```text
smbnt.mod :: Brute force module for SMB (SMBv1-3, Signing, LM/NTLM/LMv2/NTLMv2) sessions :: version 3.0
```

Isso confirma que o módulo `smbnt` está disponível no Medusa 2.3 e que oferece suporte a SMBv1–v3 e aos métodos de autenticação indicados na saída.

## Opções do módulo SMB no Medusa

Foi executado:

```bash
medusa -M smbnt -q
```

**Resultado observado:** módulo `smbnt.mod`, versão `3.0`, com suporte indicado a SMBv1–v3 e autenticação LM/NTLM/LMv2/NTLMv2.

Opções relevantes exibidas:

| Opção | Finalidade |
|---|---|
| `-U` | Arquivo com nomes de usuários |
| `-p` | Uma única senha |
| `-P` | Arquivo com senhas |
| `AUTH` | Seleção do método de autenticação |
| `MODE` | Seleção do modo de conexão SMB/NetBIOS |

Para demonstrar Password Spraying, o planejamento é utilizar uma lista pequena de contas identificadas e uma única senha de teste, sempre no alvo local autorizado.

## Enumeração de usuários

Foi executado no Kali:

```bash
enum4linux -U 192.168.100.10
```

A saída exibiu contas locais do Metasploitable 2. No trecho visível da captura foram identificados:

```text
games, nobody, bind, proxy, syslog, user, www-data, root,
news, postgres, bin, mail, distccd, proftpd, dhcp, daemon,
sshd, man, lp, mysql, gnats, libuuid, backup, msfadmin,
telnetd, sys, klog, postfix, service, list, irc, ftp,
tomcat55, sync
```

**Interpretação:** a enumeração SMB funcionou e revelou nomes de contas locais. A lista inclui contas de serviço e contas do sistema; a enumeração, por si só, não prova que todas aceitam autenticação SMB nem que sejam contas interativas. Para o exercício, a próxima etapa é conferir o módulo SMB disponível no Medusa e selecionar apenas contas de laboratório adequadas, sem tentar senhas contra a lista inteira indiscriminadamente.

## Lista de usuários para o exercício

Foi criada a lista `users-smb.txt` com três nomes que apareceram na enumeração SMB:

- `msfadmin`
- `user`
- `root`

A lista também foi adicionada ao repositório em [`wordlists/users-smb.txt`](../wordlists/users-smb.txt). A enumeração demonstra que os nomes são expostos pelo serviço, mas não confirma que todas essas contas aceitem autenticação SMB.

## Procedimento de Password Spraying

O exercício será limitado ao host de laboratório `192.168.100.10`, a uma única senha de teste e aos três nomes listados. O teste deve ser executado de forma sequencial, sem encerrar após a primeira correspondência, para que cada conta seja avaliada. O resultado será registrado apenas após a execução.

## Evidências

Adicionar capturas próprias em `images/evidencias/`, ocultando credenciais ou dados que não devam ser publicados.

## Resultado observado

- Data: 24/11/2026
- Origem: Kali Linux (`192.168.100.20`).
- Alvo: Metasploitable 2 (`192.168.100.10`).
- Portas SMB identificadas: `139/tcp` e `445/tcp`, ambas abertas.
- Serviço identificado: `netbios-ssn` / Samba `smbd 3.X - 4.X`.
- Workgroup: `WORKGROUP`.
- Enumeração de usuários: concluída; a saída visível incluiu `msfadmin`, `user`, `root` e várias contas de serviço.
- Lista para o exercício: `users-smb.txt` com `msfadmin`, `user` e `root`.
- Password Spraying: ainda não executado.

## Análise

O reconhecimento confirmou que o serviço SMB está acessível no alvo do laboratório. A enumeração de usuários será a próxima etapa. Medidas defensivas relevantes incluem restringir SMB às redes e aos hosts necessários, desabilitar versões obsoletas, aplicar privilégio mínimo, monitorar falhas de autenticação e utilizar políticas de senha fortes.
