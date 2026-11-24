# Laboratório de Brute Force com Kali Linux e Medusa

> Projeto prático de cibersegurança desenvolvido como desafio educacional da DIO.

Laboratório prático de cibersegurança utilizando Kali Linux e Medusa para simulação de ataques de força bruta e Password Spraying em ambientes controlados. Inclui testes em FTP, DVWA e SMB, documentação técnica, evidências, análise de vulnerabilidades e medidas de mitigação.

## Objetivo

Configurar um ambiente de testes isolado, executar simulações controladas de autenticação em serviços vulneráveis e documentar os resultados para compreender os riscos de senhas fracas e as medidas de proteção.

## Escopo do desafio

- **FTP:** simulação controlada de tentativas de autenticação.
- **Web (DVWA):** automação de tentativas em formulário de autenticação.
- **SMB:** estudo de enumeração de usuários e Password Spraying.
- **Documentação:** registro das configurações, wordlists de teste, comandos, evidências, resultados e recomendações de mitigação.

## Ambiente de laboratório

| Componente | Finalidade |
|---|---|
| Kali Linux | Máquina utilizada para os testes de auditoria (`192.168.100.20`) |
| Metasploitable 2 | Máquina vulnerável para práticas em laboratório (`192.168.100.10`) |
| DVWA | Aplicação web intencionalmente vulnerável para o cenário web |
| Oracle VirtualBox | Virtualização das máquinas |
| Rede Interna | Rede interna do VirtualBox chamada `lab-int` |

> A configuração observada utiliza **Rede Interna (Internal Network)** do VirtualBox, não o modo **Host-only Adapter**. Os detalhes e as pendências de validação estão em [docs/01-configuracao-laboratorio.md](docs/01-configuracao-laboratorio.md).

## Estrutura do repositório

```text
.
├── README.md
├── docs/
│   ├── 01-configuracao-laboratorio.md
│   ├── 02-ataque-ftp.md
│   ├── 03-ataque-dvwa.md
│   ├── 04-password-spraying-smb.md
│   └── 05-mitigacoes.md
├── images/
│   └── evidencias/
├── scripts/
└── wordlists/
    ├── passwords-ftp.txt
    └── passwords-dvwa.txt
```

## Andamento

- [x] Repositório público criado.
- [x] Estrutura inicial de documentação criada.
- [x] Configuração inicial da rede e endereços IP documentada.
- [x] Reconhecimento do serviço FTP e validação manual de autenticação documentados.
- [x] Simulação FTP executada com Medusa e resultado registrado.
- [x] Cenário DVWA testado com Medusa; combinação `admin` / `password` confirmada manualmente.
- [ ] Validar a persistência do IP do Metasploitable após reinicialização.
- [ ] Confirmar se a modalidade de rede utilizada atende à orientação do instrutor.
- [ ] Adicionar as capturas de tela em `images/evidencias/`.
- [x] Executar e documentar o cenário web com DVWA.
- [x] Identificar SMB no alvo: portas 139/445 abertas, Samba e workgroup WORKGROUP.
- [x] Enumerar usuários SMB e documentar o Password Spraying; `msfadmin` foi encontrado com acesso ao `ADMIN# Laboratório de Brute Force com Kali Linux e Medusa

> Projeto prático de cibersegurança desenvolvido como desafio educacional da DIO.

Laboratório prático de cibersegurança utilizando Kali Linux e Medusa para simulação de ataques de força bruta e Password Spraying em ambientes controlados. Inclui testes em FTP, DVWA e SMB, documentação técnica, evidências, análise de vulnerabilidades e medidas de mitigação.

## Objetivo

Configurar um ambiente de testes isolado, executar simulações controladas de autenticação em serviços vulneráveis e documentar os resultados para compreender os riscos de senhas fracas e as medidas de proteção.

## Escopo do desafio

- **FTP:** simulação controlada de tentativas de autenticação.
- **Web (DVWA):** automação de tentativas em formulário de autenticação.
- **SMB:** estudo de enumeração de usuários e Password Spraying.
- **Documentação:** registro das configurações, wordlists de teste, comandos, evidências, resultados e recomendações de mitigação.

## Ambiente de laboratório

| Componente | Finalidade |
|---|---|
| Kali Linux | Máquina utilizada para os testes de auditoria (`192.168.100.20`) |
| Metasploitable 2 | Máquina vulnerável para práticas em laboratório (`192.168.100.10`) |
| DVWA | Aplicação web intencionalmente vulnerável para o cenário web |
| Oracle VirtualBox | Virtualização das máquinas |
| Rede Interna | Rede interna do VirtualBox chamada `lab-int` |

> A configuração observada utiliza **Rede Interna (Internal Network)** do VirtualBox, não o modo **Host-only Adapter**. Os detalhes e as pendências de validação estão em [docs/01-configuracao-laboratorio.md](docs/01-configuracao-laboratorio.md).

## Estrutura do repositório

```text
.
├── README.md
├── docs/
│   ├── 01-configuracao-laboratorio.md
│   ├── 02-ataque-ftp.md
│   ├── 03-ataque-dvwa.md
│   ├── 04-password-spraying-smb.md
│   └── 05-mitigacoes.md
├── images/
│   └── evidencias/
├── scripts/
└── wordlists/
    ├── passwords-ftp.txt
    └── passwords-dvwa.txt
```

## Andamento

- [x] Repositório público criado.
- [x] Estrutura inicial de documentação criada.
- [x] Configuração inicial da rede e endereços IP documentada.
- [x] Reconhecimento do serviço FTP e validação manual de autenticação documentados.
- [x] Simulação FTP executada com Medusa e resultado registrado.
- [x] Cenário DVWA testado com Medusa; combinação `admin` / `password` confirmada manualmente.
- [ ] Validar a persistência do IP do Metasploitable após reinicialização.
- [ ] Confirmar se a modalidade de rede utilizada atende à orientação do instrutor.
- [ ] Adicionar as capturas de tela em `images/evidencias/`.
- [x] Executar e documentar o cenário web com DVWA.
- [x] Identificar SMB no alvo: portas 139/445 abertas, Samba e workgroup WORKGROUP.
.
- [ ] Revisar os resultados e as medidas de mitigação.

## Como a documentação será apresentada

Cada cenário terá:
1. Objetivo e descrição do teste.
2. Ambiente e pré-requisitos.
3. Procedimento realizado e comandos utilizados.
4. Evidências (quando aplicável).
5. Resultado observado — preenchido somente após a execução.
6. Análise e recomendações de segurança.

## Uso responsável

Este material tem finalidade exclusivamente educacional. Os testes devem ser realizados somente em máquinas próprias ou em ambientes para os quais exista autorização explícita. Mantenha o laboratório isolado e não utilize credenciais reais, listas de senhas obtidas de terceiros ou alvos públicos.

## Referência do desafio

Desafio: **Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux** — DIO.
