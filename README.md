# proxyidp-nagios-scripts

## List of Local scripts
Local scripts are located in /usr/lib/check_mk/local/

###  php_syntax_check.sh
* Attributes to be filled:
<pre>
# List of paths to check separated by space
paths=""
</pre>

###  git_pull_check.sh
* Attributes to be filled:
<pre>
# The root directory to check
dir=""
</pre>

### services_running_check.sh
* Attributes to be filled:
<pre>
# List of service names separated by space
services=""
</pre>

### Proxy idp authentication test - local
There are two main scripts (one of them uses SAML, the other uses OIDC) checking the login to SP via the host from which the scripts run and some helper scripts located in folder `proxy_idp_auth_test_script/`
* Main scripts:
    * proxy_idp_auth_test_saml.sh
    * proxy_idp_auth_test_oidc.sh
* Helper scripts:
    * proxy_idp_auth_test_script/saml_auth_test_cesnet.sh
    * proxy_idp_auth_test_script/saml_auth_test_muni.sh
    * proxy_idp_auth_test_script/oidc_auth_test_cesnet.sh
    * proxy_idp_auth_test_script/oidc_auth_test_muni.sh
* Requirements:
    * library *bc*
        <pre>
        apt-get install bc
        </pre>
    * Configuration file proxy_idp_auth_test_config.sh in the same folder as script
        * Attributes to be filled:
            <pre>
            # The urls of tested SP
            # For example: https://aai-playground.ics.muni.cz/simplesaml/nagios_check.php?proxy_idp=cesnet&authentication=muni
            MUNI_SAML_TEST_SITE=""          # Needed only for SAML
            CESNET_SAML_TEST_SITE=""        # Needed only for SAML
            MUNI_OIDC_TEST_SITE=""          # Needed only for OIDC
            CESNET_OIDC_TEST_SITE=""        # Needed only for OIDC

            # The url of logins form of used IdP
            # For example: https://idp2.ics.muni.cz/idp/Authn/UserPassword
            MUNI_LOGIN_SITE=""
            CESNET_LOGIN_SITE=""

            # Fill in logins
            MUNI_LOGIN=""
            CESNET_LOGIN=""

            # Fill in passwords as string
            MUNI_PASSWORD=""
            CESNET_PASSWORD=""

            # Fill in the instance name
            # Instance name must not contain a space
            INSTANCE_NAME=""

            # Fill in the global domain name of ProxyIdP
            # For example: login.cesnet.cz
            PROXY_DOMAIN_NAME=""

            # How long is normal for total roundtrip (seconds)
            SAML_WARNING_TIME=10        # Needed only for SAML
            OIDC_WARNING_TIME=15        # Needed only for OIDC

            # Timeout time
            TIMEOUT_TIME=40
            </pre>

### ldap_status.sh
This script checks if the LDAP servers are accessible

* Requirements:
    * library *ldap-utils*
        <pre>
        apt-get install ldap-utils
        </pre>
* Attributes to be filled:
    <pre>
    # LDAP username
    USER=""

    # LDAP password
    PASSWORD=""

    # Base dn of LDAP tree
    BASEDN=""

    # eduPersonPrincipalName which the script will look for
    IDENTITY=""

    # List of LDAP HOSTNAMES separated by whitespace
    # Each value must start with ldap:// or ldaps://
    # For example: "ldaps://hostname.com ldap://hostname.com"
    HOSTNAMES=""
    </pre>

### rpc_status.sh
This script checks if the RPC server is accessible and if RPC returns valid user

* Requirements:
    * library *bc*
        <pre>
        apt-get install bc
        </pre>
* Attributes to be filled:
    <pre>
    # RPC username
    USER=""

    # RPC password
    PASSWORD=""

    # RPC domain with authentication method
    # Example: "perun.cesnet.cz/krb"
    DOMAIN=""

    # Valid userId - This id will be used in getUserById call
    USER_ID=""
    </pre>



## List of plugins
Plugins are located in /usr/lib/check_mk/plugins/

## Nagios active scripts
Active scripts are located in Nagios machine

### Proxy idp authentication test - active
There are two main scripts (one uses SAML, the other uses OIDC) checking the login via active ProxyIdP machine and some helper scripts located in folder `proxy_idp_auth_test_script/`
* Main scripts:
    * proxy_idp_auth_test_active_saml.sh
    * proxy_idp_auth_test_active_oidc.sh
* Helper scripts:
    * proxy_idp_auth_test_script/saml_auth_test_cesnet_active.sh
    * proxy_idp_auth_test_script/saml_auth_test_muni_active.sh
    * proxy_idp_auth_test_script/oidc_auth_test_cesnet_active.sh
    * proxy_idp_auth_test_script/oidc_auth_test_muni_active.sh
* How to run these scripts:
    * Params:
        * 1 - The url of tested SP via MU account
        * 2 - The url of login form of MU IdP
        * 3 - MU Login
        * 4 - MU Password
        * 5 - The url of tested SP via CESNET account
        * 6 - The url of login form of CESNET IdP
        * 7 - CESNET Login
        * 8 - CESNET Password
        * 9 - Roundtrip time (in seconds) - The standard login time. After this time the return value can be changed to WARNING state
        * 10 - Timeout time (in seconds) - After this time the helper script timeouts
    * Examples:
        <pre>
        ./proxy_idp_auth_test_active_saml.sh "https://aai-playground.ics.muni.cz/simplesaml/nagios_check.php?proxy_idp=cesnet&authenticate=muni" "https://idp2.ics.muni.cz/idp/Authn/UserPassword" "login" "passwd" "https://aai-playground.ics.muni.cz/simplesaml/nagios_check.php?proxy_idp=cesnet&authenticate=cesnet" "https://idp2.ics.muni.cz/idp/Authn/UserPassword" "login" "passwd" 10 40
        ./proxy_idp_auth_test_active_oidc.sh "https://aai-playground.ics.muni.cz/simplesaml/nagios_check.php?proxy_idp=cesnet&authenticate=muni" "https://idp2.ics.muni.cz/idp/Authn/UserPassword" "login" "passwd" "https://aai-playground.ics.muni.cz/simplesaml/nagios_check.php?proxy_idp=cesnet&authenticate=cesnet" "https://idp2.ics.muni.cz/idp/Authn/UserPassword" "login" "passwd" 15 40
        </pre>

### mariadb_replication_check.sh
This script checks the database replication

* How to run this script:
    * Params:
        * 1 - Login used for connection to the database
        * 2 - Password used for connection to the database (the password has to be in quotes)
        * 3 - List of addresses separated by space (the list has to be in quotes)
    * Example:
        <pre>
        ./mariadb_replication_check.sh "USER" "PASSWORD" "Address1 Address2 Address3"
        </pre>
