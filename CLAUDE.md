# CLAUDE.md - AI Employee System Blueprint
## Bronze Tier Hackathon Specification

**Version:** 1.0.0  
**Last Updated:** 2026-02-15  
**Estimated Completion:** 8-12 hours  
**Approach:** Test-Driven Development with Full Documentation

---

## 🎯 PROJECT MISSION

Build a Digital FTE (Full-Time Equivalent) - a local-first, autonomous AI assistant that manages personal and business affairs using Claude Code, implementing the Bronze tier requirements from the AI Employee Hackathon.

### Core Objectives
- ✅ Obsidian vault with automated file management
- ✅ One working Watcher script (Gmail OR filesystem monitoring)
- ✅ Claude Code successfully reading/writing to vault
- ✅ Basic folder structure with proper workflow
- ✅ Agent Skills for all AI functionality
- ✅ Test-driven implementation with comprehensive documentation

---

## 🏗️ SYSTEM ARCHITECTURE

### High-Level Components

```
┌─────────────────────────────────────────────────────────────┐
│                    AI EMPLOYEE SYSTEM                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐      ┌──────────────┐     ┌────────────┐ │
│  │   WATCHERS   │─────▶│ INBOX QUEUE  │────▶│   SKILLS   │ │
│  │  (External)  │      │  (Raw Data)  │     │ (AI Logic) │ │
│  └──────────────┘      └──────────────┘     └────────────┘ │
│       │                                            │        │
│       │                                            ▼        │
│       ▼                                     ┌────────────┐  │
│  ┌──────────────┐                          │  ACTIONS   │  │
│  │  Data Input  │                          │ (Triage &  │  │
│  │   Sources    │                          │  Execute)  │  │
│  │ - Gmail      │                          └────────────┘  │
│  │ - Filesystem │                                 │        │
│  └──────────────┘                                 ▼        │
│                                            ┌────────────┐   │
│                                            │   OUTPUT   │   │
│                                            │ - Dashboard│   │
│                                            │ - Logs     │   │
│                                            │ - Done/    │   │
│                                            └────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow (Bronze Tier)

```
Input Source → Watcher → Inbox/ → Claude Skills → Triage → Needs_Action/ OR Done/
                                        ↓
                                   Dashboard.md
                                        ↓
                                    Logs/
```

---

## 📁 FOLDER STRUCTURE (REQUIRED)

```
AI_Employee_Bronze/
├── .claude/                    # Claude Code configuration
│   └── skills/                 # Agent Skills definitions (CRITICAL)
│       ├── vault-management.md
│       ├── email-processor.md
│       ├── file-organizer.md
│       └── dashboard-updater.md
│
├── Dashboard.md                # Real-time status (REQUIRED)
├── Company_Handbook.md         # Rules & guidelines (REQUIRED)
│
├── Inbox/                      # Raw incoming items
│   ├── emails/
│   └── files/
│
├── Needs_Action/               # Triaged items awaiting processing
│   ├── urgent/
│   └── normal/
│
├── Done/                       # Completed items archive
│   └── YYYY-MM/
│
├── Logs/                       # Audit trail
│   └── YYYY-MM-DD.log
│
└── README.md                   # Project documentation

Note: Do NOT create /Plans/, /Pending_Approval/, /Approved/, /Rejected/ 
      folders - these are Silver tier features.
```

---

## 🧠 AGENT SKILLS (CORE IMPLEMENTATION)

All AI functionality MUST be implemented as Agent Skills. Skills are defined in `.claude/skills/` directory.

### Skill 1: vault-management.md

**Purpose:** Manage all file operations within the Obsidian vault

**Capabilities:**
- Read markdown files from vault
- Write/update markdown files
- List files in directories
- Update Dashboard.md with current status

**Location:** `.claude/skills/vault-management.md`

### Skill 2: email-processor.md

**Purpose:** Process and triage incoming Gmail messages

**Capabilities:**
- Parse email metadata (subject, sender, date)
- Extract key information
- Categorize by urgency
- Move to appropriate folders
- Update logs

**Location:** `.claude/skills/email-processor.md`

### Skill 3: file-organizer.md

**Purpose:** Monitor and organize filesystem items

**Capabilities:**
- Detect new files in watched directories
- Categorize files by type and content
- Move files to appropriate vault folders
- Generate summaries

**Location:** `.claude/skills/file-organizer.md`

### Skill 4: dashboard-updater.md

**Purpose:** Maintain real-time Dashboard.md with system status

**Capabilities:**
- Update inbox counts
- Log recent activities
- Track system health
- Generate timestamp updates

**Location:** `.claude/skills/dashboard-updater.md`

---

## 🔍 BRONZE TIER PRINCIPLES

### 1. Local-First Architecture
**Principle:** All data must remain on the user's machine by default.  
**Rationale:** Privacy, control, and independence from cloud services.  
**Implementation:**
- Vault stored locally
- No external database dependencies
- All processing happens on local machine

### 2. Clear Separation of Concerns
**Principle:** Clear separation between data collection, decision-making, and execution.  
**Implementation:**
- Watchers: Data collection only
- Skills: Decision-making logic
- Actions: Execution layer

### 3. Autonomous but Supervised
**Principle:** The system should be autonomous but supervised.  
**Bronze Tier:** Basic logging only; full approval workflow in Silver tier.  
**Implementation:**
- All actions logged to Logs/
- Dashboard provides real-time visibility
- No blocking approval gates (Bronze tier)

---

## 📋 DASHBOARD TEMPLATE

```markdown
# AI Employee Dashboard

**Last Updated:** {{timestamp}}  
**Status:** {{status}}

## Today's Summary
- Inbox: {{inbox_count}} items
- Needs Action: {{needs_action_count}} items
- Completed: {{done_count}} items

## Recent Activity
- [{{timestamp}}] {{activity_description}}
- [{{timestamp}}] {{activity_description}}
- [{{timestamp}}] {{activity_description}}

## System Status
- Watcher: {{watcher_status}}
- Claude Code: {{claude_status}}
- Last Check: {{last_check}}

---
*AI Employee v1.0.0 - Bronze Tier*
```

---

## 📖 COMPANY HANDBOOK TEMPLATE

```markdown
# Company Handbook

**Version:** 1.0.0  
**Last Updated:** 2026-02-15

## Communication Rules

### Email Guidelines
- Reply to known contacts within 24 hours
- Flag emails with keywords: urgent, invoice, payment
- Always use professional tone

### Response Templates
- Acknowledgment: "Thank you for your email. I've received your message and will respond within 24 hours."
- Urgent: "This has been flagged as urgent and moved to priority queue."
- Needs Review: "This requires manual review - moved to Needs_Action folder."

## File Processing Rules

### Categorization
- PDF invoices → Needs_Action/urgent/
- Regular documents → Needs_Action/normal/
- Receipts → Done/ (with logging)

### Naming Convention
- Format: YYYY-MM-DD_category_description.ext
- Example: 2026-02-15_invoice_acme-corp.pdf

## Logging Standards

### Log Entry Format
```
[TIMESTAMP] [LEVEL] [COMPONENT] Message
Example: [2026-02-15T10:30:00] [INFO] [EmailProcessor] Processed email from client@example.com
```

### Log Levels
- INFO: Normal operations
- WARN: Attention needed
- ERROR: Failed operations

---
*AI Employee Handbook v1.0.0*
```

---

## 🧪 TEST-DRIVEN DEVELOPMENT APPROACH

### Phase 1: Setup & Structure Tests (2 hours)
```
Tests to write:
1. test_vault_structure() - Verify all required folders exist
2. test_dashboard_exists() - Verify Dashboard.md is present
3. test_handbook_exists() - Verify Company_Handbook.md is present
4. test_skills_directory() - Verify .claude/skills/ exists
5. test_file_permissions() - Verify read/write permissions
```

### Phase 2: Watcher Implementation Tests (3 hours)
```
Choose ONE watcher (Gmail OR Filesystem):

Gmail Watcher Tests:
1. test_gmail_connection() - Verify API connection
2. test_fetch_new_emails() - Fetch unread emails
3. test_email_to_markdown() - Convert email to .md
4. test_save_to_inbox() - Save to Inbox/ folder

OR

Filesystem Watcher Tests:
1. test_directory_monitoring() - Monitor target directory
2. test_new_file_detection() - Detect new files
3. test_file_to_markdown() - Convert file metadata to .md
4. test_move_to_inbox() - Move to Inbox/ folder
```

### Phase 3: Skills Implementation Tests (4 hours)
```
1. test_skill_loading() - Claude Code loads all skills
2. test_vault_read() - vault-management can read files
3. test_vault_write() - vault-management can write files
4. test_email_parsing() - email-processor extracts info
5. test_triage_logic() - Correct folder placement
6. test_dashboard_update() - Dashboard reflects changes
```

### Phase 4: Integration Tests (2 hours)
```
1. test_end_to_end_email() - Email → Inbox → Triage → Done
2. test_end_to_end_file() - File → Inbox → Triage → Done
3. test_dashboard_accuracy() - Dashboard shows correct counts
4. test_log_generation() - Logs created properly
```

### Phase 5: Documentation Tests (1 hour)
```
1. test_readme_complete() - README.md has all sections
2. test_skills_documented() - All skills have descriptions
3. test_architecture_diagram() - Architecture is documented
```

---

## 🛠️ IMPLEMENTATION CHECKLIST

### Pre-Implementation (30 mins)
- [ ] Clone repository or create new project
- [ ] Create AI_Employee_Vault directory
- [ ] Set up Python virtual environment
- [ ] Install dependencies (requirements.txt)
- [ ] Configure .gitignore for sensitive data

### Core Structure (1 hour)
- [ ] Create all required folders
- [ ] Create Dashboard.md from template
- [ ] Create Company_Handbook.md from template
- [ ] Create .claude/skills/ directory
- [ ] Write initial README.md

### Watcher Development (3 hours)
- [ ] Choose watcher type (Gmail OR Filesystem)
- [ ] Set up API credentials (if Gmail)
- [ ] Write watcher script with tests
- [ ] Test data collection
- [ ] Implement error handling

### Skills Development (4 hours)
- [ ] Create vault-management.md skill
- [ ] Create email-processor.md OR file-organizer.md skill
- [ ] Create dashboard-updater.md skill
- [ ] Test skills individually with Claude Code
- [ ] Verify Claude Code can load and use skills

### Integration (2 hours)
- [ ] Connect watcher to Inbox/
- [ ] Test Claude Code processing pipeline
- [ ] Verify Dashboard updates automatically
- [ ] Check log generation
- [ ] Test end-to-end workflow

### Documentation (1 hour)
- [ ] Document architecture in README.md
- [ ] Add setup instructions
- [ ] Include troubleshooting guide
- [ ] Document all skills with examples
- [ ] Create demo video or screenshots

### Testing & Validation (1 hour)
- [ ] Run all unit tests
- [ ] Run integration tests
- [ ] Manual testing of complete workflow
- [ ] Verify compliance with Bronze tier requirements
- [ ] Code review and cleanup

---

## 🚀 GETTING STARTED COMMANDS

### 1. Initial Setup
```bash
# Create project directory
mkdir AI_Employee_Vault
cd AI_Employee_Vault

# Create folder structure
mkdir -p .claude/skills Inbox Needs_Action Done Logs

# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Start Development
```bash
# Start watcher (Gmail example)
python gmail_watcher.py &

# OR start filesystem watcher
python filesystem_watcher.py &

# Start Claude Code with vault context
cd AI_Employee_Vault
claude
```

### 3. Verify Skills
```bash
# In Claude Code session:
"List available skills"
"Show me the vault-management skill"
"Read the Dashboard.md file"
```

### 4. Test Workflow
```bash
# Trigger test input:
# - Send test email (Gmail watcher)
# - Drop test file (Filesystem watcher)

# Verify processing:
ls -la Inbox/
ls -la Needs_Action/
cat Dashboard.md
tail Logs/$(date +%Y-%m-%d).log
```

---

## 📦 DEPENDENCIES (requirements.txt)

```
# Core dependencies
anthropic>=0.25.0
PyYAML>=6.0

# Gmail watcher (if using Gmail)
google-api-python-client>=2.0.0
google-auth-httplib2>=0.1.0
google-auth-oauthlib>=0.5.0

# Filesystem watcher (if using filesystem)
watchdog>=3.0.0

# Testing
pytest>=7.0.0
pytest-cov>=4.0.0

# Utilities
python-dotenv>=1.0.0
```

---

## ⚠️ COMMON PITFALLS & SOLUTIONS

### Problem: Import Errors
**Solution:** Create virtual environment and install all dependencies:
```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Problem: Claude Code Not Loading Skills
**Solution:** Verify skills directory structure:
```bash
ls -la .claude/skills/
# Should show .md files for each skill
```

### Problem: Watcher Not Detecting Changes
**Solution:** Check file permissions and API credentials:
```bash
# Gmail: Verify credentials.json exists
ls -la credentials.json

# Filesystem: Verify watch directory exists
ls -la /path/to/watched/directory
```

### Problem: Dashboard Not Updating
**Solution:** Verify vault-management skill has write permissions:
```bash
chmod 644 Dashboard.md
# Check skill implementation
```

---

## 📊 EVALUATION CRITERIA

### Bronze Tier Requirements (Must Have)
- ✅ Working Obsidian vault
- ✅ Dashboard.md with real-time updates
- ✅ Company_Handbook.md with guidelines
- ✅ ONE working watcher (Gmail OR Filesystem)
- ✅ Proper folder structure (Inbox, Needs_Action, Done, Logs)
- ✅ At least 3 functional Skills
- ✅ Claude Code integration working
- ✅ Basic logging implemented

### Quality Indicators (Should Have)
- ✅ Comprehensive test coverage (>80%)
- ✅ Clear documentation with examples
- ✅ Error handling and recovery
- ✅ Clean code architecture
- ✅ Setup automation scripts

### Bonus Points (Nice to Have)
- ✅ Both watchers implemented
- ✅ Advanced triage logic
- ✅ Performance metrics
- ✅ Demo video
- ✅ CI/CD pipeline

---

## 🎓 LEARNING RESOURCES

### Claude Code Documentation
- Skills creation guide
- API integration patterns
- Obsidian vault patterns

### Python Libraries
- `watchdog` for filesystem monitoring
- `google-api-python-client` for Gmail
- `pytest` for testing

### Design Patterns
- Observer pattern (for watchers)
- Strategy pattern (for skills)
- Repository pattern (for vault operations)

---

## 📝 SUBMISSION REQUIREMENTS

### Repository Structure
```
AI-Employee-Bronze/
├── AI_Employee_Vault/      # Main vault
├── src/                    # Source code
│   ├── watchers/
│   └── utils/
├── tests/                  # Test suite
├── docs/                   # Documentation
├── requirements.txt
├── README.md
└── CLAUDE.md              # This file
```

### Documentation Must Include
1. Architecture diagram
2. Setup instructions
3. Skills descriptions
4. Testing guide
5. Troubleshooting section
6. Demo screenshots or video

### Code Quality Standards
- PEP 8 compliance
- Type hints where appropriate
- Docstrings for all functions
- Unit tests for all components
- Integration tests for workflows

---

## 🏆 SUCCESS METRICS

**You've successfully completed Bronze Tier when:**
1. ✅ You can send/drop a test input
2. ✅ Watcher automatically detects it
3. ✅ Claude Code processes it via Skills
4. ✅ Item appears in correct folder (Needs_Action or Done)
5. ✅ Dashboard shows updated counts
6. ✅ Logs contain accurate audit trail
7. ✅ All tests pass
8. ✅ Documentation is complete

**The ultimate test:** Could someone else clone your repo and get the system running in under 30 minutes?

---

## 🔄 VERSION HISTORY

- **v1.0.0** (2026-02-15): Initial Bronze Tier specification
- Test-driven approach defined
- Complete architecture documented
- All templates provided

---

## 📞 SUPPORT & COMMUNITY

- GitHub Issues for bugs
- Discussions for questions
- Pull requests for improvements
- Discord/Slack community channel

---

**Remember:** The goal is a working system that YOU would actually use. Focus on reliability over features. Bronze tier is about solid foundations, not fancy features.

**Start simple. Test everything. Document well. Ship it.**

---

*End of CLAUDE.md - Let's build something amazing! 🚀*