Servidor Swift Origin
=====================

    Direitos Autorais OpenStack 2012, LLC.

    Licenciado sob a Licença Apache, Versão 2.0 (a "Licença"), 
    você não pode usar esse arquivo exceto em conformidade com a Licença.
    Você pode obter uma cópia da Licença em

    http://www.apache.org/licenses/LICENSE-2.0

    A menos que exigido por lei aplicável ou concordado por escrito, 
    software distribuído sob a Licença é distribuído "COMO ESTÁ", SEM 
    GARANTIAS OU CONDIÇÕES DE QUALQUER TIPO, expressa ou implícita. 
    Consulte a Licença para as permissões de linguagem específicas que 
    regem e limitações sob a Licença.


.. toctree::
   :maxdepth: 2

   license


Visão Global
------------

Um Middleware WSGI que fornece acesso as pastas de arquivos de clientes que 
existe no Swift para uso por um provedor de serviço de CDN (Rede de Fornecimento de Conteúdo). 
Usa-se o Swift como seu backend.


Introdução
----------

Copie o arquivo etc/sos.conf-sample para /etc/swift/sos.conf e faça as alterações 
no seu proxy.conf como mostrado na etc/proxy-server.conf-sample.


Reinicie seu proxy-server:
``swift-init proxy reload``

Prepare o ambiente:
``swift-origin-prep -K password``


Quando usando a interface de gerenciamento do CDN, deve-se passar o nome do dominio que
foi configurado no parâmetro "origin_db_hosts", Ex: origin_db.com. Que uma pasta sirva 
para uso CDN, primeiro tenha que ativa-la. Se faz isso emitindo um PUT na pasta em questão, 
do mesmo modo que faria no swift, a diferença neste caso e que terá que passar o seguinte 
cabeçalho “Host: origin_db.com” durante o pedido. Depois que a pasta esteja ativada, poderá 
emitir uma HEAD contra a pasta e verá CDN URLs retornando como cabeçalhos.


Exemplo usando uma configuração SAIO

Exportando parametros:
``export AUTH_TOKEN=[AUTH_token]``
``export AUTH_USER=[AUTH_user]``

Criando uma pasta no swift:
``curl -i -H "X-Auth-Token: $AUTH_TOKEN" http://127.0.0.1:8080/v1/$AUTH_USER/pub -XPUT``

Fazer upload de um arquivo na pasta:
``curl -i -H "X-Auth-Token: $AUTH_TOKEN" http://127.0.0.1:8080/v1/$AUTH_USER/pub/file.html -XPUT -d '<html><b>It Works!!</b></html>'``

Ativando a pasta para uso CDN:
``curl -i -H "X-Auth-Token: $AUTH_TOKEN" http://127.0.0.1:8080/v1/$AUTH_USER/pub -XPUT -H 'Host: origin_db.com'``

Descobrindo as CDN URLs da pasta:
``curl -i -H "X-Auth-Token: $AUTH_TOKEN" http://127.0.0.1:8080/v1/$AUTH_USER/pub -XHEAD -H 'Host: origin_db.com'``

Emitindo um pedido origin:
``curl http://127.0.0.1:8080/file.html -H 'Host: c0cd095b4ec76c09a6549995abb62558.r56.origin_cdn.com'``


Configurando *Logging*:
-----------------------
Se você quiser adicionar *logging* separado para SOS no seu SAIO, precisara editar 
a sua configuração do rsyslogd e adicionar os seguintes detalhes 


#. Edite /etc/rsyslog.d/10-swift.conf::

    local6.*;local6.!notice /var/log/swift/sos.log
    local6.notice           /var/log/swift/sos.error
    local6.*                ~


Criando Pacotes
----------------

1. Usando *python-stdeb*: 
    Para construir pacotes ``sudo easy_install stdeb``

    Entre na pasta 'sos' e execute : ``python setup.py --command-packages=stdeb.command bdist_deb``

2. Usando *debuild*: 
    Primeiro instale ``apt-get install build-essential devscripts dh-make``

    Renomea a pasta sos como sos-VERSÃO  

    Crie um tarball original: ``tar --exclude=debian -zcvf sos-VERSÃO.orig.tar.gz``

    Entre na pasta sos-VERSÃO e execute ``debuild -us -uc``

    Isto irá criar o pacote sem assinar com sua chave GPG. Tenha em mente que é uma boa 
    idéia ter a sua chave GPG pronta para que possa ser usada durante a construção do pacotes.
    Pode queixar-se que você não tem as dependências necessárias instalada para prosseguir com 
    a construção, em caso afirmativo, instalá-los.

    Ref: ``http://wiki.debian.org/IntroDebianPackaging``


Testando
--------

Testes das Unidades são executados da pasta sos com o comando:
``./.unittests``


Testes Funcionais são executados da pasta sos com o comando:
``./.functests``

Os "Testes Funcionais" usam a mesma configuração, /etc/swift/func_test.conf, usada no Swift. 
Se for usar SOS com *Swift's staticweb middleware* adicione o seguinte no final do arquivo de configuração.

``sos_static_web = true``

Documentação gerada do código
-----------------------------

.. toctree::
    :maxdepth: 2

    sos
    sos_origin

Índices e tabelas
------------------

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
