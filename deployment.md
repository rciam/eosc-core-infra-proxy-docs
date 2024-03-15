## Deployment

This guide describes how to deploy the EOSC Core Infrastructure Proxy based on[RCIAM](https://rciam.github.io/rciam-docs/) using [Ansible](https://www.ansible.com/). The deployment comprises the following components:

- Identity Broker (auth proxy) based on one or more instances of Keycloak (see [keycloakservers.yml](https://github.com/rciam/rciam-deploy/blob/master/keycloakservers.yml) playbook)
- Database backend based on PostgreSQL (see [dbservers.yml](https://github.com/rciam/rciam-deploy/blob/master/dbservers.yml) playbook for setting up a master / hot standby PostgreSQL deployment)
- Reverse proxy based on nginx to support HTTP request load balancing among the Keycloak nodes that use the back-end Postgresql DB (see [webproxyservers.yml](https://github.com/rciam/rciam-deploy/blob/master/webproxyservers.yml) playbook)

### Configuration

#### Ansible host inventory

```ini
# file: <INVENTORY>/hosts.ini

[webproxy]
lb1.rciam.example.org
lb2.rciam.example.org
 
[db:children]
dbmaster
dbstandby
 
[dbmaster]
db1.rciam.example.org
 
[dbstandby]
db2.rciam.example.org
 
[keycloak]
kc1.rciam.example.org
kc2.rciam.example.org
```

#### Ansible directory layout

```
inventories/
   production/
      hosts.ini             # host inventory for production environment
      files/                # static files such as the Acceptable Use Policy (AUP) / Terms of Use HTML page
      group_vars/
         all/
            main.yml        # common variables for all host groups
            vault.yml       # common secrets for all host groups
         db/
            postgresql.yml  # common variables for DB hosts
         dbmaster/
            postgresql.yml  # variables for master DB node
         dbstandby/
            postgresql.yml  # variables for standby DB node
         keycloak/
            main.yml        # variables for Keycloak nodes
            vault.yml       # secrets for Keycloak nodes
         webproxy/        
            nginx.yml       # nginx variables
      host_vars/
         hostname1.yml      # variables for specific hosts (if needed)
         hostname2.yml
   staging/
      hosts.ini             # host inventory for staging environment
      files/                # static files such as the Acceptable Use Policy (AUP) / Terms of Use HTML page
      group_vars/
         all/
            main.yml        # common variables for all host groups
            vault.yml       # common secrets for all host groups
         db/
            postgresql.yml  # common variables for DB hosts
         dbmaster/
            postgresql.yml  # variables for master DB node
         dbstandby/
            postgresql.yml  # variables for standby DB node
         keycloak/
            main.yml        # variables for Keycloak nodes
            vault.yml       # secrets for Keycloak nodes
         webproxy/        
            nginx.yml       # nginx variables
      host_vars/
         hostname-stg1.yml  # variables for specific hosts (if needed)
         hostname-stg2.yml
 
roles/
    keycloak/
    nginx/
    postgresql/
 
dbservers.yml               # playbook for DB hosts
keycloakservers.yml         # playbook for Keycloak nodes
webproxyservers.yml         # playbook for reverse proxy / load balancers
```

#### Ansible variables  

- Common variables: `<INVENTORY>/group_vars/all/main.yml`
- Keycloak variables: `<INVENTORY>/group_vars/keycloak/main.yml`

##### Secrets

Use [Ansible Vault](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html#ansible-vault) to encrypt variables and files so you can protect sensitive content such as passwords or keys:

- Common secrets: `<INVENTORY>/group_vars/all/vault.yml`
- Keycloak secrets: `<INVENTORY>/group_vars/keycloak/vault.yml`

#### Theme

Each realm in keycloak can have a different theme. The following theme properties are configurable per realm:

- Logo of the login page and account console
- Privacy Policy URL
- Terms of Use / Acceptable Use Policy (AUP)
- Support link
- Footer icons
- Footer text 
  
The theme can be configured by modifying the `keycloak_plugins.wayf.theme.add_to_realms[].XYZ` parameters in the inventory's `{staging,production}/group_vars/keycloak/main.yml` files.  

More specifically, the following parameters can be defined:

- `name`: should match one of the `config.realms[].name` values.
- `terms`: expects an html snippet (i.e. a `<div></div>``) with all the terms of use inside that div. That's injected into specific parts of html pages in order to show appropriately the Terms of use for that specific realm. 
- `config.ribbonText`: If set, it will show a red ribbon on the top left part of the page with that text. It's actually used to notify user about the specific instance targeted use - for instance it can show "Demo" or "Staging". For production deployments, it should be either empty or commented out.
- `config.htmlFooterText`: This can be any html snippet. It will be injected on the page's footer.
- `config.projectLogoIconUrl`: This is either a relative path or absolute URL.
- `config.supportUrl`: Link to support, e.g helpdesk URL or email, like `mailto:support@rciam.example.org`
- `config.privacyPolicyUrl`: Privacy Policy URL.

### Ansible deployment

In order to deploy Keycloak you need to configure all required variables described in the configuration section.

You need to have ssh access from the Ansible control machine to all nodes defined in `<INVENTORY>/hosts.ini`.

On the control machine, you need a recent version of Ansible. We recommend installing Ansible via `pip` as described in https://github.com/rciam/rciam-deploy/tree/master#managed-node-requirements

Deploy Keycloak by executing the keycloak playbook:

```sh
ansible-playbook -u <SSH_USER> -i <INVENTORY>/hosts.ini keycloakservers.yml --vault-password-file <VAULT_PASSWORD_FILES>
```

where:

- `<SSH_USER>`: user (default=root) to connect to the remote keycloak nodes
- `<INVENTORY>/hosts.ini>`: host inventory file 
- `<VAULT_PASSWORD_FILES>`: path to vault password files

The above playbook, will perform the following on the keycloak nodes:

- install the correct java version on the target machines
- create the linux user under which the keycloak service will run
- download keycloak archive, extract and move to the configured folder
- add all required plugins to keycloak, e.g. custom theme, custom mappers, custom Orcid IdP, etc 
- configure standalone-ha.xml file on all keycloak nodes
- create keycloak's admin user 
- setup keycloak as a systemd service
- add a logrotate script and set it run periodically (cron job)
- start keycloak and configure realm properties, including the following:
  - Signing/encryption keys configuration
  - AUP configuration
  - SMTP configuration
  - Federation(s) and their mappers
  - Identity providers and their mappers
  - Theme
