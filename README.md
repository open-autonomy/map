# 🚜 ISO 23725:2024 Map Service API

This project implements a map service API that follows the ISO 23725:2024 standard for defining areas and road networks in mining environments. The service allows third-party software to query and consume map data with attributes such as `default_task`, `autonomy`, and `oneway`.

## 📌 Features
- **Default Task Tagging**: Define the primary function of areas (`load`, `offload`, `supply`, etc.).
- **Autonomy Attributes**: Indicate where autonomous and manned machines can operate.
- **Structured Road Model**: Provides unambiguous routing and connectivity rules.
- **RESTful API**: Supports JSON-based map service messaging.

## 📁 Project Structure
- `api/`: Contains the API implementation, data models, and utilities.
- `docs/`: Includes ISO 23725:2024 documentation and API references.
- `examples/`: Provides sample map data and API requests.
- `tests/`: Unit tests for ensuring API reliability.

## 🚀 Getting Started

### 1️⃣ Installation
Clone the repository and install dependencies:

```sh
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
pip install -r requirements.txt