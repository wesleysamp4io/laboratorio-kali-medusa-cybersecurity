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

> **Nota sobre a topologia:** as duas VMs utilizaram **Rede Interna (Internal Network)** com o nome `lab-int`. Essa informação registra o ambiente efetivamente usado nos exercícios; a comparação entre modos de rede do VirtualBox não faz parte dos resultados dos cenários de FTP, DVWA ou SMB.

## Configuração de endereços IP

O Kali utiliza o endereço `192.168.100.20/24`. No Metasploitable 2, a interface `eth0` estava configurada para DHCP e ficou sem endereço IPv4 neste laboratório. Foi aplicada uma configuração estática no arquivo `/etc/network/interfaces`:

```text
auto eth0
iface eth0 inet static
    address 192.168.100.10
    netmask 255.255.255.0
```

A configuração foi aplicada com:

```bash
sudo ifup eth0
```

O endereço voltou a aparecer na interface. A persistência após reinicialização não foi avaliada, pois não era necessária para executar os cenários solicitados.

O Kali foi observado com a rota conectada:

```text
192.168.100.0/24 dev eth0  src 192.168.100.20
```

## Teste de conectividade

A partir do Kali Linux (`192.168.100.20`), foi executado:

```bash
ping -c 4 192.168.100.10
```

**Resultado observado:** após a recuperação do endereço, o Kali recebeu 4 respostas de 4 pacotes enviados, sem perda. Os testes subsequentes de FTP, HTTP/DVWA e SMB também foram executados a partir do Kali contra o Metasploitable.

## Checklist de configuração

- [x] Adaptadores das duas VMs conectados à Rede Interna `lab-int`.
- [x] Cabo virtual conectado nas duas VMs.
- [x] Endereços IPv4 identificados.
- [x] Configuração estática registrada no Metasploitable.
- [x] Comunicação entre as máquinas validada com ping antes da configuração estática.

## Evidências e apresentação

Esta entrega não inclui capturas de tela. A configuração do ambiente e os resultados de conectividade estão descritos neste documento em texto.

## Observações

Preencha versões e detalhes adicionais após conferi-los diretamente nas máquinas. Não publique credenciais reais, tokens ou dados de redes externas.
