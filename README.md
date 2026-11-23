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
| Kali Linux | Máquina utilizada para os testes de auditoria |
| Metasploitable 2 | Máquina vulnerável para práticas em laboratório |
| DVWA | Aplicação web intencionalmente vulnerável para o cenário web |
| Oracle VirtualBox | Virtualização das máquinas |
| Rede Host-only | Comunicação controlada entre as máquinas do laboratório |

> A topologia final, os endereços IP e a configuração efetivamente utilizada serão registrados em [docs/01-configuracao-laboratorio.md](docs/01-configuracao-laboratorio.md).

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
```

## Andamento

- [x] Repositório público criado.
- [x] Estrutura inicial de documentação criada.
- [ ] Configuração do laboratório registrada.
- [ ] Teste FTP executado e documentado.
- [ ] Teste web com DVWA executado e documentado.
- [ ] Cenário SMB documentado.
- [ ] Evidências adicionadas.
- [ ] Resultados e medidas de mitigação revisados.

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
