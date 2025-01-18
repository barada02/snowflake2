# Snowflake Document Assistant - Local Setup Guide

This guide will help you set up and run the Snowflake Document Assistant application locally.

## Prerequisites

- Python 3.8.x installed on your system
- Access to a Snowflake account with appropriate permissions
- Snowflake account credentials (account, username, password)
- Proper access to Snowflake Cortex Search service and document storage

## Installation Steps

1. **Environment Setup**
   ```bash
   # Create a virtual environment
   python -m venv venv

   # Activate virtual environment
   # On Windows:
   .\venv\Scripts\activate
   # On Unix or MacOS:
   source venv/bin/activate
   ```

2. **Install Dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Snowflake Configuration**
   Configure your Snowflake credentials in `.streamlit/secrets.toml`:
   ```toml
   [snowflake]
   account = "your-account-identifier"    # e.g., "xy12345.us-east-1"
   user = "your-username"
   password = "your-password"
   warehouse = "your-warehouse"
   role = "your-role"
   database = "CC_QUICKSTART_CORTEX_SEARCH_DOCS"
   schema = "DATA"
   ```

   > **Important**: Never commit `.streamlit/secrets.toml` to version control. Add it to your `.gitignore` file.

4. **Verify Snowflake Access**
   - Ensure you have access to the Cortex Search service (CC_SEARCH_SERVICE_CS)
   - Verify access to document storage (@docs)
   - Check if your role has necessary privileges for Cortex functions

## Running the Application

1. **Start the Application**
   ```bash
   streamlit run app.py
   ```

2. **Access the Application**
   - Open your web browser
   - Navigate to `http://localhost:8501`

## Deploying to Streamlit Community Cloud

When deploying to Streamlit Community Cloud:
1. Create a new app in Streamlit Community Cloud
2. Add your secrets in the Streamlit Cloud dashboard under "Advanced Settings > Secrets"
3. Copy the contents of your local `.streamlit/secrets.toml` into the secrets management interface

## Troubleshooting

1. **Connection Issues**
   - Verify your Snowflake credentials in `.streamlit/secrets.toml`
   - Check if your IP is whitelisted in Snowflake
   - Ensure VPN is connected if required by your organization

2. **Missing Dependencies**
   - Run `pip install -r requirements.txt` again
   - Check Python version compatibility

3. **Permission Issues**
   - Verify your Snowflake role has necessary privileges
   - Check access to Cortex Search service
   - Ensure document storage access

## Support

For any issues:
1. Check Snowflake documentation
2. Verify Cortex Search service status
3. Contact your Snowflake administrator for access issues
