# Project Overview

Project Name: sous-chef
Project Description: Create a MCP Server for restaurant analytics
Purpose: Hands on experience on MCP Server, creating tools, connectig to LLM


# Features

MCP Server: 

    Server Name: restaurant-mcp-server
    Number of Tools: 2
    Communication Protocol: stdio

Tool 1: Customer Analytics

    What it is: A tool which can connect to the backend database of customers and retrives information
    Data source: File-based, locally stored

Tool 2: Menu Analytics

    What it is: A tool which can connect to the database of Menu and retrives information
    Data SourceL File-based, locally stored


# Architecture

Components: 

MCP Server
Data Layer (file-based database)
MCP Client (for testing)
Tool implementations

Data Flow:

Files -> Tools -> MCP Server -> MCP Client -> LLM


# Technology Stack

Programming language: Python

MCP Framework/Library: FastMCP

LLM Client: Anthropic/OpenAI -> to decide

Data Format: JSON, CSV, SQLite -> to decide


# Project Structure
```
restaurant-mcp-server/
├── README.md
├── data/
│   ├── customers/
│   └── menu/
├── src/
│   ├── server.py
│   ├── tools/
│   │   ├── customer_analytics.py
│   │   └── menu_analytics.py
│   └── database/
│       └── file_db.py
├── tests/
├── client/
│   └── test_client.py
└── requirements.txt
```

# Implementation Plan

### Phase 1: Setup

Tasks to complete
Dependencies to install
Initial file structure


### Phase 2: Data Layer

Create sample data files
Implement file reading functions
Data validation


### Phase 3: Tool Development

Implement customer analytics tool
Implement menu analytics tool
Unit tests


### Phase 4: MCP Server

Set up MCP server
Register tools
Test tool discovery


### Phase 5: Client Integration

Create test client
Test with LLM
End-to-end testing


### Phase 6: Documentation & Refinement

Write usage guide
Add examples
Cleanup and polish
