# YHunter - Windows Threat Hunting Collector

[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](https://github.com/yourusername/YHunter/releases)
[![Platform](https://img.shields.io/badge/platform-Windows-lightgrey.svg)](https://www.microsoft.com/windows)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

**YHunter** is a native 64-bit Windows console application designed for threat hunting and security assessment. It collects standardized host-based telemetry from Windows systems by executing built-in Windows commands and PowerShell cmdlets, producing comprehensive system snapshots for analysis.

<img width="1075" height="883" alt="image" src="https://github.com/user-attachments/assets/501ee6c6-420a-4b44-9a31-eb248a1ffd73" />

## 🎯 Overview

YHunter automates the collection of 25 different types of system information including processes, services, network connections, event logs, user accounts, scheduled tasks, and more. It's designed to be lightweight, fast, and completely self-contained with no external dependencies.

### Key Features

- ✅ **Native C Implementation** - Single executable, no dependencies
- ✅ **25 Data Collection Modules** - Comprehensive system telemetry
- ✅ **Selective Execution** - Run specific commands or groups
- ✅ **UTF-8 Output** - PowerShell-compatible encoding
- ✅ **Timeout Protection** - 3-minute timeout per command prevents hangs
- ✅ **Append Mode** - Adds to existing logs or creates new ones
- ✅ **Clear Formatting** - Timestamped section headers for easy parsing
- ✅ **Zero Network Activity** - All operations are local

## 📦 What's Included

### YHunter.exe (Standalone Collector)

The core threat hunting collector - a single executable that can be run directly on any Windows system.

**Use Cases:**
- Single-host collection
- Incident response
- Manual threat hunting
- Integration into existing workflows
- Scripted deployments

### YHunter-GUI (Optional Addon)

A modern Windows Forms application for orchestrating YHunter.exe across multiple domain-joined hosts using PowerShell Remoting.

**Use Cases:**
- Bulk collection across hundreds/thousands of hosts
- Domain-wide threat hunting operations
- Centralized log retrieval
- Operator-friendly GUI for non-technical users

> **Note:** YHunter-GUI is an optional addon. YHunter.exe works perfectly standalone and does not require the GUI.

## 🚀 Quick Start

### Standalone Usage

1. **Download** `YHunter.exe` from the [Releases](https://github.com/michaelyamout/YHunter/releases) page
2. **Run** with an output file path:
   ```cmd
   YHunter.exe -outfile C:\Windows\Tasks\YHunter_results.txt
   ```
3. **Review** the collected data in the output file

### With GUI (Bulk Operations)

1. **Download** both `YHunter.exe` and `YHunter-GUI.exe`
2. **Place** YHunter.exe on a network share (e.g., `\\dc01\SYSVOL\corp.local\scripts\`)
3. **Launch** YHunter-GUI.exe
4. **Configure** domain credentials, target hosts, and paths
5. **Click** "Run Collection" to deploy and execute across all targets

## 📋 System Requirements

### YHunter.exe
- **OS:** Windows 7 or later (64-bit)
- **PowerShell:** 2.0 or later (for PowerShell-based commands)
- **Permissions:** Some commands require administrator privileges
- **Dependencies:** None (statically linked)

### YHunter-GUI (Optional)
- **OS:** Windows 10/11 or Windows Server 2016+
- **.NET Framework:** 4.8 Runtime
- **PowerShell:** 2.0+ (for remote execution)
- **Network:** Domain-joined environment with PowerShell Remoting enabled

## 📖 Detailed Documentation

### Command Line Options

```cmd
YHunter.exe -outfile <path> [options]

Options:
  -outfile <path>      Full path to output log file (required)
  -commands <list>     Comma-separated list of command IDs to execute
  -groups <list>       Comma-separated list of group IDs to execute
  -list-commands       List all available command IDs and exit
  -list-groups         List all available group IDs and exit
  -help, -?            Display help message
```

### Examples

**Full collection:**
```cmd
YHunter.exe -outfile C:\Windows\Tasks\YHunter_results.txt
```

**Selective commands:**
```cmd
YHunter.exe -outfile results.txt -commands systeminfo,processes,services
```

**Command groups:**
```cmd
YHunter.exe -outfile results.txt -groups system,network,security
```

**List available options:**
```cmd
YHunter.exe -list-commands
YHunter.exe -list-groups
```

## 📊 Collected Data

YHunter collects 25 different types of system information organized into 7 logical groups:

### 1. System Information (5 commands)
- System Information (`systeminfo`)
- System Uptime (PowerShell)
- Disk Information (WMI)
- Network Adapters (WMI)
- Environment Variables (`set`)

### 2. Processes & Services (4 commands)
- Running Processes (`tasklist`)
- WMI Process Details
- Services (`sc query`)
- WMI Service Details

### 3. Network (4 commands)
- Network Connections - TCP (`netstat`)
- Network Connections - UDP (`netstat`)
- ARP Table (`arp`)
- DNS Cache (`ipconfig`)

### 4. Security & Logs (5 commands)
- PowerShell Event Logs (`Get-WinEvent`)
- Security Event Log (`Get-WinEvent`)
- System Event Log (`Get-WinEvent`)
- Application Event Log (`Get-WinEvent`)
- Firewall Rules (PowerShell)

### 5. Users & Access (3 commands)
- Local Users (`net user`)
- Local Groups (`net localgroup`)
- Network Shares (`net share`)

### 6. Persistence & Startup (3 commands)
- Scheduled Tasks (PowerShell)
- Startup Programs (Registry)
- Installed Software (WMI)

### 7. System Files (1 command)
- Hosts File (PowerShell)

## 📁 Output Format

The output file contains:
- **UTF-8 encoding with BOM** (for PowerShell compatibility)
- **Clear section headers** with timestamps
- **Command output** in original format
- **Error messages** if commands fail
- **Summary** at the end

Example section:
```
========================================
System Information
========================================
[Timestamp: 2024-01-15 14:30:45]
========================================

[Command output here...]

```

## 🔒 Security Considerations

### YHunter.exe
- ✅ **No network communication** - All operations are local
- ✅ **No persistence mechanisms** - No registry modifications or scheduled tasks
- ✅ **Minimal footprint** - Single executable, no installation required
- ✅ **No obfuscation** - Source code available for review
- ✅ **Writes only to specified file** - No other file system modifications

### YHunter-GUI (Optional)
- ⚠️ **Password Storage:** Passwords stored in memory only (not saved to disk)
- ⚠️ **Command Line:** Passwords embedded in PowerShell commands (documented limitation)
- ✅ **No Telemetry:** Application does not communicate externally
- ✅ **Local Operations:** All operations are local or via PowerShell Remoting

> **Security Note:** For production environments, consider using integrated authentication or Get-Credential prompts instead of direct password entry.

## 🐛 Troubleshooting

### Common Issues

**"Failed to open output file"**
- Check that the directory exists
- Verify write permissions to the directory
- Ensure the path is valid

**"Command timed out"**
- Some commands (like WMI queries) can be slow
- The command will be terminated after 3 minutes
- Check system performance and resource usage

**Missing PowerShell commands**
- Ensure PowerShell is installed (Windows 7+)
- Some PowerShell cmdlets require PowerShell 3.0+ (Windows 8+)

**Access Denied errors**
- Some commands require administrator privileges
- Run YHunter.exe as Administrator for full collection
- The tool continues execution even if individual commands fail

### Exit Codes

- `0` - Success (all commands executed)
- `1` - Fatal error (cannot open output file, invalid arguments)
- `2` - Critical system error (memory allocation failure)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## 📝 License

This tool is provided as-is for threat hunting and security assessment purposes.

## 🙏 Acknowledgments

YHunter was designed for security professionals, incident responders, and threat hunters who need reliable, repeatable host-based data collection. Special thanks to the security community for feedback and testing.

## 📞 Support

- **Issues:** [GitHub Issues](https://github.com/michaelyamout/YHunter/issues)
- **Releases:** [GitHub Releases](https://github.com/michaelyamout/YHunter/releases)
- **Documentation:** See `README.md` and `YHunter-GUI/README.md` for detailed documentation



- Log retrieval
- Troubleshooting

