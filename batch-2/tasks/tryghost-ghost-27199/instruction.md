I'm working on adding gift purchase events to the member activity feed in Ghost.

*   The EventRepository class constructor must accept a Gift model (an object with a findPage method) as part of its options parameter, stored internally for use by getGiftPurchaseEvents.

*   EventRepository must expose a getGiftPurchaseEvents(options, filter) method that calls Gift.findPage with withRelated set to ['buyer', 'tier'] and a hardcoded filter of 'buyer_member_id:-null+custom:true', ignoring any filter or type values passed in via options.

*   When getGiftPurchaseEvents receives an order option containing 'created_at', it must replace all occurrences of 'created_at' with 'purchased_at' before passing the order to Gift.findPage (e.g. 'created_at desc, id desc' becomes 'purchased_at desc, id desc').

*   getGiftPurchaseEvents must return an object with a data array where each element has type set to the string 'gift_purchase_event' and a data object containing: id, amount, currency, cadence, duration (mapped directly from the raw record), tier_name (from the related tier's name field), member_id (from buyer_member_id), and created_at (from purchased_at).

*   The data.member field in each gift_purchase_event must be the buyer object (containing at minimum id, name, and email) when a buyer is present, or null when buyer is null or absent.

*   The data object in each gift_purchase_event must NOT include the fields token, stripe_checkout_session_id, stripe_payment_intent_id, or status.

*   When payment_event is toggled in the event type toggle utility, gift_purchase_event must be included in the same group as payment_event and donation_event — toggling payment_event off must produce an excluded events string containing 'payment_event,donation_event,gift_purchase_event'.


*   Interface details: Type: Class
Name: EventRepository
Location: ghost/core/core/server/services/members/members-api/repositories/EventRepository.js
Description: Repository class for querying member activity events. The constructor options object must now accept a Gift model (an object with a findPage method). The class must expose the following new method:
Signature: getGiftPurchaseEvents(options: object, filter: object) -> Promise<{data: Array<{type: string, data: object}>, meta: object}>

The getGiftPurchaseEvents method must:
- Accept an options object (which may include an order string) and a filter object
- Replace 'created_at' with 'purchased_at' in the order string before querying
- Call Gift.findPage with withRelated: ['buyer', 'tier'] and the hardcoded filter string 'buyer_member_id:-null+custom:true'
- Return { data, meta } where each data item has:
  - type: 'gift_purchase_event'
  - data.id
  - data.amount
  - data.currency
  - data.cadence
  - data.duration
  - data.tier_name (from tier.name of related record)
  - data.member_id (from buyer_member_id)
  - data.created_at (from purchased_at)
  - data.member (buyer object with id/name/email, or null if buyer is absent)
  - Must NOT include: token, stripe_checkout_session_id, stripe_payment_intent_id, status

Type: Function
Name: toggleEventType
Location: ghost/admin/app/utils/member-event-types.js
Description: Existing utility function that toggles event type visibility in the member activity feed. When toggling payment_event, must now include gift_purchase_event in the same group alongside payment_event and donation_event. No change to function signature — the behavior change is internal to how payment_event grouping is handled.
Signature: toggleEventType(eventType: string, eventTypes: string) -> string


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.