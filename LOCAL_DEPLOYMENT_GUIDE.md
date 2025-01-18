# Local Deployment Guide: Snowflake RAG Application

This guide explains the technical changes made to deploy the Snowflake RAG (Retrieval Augmented Generation) application locally. We'll go through each change in detail, explaining why it was made and how it works.

## 1. Connection Management Changes

### Original Connection Method
```python
# Old code
session = get_active_session()
root = Root(session)
```

This method worked in Snowflake's environment but wasn't suitable for local deployment because:
- `get_active_session()` is designed for Snowflake's internal environment
- It doesn't handle credentials explicitly
- No error handling for connection failures

### New Connection Method
```python
@st.cache_resource
def init_snowflake_connection():
    try:
        # Get connection parameters from secrets
        snowflake_config = st.secrets["connections"]["snowflake"]
        
        # Create Snowpark session
        session = Session.builder.configs({
            "account": snowflake_config["account"],
            "user": snowflake_config["user"],
            "password": snowflake_config["password"],
            "role": snowflake_config["role"],
            "warehouse": snowflake_config["warehouse"],
            "database": snowflake_config["database"],
            "schema": snowflake_config["schema"]
        }).create()
        
        root = Root(session)
        svc = root.databases[CORTEX_SEARCH_DATABASE].schemas[CORTEX_SEARCH_SCHEMA].cortex_search_services[CORTEX_SEARCH_SERVICE]
        return session, root, svc
    except Exception as e:
        st.error(f"Failed to connect to Snowflake: {str(e)}")
        st.error("Please check your credentials in .streamlit/secrets.toml")
        return None, None, None
```

Key improvements:
1. `@st.cache_resource` decorator:
   - Caches the connection to avoid creating multiple sessions
   - Improves performance by reusing the connection
   - Automatically handles session lifecycle

2. Explicit configuration:
   - Uses Snowpark's Session builder
   - All connection parameters are explicitly defined
   - Credentials are securely loaded from secrets.toml

3. Error handling:
   - Try-catch block catches connection issues
   - User-friendly error messages
   - Graceful failure with None returns

## 2. Secrets Management

### secrets.toml Structure
```toml
[connections.snowflake]
account = "rnb33584"    # Just the account identifier
user = "YourUsername"
password = "YourPassword"
role = "ACCOUNTADMIN"
warehouse = "COMPUTE_WH"
database = "CC_QUICKSTART_CORTEX_SEARCH_DOCS"
schema = "DATA"
```

Important points:
- Account identifier format: Use only the account part (e.g., "rnb33584"), not the full URL
- Placed in `.streamlit/secrets.toml` for Streamlit's secret management
- Gitignored to prevent credential exposure

## 3. Error Handling Improvements

### Function-Level Error Handling
```python
def get_similar_chunks_search_service(query):
    if not svc:
        st.error("Snowflake connection not available")
        return []

    try:
        if st.session_state.category_value == "ALL":
            response = svc.search(query, COLUMNS, limit=NUM_CHUNKS)
        else: 
            filter_obj = {"@eq": {"category": st.session_state.category_value} }
            response = svc.search(query, COLUMNS, filter=filter_obj, limit=NUM_CHUNKS)

        st.sidebar.json(response.json())
        return response.json()
    except Exception as e:
        st.error(f"Search failed: {str(e)}")
        return []
```

Key features:
1. Connection validation:
   - Checks if service is available before operations
   - Returns empty results instead of crashing

2. Operation-specific error handling:
   - Catches search-specific exceptions
   - Provides context-aware error messages
   - Returns safe default values

## 4. UI Improvements for Local Deployment

### Main Function Structure
```python
def main():
    if not session:
        st.error("Failed to connect to Snowflake. Please check your credentials and try again.")
        return
        
    st.title(f":speech_balloon: Chat Document Assistant with Snowflake Cortex")
    
    try:
        st.write("This is the list of documents you already have and that will be used to answer your questions:")
        docs_available = session.sql("ls @docs").collect()
        list_docs = []
        for doc in docs_available:
            list_docs.append(doc["name"])
        st.dataframe(list_docs)
    except Exception as e:
        st.error(f"Failed to fetch document list: {str(e)}")
        st.write("No documents available or error accessing document store")
```

Improvements:
1. Early validation:
   - Checks connection before rendering UI
   - Clear error message for connection issues

2. Graceful degradation:
   - Each feature handles its own errors
   - UI remains functional even if some features fail

## 5. Dependencies Management

### Updated requirements.txt
```text
snowflake-core==0.9.0
snowflake-connector-python>=2.8.0
snowflake-snowpark-python<2.0.0
streamlit>=1.28.0
pandas>=1.3.0
```

Key changes:
- Updated Streamlit version for better compatibility
- Specified version ranges for flexibility
- Maintained compatibility with Snowflake packages

## Common Issues and Solutions

1. **Account Identifier Format**
   - Problem: Using full URL (e.g., "rnb33584.snowflakecomputing.com")
   - Solution: Use only the account part (e.g., "rnb33584")

2. **Connection Errors**
   - Problem: No error messages in original code
   - Solution: Added comprehensive error handling and messages

3. **Session Management**
   - Problem: Single point of failure with get_active_session()
   - Solution: Cached, configurable session with error handling

4. **Feature Availability**
   - Problem: Features failing silently
   - Solution: Graceful degradation with informative messages

## Best Practices Implemented

1. **Security**
   - Credentials in secrets.toml
   - File properly gitignored
   - No hardcoded sensitive information

2. **Error Handling**
   - Comprehensive try-catch blocks
   - User-friendly error messages
   - Safe default returns

3. **Performance**
   - Connection caching
   - Efficient session reuse
   - Proper resource cleanup

4. **Code Organization**
   - Modular functions
   - Clear separation of concerns
   - Consistent error handling patterns

## Testing Your Local Deployment

1. Check your secrets.toml configuration
2. Ensure proper account identifier format
3. Verify Snowflake permissions
4. Test each feature individually
5. Monitor error messages in the UI

Remember: Local deployment allows for faster development and testing, but maintain security best practices, especially regarding credential management.
