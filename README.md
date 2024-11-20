# RHEL - Register Host to Satellite

## Overview

This Ansible playbook automates the process of registering Red Hat Enterprise Linux (RHEL) hosts to Red Hat Satellite servers. It is designed to handle various environments (production, test, development), locations (On-Campus, AWS), and RHEL versions (7, 8, 9). The playbook determines the appropriate Satellite server and activation key based on the host's group memberships and system facts, ensuring that each host is registered correctly according to its environment and location.

## Features

- **Environment-Aware Registration**: Automatically selects the appropriate Satellite server and activation key based on the host's environment (`prod`, `test`, `dev`).
- **Location-Based Configuration**: Differentiates between On-Campus and AWS locations to apply specific configurations.
- **RHEL Version Support**: Supports RHEL versions 7, 8, and 9, ensuring compatibility across different systems.
- **Retry Mechanism**: Implements retries for registration tasks to handle transient network issues.
- **AWS-Specific Cleanup**: Performs additional steps for AWS hosts to ensure repositories are managed correctly.

## Prerequisites

- **Ansible Automation Platform (AAP) or AWX**: The playbook is intended to run within AAP or AWX.
- **Inventory Configuration**: Hosts should be properly defined in your Ansible inventory with appropriate group memberships.
- **Variables and Tokens**:
    - Replace placeholder URLs and tokens in the playbook with your actual Satellite server URLs and authorization tokens.
    - **Security Note**: Do not hardcode sensitive information like tokens in the playbook. Use Ansible Vault or variables to store sensitive data securely.

## Inventory Setup

### Group Names and Tags

The playbook relies on specific group names to determine which tasks to execute for each host. Ensure that your inventory groups are set up as follows:

- **Environment Tags**:
    - `tag_environment_prod`
    - `tag_environment_test`
    - `tag_environment_dev`
- **Application Tags**:
    - `tag_application_satellite` (if present, the host will be skipped, used to exclude the Satellite server itself)
- **Hypervisor Type**:
    - `hypervisor_type_aws` (for AWS hosts)

## Playbook Structure

### Variables Used

- **`survey_hostname` or `survey_hosts`**: Variables provided via survey or extra-vars to specify target hosts.
- **`ansible_distribution_major_version`**: Ansible fact used to determine the RHEL version.
- **`ansible_fqdn`**: Used to verify successful registration.
- **Group Memberships**: Used to determine environment and location.

### Tasks Overview

1. **Registration Tasks**: For each combination of environment, location, and RHEL version, a task is defined to register the host with the appropriate Satellite server using a `curl` command.
    
2. **AWS-Specific Configurations**:
    
    - Enable `manage_repos` in `/etc/rhsm/rhsm.conf`.
    - Uninstall Red Hat Update Infrastructure (RHUI) packages that are not needed when registered to Satellite.
    - Clean up and refresh repositories.

## Usage

### Step 1: Update the Playbook

- **Replace URLs and Tokens**: Update the `curl` commands in the playbook with your Satellite server URLs and valid authorization tokens.
    
- **Use Variables for Tokens**: For security, store tokens and sensitive URLs in variables or use Ansible Vault.
    

### Step 2: Ensure Proper Group Assignments

- Assign hosts to the correct groups in your inventory to reflect their environment and location.

### Step 3: Run the Playbook

- Run the playbook targeting your hosts in AAP/AWX.
