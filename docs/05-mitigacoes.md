# 05 — Medidas de mitigação

## Objetivo

Relacionar os resultados observados nos cenários FTP, DVWA e SMB a medidas defensivas aplicáveis. As ações abaixo são recomendações; elas não foram implementadas nem validadas neste laboratório.

## 1. FTP

### Achados observados

- O serviço FTP estava disponível em `192.168.100.10:21`, identificado como `vsftpd 2.3.4`.
- A autenticação automatizada identificou a combinação `msfadmin / msfadmin` no serviço.
- A validação manual de autenticação também foi bem-sucedida.

### Mitigações recomendadas

1. **Remover credenciais padrão:** alterar ou desabilitar contas padrão e garantir que cada conta tenha uma senha forte e exclusiva.
2. **Preferir transferência segura:** substituir FTP sem criptografia por SFTP ou FTPS, conforme a necessidade e a arquitetura. FTP tradicional não protege as credenciais durante o transporte.
3. **Restringir exposição:** permitir acesso à porta 21 somente a hosts e redes que realmente precisem do serviço; desativar o serviço se não for necessário.
4. **Limitar tentativas:** aplicar controles contra tentativas repetidas, atrasos progressivos e alertas para padrões anormais de autenticação.
5. **Reduzir privilégios:** utilizar contas de serviço sem privilégios administrativos e limitar o acesso a diretórios necessários.

## 2. Aplicação web — DVWA

### Achados observados

- O formulário Brute Force estava no nível de segurança `Low`.
- Os parâmetros `username`, `password` e `Login` apareceram na URL, indicando envio por GET.
- A combinação `admin / password` foi identificada pelo Medusa e confirmada manualmente no formulário.

### Mitigações recomendadas

1. **Não enviar senhas na URL:** transmitir credenciais no corpo de uma requisição POST. POST, sozinho, não protege o tráfego; utilizar HTTPS em toda a aplicação.
2. **Substituir credenciais fracas:** impedir senhas padrão, curtas ou previsíveis e exigir senhas fortes e exclusivas.
3. **Proteger o armazenamento de senhas:** armazenar senhas com funções próprias para hash de senhas, com salt exclusivo por usuário, como Argon2id, bcrypt ou scrypt. Nunca guardar senhas em texto puro.
4. **Controlar tentativas automatizadas:** aplicar limitação de tentativas, atrasos progressivos, detecção de padrões de força bruta e alertas. Ajustar bloqueios para reduzir o risco de negação de serviço contra usuários legítimos.
5. **Proteger sessões:** regenerar o identificador de sessão após autenticação e configurar cookies com atributos apropriados, como HttpOnly, Secure (em HTTPS) e SameSite.
6. **Adotar MFA:** exigir um segundo fator para reduzir o impacto de senhas descobertas ou reutilizadas.

## 3. SMB — Password Spraying e compartilhamento administrativo

### Achados observados

- As portas TCP `139` e `445` estavam abertas no Metasploitable 2.
- A enumeração SMB revelou diversos nomes de contas locais.
- O teste com uma única senha (`msfadmin`) identificou a conta `msfadmin` e o módulo informou `ADMIN$ - Access Allowed`.

### Mitigações recomendadas

1. **Restringir SMB na rede:** permitir as portas 139 e 445 apenas entre hosts que realmente necessitem de compartilhamento. Bloquear o acesso a partir de redes não confiáveis.
2. **Rever compartilhamentos administrativos:** limitar o uso de `ADMIN$` a administradores autorizados e evitar conceder privilégios administrativos a contas de uso comum.
3. **Aplicar privilégio mínimo:** revisar grupos, permissões e contas locais; remover contas desnecessárias e desabilitar contas que não precisam de acesso.
4. **Reduzir enumeração desnecessária:** restringir consultas anônimas ou não autenticadas a informações de usuários e compartilhamentos, quando compatível com a operação.
5. **Usar credenciais distintas:** nunca reutilizar a mesma senha entre contas. Senhas exclusivas reduzem o impacto de Password Spraying.
6. **Monitorar autenticação:** alertar para tentativas da mesma senha contra várias contas, falhas distribuídas e acessos inesperados a compartilhamentos administrativos.
7. **Rever protocolos e configurações:** desabilitar SMBv1 quando não houver dependência legada, manter Samba atualizado e exigir assinatura SMB quando suportada e apropriada ao ambiente.

## 4. Controles transversais

- Manter sistemas, serviços e aplicações atualizados.
- Usar contas individuais e senhas exclusivas, armazenadas em gerenciador de senhas.
- Aplicar MFA onde houver suporte; para serviços que não oferecem MFA nativo, proteger o acesso por VPN, bastion host ou controles de identidade.
- Centralizar e revisar logs de autenticação, incluindo sucessos e falhas.
- Segmentar a rede e limitar cada serviço à menor superfície de exposição possível.
- Revisar periodicamente contas, permissões, compartilhamentos e serviços ativos.

## Conclusão

Os três cenários demonstraram riscos associados a credenciais padrão ou fracas, exposição de parâmetros de autenticação e permissões SMB excessivas. As medidas prioritárias são remover credenciais padrão, utilizar senhas únicas e fortes, proteger o transporte de credenciais, limitar tentativas de autenticação, restringir o acesso aos serviços e aplicar privilégio mínimo.

**Nota:** as recomendações deste documento são propostas de mitigação. Nenhuma delas deve ser descrita como implementada ou testada sem uma atividade adicional de validação.
