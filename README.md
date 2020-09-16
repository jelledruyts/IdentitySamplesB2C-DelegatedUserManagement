# Identity Sample for Azure AD B2C - Delegated User Management

This repository contains a Visual Studio (Code) solution that demonstrates delegated user management using [Azure Active Directory B2C](https://azure.microsoft.com/en-us/services/active-directory-b2c/).

**IMPORTANT NOTE: The code in this repository is _not_ production-ready. It serves only to demonstrate the main points via minimal working code, and contains no exception handling or other special cases. Refer to the official documentation and samples for more information. Similarly, by design, it does not implement any caching or data persistence (e.g. to a database) to minimize the concepts and technologies being used.**

## Setup

- Create custom user attributes in B2C:
  - `CompanyId` (String): The identifier of the user's company.
  - `DelegatedUserManagementRole` (String): The role of the user for the purposes of delegated user management.
  - `InvitationCode` (String): The invitation code that you have received which allows you to sign up.
- Create an API connector towards the invitation redemption API exposed by this application:
  - The API connector should have the endpoint URL defined as `https://<your-host>/api/userinvitation/redeem`.
  - Note that you need a publicly accessible endpoint for this; when running locally you can consider using a tool such as [ngrok](https://ngrok.com/) to tunnel the traffic to your local machine.
- Create user flows for **Sign up and sign in**, **Password reset** and **Profile editing**:
  - For all these flows, use the *recommended* version which gives you access to the API connectors feature.
  - On all these flows, ensure to return at least `CompanyId`, `DelegatedUserManagementRole`, `Display Name`, `InvitationCode` and `User's Object ID` as the **Application claims**.
  - On the **Sign up and sign in** flow, configure the API connector you defined above to run during the **Before creating the user** step.
  - On the **Sign up and sign in** flow, ensure to collect at least `CompanyId`, `DelegatedUserManagementRole` and `InvitationCode` as the **User attributes**. Note that the final values of these attributes will be determined by the user invitation. For now, only user attributes that are explicitly selected here will be persisted to the directory, so if you do not configure these claims here as **User attributes**, they will not be populated with the information from the user invitation! To prevent end user confusion around these fields (which they should ideally never see), you can consider hiding them from the page through a CSS stylesheet.
  - On the **Profile editing** flow, ensure *not* to select `CompanyId`, `DelegatedUserManagementRole` and `InvitationCode` in the **User attributes**; otherwise, users could change their own role for example!
- Create an app registration *for use with B2C*:
  - Allow the Implicit grant flow (for Access and ID tokens).
  - Set the Redirect URI to `https://localhost:5001/signin-oidc` when running locally.
  - Create a client secret and add it to the app settings.
  - Configure **Application Permissions** for the Microsoft Graph with `User.ReadWrite.All` permissions and perform the required admin consent.
- Configure the app settings with all required values from the steps above (e.g. app client id, user flow policy id's, ...)
