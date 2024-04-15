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
  }</code>

## Referências

<a href="https://pub.dev/packages/odbc">Página do ODBC no Pub.dev</a>

<a href="https://stackoverflow.com/questions/25750834/how-to-use-odbc-binding-for-dart">Tutorial de uso do ODBC do Dart</a>
