# SharePoint operations with CLI for Microsoft 365

Use the community-maintained [CLI for Microsoft 365](https://pnp.github.io/cli-microsoft365/)
(`m365`) to provision and maintain SharePoint sites, lists and columns with
reviewed, repeatable commands. It is separate from the Power Platform CLI
(`pac`) and Canvas Authoring MCP. Canvas apps use the
[SharePoint connector](https://learn.microsoft.com/en-us/connectors/sharepointonline/)
at runtime; app users do not need the CLI.

## Authenticate and authorize

1. Install an organization-approved CLI version and Node.js LTS if missing;
   verify with `m365 version` and `node --version`.
2. Sign in with an approved Entra application registration and the correct
   tenant using `m365 login --authType browser`; check `m365 status`. See the
   [CLI login guidance](https://pnp.github.io/cli-microsoft365/user-guide/connecting-microsoft-365/).
   Never paste credentials, tokens or device codes into scripts or documentation.
3. Have an administrator review the user's permissions, site-creation policy,
   delegated API scopes and consent. The CLI documents SharePoint
   `AllSites.Manage` for list creation and `AllSites.Write` for adding columns.
   These delegated scopes are tenant-wide, not site-specific; grant only what
   policy permits. Site creation has separate requirements. App consent does
   not replace the user's site permissions; login alone does not prove access.

### Configure and verify the Entra app

1. Run `m365 status` and note the active app ID, signed-in account and tenant
   configuration. Confirm the actual resource tenant independently if the
   configured tenant is `common` or `organizations`. Treat status output as
   organizational information and do not include it in public logs.
2. In [Microsoft Entra admin center](https://entra.microsoft.com), select the
   correct tenant. Under **Entra ID > Enterprise apps > All applications**,
   find the enterprise application by its app ID; under **Security >
   Permissions**, inspect its existing grants.
3. If the registration is owned by your organization, open **Entra ID > App
   registrations > All applications**, select the matching app, and review
   **API permissions**. Add the approved permissions under **SharePoint >
   Delegated permissions**, not Microsoft Graph or application permissions.
   The documented scopes are `AllSites.Manage` for `spo list add` and
   `AllSites.Write` for `spo field add`; check the CLI documentation for each
   operation and request only the approved scopes.
4. An appropriately authorized administrator must review and grant consent
   using **Grant admin consent**, according to tenant policy. Adding configured
   permissions is not itself a consent grant. Tenant-wide consent can affect
   existing grants and exposes broad access on behalf of users; review the
   complete consent screen before accepting. If the registration belongs to
   another publisher or is not editable, coordinate with its owner or use an
   organization-approved registration; do not assume an enterprise app can
   change its registration's requested permissions.
5. Verify the granted resource is **SharePoint**, the permission type is
   **Delegated**, the relevant scopes are present, and the grant applies to the
   intended tenant and user. Verify the signed-in user can manage the target
   site separately. See [Microsoft's admin consent guidance](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/grant-admin-consent)
   and the CLI's [own-identity setup](https://pnp.github.io/cli-microsoft365/user-guide/using-own-identity/).

### Check access and troubleshoot consent

After an approved change, sign in again and check the site without modifying it:

```powershell
m365 login --authType browser
m365 status
$site = 'https://contoso.sharepoint.com/sites/example-app' # Replace with the verified URL.
m365 spo web get --url $site
```

Once the site is accessible, `m365 spo list get` and `m365 spo field list`
check an existing list and its schema. These read checks do not prove that the
account can create a list or add columns. Verify writes only through the
approved provisioning workflow and check their results.

If `AADSTS65001` persists, recheck the client app ID, resource API, delegated
scopes, consent status, account and resource tenant. A successful login or
Microsoft Graph grant alone does not prove a SharePoint consent grant exists.
Follow [Microsoft's consent troubleshooting](https://learn.microsoft.com/en-us/troubleshoot/entra/entra-id/app-integration/troubleshoot-consent-issues)
and ask an administrator to inspect the sign-in logs and consent policy; do not
repeatedly grant broader permissions or expose access tokens while debugging.

## Create and inspect resources

Confirm the target tenant, site ownership, security, alias and environment
before writes. A private team site also creates a Microsoft 365 group. Once its
absence and creation are confirmed, for example:

```powershell
m365 spo site add --type TeamSite --alias example-app --title 'Example App'
```

Use the returned, verified site URL. Check whether a list already exists before
creating it. An authorization or connectivity error is not proof of absence.

```powershell
$site = 'https://contoso.sharepoint.com/sites/example-app' # Replace with the verified URL.
m365 spo web get --url $site
m365 spo list get --webUrl $site --title 'Example Items'
```

If absence is confirmed and the list is approved:

```powershell
m365 spo list add --webUrl $site --title 'Example Items' --baseTemplate GenericList
m365 spo list get --webUrl $site --title 'Example Items'
m365 spo field list --webUrl $site --listTitle 'Example Items'
```

Define columns with stable internal names, types, choice values and required
settings before creation. Use [`m365 spo field add`](https://pnp.github.io/cli-microsoft365/cmd/spo/field/field-add/)
with a reviewed CAML field definition; inspect existing columns first, because
duplicate names fail. For later changes, inspect `m365 spo field set --help full`
and test the change against a nonproduction list. Verify returned schema and
stop on unexpected errors. Review potentially destructive or incompatible
schema changes and backups before applying them.

Use [`m365 spo listitem add`](https://pnp.github.io/cli-microsoft365/cmd/spo/listitem/listitem-add/)
and [`m365 spo listitem set`](https://pnp.github.io/cli-microsoft365/cmd/spo/listitem/listitem-set/)
for approved administrative item operations. Resolve actual internal field
names and types first; use only approved test data and preserve existing records.

## Connect and verify

In Power Apps Studio's **Data** pane, add the approved site and list; refresh
after schema changes. Confirm actual internal names and types, particularly
choice and multi-choice values, before writing Power Fx. Use `SubmitForm` or
`Patch` with required-field validation and explicit error handling; show
success and reset inputs only after the write succeeds.

Test authorized and restricted users, invalid input and failed writes. Sharing
an app does not grant list access; a canvas filter is not a security boundary.
Review DLP, licensing, delegation and list indexing. Provision lists and
permissions separately in each environment: a Power Platform solution alone
does not provision the SharePoint list. Review any data migration separately
and preserve the previous source until migration and rollback are agreed.
