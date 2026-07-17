# Launchpad

## **Overview**

**Launchpad** is an application gateway for the Apolo MLOps platform that enables Apolo users to securely expose platform applications to end-users through customizable authentication powered by Keycloak integration.

With Launchpad, you can share access to applications deployed on Apolo—including pre-built apps like OpenWebUI (a user-friendly interface for interacting with LLMs)—without requiring end-users to have full Apolo accounts. Additionally, Launchpad allows you to deploy custom applications using Apolo's Service Deployment feature and expose them to your users with personalized branding, including custom names, descriptions, and logos.

**Key capabilities include:**

* **Bring Your Own Authentication:** Deploy and manage a dedicated Keycloak instance for user authentication, with support for user management, role-based access control, groups, and integration with external identity providers (OIDC, OAuth2)
* **Flexible App Deployment Models:**
  * **Shared Applications:** Deploy a single app instance accessible to all users, with the application leveraging user identity information provided by Keycloak (ideal for apps like OpenWebUI that support multi-user collaboration)
  * **Per-User Applications:** Automatically provision isolated app instances for each user, ensuring complete data separation and personalized environments
* **Custom Branding:** Import custom applications or existing app instances with tailored metadata—display names, descriptions, and visual identities—to create a polished experience for your end-users
* **Flexible App Exposure:** Share both platform-native Apolo applications and custom-deployed services through a unified, user-friendly interface
* **Quickstart Presets:** Deploy pre-configured application stacks like OpenWebUI with all dependencies in minutes
* **External User Access:** Provide seamless application access to clients, stakeholders, or team members outside your Apolo organization through Keycloak-managed authentication

***

## Installing Launchpad

Launchpad can be installed from the Apolo Console interface or using the Apolo CLI.

### Installation via Apolo Console

1. **Navigate to the Apps page** in the Apolo Console.
2. **Find the Launchpad App** and click **Install**.
3. **Configure Launchpad Preset:**
   * Choose a suitable **Resource Preset** for the Launchpad application itself (e.g., `cpu-medium` or higher).
4. **Choose Quickstart Apps:**
   * **No Startup Apps:** Installs only Launchpad, allowing you to add applications later.
   * **OpenWebUI:** Installs OpenWebUI and its dependencies upon Launchpad startup, providing a pre-configured LLM chat interface.

**OpenWebUI Configuration (Optional)**

If you select the **OpenWebUI**, you need to configure the following dependencies:

* **LLM Configuration:**
  * **Pre-configured HuggingFace LLM Model:** Select the desired LLM model (e.g., `meta-llama/Llama-3.1-8B-Instruct`).
  * **Hugging Face Token (Optional):** If using gated or private models, provide a Hugging Face API token stored as an Apolo Secret (`HF_TOKEN`).
  * **LLM Preset:** Choose a resource preset for the LLM model (typically a GPU-enabled preset, e.g., `gpu-a100-x1`).
* **Postgres Configuration:**
  * **Postgres Preset:** Choose a resource preset for the Postgres database (e.g., `cpu-medium`).
  * **Postgres Replicas:** Set the number of replicas (e.g., `1`).
* **Text Embeddings Configuration:**
  * **Text Embeddings Preset:** Choose a resource preset for the embeddings service (e.g., `gpu-l4-x1`).
  * **Embeddings Model:** Select a pre-configured Hugging Face model for text embeddings (e.g., `BAAI/bge-m3`).

5. **Metadata (Optional):** You can customize the app display name.
6. **Click Install.**

Be aware that when choosing the OpenWebUI Quick Start App option, Apolo will also install vLLM, Text Embeddings and Postgresql apps, which will consume credits as well.

### Installation via Apolo CLI

Currently, due to a known bug in the UI, if you choose the OpenWebUI Quick Start App, it's recommended to export the configuration and install it using the Apolo CLI:

1. **In the Launchpad App installation page**, select the **OpenWebUI** Quick Start App and configure your options.
2. Scroll to the bottom and click **Export App Configuration** (download icon).
3. Open your terminal and install the app using the downloaded configuration file:

```bash
apolo app install --file launchpad-custom-app-installation-config.yaml
```

Refer to [Launchpad CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/launchpad.md) page to learn more about installing and managing Launchpad using the CLI.

***

## Managing Launchpad and Keycloak Users

Once Launchpad is installed, you can access the installed app on the **Installed Apps** page. Click **Open** to access various Launchpad URLs.

### Accessing the Launchpad Interface

1. Click **Open** on the Launchpad app card.
2. Select **App URL**.
3.  The Launchpad interface loads, requiring a login.\\

    <figure><img src="../../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
4.  Launchpad comes with a default admin user that can manage what apps are available through Launchpad UI. You can find this admin user's credentials by scrolling down the Launchpad instance details page in Apolo and copying the **Password** field in **Launchpad Default Admin User**\\

    <figure><img src="../../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
5.  Use these credentials to login to Launchpad. You should see a list of available apps - only OpenWebUI for now<br>

    <figure><img src="../../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Managing Users via Keycloak

Launchpad uses Keycloak for authentication and user management.

1. **Access Keycloak:**
   * On the Launchpad app card (in Apolo), click **Open**.
   * Select **Keycloak Config > Web App URL**.
2. **Retrieve Admin Credentials:**
   * Go to the **Launchpad Details** page in the Apolo Console.
   * Under the **Output** section, find **Launchpad Default Admin User** to retrieve the default username (`admin`) and **Password**.
   * Scroll down to **KeycloakConfig** to find the **Keycloak Admin Password** (used for the Keycloak administrative interface).
3. **Log into Keycloak:**
   * Use the Keycloak Admin Password and the username `admin` to sign in to the Keycloak Administration page.
   * Click **Manage Realms**\
     <img src="../../../../.gitbook/assets/image (3).png" alt="" data-size="original">\
     Click **Launchpad**. This will ensure that any settings you change are applied to the correct realm used by Launchpad.\
     ![](<../../../../.gitbook/assets/image (4).png>)
4. **Manage Users and Groups:**
   * **Add User:** Navigate to **Users** and click **Add user** to create new users.
   * **Manage Roles and Groups:** Navigate to **Groups** to create groups and assign roles/permissions to users.
5. **Enable New Sign-Ups (for OpenWebUI):**
   * If you deployed OpenWebUI, ensure users can sign up:
     * Navigate to **OpenWebUI** in the Installed Apps list.
     * Open the **Admin Panel** (available to the admin user).
     * Go to **Settings > Authentication**.
     * Toggle **Enable New Sign Ups** to `On`.
     * **Save** settings.

### Adding an External Identity Provider (OIDC/Social Login)

Launchpad allows you to integrate external Identity Providers (IdP), such as Google or Microsoft, so users can sign in using their existing accounts instead of manually created Keycloak credentials.

#### **Access the Launchpad Realm in Keycloak**

1. Log into the **Keycloak Administration** console.
2. In the top-left dropdown (or via the **Manage Realms** menu), ensure you have selected the **Launchpad** realm.
   * _Note: Do not perform these configurations in the "Master" realm._

#### **Add the Provider**

1. Navigate to **Identity Providers** in the left-hand sidebar.
2. Select your desired provider from the list (e.g., **Google**).
3. **Enter Credentials:** You will need the **Client ID** and **Client Secret** obtained from your provider's developer console (e.g., Google Cloud Console).
4. **Configuration Settings:**
   * **Request refresh token:** Toggle this to `On` to ensure users stay logged in.
   * **Trust Email:** Under the _Advanced settings_ at the bottom, it is recommended to toggle **Trust Email** to `On` if you want Keycloak to verify users based on their provider email.
5. Click **Add**.

#### **Configure Redirect URIs**

To avoid a `redirect_uri_mismatch` error, you must whitelist the Keycloak callback URL in your Identity Provider's settings.

1. In the Keycloak **Identity Provider settings** for the provider you just added, find and copy the **Redirect URI**. It usually looks like this: `https://<launchpad-url>/auth/realms/launchpad/broker/google/endpoint`
2. Go to your **Provider’s Developer Console** (e.g., Google Cloud Console -> APIs & Services -> Credentials).
3. Under **Authorized redirect URIs**, paste the URI you copied from Keycloak.
4. (Optional) Under **Authorized JavaScript origins**, add the base URL of your Launchpad Keycloak instance: `https://<launchpad-url>`
5. **Save** the changes in the provider’s console.

#### **Verify the Integration**

1. Open your **Launchpad App URL**.
2. Click **Log In**.
3. You should now see an option to **Sign in with \[Provider Name]** (e.g., Google) below the standard login fields.\
   ![](<../../../../.gitbook/assets/image (33).png>)
4. Click the button and complete the OAuth flow to verify that you are successfully redirected back to the Launchpad interface.

***

#### Integration Tip:

If you are using **OpenWebUI** with an external IdP, ensure that **Enable New Sign Ups** is toggled to `On` in the OpenWebUI Admin Panel (Settings > Authentication) so that users logging in via the IdP for the first time can have their accounts automatically created.

***

### Accessing App Endpoints via API (Bearer Token)

Launchpad allows you to interact with your deployed applications programmatically. By leveraging the integrated Keycloak instance, you can generate a Bearer Token to securely access your application's API endpoints (such as a vLLM inference server) without using the browser interface.

#### Enable Direct Access Grants in Keycloak

To fetch tokens using user credentials via the API, you must first enable the correct capability in the Keycloak configuration:

1. Log into the **Keycloak Administration** console.
2. Ensure you have selected the **Launchpad** realm.
3. Navigate to **Clients** in the left-hand sidebar and select the **frontend** client.
4. Scroll down to the **Capability config** section.
5. Ensure the **Direct access grants** toggle is set to **On**.
6. Click **Save**.

#### Obtaining an Access Token

You can obtain an access token by sending a POST request to the Launchpad authentication endpoint.

> **Use the Admin API URL:** When performing API authentication, you must use the **Admin API URL** provided in the Apolo Console, not the standard **App URL**. You can distinguish the API URL by the `-api` suffix in the subdomain (e.g., `https://launchpad-xxx-api.apps...`).

**Example using `curl` and `jq`:**

```bash
# Set your Launchpad Admin API URL (the one with the -api suffix)
export LAUNCHPAD_API_URL="https://launchpad-<id>-api.apps.dev.apolo.us"
export LAUNCHPAD_USER="admin"
export LAUNCHPAD_PASSWORD="<your-admin-password>"

# Fetch the token from the /auth/token endpoint
TOKEN_RESPONSE=$(curl -s -X POST "$LAUNCHPAD_API_URL/auth/token" \
  -H "Content-Type: application/json" \
  -d "{
    \"username\": \"$LAUNCHPAD_USER\",
    \"password\": \"$LAUNCHPAD_PASSWORD\",
    \"scope\": \"openid profile email offline_access\"
  }")

# Extract the access token
ACCESS_TOKEN=$(echo $TOKEN_RESPONSE | jq -r '.access_token')

echo "Access Token: $ACCESS_TOKEN"
```

#### Authenticating API Requests

Once you have the `ACCESS_TOKEN`, you can include it in the `Authorization` header of your requests to any application managed by Launchpad. Use the specific **App URL** of the service you are trying to reach (e.g., your vLLM or custom service).

**Example: Accessing vLLM Models Endpoint**

Follow the instructions in [#importing-apps-using-the-admin-panel](launchpad.md#importing-apps-using-the-admin-panel "mention")to import a running vLLM instance to run this example.

```bash
curl -X GET "https://<your-vllm-app-url>/v1/models" \
  -H "accept: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```

> If you attempt to access these endpoints without the `Authorization` header or with an invalid token, Launchpad will deny the request, ensuring your services remain secure.

***

## Importing Apps

### Importing Apps using the Admin Panel

Launchpad comes with a Admin Panel that allows you to manage app templates and running app instances directly through the Launchpad Admin Panel.

{% embed url="https://drive.google.com/file/d/1uxVy_Ry7wX3HShMURBE6mC3WjKmr6Dy6/view?t=8" %}
Video Demo
{% endembed %}

To Access the Admin Pane&#x6C;**:** Log into Launchpad using the default admin credentials (available in app outputs) and click **Admin Panel** (top right).

<div align="left"><figure><img src="../../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure></div>

\
This is what the Admin Panel looks like when there are no templates or instances imported.<br>

<div align="left"><figure><img src="../../../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure></div>

#### **Import a New Template:**

* Obtain an App Template by following [this guide](launchpad.md#obtaining-and-preparing-app-templates-for-launchpad).
*   Click **Import Template**.<br>

    <div align="left"><figure><img src="../../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure></div>
* Click **Upload File** and select the `.yaml` configuration file you prepared in the previous step.
* The fields will automatically populate with information from the Apolo Apps API. You can manually edit the:
  * **Display Name**
  * **Logo URL** (as demonstrated in the video)
  * **Short/Long Description**
  * **Tags**
  * **Input (JSON):** Modify default configuration values.
* Check or uncheck **Shared** based on whether you want a single instance for all users (Shared) or a new instance provisioned for each user (Not Shared).
*   Click **Import Template**. The new template will appear under **App Templates**.<br>

    <div align="left"><figure><img src="../../../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure></div>
*   After importing, the app will be visible in the main Launchpad interface, ready for users to click **Open** and launch their instance.<br>

    <div align="left"><figure><img src="../../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure></div>


*   After installing the app by clicking "Open", the new app instance will also appear in the list of App Instances on the Admin Page<br>

    <div align="left"><figure><img src="../../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure></div>

Using the App Templates list, you can:

* **View:** Displays all metadata and configuration associated with the template.
* **Edit:** Allows modification of the display name, logo, descriptions, tags, and sharing settings.
* **Delete:** Removes the template. **Warning:** Deleting a template will uninstall all associated running instances.

#### **Importing a Running App Instance**

You can also import apps that are already running in Apolo into Launchpad.

* Install a Service Deployment app by following [this guide](launchpad.md#deploying-a-service-deployment-app-that-uses-launchpad-authentication).
* Click **Import App Instance**.
*   The panel will load all running apps in your current Apolo project.<br>

    <div align="left"><figure><img src="../../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure></div>
* Select the desired running app instance (e.g., a Service Deployment).
* Configure the display details (Name, Logo URL, Description).
  * You can customize the app's name, description, logo and etc. For the purposes of this tutorial, we will rename this app to My Custom App
* Click **Import App**.
  * The running app instance will now be visible in the main Launchpad interface under its new name and accessible to authenticated Keycloak users.
  *   Starting from the `v26.7.1` Laucnhpad, the application ingress authentication will be automatically reconfigured to use this launchpad. Meaning, after the app is imported, the users targeting the app will be forced to authorize via this launchpad. If the app was previously imported in other Launchpad instance, this import process will delete it from old Launchpad if it of version `v26.7.1` or later, otherwise — user must do the cleanup manually.<br>

      <div align="left"><figure><img src="../../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure></div>

Using the App Instances list you can:

* **Open:** Directly navigates to the running application URL.
* **Delete:** Uninstalls the specific app instance from Apolo.

### Importing Apps using Admin API

Refer to [Launchpad CLI](../../../../apolo-concepts-cli/apps/installable-apps/available-apps/launchpad.md#importing-apps-using-admin-api) page to learn more about installing and managing Launchpad using the CLI.

## Misc features

#### Logout

The application developers can redirect their users from the apps under Launchpad to the `/logout` path in Launchpad web UI. It will cleanup a browser session for both accessing the launchpad landing page and underlying applications.

Example logout URL: `https://launchpad-123abc123abc.apps.my-cluster.org.apolo.us/logout`

***

## Customizing Launchpad appearance

Launchpad can load a custom CSS stylesheet at runtime. Use it to adapt the Launchpad interface to your organization without rebuilding the application. For example, you can change colors, fonts, borders, spacing, alignment, app card layout, dialogs, forms, tables, and the admin panel.

The custom stylesheet is applied on top of the built-in Launchpad styles. It affects the Launchpad interface only; it does not change the appearance of applications opened from Launchpad.

Use the standard branding settings for the Launchpad title, logo, favicon, and background. Use the custom stylesheet when you need more detailed control over components, layout, typography, or interactive states.

> Only use a stylesheet that you control and trust. A stylesheet can change the entire interface and can request additional fonts, images, and other external resources.

#### Add a custom stylesheet

**Create and host the CSS file**

Create a plain `.css` file. Start with a visible test rule so that you can confirm that the file is loaded:

```css
#launchpad-header {
  border-bottom: 4px solid #7c3aed;
}
```

Host the file at a URL that is accessible from your users' browsers, for example:

```
https://assets.example.com/launchpad/brand.css
```

The URL does not have to end in `.css`; the returned content type determines whether the browser treats the response as a stylesheet.

The URL must meet the following requirements:

* It is publicly reachable without cookies or authentication.
* It uses HTTPS when Launchpad uses HTTPS.
* It returns `200 OK` and a CSS content type such as `text/css`.
* Any fonts or images referenced by the file are also browser-accessible.
* Cross-origin font responses include the required CORS headers.
* If a Content Security Policy is configured, the stylesheet origin is allowed by `style-src`, and referenced assets are allowed by directives such as `font-src` and `img-src`.

> **Use HTTPS directly:** Do not configure an `http://` URL that redirects to HTTPS. Browsers can block the HTTP request as mixed content before following the redirect.

**Configure Launchpad**

1. In the **Apolo Console**, open **Installed Apps**.
2. Select your **Launchpad** instance and open its configuration for editing.
3. In the branding settings, set the custom stylesheet URL to the absolute URL of your CSS file. This value is stored as `branding.css_url`.
4. Apply the configuration and wait until the Launchpad instance is healthy.
5. Open the **Launchpad App URL** and perform a full browser refresh.

The configuration is exposed by the Launchpad Admin API as `branding.css_url`:

```json
{
  "branding": {
    "css_url": "https://assets.example.com/launchpad/brand.css"
  }
}
```

An empty or missing `css_url` disables custom CSS.

To verify the configured value, request `/config` from the **Launchpad Admin API URL** (the URL with the `-api` suffix):

```bash
curl -s "https://<launchpad-admin-api-url>/config" | jq '.branding.css_url'
```

#### Verify that the stylesheet is loaded

1. Open the Launchpad interface.
2. Open the browser developer tools.
3. On the **Network** tab, filter requests by `CSS` and reload the page.
4. Confirm that the configured stylesheet returns `200` and has the `text/css` content type.
5. On the **Elements** tab, find the following element:

```html
<link id="launchpad-custom-stylesheet" rel="stylesheet" href="..." />
```

Launchpad loads this stylesheet after its built-in styles. In most cases, a custom rule that uses a documented `launchpad-*` selector overrides the default rule without `!important`.

***

#### Select the element you want to customize

Launchpad exposes stable CSS hooks for customization:

* IDs such as `#launchpad-header` identify unique page landmarks.
* Classes beginning with `.launchpad-` identify components and their parts.
* Attributes such as `[data-action]`, `[data-section]`, and `[data-state]` identify a component's purpose or state.

Use these hooks instead of Tailwind utility classes, element positions, or generated IDs. Utility classes and third-party component classes are internal implementation details and can change between releases.

For example, the following selector targets every app card:

```css
.launchpad-app-card {
  border: 1px solid #d8dee9;
}
```

The following selector targets only one app. Replace `my-app` with the value of the card's `data-app-name` attribute:

```css
.launchpad-app-card[data-app-name='my-app'] {
  border-color: #7c3aed;
}
```

The following selector targets only the **Open** action inside app cards:

```css
.launchpad-app-card [data-action='open'] {
  background: #7c3aed;
}
```

Use the browser's element inspector to see the hooks available on a specific element.

#### CSS selector reference

**Page layout and background**

| Area                   | Selector                       | What it targets                                              |
| ---------------------- | ------------------------------ | ------------------------------------------------------------ |
| HTML document          | `#launchpad-root`              | The root `<html>` element                                    |
| Page body              | `#launchpad-body`              | The complete Launchpad page                                  |
| Layout                 | `.launchpad-layout`            | The standard Launchpad layout                                |
| Scrollable wrapper     | `.launchpad-layout-wrapper`    | The wrapper around page content                              |
| Main content           | `#launchpad-main`              | The main page region                                         |
| Content width          | `.launchpad-container`         | Shared centered content containers                           |
| Image background state | `.launchpad-background--image` | Root elements when a branding background image is configured |
| Custom stylesheet link | `#launchpad-custom-stylesheet` | The injected external stylesheet link                        |

Launchpad also exposes the following background custom properties:

| Custom property                   | Purpose                            |
| --------------------------------- | ---------------------------------- |
| `--launchpad-background`          | Global background color            |
| `--launchpad-background-image`    | Global background image            |
| `--launchpad-background-size`     | Background sizing, such as `cover` |
| `--launchpad-background-position` | Background alignment               |
| `--launchpad-background-repeat`   | Background repeat behavior         |

The configured branding values are stored in these properties. To replace the rendered background from custom CSS, set the final background properties on the root and body elements:

```css
#launchpad-root,
#launchpad-body {
  background: #f8fafc;
  background-image: none;
}
```

**Header and user menu**

| Area              | Selector                                   | What it targets                      |
| ----------------- | ------------------------------------------ | ------------------------------------ |
| Header            | `#launchpad-header` or `.launchpad-header` | The complete page header             |
| Header content    | `.launchpad-header__container`             | Header width, spacing, and alignment |
| Branding          | `.launchpad-header__brand`                 | Logo and title group                 |
| Logo wrapper      | `.launchpad-header__logo-wrapper`          | Logo container                       |
| Logo              | `.launchpad-header__logo`                  | Custom or default logo               |
| Default logo      | `.launchpad-header__logo--default`         | Default Launchpad logo only          |
| Title             | `.launchpad-header__title`                 | Configured Launchpad title           |
| User menu trigger | `.launchpad-user-panel`                    | User avatar/menu button              |
| Avatar            | `.launchpad-user-panel__avatar`            | Avatar circle                        |
| Initials          | `.launchpad-user-panel__initials`          | User initials inside the avatar      |
| Menu              | `.launchpad-user-panel__popover`           | Open user menu                       |
| User name         | `.launchpad-user-panel__name`              | User display name                    |
| User email        | `.launchpad-user-panel__email`             | User email address                   |
| Admin link        | `.launchpad-user-panel__admin-link`        | **Admin Panel** action               |
| Logout action     | `.launchpad-user-panel__logout`            | **Log out** action                   |

Use `[data-logo='custom']` or `[data-logo='default']` on the branding element when the rule must depend on which logo is displayed.

**Authentication page**

| Area                | Selector                             | What it targets                  |
| ------------------- | ------------------------------------ | -------------------------------- |
| Authentication page | `#launchpad-auth-page`               | The complete login page          |
| Login content       | `.launchpad-auth-page__content`      | Login content width and position |
| Login card          | `.launchpad-auth-page__card`         | Login card surface               |
| Login title         | `.launchpad-auth-page__title`        | Welcome/title text               |
| Login actions       | `.launchpad-auth-page__actions`      | Login button group               |
| Main login button   | `.launchpad-auth-page__login-button` | Main **Log in** button           |

**App catalog and app cards**

| Area            | Selector                                    | What it targets                          |
| --------------- | ------------------------------------------- | ---------------------------------------- |
| App catalog     | `#launchpad-apps-page`                      | The complete app catalog                 |
| App grid        | `.launchpad-app-grid`                       | Grid columns, gaps, and alignment        |
| App card        | `.launchpad-app-card`                       | Every app card, including list states    |
| Loading card    | `.launchpad-app-card--loading`              | App loading skeletons                    |
| Card header     | `.launchpad-app-card__header`               | Logo and heading region                  |
| Logo wrapper    | `.launchpad-app-card__logo-wrapper`         | App logo surface                         |
| Logo            | `.launchpad-app-card__logo`                 | App logo image                           |
| Logo fallback   | `.launchpad-app-card__logo-fallback`        | Default logo when no image is configured |
| Card title      | `.launchpad-app-card__title`                | App display name                         |
| Description     | `.launchpad-app-card__description`          | Short app description                    |
| Tags            | `.launchpad-app-card__tags`                 | Complete tag list                        |
| Tag             | `.launchpad-app-card__tag`                  | Individual visible tag                   |
| Extra tag count | `.launchpad-app-card__tag-overflow`         | The `+N` tag badge                       |
| Actions         | `.launchpad-app-card__actions`              | **Explore** and **Open** action group    |
| Explore action  | `.launchpad-app-card__explore`              | **Explore** button                       |
| Open action     | `.launchpad-app-card__open`                 | **Open** link                            |
| Loading state   | `.launchpad-app-card[data-state='loading']` | Loading cards                            |
| Error state     | `.launchpad-app-grid__error`                | App catalog error message                |
| Retry action    | `.launchpad-app-grid__retry`                | Error retry button                       |
| Empty state     | `.launchpad-app-grid__empty`                | Empty app catalog                        |

Each real app card exposes `[data-app-name]`. This is the safest way to style a specific app independently from the rest of the catalog.

The computed branding color of app cards is available as `--launchpad-app-card-background` for derived rules. Its configured value is set inline. To replace the card surface, set `background` or `background-color` on `.launchpad-app-card`, as shown in the examples below.

**App details dialog and startup page**

| Area              | Selector                                  | What it targets                   |
| ----------------- | ----------------------------------------- | --------------------------------- |
| App details       | `.launchpad-app-modal`                    | Complete app details content      |
| Hero region       | `.launchpad-app-modal__hero`              | Logo, title, and primary action   |
| App logo          | `.launchpad-app-modal__logo`              | Logo image in app details         |
| Title             | `.launchpad-app-modal__title`             | App title                         |
| Short description | `.launchpad-app-modal__short-description` | App summary                       |
| Long description  | `.launchpad-app-modal__long-description`  | Detailed description              |
| Actions           | `.launchpad-app-modal__actions`           | App details actions               |
| Open action       | `.launchpad-app-modal__open`              | **Open** action                   |
| Content section   | `.launchpad-app-modal__section`           | Documentation or references group |
| Section title     | `.launchpad-app-modal__section-title`     | Group heading                     |
| Section link      | `.launchpad-app-modal__link`              | Documentation/reference link      |
| Link label        | `.launchpad-app-link__label`              | Visible link text                 |
| Startup page      | `#launchpad-app-loading-page`             | Page shown while an app starts    |
| Startup content   | `.launchpad-app-loading-page__content`    | Centered startup content          |
| Startup logo      | `.launchpad-app-loading-page__logo`       | Startup page logo                 |
| Startup brand     | `.launchpad-app-loading-page__brand`      | Launchpad brand text              |
| Startup message   | `.launchpad-app-loading-page__message`    | Startup status text               |
| Animated dots     | `.launchpad-app-loading-page__dots`       | Startup loading animation         |

The app details element and startup page also expose `[data-app-name]`. App details sections use `[data-section='documentation']` and `[data-section='references']`.

**Buttons, links, badges, and visual elements**

| Area           | Selector                     | Useful attributes or parts                             |
| -------------- | ---------------------------- | ------------------------------------------------------ |
| Button         | `.launchpad-button`          | `[data-variant]`, `[data-status]`, `[data-with-icon]`  |
| Button icon    | `.launchpad-button__icon`    | Icon inside a button                                   |
| Button spinner | `.launchpad-button__spinner` | Loading spinner inside a button                        |
| Link           | `.launchpad-link`            | `[data-variant]`, `[data-disabled]`, `[data-external]` |
| Link icon      | `.launchpad-link__icon`      | Icon inside a link                                     |
| Badge          | `.launchpad-badge`           | `[data-variant]`                                       |
| Image          | `.launchpad-image`           | Shared images                                          |
| Icon           | `.launchpad-icon`            | Shared icons                                           |
| Spinner        | `.launchpad-spinner`         | `[data-color]` and `.launchpad-spinner__dot`           |
| Logo           | `.launchpad-logo`            | `[data-variant='full']` or `[data-variant='icon']`     |

Button statuses are `idle`, `loading`, and `disabled`. Common button variants include `primary`, `secondary`, `ghost`, `ghost-primary`, `ghost-error`, `error`, `error-outline`, and `primary-outline`.

**Forms**

| Area               | Selector                         | What it targets                 |
| ------------------ | -------------------------------- | ------------------------------- |
| Field wrapper      | `.launchpad-field`               | Complete form field             |
| Input field        | `.launchpad-field--input`        | Input wrapper                   |
| Text input         | `.launchpad-input`               | `<input>` control               |
| Textarea           | `.launchpad-textarea`            | `<textarea>` control            |
| Select             | `.launchpad-select`              | Native or custom select control |
| Field control      | `.launchpad-field__control`      | Any text/select control         |
| Label              | `.launchpad-field__label`        | Field label                     |
| Note               | `.launchpad-field__note`         | Supporting text                 |
| Error              | `.launchpad-field__error`        | Validation message              |
| Checkbox           | `.launchpad-checkbox`            | Checkbox control and label      |
| Checkbox indicator | `.launchpad-checkbox__indicator` | Visible checkbox square         |
| Radio              | `.launchpad-radio`               | Radio control and label         |
| Radio indicator    | `.launchpad-radio__indicator`    | Visible radio circle            |
| Select dropdown    | `.launchpad-select__dropdown`    | Open custom-select menu         |
| Select item        | `.launchpad-select__item`        | Custom-select option            |

Most fields expose `[data-field='<field-name>']` on the wrapper and `[data-invalid='true']` when validation fails. Labels can expose `[data-required='true']` and `[data-disabled='true']`.

**Dialogs, popovers, tooltips, and notifications**

| Area            | Selector                      | What it targets              |
| --------------- | ----------------------------- | ---------------------------- |
| Dialog overlay  | `.launchpad-modal__overlay`   | Page overlay behind a dialog |
| Dialog position | `.launchpad-modal__dialog`    | Positioned dialog wrapper    |
| Dialog surface  | `.launchpad-modal__content`   | Visible dialog card          |
| Dialog header   | `.launchpad-modal__header`    | Dialog header                |
| Dialog title    | `.launchpad-modal__title`     | Dialog title                 |
| Dialog body     | `.launchpad-modal__body`      | Dialog content               |
| Dialog footer   | `.launchpad-modal__footer`    | Dialog actions               |
| Close action    | `.launchpad-modal__close`     | Dialog close button          |
| Popover trigger | `.launchpad-popover__trigger` | Element that opens a popover |
| Popover surface | `.launchpad-popover__content` | Open popover                 |
| Popover arrow   | `.launchpad-popover__arrow`   | Popover arrow                |
| Tooltip surface | `.launchpad-tooltip__content` | Open tooltip                 |
| Tooltip arrow   | `.launchpad-tooltip__arrow`   | Tooltip arrow                |
| Toast region    | `.launchpad-toast-container`  | Notification container       |
| Toast           | `.launchpad-toast`            | Individual notification      |
| Toast content   | `.launchpad-toast__content`   | Notification message         |

Dialogs, popovers, tooltips, and custom-select menus are rendered in portals under the document body. Target them directly instead of nesting their selector under the component that opened them. Their open/closed state is available through `[data-state]`.

**Tables**

| Area        | Selector                   | What it targets       |
| ----------- | -------------------------- | --------------------- |
| Table       | `.launchpad-table`         | Complete shared table |
| Header      | `.launchpad-table__header` | Header group          |
| Body        | `.launchpad-table__body`   | Body group            |
| Row         | `.launchpad-table__row`    | Logical row           |
| Header cell | `.launchpad-table__head`   | Column heading        |
| Body cell   | `.launchpad-table__cell`   | Data cell             |

Table rows use `display: contents`, so backgrounds and borders must be applied to their cells instead of the row wrapper.

**Admin panel**

| Area          | Selector                              | What it targets                    |
| ------------- | ------------------------------------- | ---------------------------------- |
| Admin page    | `#launchpad-admin-page`               | Complete admin panel               |
| Admin title   | `#launchpad-admin-title`              | **Admin Panel** heading            |
| Header        | `.launchpad-admin-header`             | Admin title and refresh action     |
| Templates     | `#launchpad-admin-templates-section`  | App Templates section              |
| Instances     | `#launchpad-admin-instances-section`  | App Instances section              |
| Section       | `.launchpad-admin-section`            | Any major admin section            |
| Section title | `.launchpad-admin-section-title`      | Major section heading              |
| Form          | `.launchpad-admin-form`               | Any admin form                     |
| Form field    | `.launchpad-admin-form-field`         | A field group in an admin form     |
| Form actions  | `.launchpad-admin-form-actions`       | Form action row                    |
| Admin dialog  | `.launchpad-admin-modal`              | Any admin dialog                   |
| Table         | `.launchpad-admin-table`              | Any admin table                    |
| Table row     | `.launchpad-admin-table-row`          | Any admin record row               |
| Table cell    | `.launchpad-admin-table-cell`         | Any admin table cell               |
| Record action | `.launchpad-admin-record-action`      | View, edit, open, or delete action |
| File upload   | `.launchpad-admin-file-upload`        | Template upload control            |
| Confirmation  | `.launchpad-admin-confirmation-modal` | Delete confirmation dialog         |
| Loading       | `.launchpad-admin-loading-state`      | Admin loading state                |
| Error         | `.launchpad-admin-error-state`        | Admin error state                  |

Use semantic attributes to target one admin element without depending on its position:

| Attribute            | Example values                                             | Purpose                         |
| -------------------- | ---------------------------------------------------------- | ------------------------------- |
| `[data-section]`     | `templates`, `instances`, `file-upload`, `confirmation`    | Select a page or dialog section |
| `[data-action]`      | `open-import-template`, `edit-template`, `delete-instance` | Select a specific action        |
| `[data-field-slot]`  | `logo`, `display-name`, `tags`, `internal`, `shared`       | Select a logical form field     |
| `[data-column]`      | `name`, `template`, `state`, `flags`, `actions`            | Select a table column           |
| `[data-record-type]` | `template`, `instance`, `unimported-instance`              | Select a record type            |
| `[data-record-id]`   | Dynamic record ID                                          | Select one record               |
| `[data-record-name]` | Dynamic record name                                        | Select one named record         |
| `[data-row-variant]` | `primary`, `alternate`                                     | Select alternating rows         |
| `[data-flag]`        | `internal`, `shared`                                       | Select an app/template flag     |
| `[data-state]`       | `loading`, `error`, `empty`, `open`, `closed`              | Select UI state                 |

***

#### Customization examples

The following examples can be copied independently or combined into one stylesheet.

**Change the global font, text color, and background**

```css
@font-face {
  font-family: 'My Brand Sans';
  src: url('https://assets.example.com/fonts/my-brand-sans.woff2')
    format('woff2');
  font-display: swap;
}

#launchpad-root,
#launchpad-body {
  background: #f6f7fb;
  background-image: none;
  color: #172033;
  font-family: 'My Brand Sans', Arial, sans-serif;
}
```

Some headings and controls have their own built-in colors. Target their public hooks when they must inherit the global color:

```css
.launchpad-header__title,
.launchpad-app-card__title,
.launchpad-app-modal__title,
.launchpad-admin-title,
.launchpad-admin-section-title {
  color: #172033;
}
```

**Restyle the header and logo**

```css
#launchpad-header {
  background: #111827;
  border-bottom: 1px solid #334155;
}

.launchpad-header__container {
  min-height: 72px;
  padding-top: 16px;
  padding-bottom: 16px;
}

.launchpad-header__logo {
  width: auto;
  height: 48px;
}

.launchpad-header__title {
  color: #ffffff;
  font-size: 20px;
}

.launchpad-user-panel__avatar {
  background: #7c3aed;
}
```

**Restyle the login page**

```css
#launchpad-auth-page {
  background: linear-gradient(135deg, #0f172a, #312e81);
}

.launchpad-auth-page__card {
  padding: 48px;
  background: rgba(255, 255, 255, 0.96);
  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 24px;
  box-shadow: 0 24px 64px rgba(15, 23, 42, 0.3);
}

.launchpad-auth-page__login-button {
  min-width: 220px;
  background: #7c3aed;
  border-radius: 999px;
}
```

**Change the app grid and cards**

```css
.launchpad-app-grid {
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 20px;
}

.launchpad-app-card {
  background: #ffffff;
  border: 1px solid #d8dee9;
  border-radius: 8px;
  box-shadow: 0 8px 24px rgba(15, 23, 42, 0.08);
}

.launchpad-app-card:hover {
  border-color: #7c3aed;
  box-shadow: 0 12px 32px rgba(124, 58, 237, 0.14);
}

.launchpad-app-card__title {
  color: #172033;
  font-size: 18px;
}

.launchpad-app-card__tag {
  color: #5b21b6;
  background: #ede9fe;
}

@media (max-width: 1200px) {
  .launchpad-app-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 640px) {
  .launchpad-app-grid {
    grid-template-columns: 1fr;
  }
}
```

**Restyle one app only**

Inspect the card to find its `data-app-name`, then use it in the selector:

```css
.launchpad-app-card[data-app-name='open-webui'] {
  background: #eef2ff;
  border-color: #6366f1;
}

.launchpad-app-card[data-app-name='open-webui'] .launchpad-app-card__open {
  background: #4f46e5;
}
```

**Restyle buttons and their states**

```css
.launchpad-button[data-variant='primary'],
.launchpad-link[data-variant='primary'] {
  color: #ffffff;
  background: #7c3aed;
  border-radius: 8px;
}

.launchpad-button[data-variant='primary']:hover,
.launchpad-link[data-variant='primary']:hover {
  background: #6d28d9;
}

.launchpad-button[data-status='disabled'] {
  cursor: not-allowed;
  opacity: 0.5;
}

.launchpad-button[data-status='loading'] {
  box-shadow: none;
}
```

**Restyle app details and dialogs**

```css
.launchpad-modal__overlay {
  background: rgba(15, 23, 42, 0.72);
  backdrop-filter: blur(4px);
}

.launchpad-modal__content,
.launchpad-app-modal {
  background: #ffffff;
  border: 1px solid #d8dee9;
  border-radius: 12px;
}

.launchpad-app-modal__section[data-section='references'] {
  padding-top: 24px;
  border-top: 1px solid #e2e8f0;
}

.launchpad-app-modal__link {
  color: #6d28d9;
}
```

**Restyle form fields and validation**

```css
.launchpad-field__control {
  color: #172033;
  background: #ffffff;
  border-color: #cbd5e1;
  border-radius: 8px;
}

.launchpad-field__control:focus {
  border-color: #7c3aed;
  box-shadow: 0 0 0 3px rgba(124, 58, 237, 0.15);
}

.launchpad-field[data-invalid='true'] .launchpad-field__control {
  border-color: #dc2626;
}

.launchpad-field[data-field='logo'] .launchpad-field__label {
  color: #5b21b6;
}
```

**Restyle the admin panel and tables**

```css
#launchpad-admin-page {
  color: #172033;
}

.launchpad-admin-section[data-section='templates'],
.launchpad-admin-section[data-section='instances'] {
  padding: 24px;
  background: #ffffff;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
}

.launchpad-admin-table-header-cell {
  color: #ffffff;
  background: #312e81;
}

/* Rows use display: contents, so apply the background to their cells. */
.launchpad-admin-table-row[data-row-variant='alternate']
  > .launchpad-table__cell {
  background: #f8fafc;
}

.launchpad-admin-table-cell[data-column='actions'] {
  min-width: 180px;
}

.launchpad-admin-record-action[data-action='delete-template'],
.launchpad-admin-record-action[data-action='delete-instance'] {
  color: #b91c1c;
}

.launchpad-admin-form-field[data-field-slot='logo'] {
  padding: 16px;
  background: #f8fafc;
  border-radius: 8px;
}
```

**Restyle popovers, tooltips, and notifications**

```css
.launchpad-popover__content,
.launchpad-tooltip__content {
  color: #ffffff;
  background: #172033;
  border: 1px solid #334155;
}

.launchpad-popover__arrow,
.launchpad-tooltip__arrow {
  fill: #172033;
}

.launchpad-toast {
  color: #ffffff;
  background: #312e81;
  border-radius: 8px;
}
```

***

#### Troubleshooting

**The stylesheet does not appear in the Network tab**

* Confirm that `branding.css_url` is present in the Admin API `/config` response.
* Confirm that the URL is absolute and starts with `https://` on an HTTPS Launchpad deployment.
* Apply the Launchpad reconfiguration and wait until the instance becomes healthy.
* Open a new browser tab or perform a full refresh instead of relying on client-side navigation.

**The request is blocked or returns an error**

* Open the stylesheet URL directly in the browser.
* Confirm that it returns `200 OK` and `Content-Type: text/css`.
* Check the browser console for mixed-content or Content Security Policy errors.
* If the CSS imports fonts, confirm that the font server allows cross-origin requests.

**The file loads, but a rule has no visible effect**

* Inspect the target element and confirm that the selector matches it.
* Use a `launchpad-*` class, ID, or semantic `data-*` attribute from this reference.
* Check the browser's **Styles** panel to see whether a more specific rule wins.
*   Add page or component context instead of immediately using `!important`:

    ```css
    #launchpad-admin-page .launchpad-button[data-variant='primary'] {
      background: #7c3aed;
    }
    ```
* For table row backgrounds, apply the rule to cells because table rows use `display: contents`.

**Changes appear only after clearing the cache**

Browsers and CDNs can cache CSS. Prefer versioned filenames or update a query parameter whenever the file changes:

```
https://assets.example.com/launchpad/brand.css?v=2
```

Update the custom stylesheet URL during Launchpad reconfiguration, then perform a full refresh.

#### Compatibility recommendations

* Treat documented `launchpad-*` selectors and semantic `data-*` attributes as the customization contract.
* Do not depend on Tailwind utility classes, DOM child positions, generated React IDs, or third-party library classes.
* Keep selectors scoped to the smallest relevant Launchpad component.
* Test the stylesheet on desktop and mobile widths.
* Test login, app catalog, app details, app startup, and admin workflows after major CSS changes.
* Keep text contrast, focus indicators, and disabled states accessible.
* Pin or version your stylesheet so that changes can be rolled back quickly.

## Summary

The Launchpad app transforms the Apolo MLOps platform into a user-friendly deployment environment, simplifying application access and user authentication for internal and external users via Keycloak integration. By enabling the import of custom app templates and existing app instances, Launchpad offers a versatile solution for showcasing and managing applications within your ecosystem.

## Quick Guides

#### Obtaining and Preparing App Templates for Launchpad

Before importing a custom application template into Launchpad using the API, you must first obtain the application's configuration file from the Apolo Console and convert it to the required JSON format.

**Downloading the App Configuration File**

This process generates a YAML configuration file based on the app's standard installation page, pre-filled with the Launchpad authentication settings.

1. **Navigate to the App Installation Page:** In the Apolo Console, go to the **All Apps** page and select the application you wish to import (e.g., **Visual Studio Code**). Click **Install**.
2. **Configure Resource Presets:** Select the necessary resource presets for the application.
3. **Configure Authentication:** Scroll down to the **Networking Settings** section and configure the HTTP Ingress:
   * Change the **Authentication** dropdown to **Custom Authentication**.
   * In the **Custom Authentication** section, click **Choose App** next to the `AuthIngressMiddleware` to integrate Launchpad.
   * Select the installed `Launchpad` instance and click **Apply**.
4.  **Download Configuration:** Scroll to the bottom of the installation page. Click the **Export App Configuration** download icon (a down arrow pointing into a cloud or similar icon, typically next to the "Install" button) to download the `.yaml` configuration file.\
    \
    The following yaml file is an example Downloaded from the VSCode App installation page.<br>

    ```yaml
    display_name: ""
    template_name: vscode
    template_version: v25.10.1
    input:
        vscode_specific:
            override_code_storage_mount: null
        extra_storage_mounts: null
        mlflow_integration: null
        preset:
            name: cpu-small
        networking:
            ingress_http:
                auth: {
                    middleware: {
                        type: app-instance-ref, 
                        instance_id: "<launchpad-app-instance-id>", 
                        path: $.auth_middleware
                    }, 
                    type: custom_auth
                }
    ```

**Converting the YAML Configuration to JSON**

If you are using Launchpad API to import templates, follow the instructions below to convert the exported `yaml` file into `json`

The Launchpad API requires the app configuration payload to be in JSON format. Use a command-line tool like `yq` or a combination of `cat` and `python` to perform this conversion.

**Method A: Using `python`**

If you don't have `yq`, you can use Python's built-in YAML and JSON libraries:

1.  **Install PyYAML:** If you haven't already, install the necessary library:

    ```bash
    pip install PyYAML
    ```
2.  **Run the Conversion Script:**

    ```bash
    # Replace [FILE_NAME] with the name of your downloaded YAML file
    python -c "import yaml, json, sys; d=yaml.safe_load(sys.stdin.read()); print(json.dumps(d))" < [FILE_NAME].yaml
    ```

**Method B: Using `yq`**&#x20;

If you have `yq` [installed](https://github.com/mikefarah/yq), this is the simplest method:

```bash
# Replace [FILE_NAME] with the name of your downloaded YAML file
cat [FILE_NAME].yaml | yq -o json
```

The output will be the single-line JSON string containing the app template data, which is ready to be used in the API call to import the template into Launchpad.

Here is the resulting json file.

```json
{
  "display_name": "",
  "template_name": "vscode",
  "template_version": "v25.10.1",
  "input": {
    "vscode_specific": {
      "override_code_storage_mount": null
    },
    "extra_storage_mounts": null,
    "mlflow_integration": null,
    "preset": {
      "name": "cpu-small"
    },
    "networking": {
      "ingress_http": {
        "auth": {
          "middleware": {
            "type": "app-instance-ref",
            "instance_id": "<launchpad-app-instance-id>",
            "path": "$.auth_middleware"
          },
          "type": "custom_auth"
        }
      }
    }
  }
}

```

#### Deploying a Service Deployment App that uses Launchpad authentication

After installing Launchpad, you will be able to deploy Apps that require Launchpad authentication. This small guide will walk you through how you can deploy your custom application using the Service Deployment App.

* Navigate to Apolo Console's **Home Page**, find the Service Deployment app and click **Install**
* Choose a **Resource Preset** (e.g., `cpu-small`).
* **Container Image:** Provide a container image (e.g., `nginx`).
* **Network Configuration:**
  * Enable **HTTP Ingress**.
  * Change **Authentication** to **Custom Authentication**.
  * In **Authentication Middleware**, click **Choose App** and select **Launchpad** with the path&#x20;
  * **Install** the application.
* Import this App Instance into Launchpad so that it enables authentication
  * Follow this guide for Admin Panel usage
  * Follow this guide for API usage

## References

* [Keycloak documentation](https://www.keycloak.org/documentation)
* [OpenWebUI app documentation](openwebui.md)
* [Managing Apps](../../../../apolo-concepts-cli/apps/)
