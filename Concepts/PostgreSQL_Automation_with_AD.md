
## Identity Provisioning Automation (PostgreSQL & Active Directory)

The goal is to eliminate manual AD management and implement automated Identity Lifecycle
based on the **JML process.

### Architecture and Realization Concepts:
1. **Single Source of Truth: **PostgreSQL should store employee records.
2. **Automation: A Python script should run on schedule and query the Database for state changes.
3. **Provisioning Execution<to_research>: Powershell/LDAP -----

#### Current Infrastructure State:
* PostgreSQL engine is fully deployed and isolated within a Docker container. Connection can be established.
* Database schema design is currently being developed.
