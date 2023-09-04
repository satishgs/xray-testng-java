Certainly, here's the updated version without the colored output part:

---

# Project Documentation: Backup and Cleanup Utility 

## Table of Contents

1. [Introduction](#introduction)
    1. [Purpose](#purpose)
    2. [Scope](#scope)
    3. [Intended Audience](#intended-audience)
2. [Technology Stack](#technology-stack)
3. [Installation Guide](#installation-guide)
    1. [Pre-requisites](#pre-requisites)
    2. [Setup Steps](#setup-steps)
4. [Configuration](#configuration)
5. [Logging](#logging)
6. [Scripts](#scripts)
    1. [Backup Script](#backup-script)
    2. [Cleanup Script](#cleanup-script)
    3. [User Selection Script](#user-selection-script)
7. [Best Practices](#best-practices)
8. [Examples](#examples)
9. [FAQ](#faq)
10. [Contributors](#contributors)

---

## Introduction

### Purpose

This utility automates the backup and cleanup of database and files. 

### Scope

The scripts handle both incremental backups and cleanup operations, ensuring data integrity and optimizing storage.

### Intended Audience

This documentation is intended for DevOps Engineers, Database Administrators, and developers involved in maintaining the utility.

---

## Technology Stack

- **Node.js**: Runtime Environment
- **OracleDB**: Database 
- **Winston**: Logging Framework
- **SuperAgent**: HTTP Request Library

---

## Installation Guide

### Pre-requisites

- Node.js v14+
- OracleDB instance

### Setup Steps

1. Clone the repository: `git clone <repository_url>`
2. Navigate to the directory: `cd <directory>`
3. Install dependencies: `npm install`

---

## Configuration

- `CYBERARK_CA_PATH`: Path to CyberArk CA certificate
- `CYBERARK_CERT_PATH`: Path to client certificate
- `CYBERARK_KEY_PATH`: Path to private key
- [More about the config file](#)

---

## Logging

### Winston Logging

For logging, we're using Winston, which offers multiple levels and transports.

#### Code Sample

```javascript
// Winston code sample
```

---

## Scripts

### Backup Script

#### Features
- Incremental backups
- Secure password retrieval from CyberArk

#### Code Sample

```javascript
// Backup script here
```

### Cleanup Script

#### Features
- File age-based deletion
- Error handling

#### Code Sample

```javascript
// Cleanup script here
```

### User Selection Script

Allows user to choose the operation to perform: either backup or cleanup.

#### Code Sample

```javascript
// User selection script here
```

---

## Best Practices

- Always test the backup and restore procedures on a non-production system first.
- Monitor the logs regularly.

---

## Examples

### Sample Log Output

```plaintext
[2023-09-01T12:34:56] INFO: Oracle connection pool created
[2023-09-01T12:35:00] ERROR: Failed to perform backup
```

---

## FAQ

Q: How to know if the backup was successful?

A: Check the logs for a success message with timestamp.

---

## Contributors

- [Name](email@domain.com)
- [Name](email@domain.com)

---

Feel free to insert this updated template into your Confluence page.




while ($true) {
    Write-Host "Applying folder permissions... $(Get-Date)"
    
    # Set permissions
    icacls "C:\Path\To\Folder" /inheritance:r /grant "DOMAIN\GroupName:R"

    # Wait for 15 minutes
    Start-Sleep -Seconds 900
}
