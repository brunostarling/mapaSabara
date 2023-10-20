# 1. Configuração da máquina

 * Verifique os [Requisitos Mínimos](https://github.com/edsongs/instal-mapas/tree/master/draft/Requisitos%20m%C3%ADnimos#instala%C3%A7%C3%A3o-mapas-culturais---requisitos-m%C3%ADnimos)

# 2. Softwares Requeridos

  #### Atualize os repositórios de referência de sua máquina:
  
  ```
sudo apt-get update -y
  ```
  ```
sudo apt-get upgrade -y
  ```
  
  #### Instale as dependências diversas:
  
  ```
sudo apt-get install git curl npm ruby ruby-dev -y
  ```
  
  ####  Atualizar referências para a versão de ruby:
  
  ```
sudo update-alternatives --install /usr/bin/ruby ruby /usr/bin/ruby2.7 10
  ```
  ```
sudo update-alternatives --install /usr/bin/gem gem /usr/bin/gem2.7 10
  ```
  _Nota: O comando update-alternatives --install precisa das informações <link> <name> <path> <priority>_
  
  #### Instale a versão stable mais nova do nodejs:
  
  ```
sudo curl -sL https://deb.nodesource.com/setup_12.x -o nodesource_setup.sh
  ```
  ```
sudo bash nodesource_setup.sh
  ```
  ```
sudo apt install nodejs -y
  ```

  #### Verificando se foi instalada a versão mais recente do NodeJS e do NPM:
  
  _Ubuntu20.04_
  ```
node -v 
  ```
  ```  
npm -v
  ```
  ####  Instale o postgresql e postgis: 
  
   _Ubuntu20.04_
  
  ```
sudo apt-get install postgresql-12 postgresql-contrib postgis postgresql-12-postgis-3 postgresql-12-postgis-3-scripts -y
  ```
  
  ####  Instale o php7.2, php7.2-fpm e extensões do php utilizadas no sistema
  
  _Ubuntu20.04_
  
  ```
sudo add-apt-repository ppa:ondrej/php
  ``` 
  ``` 
sudo apt-get update
  ``` 
  
  _Ubuntu20.04 e Ubuntu18.04_
  
  ```
sudo apt-get install php7.4 php7.4-mbstring php7.4-xml php7.4-gd php7.4-cli php7.4-json php7.4-curl php7.4-pgsql php-apcu php7.4-fpm imagemagick libmagickcore-dev libmagickwand-dev php7.4-imagick -y
  ```
  ```
sudo apt install php7.4-{cli,common,curl,zip,gd,mysql,xml,mbstring,json,intl}
  ```
  _Alterar PHP_
  ```  
sudo update-alternatives --config php
  ```
    
  #### Instale o nginx
  
  ```
sudo apt-get install nginx
  ```
  
  #### Instale o gerenciador de dependências do PHP Composer
  
  ```
curl -sS https://getcomposer.org/installer | php
  ```
  
  ```
sudo mv composer.phar /usr/local/bin/composer.phar
  ```
  
  #### Também é importante ter o pacote zip instalado no seu servidor. Ele é usado para gerar o pacote com os anexos enviados nas inscrições. Caso ainda não tenha:
  
  ```
  ubuntu@server$ sudo apt-get install zip unzip -y
  ```
  
  #### No Ubuntu o executável do NodeJS se chama nodejs, porém para o correto funcionamento das bibliotecas utilizadas, o executável deve se chamar node. Para isto criamos um link simbólico com o comando abaixo:
  
   _Ubuntu20.04 e Ubuntu18.04_
   
  #### Instalando os minificadores de código Javascript e CSS: uglify-js, uglifycss e autoprefixer:
  
  ```
sudo npm install -g terser uglifycss autoprefixer
  ```
  
  #### Instalando o SASS, utilizado para compilar os arquivos CSS:
  
  ```
sudo gem install sass -v 3.4.22
  ```
  ```  
npm i postcss
  ```
# 3. Clonando o Repositório

  #### Primeiro vamos criar o usuário que rodará a aplicação e que será proprietário do banco de dados, definindo sua home para /srv e colocando-o no grupo www-data:
  
  ```
sudo useradd -G www-data -d /srv/mapas -m mapas
  ```
  
  #### Vamos clonar o repositório usando o usuário criando, então precisamos primeiro "logar" com este usuário:
  
  #### Criando usuário "mapas"  
  ```
sudo su - mapas
  ```

  #### Clonando o repositório (USUÁRIO MAPAS)

  ```
git clone https://github.com/hacklabr/mapasculturais.git
  ```

  #### Agora vamos colocar o repositório na branch master. Caso queira você poderá ver todas as versões dosponíveis [aqui](https://github.com/hacklabr/mapasculturais/releases)
  
  ```
cd mapasculturais
  ```
  ```    
git checkout master
  ```
  ```    
git pull origin master
  ```
  
  #### Agora vamos instalar as dependências de PHP utilizando o Composer.
  
  ```
cd ~/mapasculturais/src/protected/
  ```
  ```  
composer.phar install
  ```
  
# 4. Banco de Dados

  #### Vamos voltar ao usuário root para criar o banco de dados.
  
  ```
exit
  ```
  
  #### Primeiro vamos criar o usuário no banco de dados com o mesmo nome do usuário do sistema
  
  ```
sudo -u postgres psql -c "CREATE USER mapas"
  ```
  
  #### Agora vamos criar a base de dados para a aplicação com o mesmo nome do usuário
  
  ```
sudo -u postgres createdb --owner mapas mapas
  ```
  #### Criar as extensões necessárias no banco
  
  ```
sudo -u postgres psql -d mapas -c "CREATE EXTENSION postgis;"
  ```
  ```  
sudo -u postgres psql -d mapas -c "CREATE EXTENSION unaccent;"
  ```
  
  #### Volte a "logar" com o usuário criado e importar o esquema da base de dados
  
  ```
sudo su - mapas
  ```
  ```  
psql -f mapasculturais/db/schema.sql
  ```
# 5. Configurações de instalação
  
  #### Primeiro crie um arquivo de configuração copiando o arquivo de template de configuração. Este arquivo está preparado para funcionar com este guia, utilizando o método de autenticação Fake.
  
  ```
cp mapasculturais/src/protected/application/conf/config.template.php mapasculturais/src/protected/application/conf/config.php
  ```
  
  #### Criando diretórios de log, files e estilo
  
  ```
exit
  ```
  ```    
sudo mkdir /var/log/mapasculturais
  ```   
  ```     
sudo chown mapas:www-data /var/log/mapasculturais
  ```
  
  #### Com o usuário criado, crie a pasta para os assets, para os uploads e para os uploads privados (arquivos protegidos, como anexos de inscrições em oportunidades):
  
  ```
sudo su - mapas
  ```
  ```
mkdir mapasculturais/src/assets
  ```
  ```
mkdir mapasculturais/src/files
  ```
  ```
mkdir mapasculturais/private-files
  ```
  
# 6. Configuração do nginx
  
  #### Precisamos criar o virtual host do nginx para a aplicação. Para isto crie, como root, o arquivo /etc/nginx/sites-available/mapas.conf
  
  ```
exit
  ```
  ```
sudo nano /etc/nginx/sites-available/mapas.conf
  ```
  
  _Eu costumo utilizar o vi, mas você pode usar o vim, nano e afins para fazer a edição deste arquivo_
  
  #### Coloque o conteúdo abaixo dentro do arquivo /etc/nginx/sites-available/mapas.conf criado anteriormente: Muita atenção aqui, pois você precisará subistituir todos os "meu.dominio.gov.br" pelo seu domínio ou IP fixo dependendo de qual for o seu caso.
  
  ```
  server {
    set $site_name mapacultural.sabara.mg.gov.br;

    listen *:80;
    server_name mapacultural.sabara.mg.gov.br;
    access_log   /var/log/mapasculturais/nginx.access.log;
    error_log    /var/log/mapasculturais/nginx.error.log;

    index index.php;
    root  /srv/mapas/mapasculturais/src/;

    location / {
      try_files $uri $uri/ /index.php?$args;
    }
    
    location ~ /files/.*\.php$ {
      return 80;
    }
    

    location ~* \.(js|css|png|jpg|jpeg|gif|ico|woff)$ {
            expires 1w;
            log_not_found off;
    }

    location ~ \.php$ {
      try_files $uri =404;
      include fastcgi_params;
      fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
      fastcgi_pass unix:/var/run/php/php7.4-fpm-$site_name.sock;
      client_max_body_size 0;
    }

    charset utf-8;
  }

  server {
    listen *:80;
    server_name www.mapacultural.sabara.mg.gov.br;
    return 301 $scheme://mapacultural.sabara.mg.gov.br$request_uri;
  }
  ```
  
  #### Feita a alteração no arquivo, salve!
  
  #### Crie o link para habilitar o virtual host
  
  ```
sudo ln -s /etc/nginx/sites-available/mapas.conf /etc/nginx/sites-enabled/mapas.conf
  ```
  
  #### Remover o arquivo default da pasta /etc/nginx/sites-available/ e /etc/nginx/sites-enabled/
  
  ```
sudo rm /etc/nginx/sites-available/default
  ```
  ```
sudo rm /etc/nginx/sites-enabled/default
  ```
  
  #### Configurações pool do php7.2-fpm: Crie o arquivo ```/etc/php/7.2/fpm/pool.d/mapas.conf```. Muita atenção na alteração da linha ```listen = /var/run/php/php7.2-fpm-meu.dominio.gov.br.sock``` pois o nome do arquivo .sock precisará ser extamente como foi configurado do arquivo ```/etc/nginx/sites-available/mapas.conf```. O diretório ```/var/run/php/``` podrá mudar dependendo da versão que estiver trabalhando. 
  
  ```
sudo nano /etc/php/7.4/fpm/pool.d/mapas.conf
  ```
  
  _Eu costumo utilizar o vi, mas você pode usar o vim, nano e afins para fazer a edição deste arquivo_
  
  ```
  [mapas]
listen = /var/run/php/php7.4-fpm-mapacultural.sabara.mg.gov.br.sock
listen.owner = mapas
listen.group = www-data 
user = mapas
group = www-data
catch_workers_output = yes
pm = dynamic
pm.max_children = 10
pm.start_servers = 1
pm.min_spare_servers = 1
pm.max_spare_servers = 3
pm.max_requests = 500
chdir = /srv/mapas
; php_admin_value[open_basedir] = /srv/mapas:/tmp
php_admin_value[session.save_path] = /tmp/
; php_admin_value[error_log] = /var/log/mapasculturais/php.error.log
; php_admin_flag[log_errors] = on
php_admin_value[display_errors] = 'stderr'
  ```
  
# 7. Concluindo
  
  #### Para finalizar, precisamos popular o banco de dados com os dados iniciais e executar um script que entre outras coisas compila e minifica os assets, otimiza o autoload de classes do composer e roda atualizações do banco.
  
  
  ```
sudo su - mapas
  ```
  ```
psql -f mapasculturais/db/initial-data.sql
  ```
  ```
./mapasculturais/scripts/deploy.sh
  ```
  ```
exit
  ```
  
  #### Reinicie os serviços do nginx e php7.2-fpm
  
  
  ```
sudo service nginx restart
  ```
  ```
sudo service php7.4-fpm restart
  ```
 
_A partir deste momento sua aplicação estará disponível para acesso utilizando a Autenticação Fake_


# Instalação do certificado SSL com LetsEncrypt
  
  _Ubuntu18.04_
  
  ### Passo 1 — Instalando o Certbot
  
  #### Primeiro, adicione o repositório:

  ```
  ubuntu@server$ sudo add-apt-repository ppa:certbot/certbot
  ```

  #### Você vai precisar pressionar ENTER para aceitar. Depois, atualize a lista de pacotes para pegar as novas informações de pacotes do repositório:

  ```
  ubuntu@server$ sudo apt-get update
  ```
  #### E, finalmente, instale o pacote Nginx do Certbot com o apt-get:
  
  _ubuntu18.04_
  ```
  ubuntu@server$ sudo apt-get install python-certbot-nginx
  ```
  _Ubuntu20.04_
  ```
  ubuntu@server$ sudo apt install certbot python3-certbot-nginx -y
  ```
  
  ### Passo 2 — Confirmando a Configuração do Nginx
  
  #### Para verificar, abra o arquivo de bloco do servidor para o seu domínio usando o ```vi``` ou o seu editor de textos favorito:
  
  ```
  ubuntu@server$ sudo vi /etc/nginx/sites-available/mapas.conf
  ```
  
  #### Verifique se todas as informações abaixo foram configuradas corretamente, já fizemos nas [etapas anteriores](https://github.com/wiusmarques/mapasculturais#6-configura%C3%A7%C3%A3o-do-nginx), iremos apenas conferir.
  
  ```
  set $site_name meu.dominio.gov.br;
  server_name  meu.dominio.gov.br;
  server_name meu.dominio.gov.br;
  return 301 $scheme://mapas.siapp.one$request_uri;
  ```
  
  #### Depois de conferir o arquivo, saia do seu editor, e verifique a sintaxe da edição da sua configuração:
  
  ```
  ubuntu@server$ sudo nginx -t
  ```
  
  #### Se você receber um erro, reabra o arquivo de bloco do servidor e verifique se há erros de digitação ou caracteres ausentes. Quando a sintaxe do seu arquivo de configuração estiver correta, recarregue o Nginx para carregar a nova configuração:
  
  ```
  ubuntu@server$ sudo systemctl reload nginx
  ```
  
  ### Passo 3 — Permitindo HTTPS Através do Firewall
  
  _Cada servidor possuí sua particularidade, garanta que as portas 443(https) e 80(http) estejam liberadas no Firewall [Permitindo HTTPS Através do Firewall](https://www.digitalocean.com/community/tutorials/como-proteger-o-nginx-com-o-let-s-encrypt-no-ubuntu-18-04-pt) vá até o tópico Passo 3 — Permitindo HTTPS Através do Firewall caso esteja utilizando um servidor padrão, no meu caso estou utilizando AWS e por isso o procedimento é um pouco diferente_ 
  
  ### Passo 4 — Obtendo um Certificado SSL
  
  #### Obtendo o certificado
  
  ```
  ubuntu@server$ sudo certbot --nginx -d meu.dominio.gov.br
  ```
  
  #### Se isso for bem sucedido, o certbot perguntará como você gostaria de definir suas configurações de HTTPS.
  
  ```
  Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
  -------------------------------------------------------------------------------
  1: No redirect - Make no further changes to the webserver configuration.
  2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
  (...)
  ```
  
  #### Escolha 1 para redirecionar não redirecionar o tráfego forçado para HTTPS e 2 para fazer com que todaas as requisições sejam forçadas para HTTPS
  
  #### Se isso for bem sucedido, o certibot irá exibir uma mensagem de sucesso:
  
  ```
  - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Congratulations! You have successfully enabled https://mapas.siapp.one

  You should test your configuration at:
  (...)
  ```
  
  ### Passo 5 — Verificando a Auto-Renovação do Certbot
  
  ```
  ubuntu@server$ sudo certbot renew --dry-run
  ```
  
  
  
_Em uma das máquinas que fiz a instalação precisei reiniciar o serivdor inteiro para que o NGINX funcione corretamente. A configuração do SSL foi feita com base no seguinte [artigo](https://www.digitalocean.com/community/tutorials/como-proteger-o-nginx-com-o-let-s-encrypt-no-ubuntu-18-04-pt)._

# Autenticação - MultipleLocalAuth

  _Após a instalção do Mapa Cultural teremos várias possibilidades para autenticação, mas iremos abordar aqui apenas a autenticação utilizando MultipleLocalAuth que dará ao usuário a possibilidade de fazer a autenticação utilizando contas Locais, Gooogle, Facebook e afins. 
  Após os procedimentos descritos abaixo iremos sair do modo de 'Autenticação Fake'. 
  Para isso iremos utilizar as informações já disponibilidas no repositório [MultipleLocalAuth](https://github.com/secultce/MultipleLocalAuth) e adicionar alguns detalhes com o objetivo de facilitar a implementação_
  
  
  #### Clonando o repositório, iremos acessar a pasta ```plugins``` do nosso projeto utilizando o usuário mapas
  
  ```
  ubuntu@server$ sudo su - mapas
  mapas@server$ cd mapasculturais/src/protected/application/plugins
  mapas@server$ pwd
    output: /srv/mapas/mapasculturais/src/protected/application/plugins
  ```
  
  #### Após nos certificarmos de que estamos no diretório correto iremos clonar o repositório MultipleLocalAuth
  
  ```
  mapas@server$ git clone https://github.com/secultce/MultipleLocalAuth.git
  mapas@server$ exit
  mapas@server$
  ```
  
  ##### Agora, Faça a edição do arquivo de configuração do mapas (criado no passo 5) utilizando vi ou o editor de sua preferência.
  
  ```
  ubuntu@server$ sudo vi /srv/mapas/mapasculturais/src/protected/application/conf/config.php
  ```
  
  _Neste momento teremos três etapas_
  
  1. Ativar o plugin
  2. Configurar MultipleLocalAuth como seu Provider de autenticação
  3. Configurar as chaves das redes sociais
  
  ### 1- Ativar o plugin
  
  ##### Ainda com o arquivo ```config.php``` aberto adicione a seguinte configuração: 
  
  ```
  'plugins' => [
      // ... outros plugins
      'MultipleLocalAuth' => [
          'namespace' => 'MultipleLocalAuth',
      ],
  ],
  ```
  _Nota: A linha acima ficará dentro do array de configurações do arquivo ```config.php``` que estamos editando neste momento._
  
  ### 2- Configurar MultipleLocalAuth como seu Provider de autenticação
  
  ##### Procure a linha com o código 
  
  ```'auth.provider' => 'Fake'```, 
  
  #### comente e adicione uma nova ou altere para 
  
  ```'auth.provider' => '\MultipleLocalAuth\Provider'```
  
  ### 3- Configurar as chaves das redes sociais
  
  ##### Adicione a configuração auth.config para definir as estratégias utilizadas e as chaves dos serviços:
  
  ```
  'auth.config' => array(
    'salt' => 'LT_SECURITY_SALT_SECURITY_SALT_SECURITY_SALT_SECURITY_SALT_SECU',
     'timeout' => '24 hours',
      'strategies' => [
         'Facebook' => array(
           'app_id' => 'SUA_APP_ID',
           'app_secret' => 'SUA_APP_SECRET', 
           'scope' => 'email'
         ),

        'LinkedIn' => array(
          'api_key' => 'SUA_API_KEY',
          'secret_key' => 'SUA_SECRET_KEY',
          'redirect_uri' => URL_DO_SEU_SITE . '/autenticacao/linkedin/oauth2callback',
          'scope' => 'r_emailaddress'
        ),
        'Google' => array(
          'client_id' => 'SEU_CLIENT_ID',
          'client_secret' => 'SEU_CLIENT_SECRET',
          'redirect_uri' => URL_DO_SEU_SITE . '/autenticacao/google/oauth2callback',
          'scope' => 'email'
        ),
        'Twitter' => array(
            'app_id' => 'SUA_APP_ID', 
            'app_secret' => 'SUA_APP_SECRET', 
        ),

      ]
  ),
  ```
  
##### **Nota:**
  1. _Em nosso projeto utilizamos apenas a autenticação **Padrão** e **Google**;_
  2. _Para obter as chaves para a estratégia **"Google"**, consulte [Google Console Developers](https://console.developers.google.com/apis/credentials), após gerar as chaves basta adiciona-las na configurações citadas acima;_
  3. _Para obter as chaves para a estratégia **"Facebook"**, consulte [aqui](http://facebook.com), após gerar as chaves basta adiciona-las na configurações citadas acima;_
  4. _Para obter as chaves para a estratégia **"LinkedIn"**, consulte [aqui](http://linkedin.com), após gerar as chaves basta adiciona-las na configurações citadas acima;_
  5. _Para obter as chaves para a estratégia **"Twitter"**, consulte [aqui](http://twitter.com), após gerar as chaves basta adiciona-las na configurações citadas acima._

  
  
  ##### Prontinho!! Após o procedimento você deverá ser capaz de acessar a página de autenticação utilizando MultipleLocalAuth

  
_Autor: [Rafael Chaves](https://github.com/rafaelchavesfreitas/) & [Wiusmarques Moreira](https://github.com/wiusmarques/) & [Edson Gellert Schubert](https://github.com/edsongs/)_
