# How to Get Your Google Business Profile Account ID (via Activepieces)

Quick guide to get the `accountId` you need to call Google Business Profile APIs (e.g. `localPosts`, `locations`, etc.), using Activepieces as the OAuth client.

## Prerequisites

- Approved access to the Business Profile APIs in your Google Cloud project (non-zero quota).
- Your Google account already connected via the **Google My Business** piece in Activepieces (OAuth authorized).

## Steps

1. In your Activepieces flow, add an **HTTP Request** step (the generic piece, not the Google My Business one).
2. Configure the step:
   - **Method:** `GET`
   - **URL:** `https://mybusinessaccountmanagement.googleapis.com/v1/accounts`
   - **Authentication:** `Connection` → select your existing **Google My Business** connection (reuses the same OAuth, no need to create a new one).
3. Run **Test step** / **Generate sample data**.
4. You'll get a JSON response like this:

```json
{
  "accounts": [
    {
      "name": "accounts/4306603932123475146",
      "accountName": "My Business",
      "type": "PERSONAL"
    }
  ]
}
```

The number after `accounts/` is your **Account ID**. In this example: `4306603932123475146`.

## Manual shortcut (no API call needed)

You can also get it directly from the browser:

1. Go to [business.google.com/locations](https://business.google.com/locations) while signed in to the right account.
2. Right-click the business → **Copy link address**.
3. The copied URL looks like this:

   ```
   https://business.google.com/n/{ACCOUNT_ID}/profile?fid={LOCATION_ID}
   ```

   Example:

   ```
   https://business.google.com/n/4306603932123475146/profile?fid=5411663991309904725
   ```

   - `4306603932123475146` → **Account ID**
   - `5411663991309904725` → **Location ID** (the one you were already using for `localPosts`)

## Putting it together

With both IDs, you can build your call, e.g.:

```
POST https://mybusiness.googleapis.com/v4/accounts/4306603932123475146/locations/5411663991309904725/localPosts
```

## Notes

- The Account ID doesn't change, so it's worth storing it (variable, storage, spreadsheet) instead of fetching it on every flow run.
- If the HTTP Request returns `401` or `403`, check that the OAuth connection has the `https://www.googleapis.com/auth/business.manage` scope enabled; you may need to reconnect the account.
