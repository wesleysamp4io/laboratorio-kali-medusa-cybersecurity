# 01 — Configuração do laboratório

## Objetivo

Documentar a preparação do ambiente virtual utilizado nos testes do desafio.

## Componentes

| Máquina / ferramenta | Função | Versão utilizada |
|---|---|---|
| Kali Linux | Estação de auditoria | A preencher |
| Metasploitable 2 | Alvo vulnerável de laboratório | Metasploitable 2 |
| DVWA | Aplicação web vulnerável para o cenário HTTP | Hospedagem a confirmar |
| Oracle VirtualBox | Virtualização | A preencher |

## Topologia de rede observada

As duas máquinas virtuais estão conectadas ao **Adaptador 1** do VirtualBox, configurado como **Rede Interna (Internal Network)**, com o mesmo nome de rede: `lab-int`.

| Item | Kali Linux | Metasploitable 2 |
|---|---|---|
| Adaptador VirtualBox | Adaptador 1 | Adaptador 1 |
| Modo de conexão | Rede Interna | Rede Interna |
| Nome da rede | `lab-int` | `lab-int` |
| Tipo de placa | Intel PRO/1000 MT Desktop | Intel PRO/1000 MT Desktop |
| Cabo conectado | Sim | Sim |
| Interface no sistema | `eth0` | `eth0` |
| Endereço IPv4 | `192.168.100.20/24` | `192.168.100.10/24` |

> **Nota sobre o modo de rede:** os prints de configuração mostram o modo **Rede Interna (Internal Network)**, e não **Host-only Adapter**. A Rede Interna mantém a comunicação entre as VMs na rede virtual nomeada, sem fornecer por si só acesso do host a essa rede. O desafio menciona “rede interna (host-only)”; como esses são modos distintos no VirtualBox, a configuração atual foi registrada exatamente como está e deverá ser comparada com a exigência do instrutor antes da entrega final.

## Configuração de endereços IP

Durante a verificação, a interface `eth0` do Metasploitable estava sem endereço IPv4. O endereço foi aplicado manualmente para recuperar a comunicação:

```bash
sudo ifconfig eth0 192.168.100.10 netmask 255.255.255.0 up
```

**Atenção:** essa configuração feita com `ifconfig` é temporária e pode ser perdida após reiniciar a máquina. A persistência do endereço ainda precisa ser configurada e validada.

O Kali foi observado com a rota conectada:

```text
192.168.100.0/24 dev eth0  src 192.168.100.20
```

## Teste de conectividade

A partir do Kali Linux (`192.168.100.20`), foi executado:

```bash
ping -c 4 192.168.100.10
```

**Resultado observado:**
- Pacotes transmitidos: 4
- Pacotes recebidos: 4
- Perda: 0%
- Estado: comunicação entre Kali e Metasploitable validada neste teste.

## Checklist de configuração

- [x] Adaptadores das duas VMs conectados à Rede Interna `lab-int`.
- [x] Cabo virtual conectado nas duas VMs.
- [x] Endereços IPv4 identificados.
- [x] Comunicação entre as máquinas validada com ping.
- [ ] Configurar o IP do Metasploitable de forma persistente.
- [ ] Confirmar se a modalidade Rede Interna atende à exigência do instrutor ou se é necessário utilizar Host-only.
- [ ] Verificar e documentar o isolamento da rede externa.
- [ ] Adicionar capturas de tela em `images/evidencias/`.

## Evidências

As capturas de tela das configurações e do teste de conectividade ainda serão organizadas na pasta `images/evidencias/`.

## Observações

Preencha versões e detalhes adicionais após conferi-los diretamente nas máquinas. Não publique credenciais reais, tokens ou dados de redes externas.
