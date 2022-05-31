# Live Coding - Hacking prático na web: Ataques, Mitigações e Desafios

> DIO.me com Isadora Garcia Ferrão e Denilson Bonatti.

## Desafio da Live

"Ser o guardião da segurança de sistemas WEB"

## 1 Conceitos

- Ataque: Danificar ou destruir uma rede de sistemas explorando vulnerabilidades, roubo de dados.

- Como evitar ataques: Codificar sistemas seguros, uso de web application firewalls, testes de black-box

- OWASP Top Ten: [link](https://owasp.org/www-project-top-ten/)

- Nesta live foco em Injection (injeção de código) e cross site scripting XSS.

## 2 Instalação de Programas

- Virtualbox: para virtualização

- OWASP BWA: Máquina virtual para estudos de vulnerabilidades é basicamente um servidor web com várias aplicações com vulnerabilidades intencionais.
  download: https://sourceforge.net/projects/owaspbwa/
  user: root, pass: owaspbwa

- Andiparos: Ferramenta de testes de vulnerabilidades

## 3 Explorando ataques

### OWASP Multilidae II:

Menu superior entrar/registrar

1 - entrar com usuário e senha qualquer o sistema response "conta não existe" (isso é uma vulnerabilidade pois sabemos que não existe esta conta mas podemos testar com outras)

2 - entrar com usuário admin e senha qualquer o sistema response "senha incorreta" (então quer disser que o usuário admin existe)

#### SQL Injection

3 - vamos pensar em um SQL que possa ser usado no sistema:

SELECT username FROM tableusers WHERE password='1234'

vamos manipular este código usando aspas simples ao redor dele assim:

'SELECT username FROM tableusers WHERE password='1234''

quando colocamos aspas simples ao redor o banco de dados normalmente response erro de sintaxe.

Sabendo disso podemos ver ser o sistema tem uma vulnerabilidade digitando no username aspas simples mais um espaço: [' ]

O sistema responde um erro com informações como: MySQLHandler

e Query: SELECT username FROM accounts WHERE username='' ';

(Assim sabemos que o sistema usa o MySQL e também sabemos o nome do campo da tabela "username" e o nome da tabela "accounts")

Então temos concreto: SELECT username FROM accounts WHERE username='' '

vamos manipular o código concreto:

SELECT username FROM accounts WHERE username='admin' or '8'='8';

neste caso 8 sempre será igual a 8 então no campo username no sistema vamos colocar: admin or '8'='8 na senha vamos colocar qualquer coisa.

E conseguimos logar no sistema! e como Admin!

Vamos estar outra coisa, deslogando do sistema primeiro.

Agora colocamos um usuário qualquer e na senha vamos colocar nossa expressão: admin or '8'='8

E também conseguimos logar no sistema! e como Admin!

4 - exemplo de mitigação deste erro:

          código errado em php:
            <?php
              $sql = "SELECT * FROM accounts WHERE username='$nome'";
              $result = $connection->query($sql);
              if($result-> num_rows){
                while($accounts = $result->fetch_object()){
                  echo "{$account->id} - {$account->username}";
                }
              }
            >

          código ok em php:
            <?php
              $nome="Isadora";
              $sql = "SELECT username FROM accounts WHERE username=?;
              $aux = $connection->prepare($sql);
              $aux->bind_param('s', $nome);
              $aux->execute();
              $result = $aux->get_result()->fetch_object();
              var_dump($result);
            >

#### Cross site scripting XSS

5 - Continuando no OWASP Multilidae II e deslogado, menu lateral:
OWASP 2013 > A3 - Cross Site Scripting (XSS) > Persistence (second order) > Add to your blog

        No campo de input coloquemos:
          <script>
            alert("HACKER ONLINE... PEQUEI SEUS DADOS");
          </script>

Instalar e abrir o XAMPP Control Panel:

Apache config > alterar linha: Listen 192.168.56.101:80 (obs: o ip que aparecer ao rodar o OWASP BWA)
alterar também dentro da tag "Directory" no final colocar "Allow from all"

Abrir outro browser no endereço ip que configuramos:

        Vamos adicionar outro script:
          <script>
            document.body.innerHTML="";
          </script>

Isso pode simular que o sistema caiu.

6 - Como evitar:

Etapa 1: treinar e manter a conscientização

Etapa 2: não confie em nenhuma entrada do usuário

Etapa 3: usar escape/codificação

Etapa 4: limpar o HTML

Etapa 5: definir o sinalizador HttpOnly

Etapa 6: use uma política de segurança de conteúdo (CSP)

Etapa 7: digitalize regularmente (exemplo com Acunetix)

## 4 Análise black-box e web firewalls

- Análise black-box: é teste da perspectiva de um usuário final, mais próximo de um ataque externo

- Scanners de vulnerabilidades: Uniscan, Nessus, Wapiti, Andiparos, Skipfish, Vega

- saber mais: pesquise por Avaliação de scanners de vulnerabilidades de aplicações WEB - LEA UNIPAMPA

- saber mais: pesquise por Investigação do Impacto de Frameworks de Desenvolvimento de Softwares na Segurança de Sistemas WEB - LEA UNIPAMPA / CIN UFSC

- OWASP ZAP

- WAF - WEB Application Firewall: ModSecurity, Shadow, Naxsi

## 5 Você pode ficar rico

- hackerone: site de desafios
