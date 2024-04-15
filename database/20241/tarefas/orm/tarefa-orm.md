# ORM com Dart

### Resumo - ODBC com Dart

O Dart possui um pacote (um pouco defasado, já que a última versão tem 9 anos de idade e é incompatível com o Dart 3) para ODBC, contendo métodos para conectar ao banco (sqlConnect), preparar Statements (sqlPrepare) , e executar querys (sqlExecute), entre outras.
Os principais objetos no pacote são os SqlHandlers, e são usados tanto para conectar quanto para executar comandos SQL.
Para instanciar um SQLHandle, usamos <code>var hStmt = new SqlHandle()</code>. Logo depois precisamos preparar um statement que será armazenado dentro deste objeto, usando 

  <code> sqlAllocHandle(SQL_HANDLE_STMT, hConn, hStmt); 
  sqlPrepare(hStmt, "SELECT * FROM vendors", SQL_NTS); </code>

, onde hConn é um objeto SqlHandle já preparado com a conexão com o banco de dados (usa-se a função sqlConnect).
Cada coluna dos resultados obtidos deve ser instanciada usando Buffers, por exemplo:

  <code>var col1 = new SqlLongBuffer();
  var flags1 = new SqlIntBuffer();
  sqlBindCol(hStmt, 1, col1.ctype(), col1.address(), 0, flags1.address());</code>

O método sqlBindCol 'liga' o buffer da coluna com o sqlHandle, dessa forma, os buffers de coluna armazenarão os resultados. Para executar a query e mostrar os resultados basta fazer: 

  <code>sqlExecute(hStmt);

  while (sqlFetch(hStmt) != SQL_NO_DATA_FOUND) {

        print("${col1.peek()} ${col2.peek()} ${col3.peek()} ${col4.peek()} ");
  }
  </code>

## Resumo - ORM com Dart e Flutter

Para flutter, indica-se o uso do pacote ORM. Ele utiliza a engine do Prisma para fazer o tratamento de objetos vindos do banco de dados. Talvez seja importante ressaltar que o pacote é Open Source e não é ligado ao Prisma Data Inc., mas faz parte do ecossistema Prisma.
Para instalar o ORM no projeto escolhido, basta ter o Node.js instalado, e executar os comandos:

<code>npm install prisma</code>
<code>dart pub add orm</code>
<code>npx prisma init --generator-provider="dart run orm" --datasource-provider=postgresql</code>

Depois, basta configurar os arquivos .env e schema.prisma criados na raiz do projeto. O .env é o arquivo que armazena as variáveis de ambiente, como endereço do banco, user e password. Já o schema.prisma define os modelos que serão utilizados do Banco de Dados.
Depois de configurado, basta usarmos <code>npx prisma generate</code>. 

Esse comando criará os arquivos client.dart, prisma.dart e model.dart. O client.dart guarda as implementações do Prisma Client, o prisma.dart guarda os tipos que o Prisma gera automaticamente, enquanto o model.dart guarda os models, views e enums a serem utilizados.
O Prisma recomenda usar todos os recursos junto com try/catch/finally, para tratar quaisquer erros de conexão com o banco.

## Outras questões
### Questão 4
```
import 'package:odbc/odbc.dart';

void main() {
     var hEnv = new SqlHandle();
     sqlAllocHandle(SQL_HANDLE_ENV, null, hEnv);

     var version = new SqlPointer()..value = SQL_OV_ODBC3;
    sqlSetEnvAttr(hEnv, SQL_ATTR_ODBC_VERSION, version, 0);

    var hConn = new SqlHandle();
    sqlAllocHandle(SQL_HANDLE_DBC, hEnv, hConn);

    sqlConnect(hConn, "<DSN>", SQL_NTS, "<USER>", SQL_NTS, "<PASS>", SQL_NTS);

    var hStmt = new SqlHandle();

    sqlAllocHandle(SQL_HANDLE_STMT, hConn, hStmt);

    sqlPrepare(hStmt, "SELECT * FROM vendors", SQL_NTS);

    var col1 = new SqlLongBuffer();
    var flags1 = new SqlIntBuffer();
    sqlBindCol(hStmt, 1, col1.ctype(), col1.address(), 0, flags1.address());

    var col2 = new SqlLongBuffer();
    var flags2 = new SqlIntBuffer();
    sqlBindCol(hStmt, 2, col2.ctype(), col2.address(), 0, flags2.address());

    var col3 = new SqlStringBuffer(255);
    var flags3 = new SqlIntBuffer();
    sqlBindCol(hStmt, 3, col3.ctype(), col3.address(), col3.length(), flags3.address());

    var col4 = new SqlStringBuffer(255);
    var flags4 = new SqlIntBuffer();
    sqlBindCol(hStmt, 4, col4.ctype(), col4.address(), col4.length(), flags4.address());

    sqlExecute(hStmt);

    while (sqlFetch(hStmt) != SQL_NO_DATA_FOUND) {
          print("${col1.peek()} ${col2.peek()} ${col3.peek()} ${col4.peek()} ");
     }

   sqlFreeHandle(SQL_HANDLE_STMT, hStmt);

   sqlDisconnect(hConn);

   sqlFreeHandle(SQL_HANDLE_DBC, hConn);

   sqlFreeHandle(SQL_HANDLE_ENV, hEnv);
}
```
Obs.: Esse programa é antigo e não foi criado por mim. Está no tutorial do uso do ODBC para Dart. Não criei pois o pacote não é mais compatível com a versão atual do Dart.


## Referências

<a href="https://pub.dev/packages/odbc">Página do ODBC no Pub.dev</a>

<a href="https://stackoverflow.com/questions/25750834/how-to-use-odbc-binding-for-dart">Tutorial de uso do ODBC do Dart</a>

<a href="https://prisma.pub">Site e documentação do Prisma Client Dart</a>

<a href="https://pub.dev/packages/orm">Pàgina do ORM no Pub.dev</a>
