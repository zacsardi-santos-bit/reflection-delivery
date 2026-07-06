Implement a new section in the back-office administration panel for managing offer price limitation rules. Ensure that only administrators with the appropriate permissions can access and manage these rules, including viewing, creating, editing, and deleting them.

*   Create a new database model:
    *   Name: `OfferPriceLimitationRule`
    *   Location: `api/src/pcapi/core/offers/models.py`
    *   Fields: `id` (auto-increment integer primary key), `subcategoryId` (text, non-null, unique), `rate` (numeric with precision 5 and scale 4, non-null)
    *   Table name: `offer_price_limitation_rule`

*   Add a factory class:
    *   Name: `OfferPriceLimitationRuleFactory`
    *   Location: `api/src/pcapi/core/offers/factories.py`
    *   Default values: `subcategoryId` of `ACHAT_INSTRUMENT`, `rate` of `Decimal(0.3)`

*   Register a Flask blueprint:
    *   Name: `offer_price_limitation_rules`
    *   Location: `api/src/pcapi/routes/backoffice/offer_price_limitation_rules/blueprint.py`
    *   URL prefix: `/offer-price-limitation-rules`
    *   Required permission: `PRO_FRAUD_ACTIONS`
    *   Reject unauthenticated users and those without the required permission

*   Implement the `list_rules` endpoint:
    *   Type: GET
    *   Return: HTTP 200 with an HTML table
    *   Columns: 'ID', 'Catégorie', 'Sous-catégorie', 'Limite de modification de prix'
    *   Rate format: '± XX,XX %'
    *   Support filtering by 'category' and 'subcategory' query parameters

*   Implement the `create_rule` endpoint:
    *   Type: POST
    *   Accept form fields: 'subcategory' (ID string), 'rate' (French locale percentage)
    *   Store rate as input value divided by 100
    *   On success: HTTP 303 redirect with flash 'La nouvelle règle a été créée'
    *   On duplicate subcategory: HTTP 303 redirect with flash containing 'Erreur dans la création de la règle'

*   Implement the `get_delete_offer_price_limitation_rule_form` endpoint:
    *   Type: GET
    *   Accept: `rule_id` URL parameter
    *   Return: HTTP 200 with a deletion confirmation form

*   Implement the `delete_rule` endpoint:
    *   Type: POST
    *   Accept: `rule_id` URL parameter
    *   Permanently remove the rule from the database
    *   Return: HTTP 303 redirect with flash 'La règle sur la sous-catégorie {subcategory_pro_label} a été supprimée'

*   Implement the `edit_rule` endpoint:
    *   Type: POST
    *   Accept: `rule_id` URL parameter and 'rate' form field
    *   Valid rate range: 0–999
    *   Store rate as input value divided by 100
    *   On success: HTTP 303 redirect with flash 'La règle sur la sous-catégorie {subcategory_pro_label} a été modifiée'
    *   On invalid rate: HTTP 303 redirect leaving rule unchanged

*   Implement GET handlers for form rendering:
    *   Create form: GET at `/create`, return HTTP 200
    *   Edit form: GET at `/<int:rule_id>/edit`, return HTTP 200

*   Register the blueprint in `api/src/pcapi/routes/backoffice/__init__.py` as `offer_price_limitation_rule_blueprint`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.