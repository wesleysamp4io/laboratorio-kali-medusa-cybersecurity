# 05 — Medidas de mitigação

Este documento reúne medidas defensivas relacionadas aos cenários estudados. Ajuste as recomendações à luz dos resultados realmente observados no laboratório.

## Proteção de autenticação

- Utilizar senhas longas, únicas e difíceis de adivinhar.
- Implementar autenticação multifator (MFA), sempre que disponível.
- Aplicar limitação de tentativas e atrasos progressivos.
- Configurar bloqueio ou proteção contra tentativas repetidas de acordo com a necessidade do serviço.
- Evitar mensagens de autenticação que revelem se o usuário existe.

## FTP

- Preferir SFTP ou FTPS quando aplicável, evitando FTP sem criptografia.
- Desabilitar contas anônimas quando não forem necessárias.
- Restringir por firewall quais origens podem acessar o serviço.
- Monitorar falhas e sucessos de autenticação.

## Aplicações web

- Aplicar rate limiting e controles contra automação abusiva.
- Utilizar MFA e proteção contra reutilização de senhas.
- Registrar tentativas de login e alertar sobre padrões anormais.
- Manter a aplicação e seus componentes atualizados.

## SMB

- Restringir SMB às redes e aos hosts que realmente precisam do serviço.
- Desabilitar versões obsoletas e configurações inseguras.
- Aplicar privilégios mínimos às contas.
- Monitorar falhas de autenticação e padrões de Password Spraying.
- Utilizar políticas de senha e MFA quando suportado pela arquitetura.

## Validação

Após concluir os testes, relacione cada achado observado à medida de mitigação correspondente. Não declare uma vulnerabilidade como confirmada sem evidência.
