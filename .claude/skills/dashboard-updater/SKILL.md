---
name: dashboard-updater
description: Maintain real-time Dashboard.md with system status. Use when Claude needs to update counts (inbox, needs_action, done), add activity log entries, check system status, update timestamps, or generate daily statistics for the AI Employee vault.
---

# Dashboard Updater Skill

Maintain real-time Dashboard.md with accurate system status, activity logs, and health metrics for the AI Employee system.

## Purpose

Provide a single source of truth for the AI Employee's current state by maintaining Dashboard.md with real-time counts, recent activities, system status, and daily statistics.

## Core Capabilities

### 1. Calculate Item Counts
Count items across all vault folders:
```python
from pathlib import Path

def calculate_vault_counts() -> dict:
    """
    Calculate current item counts for all vault folders.
    
    Returns:
        Dictionary with counts for each folder
    """
    vault_base = Path("AI_Employee_Vault")
    
    def count_md_files(path: Path, recursive: bool = True) -> int:
        """Count markdown files in directory"""
        if not path.exists():
            return 0
        if recursive:
            return len(list(path.rglob("*.md")))
        else:
            return len(list(path.glob("*.md")))
    
    # Count items in each folder
    inbox_count = count_md_files(vault_base / "Inbox", recursive=True)
    
    needs_action_urgent = count_md_files(vault_base / "Needs_Action" / "urgent", recursive=False)
    needs_action_normal = count_md_files(vault_base / "Needs_Action" / "normal", recursive=False)
    needs_action_total = needs_action_urgent + needs_action_normal
    
    done_count = count_md_files(vault_base / "Done", recursive=True)
    
    return {
        'inbox': inbox_count,
        'needs_action': {
            'total': needs_action_total,
            'urgent': needs_action_urgent,
            'normal': needs_action_normal
        },
        'done': done_count,
        'total_items': inbox_count + needs_action_total + done_count
    }
```

### 2. System Status Monitoring
Check watcher and system health:
```python
import psutil
from datetime import datetime

def get_system_status() -> dict:
    """
    Get current system health and watcher status.
    
    Returns:
        Dictionary with status indicators
    """
    # Check if watcher process is running
    watcher_status = check_watcher_process()
    
    # Claude Code is assumed connected if dashboard is being updated
    claude_status = '✅ Connected'
    
    # Current time
    last_check = datetime.now().strftime('%H:%M:%S')
    
    # Overall system status
    if watcher_status == '✅ Active':
        overall_status = '✅ Active'
    elif watcher_status == '⚠️ Warning':
        overall_status = '⚠️ Warning'
    else:
        overall_status = '❌ Stopped'
    
    return {
        'overall': overall_status,
        'watcher': watcher_status,
        'claude': claude_status,
        'last_check': last_check,
        'timestamp': datetime.now().isoformat()
    }

def check_watcher_process() -> str:
    """Check if watcher process is running"""
    try:
        for proc in psutil.process_iter(['pid', 'name', 'cmdline']):
            cmdline = ' '.join(proc.info.get('cmdline', []))
            if 'watcher.py' in cmdline.lower():
                return '✅ Active'
        return '❌ Stopped'
    except Exception:
        return '⚠️ Unknown'
```

### 3. Activity Tracking
Maintain recent activity buffer:
```python
from collections import deque
from datetime import datetime

# Global activity buffer (max 10 activities)
activity_buffer = deque(maxlen=10)

def add_activity(description: str):
    """
    Add new activity to recent activities.
    
    Args:
        description: Activity description (e.g., "Processed email from client@example.com")
    """
    timestamp = datetime.now().strftime('%H:%M')
    activity = f"[{timestamp}] {description}"
    activity_buffer.appendleft(activity)  # Add to front

def get_recent_activities() -> str:
    """
    Get formatted recent activities for dashboard.
    
    Returns:
        Formatted activity list as markdown
    """
    if not activity_buffer:
        return "- No recent activity"
    
    return '\n'.join(f"- {activity}" for activity in list(activity_buffer)[:10])
```

### 4. Daily Statistics
Track and update daily stats:
```python
import json
from pathlib import Path
from datetime import datetime

def get_stats_file() -> Path:
    """Get path to daily stats file"""
    return Path("AI_Employee_Vault/Logs/daily_stats.json")

def load_daily_stats() -> dict:
    """
    Load or initialize daily statistics.
    
    Returns:
        Dictionary with daily stats
    """
    stats_file = get_stats_file()
    today = datetime.now().strftime('%Y-%m-%d')
    
    if stats_file.exists():
        try:
            stats = json.loads(stats_file.read_text())
            # Reset if new day
            if stats.get('date') != today:
                stats = create_new_stats(today)
        except Exception:
            stats = create_new_stats(today)
    else:
        stats = create_new_stats(today)
        stats_file.parent.mkdir(parents=True, exist_ok=True)
    
    return stats

def create_new_stats(date: str) -> dict:
    """Create new daily stats dictionary"""
    return {
        'date': date,
        'emails_processed': 0,
        'files_organized': 0,
        'actions_completed': 0,
        'errors': 0
    }

def update_daily_stats(stat_type: str):
    """
    Update specific daily statistic.
    
    Args:
        stat_type: Type of stat to increment ('email', 'file', 'completed', 'error')
    """
    stats = load_daily_stats()
    
    # Increment appropriate counter
    if stat_type == 'email':
        stats['emails_processed'] += 1
    elif stat_type == 'file':
        stats['files_organized'] += 1
    elif stat_type == 'completed':
        stats['actions_completed'] += 1
    elif stat_type == 'error':
        stats['errors'] += 1
    
    # Save updated stats
    stats_file = get_stats_file()
    stats_file.write_text(json.dumps(stats, indent=2))

def get_daily_stats_formatted() -> str:
    """
    Get formatted daily statistics for dashboard.
    
    Returns:
        Formatted stats as markdown
    """
    stats = load_daily_stats()
    
    return f"""- Emails Processed: {stats['emails_processed']}
- Files Organized: {stats['files_organized']}
- Actions Completed: {stats['actions_completed']}
- Errors: {stats['errors']}"""
```

### 5. Dashboard Generation
Generate complete dashboard content:
```python
def generate_dashboard_content(counts: dict, status: dict) -> str:
    """
    Generate complete Dashboard.md content.
    
    Args:
        counts: Vault item counts
        status: System status information
    
    Returns:
        Complete dashboard markdown
    """
    timestamp = status['timestamp']
    
    dashboard = f"""# AI Employee Dashboard

**Last Updated:** {timestamp}  
**Status:** {status['overall']}

## Today's Summary
- Inbox: {counts['inbox']} items
- Needs Action: {counts['needs_action']['total']} items ({counts['needs_action']['urgent']} urgent, {counts['needs_action']['normal']} normal)
- Completed: {counts['done']} items

## Recent Activity
{get_recent_activities()}

## System Status
- Watcher: {status['watcher']}
- Claude Code: {status['claude']}
- Last Check: {status['last_check']}

## Today's Stats
{get_daily_stats_formatted()}

---
*AI Employee v1.0.0 - Bronze Tier*
"""
    return dashboard
```

### 6. Complete Dashboard Update
Update entire dashboard:
```python
def update_dashboard_complete() -> dict:
    """
    Perform complete dashboard update.
    
    Steps:
    1. Calculate current counts
    2. Get system status
    3. Generate dashboard content
    4. Write to Dashboard.md
    
    Returns:
        Result dictionary with status
    """
    try:
        # Step 1: Calculate counts
        counts = calculate_vault_counts()
        
        # Step 2: Get status
        status = get_system_status()
        
        # Step 3: Generate content
        content = generate_dashboard_content(counts, status)
        
        # Step 4: Write to file
        dashboard_path = Path("AI_Employee_Vault/Dashboard.md")
        dashboard_path.write_text(content, encoding='utf-8')
        
        # Log success
        from vault_management import write_log
        write_log('INFO', 'DashboardUpdater', 'Dashboard updated successfully')
        
        return {
            'status': 'success',
            'timestamp': status['timestamp'],
            'counts': counts
        }
    except Exception as e:
        from vault_management import write_log
        write_log('ERROR', 'DashboardUpdater', f'Dashboard update failed: {e}')
        return {
            'status': 'error',
            'error': str(e)
        }
```

### 7. Quick Count Update
Update only counts (faster):
```python
import re

def quick_count_update():
    """
    Quick dashboard update - counts and timestamp only.
    Faster than full update for frequent changes.
    """
    dashboard_path = Path("AI_Employee_Vault/Dashboard.md")
    
    # Read current content
    content = dashboard_path.read_text()
    
    # Calculate new counts
    counts = calculate_vault_counts()
    
    # Update timestamp
    timestamp = datetime.now().isoformat()
    content = re.sub(
        r'\*\*Last Updated:\*\* .+',
        f'**Last Updated:** {timestamp}',
        content
    )
    
    # Update counts
    content = re.sub(
        r'- Inbox: \d+ items',
        f'- Inbox: {counts["inbox"]} items',
        content
    )
    
    content = re.sub(
        r'- Needs Action: \d+ items \(\d+ urgent, \d+ normal\)',
        f'- Needs Action: {counts["needs_action"]["total"]} items ({counts["needs_action"]["urgent"]} urgent, {counts["needs_action"]["normal"]} normal)',
        content
    )
    
    content = re.sub(
        r'- Completed: \d+ items',
        f'- Completed: {counts["done"]} items',
        content
    )
    
    # Write back
    dashboard_path.write_text(content)
```

## Convenience Function

Combined activity add and dashboard update:
```python
def log_and_update(activity: str, stat_type: str = None):
    """
    Add activity and update dashboard in one call.
    
    Args:
        activity: Activity description to log
        stat_type: Optional stat type to increment ('email', 'file', 'completed', 'error')
    """
    # Add activity
    add_activity(activity)
    
    # Update stats if provided
    if stat_type:
        update_daily_stats(stat_type)
    
    # Full dashboard update
    update_dashboard_complete()
```

## Background Scheduler

Optional: Schedule periodic dashboard updates:
```python
import threading
import time

class DashboardScheduler:
    """Background thread for scheduled dashboard updates"""
    
    def __init__(self, interval_seconds: int = 300):
        self.interval = interval_seconds
        self.running = False
        self.thread = None
    
    def start(self):
        """Start scheduled updates"""
        self.running = True
        self.thread = threading.Thread(target=self._run, daemon=True)
        self.thread.start()
        print(f"📊 Dashboard scheduler started (every {self.interval}s)")
    
    def stop(self):
        """Stop scheduled updates"""
        self.running = False
        if self.thread:
            self.thread.join()
        print("📊 Dashboard scheduler stopped")
    
    def _run(self):
        """Background update loop"""
        while self.running:
            try:
                update_dashboard_complete()
            except Exception as e:
                print(f"Scheduler error: {e}")
            time.sleep(self.interval)

# Usage
scheduler = DashboardScheduler(interval_seconds=300)  # Update every 5 minutes
# scheduler.start()
```

## Update Triggers

Dashboard should be updated after:
- New email processed
- New file organized
- Task completed
- Error occurred
- Every 5 minutes (scheduled)

## Dashboard Template

Initial dashboard structure:
```markdown
# AI Employee Dashboard

**Last Updated:** 2026-02-15T10:00:00  
**Status:** ✅ Active

## Today's Summary
- Inbox: 0 items
- Needs Action: 0 items (0 urgent, 0 normal)
- Completed: 0 items

## Recent Activity
- No recent activity

## System Status
- Watcher: ✅ Active
- Claude Code: ✅ Connected
- Last Check: 10:00:00

## Today's Stats
- Emails Processed: 0
- Files Organized: 0
- Actions Completed: 0
- Errors: 0

---
*AI Employee v1.0.0 - Bronze Tier*
```

## Error Handling

- **Can't read directory**: Show last known count with ⚠️ warning
- **Dashboard file locked**: Retry after 1 second, max 3 attempts
- **Timestamp error**: Use current system time as fallback
- **Count mismatch**: Recount and log discrepancy
- **Write failure**: Queue update for retry

## Performance Considerations

- **Cache counts**: Store for 30 seconds to reduce I/O
- **Quick update**: Use for frequent changes (counts only)
- **Full update**: Use every 5 minutes or on demand
- **Background thread**: Schedule periodic refreshes
- **Debounce**: Prevent rapid consecutive updates

## Integration

Called by:
- `email-processor` after processing email
- `file-organizer` after organizing file
- User commands for manual refresh
- Background scheduler for periodic updates

## Examples

### Example 1: After Email Processing
```python
# Email was processed
log_and_update(
    activity="Processed email from client@example.com → Needs_Action/urgent/",
    stat_type='email'
)
```

### Example 2: Manual Dashboard Refresh
```python
# User requests dashboard update
result = update_dashboard_complete()
if result['status'] == 'success':
    print(f"✅ Dashboard updated at {result['timestamp']}")
```

### Example 3: Quick Count Check
```python
# Just update counts quickly
quick_count_update()
```

## Testing

Test dashboard functions:
```python
def test_dashboard():
    """Test dashboard updater"""
    # Add test activity
    add_activity("Test activity")
    
    # Update dashboard
    result = update_dashboard_complete()
    assert result['status'] == 'success'
    
    # Verify dashboard exists
    dashboard_path = Path("AI_Employee_Vault/Dashboard.md")
    assert dashboard_path.exists()
    
    content = dashboard_path.read_text()
    assert "AI Employee Dashboard" in content
    assert "Test activity" in content
```

## Notes

- Dashboard.md must exist for system to function
- Activity buffer keeps last 10 activities only
- Daily stats reset at midnight
- System status checks watcher process name
- All updates are logged for audit trail