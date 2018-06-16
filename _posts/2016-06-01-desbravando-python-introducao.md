---
# Posts need to have the `post` layout
layout: post

# The title of your post
title: Desbravando Python - Introdução

# (Optional) Write a short (~150 characters) description of each blog post.
# This description is used to preview the page on search engines, social media, etc.
# description: >

# (Optional) Link to an image that represents your blog post.
# The aspect ratio should be ~16:9.
image: /assets/img/default.jpg

comments: true

# You can hide the description and/or image from the output
# (only visible to search engines) by setting:
# hide_description: true
# hide_image: true

# (Optional) Each post can have zero or more categories, and zero or more tags.
# The difference is that categories will be part of the URL, while tags will not.
# E.g. the URL of this post is <site.baseurl>/hydejack/2017/11/23/example-content/
categories: [python]
tags: [python, builtins, desenvolvimento]
# If you want a category or tag to have its own page,
# check out `_featured_categories` and `_featured_tags` respectively.
---

Nesse post vamos introduzir uma nova idéia chamado de **Desbravando Python**, que tem o objetivo de entrar na documentação Python, e abordar de forma simples os seus recursos. Vamos focar na [versão 3.5](https://docs.python.org/3.5) da linguagem, então se até lá tiver melhorias e novas funcionalidades, saiba que não entrará enquanto conseguir passar por toda a [referência da biblioteca](https://docs.python.org/3/library/index.html) que oferece. Vai ser um caminho longo, mas vai ser bem interessante ;)

Poderia partir somente para o básico da ferramenta, mas como uma das destacadas características do Python é suas baterias incluídas, nada mais interessante que aprofundar e entender todas elas (ou pelo menos a maioria rs). Abaixo segue a sequência de posts que compõem a série. Essa ordem foi baseado no índice da documentação, em cada item e/ou subitem, será um post único.

- Funções Built-in
  - [Parte 01]({% post_url 2016-06-02-desbravando-python-funcoes-builtin-parte-01 %})
  - [Parte 02]({% post_url 2016-06-12-desbravando-python-funcoes-builtin-parte-02 %})
  - Parte 03
  - Parte 04
  - Parte 05
- Constantes Built-in
- Tipos Built-in
  - Testando valores verdadeiros
  - Operações booleanas - _and_, _or_, _not_
  - Comparações
  - Tipos numéricos
  - Tipos iteradores
  - Tipos sequenciais
  - Tipos de cadeias de caracteres
  - Tipos de sequências binárias
  - Tipos de set
  - Tipos de mapeamento
  - Tipos de gerenciamento de contexto
  - Outros tipos built-in
  - Atributos especiais
- Exceções built-in
  - Classes base
  - Exceções concretas
  - Avisos
  - Hierarquia de exceções
- Serviços de processamento de textos
  - _string_ - Operações comuns de string
  - _re_ - Operações com expressões regulares
  - _difflib_ - Auxiliares para computing deltas
  - _textwrap_ - Quebras de texto e preenchimento
  - _unicodedata_ - Banco de dados unicode
  - _stringprep_ - Preparação de string unicode para Internet
  - _readline_ - Interface de linha de comando
  - _rlcompleter_ - Função de auto-complete para readline
- Serviços e dados binários
  - _struct_ - Tratando valores Python e structs da linguagem C
  - _codecs_ - Registro de codecs e classes base
- Tipos de dados
  - _datetime_ - Tipos de data e hora básicos
  - _calendar_ - Funções gerais de calendários
  - _collections_ - Módulo especializado para tipos de dados como _dict_, _list_, _set_ e _tuple_.
  - _collections.abc_ - Classes abstratas para containers de dados para _collections_
  - _heapq_ - Módulo para aplicação do algoritmo de _heap queue_
  - _bisect_ - Módulo para aplicação do algoritmo _array bisection_
  - _array_ - Módulo para tratamento eficiente de arrays de valores numéricos
  - _weakref_ - Referência weak
  - _types_ - Criação de tipos dinamicos e nomes para tipos built-in
  - _copy_ - Operações de _deep copy_ e _shallow_
  - _pprint_ - Impressão organizada de dados
  - _reprlib_ - Implementação alternativa do _repr()_
  - _enum_ - Suporte para enumerations
- Módulos matemáticos e numéricos
  - _numbers_ - Base de classes abstratos para outras operações numéricas
  - _math_ - Funções matemáticas
  - _cmath_ - Funções matemáticas para número complexos
  - _decimal_ - Módulo para suporte de números decimais e flutuantes
  - _fractions_ - Números racionais
  - _random_ - Geração de números pseudo-aleatórios
  - _statistics_ - Funções matemáticas para estatistica
- Módulos para programação funcional
  - _itertools_ - Funções para criação de iteradores e loopings eficientes
  - _functools_ - Funções de primeira ordem e operações de objetos _callable_
  - _operator_ - Operações padrões como funções
- Acesso de arquivos e diretórios
  - _pathlib_ - Caminho do sistema de arquivos orientado a objetos
  - _os.path_ - Manipulações comuns de caminho de diretórios
  - _fileinput_ - Iteração de linhas sobre multiplos _input streams_
  - _stat_ - Interpretando resultados do _stat()_
  - _filecmp_ - Comparações de arquivos e diretórios
  - _tempfile_ - Gerando arquivos e diretórios temporários
  - _glob_ - Estilo Unix de padrões para caminho de diretórios
  - _fnmatch_ - Padrões de comparação para nome de arquivos Unix
  - _linecache_ - Acesso randômico de linhas de textos
  - _shutil_ - Operação de arquivos de alto nível
  - _macpath_ - Funções de manipulação de caminhos para Mac OS 9
- Persistência de dados
  - _pickle_ - Serialização de objetos Python
  - _copyreg_ - Funções para suporte de registros _pickle_
  - _shelve_ - Persistência de objetos Python
  - _marshal_ - Serialização de objetos internos do Python
  - _dbm_ - Interface para "base de dados" Unix
  - _sqlite3_ - Interface DB-API 2.0 para banco de dados SQLite
- Arquivamento e compressão de dados
  - _zlib_ - Compressão compatível com _gzip_
  - _gzip_ - Suporte para arquivos _gzip_
  - _bz2_ - Suporte para compressão _bzip2_
  - _lzma_ - Compressão usando algoritmo _LZMA_
  - _zipfile_ - Trabalhando com arquivos _ZIP_
  - _tarfile_ - Leitura e escrita de arquivos _tar_
- Formatos de arquivos
  - _csv_ - Leitura e escrita de arquivos _CSV_
  - _configparser_ - Parseamento de arquivos de configuração
  - _netrc_ - Processamento de arquivos _netrc_
  - _xdrlib_ - Codificação e Decodificação de dados XDR
  - _plistlib_ - Geração e parseamento de arquivos _.plist_ para Mac OS X
- Serviços de criptografia
  - _hashlib_ - Tratamento de mensagens e hashes seguras
  - _hmac_ - _Hashing_ chaveada para mensagens de autenticação
- Serviços genéricos de Sistemas Operacionais
  - _os_ - Interface diversos do Sistema Operacional
  - _io_ - Ferramentas base para trabalho com _streams_
  - _time_ - Acesso a tempo e conversões
  - _argparse_ - Parseamento de opções e linha de comando, argumentos e subcomandos
  - _getopt_ - Parseamento no estilo C para opções de linha de comando
  - _logging_ - Facilitador de logging para Python
  - _logging.config_ - Configuração de logging
  - _logging.handlers_ - Manipulação de logging
  - _getpass_ - Entrada de senha portável
  - _curses_ - Manipulação de caracteres para displays
  - _curses.textpad_ - Widget de entrada de texto para programas cursivos
  - _curses.ascii_ - Pilha de painel de extensão para _curses_
  - _platform_ - Acesso aos dados da plataforma
  - _errno_ - Padrão de símbolos de sistema _errno_
  - _ctypes_ - Biblioteca de funções externas para Python
- Execução concorrente
  - _threading_ - Paralelismo baseado em _thread_
  - _multiprocessing_ - Paralelismo baseado em processo
  - O pacote _concurrent_
  - _concurrent.futures_ - Executando tarefas paralelas
  - _subprocess_ - Gerenciamento de subprocessos
  - _sched_ - Agendamento de eventos
  - _queue_ - Classe de file sincronizada
  - _dummy-threading_ - Substituto para o módulo _threading_
  - _thread_ - API baixo nível para _threading_
  - _dummy-thread_ - Substituto para o módulo _thread_
- Processo de comunicação e rede
  - _socket_ - Interface de rede de baixo nível
  - _ssl_ - Wrapper TLS/SSL para objetos socket
  - _select_ - Módulo par multiplexing de I/O de alto nível
  - _selectors_ - Multiplexing de I/O de alto nível
  - _asyncio_ - I/O assíncrono, loop de eventos, _coroutines_ e tarefas
  - _asynchat_ - Comandos de socket assíncrono para handler de comando/resposta
  - _signal_ - Definição de manipulador para eventos assíncronos
  - _nmap_ - Suporte de arquivo _memory-mapped_
- Manipulação de dados da Internet
  - _email_ - Pacote para manipulação _MIME_ e e-mail
  - _json_ - Codificação e decodificação _JSON_
  - _mailcap_ - Manipulação de arquivos mailcap
  - _mailbox_ - Manipulação no mailboxes em vários formatos
  - _mimetypes_ - Mapeamento de nome de arquivos para tipos _MIME_
  - _base64_ - Codificando dados de Base16, Base32, Base64, Base85
  - _binhex_ - Codificação e decodificação de arquivos _binhex4_
  - _binascii_ - Conversão entre ASCII e dados binários
  - _quopri_ - Codificação e decodificação de dados de extensão de e-mail
  - _uu_ - Codificação e decodificação e arquivos _uuencode_
- Ferramentas de processamento de marcação estruturada (_Structured Markup)_
  - _html_ - Suporte a linguagem _HTML_
  - _html.parser_ - Parseamento de HTML e XHTML
  - _html.entities_ - Definições de entidades gerais do HTML
- Módulos de processamento XML
  - _xml.etree.ElementTree_ - API do _ElementTree XML_
  - _xml.dom_ - API do _Document Object Model (DOM)_
  - _xml.dom.minidom_ - Implementação mínima do DOM
  - _xml.dom.pulldom_ - Suporte para gerar árvore parcial do _DOM_
  - _xml.sax_ - Suporte para parseamento do _SAX2_
  - _xml.sax.handler_ - Classes base para manipulação do _SAX_
  - _xml.sax.saxutils_ - Utilitários _SAX_
  - _xml.sax.xmlreader_ - Interface para parsers XML
  - _xml.parsers.expat_ - Parseamento rápido de XML usando _Expat_
- Protocolo para Internet e Suporte
  - _webbrowser_ - Conveniente controlador web-browser
  - _cgi_ - Suporte para _Common Gateway Interface (CGI)_
  - _cgitb_ - Gerenciador de _Traceback_ para scripgs _CGI_
  - _wsgiref_ - Implementação e utilitários da referência _WSGI_
  - _urllib_ - Módulo para manipulação de URLs
  - _urllib.request_ - Biblioteca para abertura de URLs
  - _urllib.response_ - Classe de resposta usado pelo _urllib_
  - _urllib.parse_ - Parseamento de URLs dentro de componentes
  - _urllib.error_ - Classes de exceção disparados pelo _urllib.request_
  - _urllib.robotparser_ - Parseamento para _robot.txt_
  - _http_ - Módulo HTTP
  - _http.client_ - Cliente para protocolo _HTTP_
  - _ftplib_ - Cliente do protocolo _FTP_
  - _poplib_ - Cliente do protocolo _POP3_
  - _imaplib_ - Cliente do protocolo _IMAP4_
  - _nntplib_ - Cliente do protocolo _NNTP_
  - _smtplib_ - Cliente do protocolo _SMTP_
  - _smtpd_ - Servidor _SMTP_
  - _telnetlib_ - Cliente Telnet
  - _uuid_ - Objetos UUID de acordo com o _RFC 4122_
  - _socketserver_ - Um framework para servidores de rede
  - _http.server_ - Servidores _HTTP_
  - _http.cookies_ - Gerenciamento do estado _HTTP_
  - _http.cookiejar_ - Manipulação de cookies para clientes HTTP
  - _xmlrpc_ - Servidor _XMLRPC_ e módulos cliente
  - _xmlrpc.client_ - Acesso do cliente _XML-RPC_
  - _xmlrpc.server_ - Servidores _XML-RPC_ básicos
  - _ipaddress_ - Biblioteca de manipulação _IPv4/IPv6_
- Serviços de multimídia
  - _audioop_ - Manipulação de dados de áudio _raw_
  - _aifc_ - Leitura e escrita de arquivos _AIFF_ e _AIFC_
  - _sunau_ - Leitura e escrita de arquivos _Sun AU_
  - _wave_ - Leitura e escrita de arquivos _WAV_
  - _chunk_ - Leitura de dados do _IFF chunked_
  - _colorsys_ - Conversões entre sistemas de cores
  - _imghdr_ - Determinação do tipo de imagem
  - _sndhdr_ - Determina o tipo de som
  - _ossaudiodev_ - Acesso de dispositivos de áudio OSS compatíveis
- Internacionalização
  - _gettext_ - Serviços de Internacionalização multilíngue
  - _locale_ - Serviços de Internacionalização
- Frameworks de Programas
  - _turtle_ - Gráficos _Turtle_
  - _cmd_ - Suporte a interpretador de comando orientados a linha
  - _shlex_ - Análise léxica simples
  - Interface gráfica do utilizador com Tk
  - _tkinter_ - Interface python com Tcl/Tk
  - _tkinter.ttk_ - Widgets tematizados para Tk
  - _tkinger.tix_ - Extensão de widgets para Tk
  - _tkinter.scrolledtext_ - Widget de texto com scroll
  - _IDLE_ - Ambiente integrado de desenvolvimento Python para Aprendizado
  - Outros pacotes de interface gráfica do utilizador
- Ferramentas de desenvolvimento
  - _typing_ - Suporte para _type hints_
  - _pydoc_ - Gerador de documentação and sistema de ajuda online
  - _doctest_ - Exemplos de testes interativos para Python
  - _unittest_ - Framework de testes unitários
  - _unittest.mock_ - Biblioteca de objetos "mocados"
  - _unittest.mock_ - Primeiros passos
  - _2to3_ - Tradução do código do Python 2 para 3 automático
  - _test_ - Pacote de testes de regressão para Python
  - _test.support_ - Utilitários para ambiente de teste Python
  - Debugging e Profiling
  - _bdb_ - Framework debugger
  - _faulthandler_ - Descarregamento de traceback
  - _pdb_ - Debugger Python
  - Profilers python
  - _timeit_ - Mensuração do tempo de execução de pequenos pedaços de código
  - _trace_ - Módulo que permite rastrear execução do programa
  - _tracemalloc_ - Módulo que permite rastrear blocos de memória alocados pelo Python
- Distribuição e empacotamento do software
  - _distutils_ - Construindo e instalando módulos Python
  - _ensurepip_ - Inicialização o instalador _pip_
  - _venv_ - Criação de ambientes virtualizados
  - _zipapp_ - Módulo que disponibiliza ferramenta para criacao de arquivo zip com código Python
- Serviços de execução Python
  - _sys_ - Funções e parametros específicos do sistemas
  - _sysconfig_ - Provêm acesso acesso a informações de configuração do Python
  - _builtins_ - Objetos built-in
  - _--main--_ - Ambiente de script alto nível
  - _warnings_ - Controle de alertas
  - _contextlib_ - Utilitários para declaração de contexto usando _with_
  - _abc_ - Base de classes abstratas
  - _atexit_ - Manipulação de saída
  - _traceback_ - Imprimir ou recuperar _stack traceback_
  - _--future--_ - Definições de declarações futuras
  - _gc_ - Interface para _Garbage Collector_
  - _inspect_ - Inspeção de objetos vivos
  - _site_ - Configurações específica de pesquisa de módulos locais
  - _fpectl_ - Controle de exceção de pontos flutuantes
- Interpretadores customizados Python
  - _code_ - Classe base do interpretador
  - _codeop_ - Código python do compilador
- Importando módulos
  - _zipimport_ - Importando módulos de arquivos _ZIP_
  - _pkgutil_ - Extensão de pacote utilitário
  - _modulefinder_ - Procura de módulos usados pelo script
  - _runpy_ - Localizando e executando módulos Python
  - _importlib_ - A implementação do _import_
- Serviços da linguagem Python
  - _parser_ - Acesso python para _parse tree_
  - _ast_ - _Syntax tree_ abstrato
  - _symtable_ - Acesso a tabela de símbolos do compilador
  - _symbol_ - Constantes usados com _parse trees_ no Python
  - _token_ - Constantes usados com _parse trees_ no Python
  - _keyword_ - Testes para palavras-chave do Python
  - _tokenize_ - Tokenizador para código-fonte Python
  - _tabnanny_ - Detecção de endentação ambígua
  - _pyclbr_ - Classe python de suporte a a informações sobre classes, métodos a funções de alto nível
  - _py-compile_ - Compilação do código fonte Python
  - _compileall_ - Biblioteca de compilação binária com Python
  - _dis_ - _Disassembler_ para bytecodes Python
  - _pickletools_ - Ferramenta para desenvolvedores _pickle_
- Serviços diversos
  - _formatter_ - Formatando saídas genéricas
- Serviços específicos para MS Windows
  - _msilib_ - Leitura e escrita de arquivos _Microsoft Installer_
  - _msvcrt_ - Rotinas úteis para _MS VC++ runtime_
  - _winreg_ - Acesso aos registros Windows
  - _winsound_ - Interface de som para Windows
- Serviços especifícos Unix
  - _posix_ - Chamadas de sistema mais comuns para o padrão _POSIX_
  - _pwd_ - Banco de dados de senhas
  - _spwd_ - Banco de dados de senhas no estilo _shadow_
  - _grp_ - Banco de dados de grupos
  - _crypt_ - Função para chacagem de senhas Unix
  - _termios_ - Controle de _tty_ no estilo _POSIX_
  - _tty_ - Funções para controle de terminal
  - _pty_ - Utilitários para pseudo-terminais
  - _fcntl_ - Chamadas de sistemas de _fcntl_ e _ioctl_
  - _pipes_ - Interface para _shell pipelines_
  - _resource_ - Informações de uso de recursos
  - _nis_ - Interface para _Sun's NIS (Yellow Pages)_
  - _syslog_ - Biblioteca de rotinas para acesso ao _syslog Unix_
- Módulos substituídos
  - _optparse_ - Parser de opções para linhas de comando
  - _imp_ - Acesso interno ao _import_

Depois desse índice **gigantesco**, pode parecer que sou louco. Mas como falei, é uma grande aventura, e o nome da série não foi pensado atoa. Assim, espero que consiga completar essa jornada, mas para isso um desafio de cada vez ;)

Até o próximo post sobre [Funções Built-in: Parte 01]({filename}/2016-06-02-desbravando-python-funcoes-builtin-parte-01.md).
