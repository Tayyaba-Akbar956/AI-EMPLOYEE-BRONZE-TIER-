# AI Employee - Bronze Tier

A local-first, autonomous AI assistant that manages personal and business affairs. This implementation fulfills the Bronze tier requirements from the AI Employee Hackathon and includes Silver tier features (Gmail integration).

## 🎯 Project Overview

The AI Employee system provides automated file and email management through an Obsidian vault structure, with skills handling all AI processing logic.

### Core Features

- **Obsidian Vault**: Local-first file management system
- **Filesystem Watcher**: Monitors directories and auto-organizes files
- **Gmail Watcher**: Monitors Gmail inbox for important emails (Silver Tier)
- **Automated Triage**: Files and emails categorized as urgent/normal and routed appropriately
- **Real-time Dashboard**: Live status tracking and activity logs
- **Comprehensive Testing**: 91 tests with >98% pass rate

## 📁 Project Structure

```
AI_EMPLOYEE_BRONZE/
├── AI_Employee_Vault/          # Main Obsidian vault
│   ├── Inbox/                  # Raw incoming items
│   │   ├── emails/
│   │   └── files/
│   ├── Needs_Action/           # Triaged items
│   │   ├── urgent/
│   │   └── normal/
│   ├── Done/                   # Completed items archive
│   ├── Logs/                   # Audit trail
│   ├── Dashboard.md            # Real-time status
│   └── Company_Handbook.md     # Rules & guidelines
├── src/
│   ├── watchers/
│   │   ├── filesystem_watcher.py
│   │   └── gmail_watcher.py    # NEW: Gmail integration
│   └── utils/
│       ├── vault_management.py
│       └── dashboard_updater.py
├── tests/
│   ├── test_structure.py
│   ├── test_filesystem_watcher.py
│   ├── test_skills.py
│   └── test_integration.py
├── requirements.txt
└── README.md
```

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- Windows/Linux/MacOS
- Google Cloud Credentials (for Gmail Watcher)

### Installation

1. **Clone or extract the project:**
   ```bash
   cd AI_EMPLOYEE_BRONZE
   ```

2. **Create virtual environment:**
   ```bash
   python -m venv venv
   # Windows:
   venv\Scripts\activate
   # Linux/Mac:
   source venv/bin/activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

### Running the System

#### 1. Start the Filesystem Watcher

```bash
python -m src.watchers.filesystem_watcher ./watch_folder
```

#### 2. Start the Gmail Watcher

*Note: Requires `credentials.json` from Google Cloud Console in the project root.*

```bash
python -m src.watchers.gmail_watcher
```

### 3. View Dashboard

Open `AI_Employee_Vault/Dashboard.md` in Obsidian or any markdown viewer.

## 📊 Automated Triage System

The system automatically categorizes incoming items:

| Source | Pattern/Keyword | Category | Priority | Destination |
|--------|-----------------|----------|----------|-------------|
| **File** | invoice, bill | Financial | URGENT | Needs_Action/urgent/ |
| **File** | contract, nda | Legal | URGENT | Needs_Action/urgent/ |
| **Email**| "urgent", "asap"| Urgent | URGENT | Needs_Action/urgent/ |
| **Email**| stripe, paypal | Financial | URGENT | Needs_Action/urgent/ |
| **Email**| newsletter | Promo | LOW | Done/ |

## 🧪 Testing

Run the full test suite:

```bash
python -m pytest tests/ -v
```

## 📦 Dependencies

```
anthropic>=0.25.0
PyYAML>=6.0
watchdog>=3.0.0
google-api-python-client>=2.0.0
google-auth-httplib2>=0.1.0
google-auth-oauthlib>=1.0.0
pytest>=7.0.0
pytest-cov>=4.0.0
python-dotenv>=1.0.0
```

## 🏆 Tier Compliance

✅ **Bronze Tier Achieved**
- Working Obsidian vault
- Filesystem watcher
- Claude Code integration (4 skills)
- proper folder structure & logging

✅ **Silver Tier Features**
- **Gmail Integration**: Functional `gmail_watcher.py`
- **Email Processing**: `email-processor` skill capable of parsing and categorizing emails


---

*Built for the AI Employee Hackathon*
*Version 1.1.0 - February 2026*
