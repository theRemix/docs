---
description: Auth0 suggests that you disable sign-ups for the `Initial-Connection` database connection prior to going live in your production environments.
section: appliance
---

# Auth0 Appliance Administration: Disabling Sign-Ups

The Appliance, when running in multi-tenancy mode, behaves just like the Auth0 cloud environment. Users will be able to sign up and create new accounts in the cluster, and this ability is *not* limited to internal users. For this reason, Auth0 suggests that you disable sign-ups for the `Initial-Connection` database connection prior to going live in your production environments.

::: panel-warning Connections for the Auth0 Client
The **Auth0** client in the Root Tenant Authority (RTA) controls access to the Management Dashboard, and the only Connections that you should enable for this client is the built-in Database Connection (the default Connection for this client is a Database Connection called *Initial-Connection*) or an enterprise connection like the AD-LDAP Connector. You should **never** enable a Social Connection for the **Auth0** client.

Additionally, please [disable signups](/appliance/admin/disabling-sign-ups) for the **Auth0** client.
:::

Disabling signups will prevent users from signing up for an account in your Appliance. Therefore, if you want to [invite co-administrators](/appliance/admin/inviting-coadmins) to a specific tenant, you will need to do the following:

* create the user manually using the Dashboard;
* send the newly-created user's credentials to your co-administrator;
* invite the user to the appropriate account.
