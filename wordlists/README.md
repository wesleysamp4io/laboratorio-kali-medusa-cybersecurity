# Wordlists de laboratório

As listas desta pasta são pequenas e artificiais, criadas exclusivamente para os exercícios autorizados no ambiente local.

## FTP

O arquivo [`passwords-ftp.txt`](passwords-ftp.txt) contém quatro candidatos de teste, um por linha, para a atividade FTP no Metasploitable 2.

- Lista curta, destinada somente ao host de laboratório `192.168.100.10`.
- Foi utilizada no exercício com a conta de laboratório `msfadmin`.
- Não use esta lista contra sistemas externos ou contas reais.

## DVWA

O arquivo [`passwords-dvwa.txt`](passwords-dvwa.txt) contém os quatro candidatos utilizados no cenário Brute Force do DVWA local.

- Lista curta, utilizada somente no formulário de laboratório.
- No teste corrigido, o Medusa encontrou `password` para `admin`, e o login foi confirmado manualmente no DVWA.
- Não reutilize esses valores fora deste exercício.

## SMB

O arquivo [`users-smb.txt`](users-smb.txt) contém três nomes de contas que apareceram na enumeração do Metasploitable 2: `msfadmin`, `user` e `root`. A presença de um nome na enumeração não garante que a conta aceite autenticação SMB.

## Boas práticas

- Não armazene senhas pessoais, credenciais corporativas ou dados vazados.
- Não inclua wordlists grandes de terceiros sem necessidade.
- Explique no relatório como a lista foi criada e qual era o propósito do teste.
- Não reutilize os valores deste exercício fora do laboratório.
