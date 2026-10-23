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

## Getting the Location ID from the browser

The profile URL gives you the **Location ID** (the "Business Profile ID" shown in Google's UI), **not** the Account ID:

1. Go to [business.google.com/locations](https://business.google.com/locations) while signed in to the right account.
2. Right-click the business → **Copy link address**.
3. The copied URL looks like this:

   ```
   https://business.google.com/n/{LOCATION_ID}/profile?fid={FID}
   ```

   - `{LOCATION_ID}` → the number right after `/n/`. This is the **Location ID** used in `.../locations/{locationId}/...` (e.g. `localPosts`).
   - `{FID}` → an internal Google identifier. Its meaning isn't documented and the API doesn't use it; don't use it as the Location ID.

The **Account ID** is not part of this URL — get it with the API call in the previous section.

## Putting it together

With the Account ID (from `/v1/accounts`) and the Location ID (from the `/n/` URL), build your call:

```
POST https://mybusiness.googleapis.com/v4/accounts/{ACCOUNT_ID}/locations/{LOCATION_ID}/localPosts
```

## Notes

- `Account ID`, `Location ID` and `fid` are all different numbers that look alike; don't swap them.
- The Account ID doesn't change, so it's worth storing it (variable, storage, spreadsheet) instead of fetching it on every flow run.
- If the HTTP Request returns `401` or `403`, check that the OAuth connection has the `https://www.googleapis.com/auth/business.manage` scope enabled; you may need to reconnect the account.
