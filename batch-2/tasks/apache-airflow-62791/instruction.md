I'm using the Airflow Google Ads integration and running into a frustrating issue.

*   GoogleAdsHook must accept connection extras in a flat format where credential fields (developer_token, refresh_token, client_id, client_secret) are top-level keys in the extras dictionary, not nested under a google_ads_client key.

*   When connection extras contain a google_ads_client key, GoogleAdsHook must continue to use the nested value as the config (backward compatibility with legacy format).

*   When connection extras are in flat format (no google_ads_client key), GoogleAdsHook must successfully create Google Ads service clients via get_service and get_customer_service.

*   When connection extras are in flat format, the hook's search operations must execute without error.

*   When connection extras are in flat format, the hook's list_accessible_customers operation must execute without error.

*   When connection extras are in flat format and a developer_token key is present, the authentication method determination must return 'developer_token'.


*   Interface details: Type: Class
Name: GoogleAdsHook
Location: providers/google/src/airflow/providers/google/ads/hooks/ads.py
Description: The Google Ads hook class whose internal config-loading logic must be updated to support flat-format connection extras (individual credential keys at the top level of extras) in addition to the existing nested google_ads_client format. The method responsible for loading the config from the connection extras must be modified so that when the google_ads_client key is absent, it treats the extras dict itself as the ads config. The _determine_authentication_method method (already existing) must return "developer_token" when called on a hook initialized with flat-format credentials containing a developer_token key.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.