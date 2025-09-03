# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

"document-composer" is a web application responsible for collaborative authoring of document segments that can be related to create a document. The intent is to minimize rework of common document segments common across all documents of the same type. For example, a standard document would include a common compliance and definitions segment that is used by all documents of the same type. Those document sections should be defined once and reused in the same document type. If the common document segment is updated, all documents that use that common segment will be updated automatically.

Documents will be generated in multiple formats including Microsoft Word, plain text, markdown automatically after they have been approved for publication. Draft documents can be generated in multiple formats including Microsoft Word, plain text, markdown as requested.

Key features:
- Python 3.12
- Flask
- Flask blueprints
- Jinja 2
- SQLAlchemy
- PostgreSQL
- GitHub Pages
- Pre-commit hooks
- YAML libraries
- Dinky Jekyll theme
- Basic documentation structure

### Technical Requirements
- Python 3.12
- Flask
- Flask blueprints
- Jinja 2
- SQLAlchemy
- PostgreSQL
- GitHub Pages
- Pre-commit hooks
- YAML libraries
- Dinky Jekyll theme

### Security Functionality
  - SAML2 authentication to Azure Entra ID
  - SAML2 authentication to Google Identity
  - User management
  - Authorization
  - CSRF protection
  - XSS protection
  - Session management
  - Role-based access control
  - Input validation
  - Error handling
  - Logging
  - Data access control
  - Data access auditing

### Business Functionality

The following business functionality is required.

#### Document Management
 - Document creation
 - Document metadata
 - Document metadata management
 - Document metadata search
 - Document metadata history
 - Document composition from Document sections
 - Document versioning
 - Document viewing
 - Document downloading
 - Document management
 - Document approval workflow and security corresponding roles
 - Document editing
 - Document publishing in multiple formats including Microsoft Word, plain text, markdown
 - Document sharing
 - Document search
 - Document history
 - Document comments
 - Document tags
 - Document access control
 - Document comments

#### Document Segment Management
 - Document sections
 - Document section management
 - Document section comments
 - Document section tags
 - Document section history
 - Document section search
 - Document section versioning
 - Document section comments
 - Document section access control

All code and artifacts must be created and maintained in this repository.

## High-Level Architecture

### Application Factory Pattern
The application uses Flask's application factory pattern implemented in `app/__init__.py:create_app()`. The factory creates Flask instances with proper configuration and blueprint registration.

### Blueprint Structure
- **Main Blueprint** (`app/main/`): Core application routes and landing pages
- **Auth Blueprint** (`app/auth/`): SAML authentication, user models, and role management
- **Admin Blueprint** (`app/admin/`): Administrative interface with role-based access control
- **Document Blueprint** (`app/doc/`): Document management interface with role-based access control
- **Document Segment Blueprint** (`app/docsegment/`): Document segment management interface with role-based access control
- **Workflow Blueprint** (`app/workflow/`): Workflow interface with role-based access control

### Role-Based Access Control (RBAC)
The application implements a comprehensive RBAC system:

- **Roles**: `UserRole` enum defines USER_ADMINISTRATOR, DOCUMENT_EDITOR, DOCUMENT_REVIEWER, DOCUMENT_VIEWER, WORKFLOW_ADMIN, WORKFLOW_REVIEWER, WORKFLOW_VIEWER, ANONYMOUS
- **Permissions**: `Permission` enum defines granular access rights
- **Role Manager**: `RoleManager` class handles role-permission mappings and validation
- **Decorators**: `@admin_required`, `@require_role()`, `@require_permission()` for route protection

### SAML Authentication Architecture
Multi-provider SAML 2.0 authentication supporting Google Workspace and Microsoft Entra:

- **Abstract Base**: `SAMLConfig` class defines common SAML configuration interface
- **Provider Configs**: `GoogleSAMLConfig` and `AzureSAMLConfig` implement provider-specific settings
- **Factory Pattern**: `get_saml_config(provider)` returns appropriate configuration instance
- **User Model**: `User.from_saml_attributes()` creates user instances from SAML assertions

### Session-Based User Management
Users are stored in database with role persistence:

- **Session Storage**: `User.save_to_session()` stores user data and roles
- **Session Retrieval**: `User.from_session()` loads authenticated user
- **Session Expiration**: `User.logout()` invalidates user session and redirects to login page
- **Session Timeout**: Configurable session timeout for security with maximum session inactivity of 20 minutes and expiration of 12 hours
- **Role Assignment**: Admins can assign/remove roles via admin interface

### Configuration System
Environment-based configuration using `config/config.py`:

- **Base Config**: Common settings and environment variable loading
- **Environment Configs**: DevelopmentConfig, ProductionConfig, TestingConfig
- **Config Mapping**: `CONFIG_MAP` dictionary for environment selection

## Important Security Considerations

### Production vs Development
- **CRITICAL**: Always set `FLASK_DEBUG=false` in production
- **CRITICAL**: Use strong `SECRET_KEY` (64+ random characters) in production
- Development mode exposes sensitive information and allows code execution

### Software Composition Analysis (SCA)
- **CRITICAL**: Always use a static analysis tool to scan for vulnerabilities
- **CRITICAL**: Regularly update dependencies to patch known vulnerabilities
- **CRITICAL**: Always check that a dependency actually exists
- **CRITICAL**: Always check that a dependency is secure
- **CRITICAL**: Always check the latest version of dependencies

### SAML Integration
- Certificate validation is essential for security
- Clock synchronization between SP and IdP is critical
- URL matching must be exact between configuration and IdP settings

## Key Files and Locations

Project structure will follow the standard Flask application structure.

### Core Application Files
- `app.py`: Application entry point using factory pattern
- `app/__init__.py`: Application factory and blueprint registration
- `config/config.py`: Environment-based configuration classes

### Authentication Module
- `app/auth/models.py`: User model with SAML attribute parsing
- `app/auth/roles.py`: Complete RBAC system (roles, permissions, validation)
- `app/auth/saml_config.py`: SAML provider configurations
- `app/auth/routes.py`: Authentication routes and SAML handlers

### Administration Module
- `app/admin/decorators.py`: Access control decorators for route protection
- `app/admin/user_manager.py`: User management logic and role assignment
- `app/admin/routes.py`: Admin interface routes
- `app/admin/templates/`: Admin UI templates

### Testing
- `tests/test_admin.py`: Admin functionality and access control tests
- `tests/test_roles.py`: Role-based access control system tests
- `tests/test_basic.py`: Basic application functionality tests
- `tests/test_documents.py`: Document tests
- `tests/test_document_segments.py`: Document segment tests
- `tests/test_document_generation.py`: Document generation tests
- `tests/test_workflow.py`: Workflow tests

### Environment Configuration
- `.env.example`: Template with required environment variables for SAML providers
- `requirements.txt`: Production dependencies including Flask, SQLAlchemy, python3-saml
- `requirements-ci.txt`: CI/CD specific dependencies

## Your role

Your role is to write code. You do NOT have access to the running app, so you cannot test the code. You MUST rely on me, the user, to test the code.

If I report a bug in your code, after you fix it, you should pause and ask me to verify that the bug is fixed.

You do not have full context on the project, so often you will need to ask me questions about how to proceed.

Don't be shy to ask questions -- I'm here to help you!

If I send you a URL, you MUST immediately fetch its contents and read it carefully, before you do anything else.

## Workflow

We use GitHub issues to track work we need to do, and PRs to review code. Whenever you create an issue or a PR, tag it with "by-claude". Use the `gh' bash command to interact with GitHub.

To start working on a feature, you should:

1. Setup

Read the relevant GitHub issue (or create one if needed)
Checkout main and pull the latest changes
Create a new branch like 'claude/feature-name'. NEVER commit to main. NEVER push to origin/main.

2. Development

Commit often as you write code, so that we can revert if needed.
When you have a draft of what you're working on, ask me to test it in the app to confirm that it works as you expect. Do this early and often.

3. Review

    When the work is done, verify that the diff looks good with `git diff main`
While you should attempt to write code that adheres to our coding style, don't worry about manually linting or formatting your changes. There are pre-commit Git hooks that will do this for you.
Push the branch to GitHub
Open a PR.
The PR title should not include the issue number
The PR description should start with the issue number and a brief description of the changes.
- Next, you should write a test plan. I (not you) will execute the test plan before merging the PR. If I can't check off any of the items, I will let you know. Make sure the test plan covers both new functionality and any EXISTING functionality that might be impacted by your changes

4. Fixing issues

To reconcile different branches, always rebase or cherry-pick. Do not merge.

Sometimes, after you've been working on one feature, I will ask you to start work on an unrelated feature. If I do, you should probably repeat this process from the beginning (checkout main, pull changes, create a new branch). When in doubt, just ask.

