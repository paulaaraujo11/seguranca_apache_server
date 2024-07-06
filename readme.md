# 10 Configurações de segurança do servidor apache2 no linux
Diretório root: /var/www/html
Arquivo principal de configurações: /etc/apache2/apache2.conf
Porta padrão HTTP: 80 TCP
Porta padrão HTTPS: 443 TCP
## 1. Atualize o apache regularmente

O Apache está em constante atualização para corrigir problemas de
segurança, disponibilizando atualizações e novas versões. Deixe sua versão sempre
na mais atual.
ver versão -> apache2 -v
atualizar -> sudo apt install apache2
## 2. Esconder a versão do Apache e o sistema operacional nas páginas de
erro
Caso você instale o apache utilizando o código fonte ou pelo gerenciador de
pacotes apt, ele exibe a versão do apache, além do sistema operacional, quando
uma página de erro é retornada para o usuário final. Isso pode ser uma falha de
segurança.
● sudo vim /etc/apache2/apache2.conf
● adicionar as linhas no arquivo:
ServerSignature Off
ServerTokens Prod
● reiniciar o apache -> systemctl restart apache2

## 3. Desabilitar a listagem de diretórios

Por padrão, o Apache lista todo o conteúdo dos diretórios que não possuam
um arquivo de índice, como index.html. Você deve desabilitar essa listagem.
● sudo vim /etc/apache2/apache2.conf
● adicionar as linhas abaixo:
<Directory /var/www/html>
Options -Indexes
</Directory>
● reiniciar o apache -> systemctl restart apache2
## 4. Desabilitar módulos desnecessários

Para minimizar as chances de você ser vítima de um ataque web, é
recomendado desabilitar todos os módulos que não estejam sendo utilizados. Para
desabilitar os módulos, acrescente # no início de cada linha.
Para desabilitar os módulos, acrescente # no início de cada linha.
Busca módulos -> grep LoadModule /etc/apache2/apache2.conf
## 5. Execute o Apache em um Usuário e um Grupo separado

Recomenda-se que o Apache seja executado em uma conta própria sem
privilégios.
# groupadd apache-web
# useradd -d /var/www/ -g http-web -s /sbin/nologin apache-user
/etc/httpd/conf/httpd.conf -> especificar os novos username e groupname.
User apache-user
Group apache-web
## 6. Utilize Allow e Deny para restringir o acesso aos diretórios

Podemos restringir o acesso aos diretórios com as opções "Allow" e "Deny"
no arquivo de configuração do Apache.
● adicionar a etc/apache2.conf
<Directory />
Options None
Order deny,allow
Deny from all
</Directory>

● reiniciar o apache -> systemctl restart apache2
## 7. Utilize os módulos MOD_SECURITY na segurança do Apache

O MOD_SECURITY atua como um firewall para suas aplicações web e
viabiliza o monitoramento do tráfego em tempo real.
sudo apt install libapache2-mod-security2 -y
sudo a2enmod headers
sudo systemctl restart apache2
## 8. Use mod_evasive Module

Bloqueie ataques DDoS usando o módulo mod_evasive, você pode usar o
módulo mod_evasive para bloquear e impedir ataques DDoS em seu servidor.
sudo apt-get install apache2-utils
sudo apt-get install libapache2-mod-evasive
sudo vim /etc/apache2/mods-enabled/evasive.conf
descomentar todas as linhas!
## 9. Limite o tamanho da Requisição Request

O apache não possui um limite no tamanho da requisição HTTP Request, o
tamanho por padrão é ilimitado e por isso, essa configuração é perigosa, permitindo
que sejam realizadas requisições muito grandes e você será uma vítima de um
ataque DDoS
Vamos definir um valor de 512000 (512 MB):
<Directory "/var/www/">
LimitRequestBody 512000
</Directory>
## 10. Habilitando logs

O registro do Apache fornece informações detalhadas sobre as solicitações
do cliente feitas em seu servidor da Web, portanto, habilitar este registro será útil ao
investigar a causa de problemas específicos. Para ativar o registro, o módulo
mod_log_config precisa ser incluído no arquivo apache2.conf.
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" detailed
CustomLog logs/access.log detailed

## Referências
https://www.vivaolinux.com.br/artigo/13-dicas-de-seguranca-para-o-Apache
https://www.tecmint.com/apache-security-tips/
https://beaglesecurity.com/blog/article/apache-web-server-hardening.html
https://www.acunetix.com/blog/articles/10-tips-secure-apache-installation/
