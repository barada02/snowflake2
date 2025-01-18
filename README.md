# Snowflake RAG Document Assistant

A Streamlit-based application that implements RAG (Retrieval-Augmented Generation) using Snowflake's Cortex Search and LLM capabilities.

## Features

- Document search using Snowflake Cortex Search
- LLM-powered question answering
- Document context retrieval
- Multiple model selection options
- Category-based filtering
- Document preview links

## Prerequisites

- Python 3.8.x
- Snowflake account with appropriate permissions
- Access to Snowflake Cortex Search service
- Streamlit 1.26.0 or higher

## Installation

1. Clone the repository:
```bash
git clone [your-repository-url]
cd snowflake2
```

2. Create and activate a virtual environment:
```bash
python -m venv venv
# On Windows
.\venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure Snowflake credentials:
   - Copy `.streamlit/secrets.toml.example` to `.streamlit/secrets.toml`
   - Update the credentials in `.streamlit/secrets.toml`

## Usage

Run the application:
```bash
streamlit run app.py
```

## Configuration

Update the following parameters in `app.py` as needed:
- `NUM_CHUNKS`: Number of context chunks to retrieve
- `CORTEX_SEARCH_DATABASE`: Your Cortex Search database
- `CORTEX_SEARCH_SCHEMA`: Your schema
- `CORTEX_SEARCH_SERVICE`: Your Cortex Search service name

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
