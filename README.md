# Laboratório de Brute Force com Kali Linux e Medusa

> Projeto prático de cibersegurança desenvolvido como desafio educacional da DIO.

Laboratório de cibersegurança com Kali Linux, Medusa e máquinas vulneráveis em ambiente virtual isolado. O projeto documenta três cenários: autenticação FTP, formulário web DVWA e enumeração/Password Spraying em SMB.

## Objetivo

Preparar um ambiente controlado, executar simulações de auditoria de autenticação e documentar os resultados, os riscos observados e as medidas de mitigação.

## Ambiente

| Componente | Função |
|---|---|
| Kali Linux (`192.168.100.20`) | Estação de auditoria |
| Metasploitable 2 (`192.168.100.10`) | Máquina vulnerável de laboratório |
| DVWA | Aplicação web vulnerável hospedada no Metasploitable 2 |
| Oracle VirtualBox | Virtualização |
| Rede `lab-int` | Rede Interna do VirtualBox |

> **Topologia utilizada:** as VMs se comunicaram pela Rede Interna (`lab-int`) do VirtualBox. Os detalhes estão registrados em [docs/01-configuracao-laboratorio.md](docs/01-configuracao-laboratorio.md).

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
├── scripts/
│   └── README.md
└── wordlists/
    ├── README.md
    ├── passwords-ftp.txt
    ├── passwords-dvwa.txt
    └── users-smb.txt
```

## Cenários documentados

- [x] **FTP:** reconhecimento da porta 21, validação manual e simulação com Medusa. Resultado registrado em [docs/02-ataque-ftp.md](docs/02-ataque-ftp.md).
- [x] **DVWA:** análise do formulário GET e simulação com Medusa. A combinação encontrada foi validada manualmente. Resultado registrado em [docs/03-ataque-dvwa.md](docs/03-ataque-dvwa.md).
- [x] **SMB:** reconhecimento das portas 139/445, enumeração de contas e Password Spraying. O Medusa reportou acesso ao compartilhamento `ADMIN$` para `msfadmin`. Resultado registrado em [docs/04-password-spraying-smb.md](docs/04-password-spraying-smb.md).

## Mitigações

As recomendações defensivas relacionadas aos achados dos três cenários estão em [docs/05-mitigacoes.md](docs/05-mitigacoes.md). Elas são propostas de melhoria e não são apresentadas como correções já implementadas ou testadas.

## Evidências e formato da entrega

Esta versão do repositório **não inclui capturas de tela**, conforme opção adotada para a entrega. Os procedimentos, comandos, saídas observadas e análises estão descritos nos arquivos da pasta `docs/`.

## Uso responsável

Este material tem finalidade exclusivamente educacional. Os testes foram realizados em máquinas virtuais próprias, dentro da rede local de laboratório. Não utilize os comandos, listas ou credenciais de teste contra sistemas de terceiros.

## Referência

Desafio: **Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux** — DIO.