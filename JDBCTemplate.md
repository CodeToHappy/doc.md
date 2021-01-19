# JDBCTemplate

## JDBCTemplate类

### 继承JdbcAccessor

### 实现JDBCOperation

## 变量

### RETURN_RESULT_SET_PREFIX

### RETURN_UPDATE_COUNT_PREFIX

### nativeJdbcExtractor

- setNativeJdbcExtractor
- getNativeJdbcExtractor

### ignoreWarnings

- setIgnoreWarnings
- isIgnoreWarnings

### fetchSize

- setFetchSize
- getFetchSize

### maxRows

- setMaxRows
- getMaxRows

### queryTimeout

- setQueryTimeout
- getQueryTimeout

### skipResultsProcessing

- setSkipResultsProcessing
- getSkipResultsProcessing

### skipUndeclaredResults

- isSkipResultsProcessing
- setSkipUndeclaredResults

### resultsMapCaseInsensitive

- setResultsMapCaseInsensitive
- isResultsMapCaseInsensitive

## 构造一个JDBCTemplate

### JdbcTemplate() {}

### JdbcTemplate(DataSource dataSource) {}

### JdbcTemplate(DataSource dataSource, boolean lazyInit) {}

## 建立简单的java.sql.connection连接

### public <T> T execute(ConnectionCallback<T> action) throws DataAccessException {}

### protected Connection createConnectionProxy(Connection con) {}

## 处理静态SQL的方法

### public <T> T execute(StatementCallback<T> action) throws DataAccessException {}

### public void execute(final String sql) throws DataAccessException {}

### public <T> T query(final String sql, final ResultSetExtractor<T> rse) throws DataAccessException{}

### public void query(String sql, RowCallbackHandler rch) throws DataAccessException{}

### public <T> List<T> query(String sql, RowMapper<T> rowMapper) throws DataAccessException{}

### public Map<String, Object> queryForMap(String sql) throws DataAccessException{}

### public <T> T queryForObject(String sql, RowMapper<T> rowMapper) throws DataAccessException{}


### public <T> T queryForObject(String sql, Class<T> requiredType) throws DataAccessException{}


### public <T> List<T> queryForList(String sql, Class<T> elementType) throws DataAccessException{}


### public List<Map<String, Object>> queryForList(String sql) throws DataAccessException{}

### public SqlRowSet queryForRowSet(String sql) throws DataAccessException{}

### public int update(final String sql) throws DataAccessException{}

### public int[] batchUpdate(final String... sql) throws DataAccessException{}

## 预处理语句的方法

### public <T> T execute(PreparedStatementCreator psc, PreparedStatementCallback<T> action) throws DataAccessException{}

### public <T> T execute(String sql, PreparedStatementCallback<T> action) throws DataAccessException{}

### public <T> T query(PreparedStatementCreator psc, final PreparedStatementSetter pss, final ResultSetExtractor<T> rse) throws DataAccessException{}

### public <T> T query(PreparedStatementCreator psc, ResultSetExtractor<T> rse) throws DataAccessException{}

### public <T> T query(String sql, PreparedStatementSetter pss, ResultSetExtractor<T> rse) throws DataAccessException{}

### public <T> T query(String sql, Object[] args, int[] argTypes, ResultSetExtractor<T> rse) throws DataAccessException{}

### public <T> T query(String sql, Object[] args, ResultSetExtractor<T> rse) throws DataAccessException{}


### public <T> T query(String sql, ResultSetExtractor<T> rse, Object... args) throws DataAccessException{}


### public void query(PreparedStatementCreator psc, RowCallbackHandler rch) throws DataAccessException{}

### public void query(String sql, PreparedStatementSetter pss, RowCallbackHandler rch) throws DataAccessException{}

### public void query(String sql, Object[] args, int[] argTypes, RowCallbackHandler rch) throws DataAccessException{}

### public void query(String sql, Object[] args, RowCallbackHandler rch) throws DataAccessException{}

### public void query(String sql, RowCallbackHandler rch, Object... args) throws DataAccessException{}

### public <T> List<T> query(PreparedStatementCreator psc, RowMapper<T> rowMapper) throws DataAccessException{}


### public <T> List<T> query(String sql, PreparedStatementSetter pss, RowMapper<T> rowMapper) throws DataAccessException{}

### public <T> List<T> query(String sql, Object[] args, int[] argTypes, RowMapper<T> rowMapper) throws DataAccessException{}

### public <T> List<T> query(String sql, Object[] args, RowMapper<T> rowMapper) throws DataAccessException{}

### public <T> List<T> query(String sql, RowMapper<T> rowMapper, Object... args) throws DataAccessException{}

### public <T> T queryForObject(String sql, Object[] args, int[] argTypes, RowMapper<T> rowMapper) throws DataAccessException{}

### public <T> T queryForObject(String sql, Object[] args, RowMapper<T> rowMapper) throws DataAccessException{}

### public <T> T queryForObject(String sql, RowMapper<T> rowMapper, Object... args) throws DataAccessException{}

### public <T> T queryForObject(String sql, Object[] args, int[] argTypes, Class<T> requiredType) throws DataAccessException{}


### public <T> T queryForObject(String sql, Object[] args, Class<T> requiredType) throws DataAccessException{}

### public <T> T queryForObject(String sql, Class<T> requiredType, Object... args) throws DataAccessException{}

### public Map<String, Object> queryForMap(String sql, Object[] args, int[] argTypes) throws DataAccessException{}

### public Map<String, Object> queryForMap(String sql, Object... args) throws DataAccessException{}


### public <T> List<T> queryForList(String sql, Object[] args, int[] argTypes, Class<T> elementType) throws DataAccessException{}

### public <T> List<T> queryForList(String sql, Object[] args, Class<T> elementType) throws DataAccessException{]


### public <T> List<T> queryForList(String sql, Class<T> elementType, Object... args) throws DataAccessException{}


### public List<Map<String, Object>> queryForList(String sql, Object[] args, int[] argTypes) throws DataAccessException{}

### public List<Map<String, Object>> queryForList(String sql, Object... args) throws DataAccessException{}

### public SqlRowSet queryForRowSet(String sql, Object[] args, int[] argTypes) throws DataAccessException{}

### public SqlRowSet queryForRowSet(String sql, Object... args) throws DataAccessException{}

### protected int update(final PreparedStatementCreator psc, final PreparedStatementSetter pss) throws DataAccessException{}

### public int update(PreparedStatementCreator psc) throws DataAccessException{}

### public int update(final PreparedStatementCreator psc, final KeyHolder generatedKeyHolder) throws DataAccessException{}

### public int update(String sql, PreparedStatementSetter pss) throws DataAccessException{}

### public int update(String sql, Object[] args, int[] argTypes) throws DataAccessException{}

### public int update(String sql, Object... args) throws DataAccessException{}

### public int[] batchUpdate(String sql, final BatchPreparedStatementSetter pss) throws DataAccessException{}

### public int[] batchUpdate(String sql, List<Object[]> batchArgs) throws DataAccessException{}

### public int[] batchUpdate(String sql, List<Object[]> batchArgs, int[] argTypes) throws DataAccessException{}

### public <T> int[][] batchUpdate(String sql, final Collection<T> batchArgs, final int batchSize, final ParameterizedPreparedStatementSetter<T> pss) throws DataAccessException{}

## 处理可调用语句

### public <T> T execute(CallableStatementCreator csc, CallableStatementCallback<T> action) throws DataAccessException{}

### public <T> T execute(String callString, CallableStatementCallback<T> action) throws DataAccessException{}

### public Map<String, Object> call(CallableStatementCreator csc, List<SqlParameter> declaredParameters) throws DataAccessException{}

### protected Map<String, Object> extractReturnedResults(CallableStatement cs, List<SqlParameter> updateCountParameters, List<SqlParameter> resultSetParameters, int updateCount) throws SQLException{}

### protected Map<String, Object> extractOutputParameters(CallableStatement cs, List<SqlParameter> parameters) throws SQLException{}

### protected Map<String, Object> processResultSet(ResultSet rs, ResultSetSupportingSqlParameter param) throws SQLException{}

## 实现挂钩和辅助方法

### protected RowMapper<Map<String, Object>> getColumnMapRowMapper(){}

### protected <T> RowMapper<T> getSingleColumnRowMapper(Class<T> requiredType){}

### protected Map<String, Object> createResultsMap(){}

### protected void applyStatementSettings(Statement stmt) throws SQLException{}

### protected PreparedStatementSetter newArgPreparedStatementSetter(Object[] args)

### protected PreparedStatementSetter newArgTypePreparedStatementSetter(Object[] args, int[] argTypes){}

### protected void handleWarnings(Statement stmt) throws SQLException{}

### protected void handleWarnings(SQLWarning warning) throws SQLWarningException{}


### private static String getSql(Object sqlProvider){}

### private class CloseSuppressingInvocationHandler implements InvocationHandler{}

### private static class SimplePreparedStatementCreator implements PreparedStatementCreator, SqlProvider{}

### private static class SimpleCallableStatementCreator implements CallableStatementCreator, SqlProvider{}

### private static class RowCallbackHandlerResultSetExtractor implements ResultSetExtractor<Object>{}


