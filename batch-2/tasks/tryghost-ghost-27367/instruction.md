I'm working on the gift subscription feature in a Ghost publication platform.

*   Gift.checkRedeemable(memberStatus) must accept a string-or-null memberStatus parameter and return {redeemable: true} when the gift is in a purchasable state and the member status is null, 'free', or any non-paid status. It must return {redeemable: false, reason: 'paid-member'} when memberStatus is 'paid' or 'comped'. It must return {redeemable: false, reason: 'redeemed'}, {redeemable: false, reason: 'consumed'}, {redeemable: false, reason: 'expired'}, or {redeemable: false, reason: 'refunded'} based on which timestamp (redeemedAt, consumedAt, expiredAt, refundedAt) is set on the gift.

*   Gift.prototype.redeem({memberId, redeemedAt}) must return a new Gift object without mutating the original. The returned gift must have status 'redeemed', redeemerMemberId set to memberId, redeemedAt set to the provided date, and consumesAt calculated by adding duration units of cadence ('month' or 'year') to redeemedAt. Month-end overflow must be handled by carrying over into the following month (e.g. Jan 31 plus 1 month = Mar 3 when February has 28 days; Feb 29 plus 1 year = Mar 1 of the following year).

*   GiftBookshelfRepository.getByToken(token, options) must forward the options object (including transacting and forUpdate fields) directly to GiftModel.findOne as the second argument alongside {require: false}.

*   GiftBookshelfRepository.save(gift, options) must look up any existing row by token using GiftModel.findOne({token}, {require: false, transacting: options?.transacting}). If a row exists, it must call existing.save(snakeCasedGiftData, {transacting: options?.transacting, method: 'update', patch: true}). If no row exists, it must call GiftModel.add(snakeCasedGiftData, {transacting: options?.transacting}).

*   GiftBookshelfRepository.transaction(callback) must delegate to GiftModel.transaction(callback) and return the callback's resolved value.

*   GiftService.getByToken(token) must call giftRepository.getByToken(token) and return the gift. When the repository returns null, it must throw a NotFoundError with the message 'This gift does not exist.'

*   GiftService.getRedeemable(token, memberStatus) must call this.getByToken(token) (propagating NotFoundError on failure) and then call this.assertRedeemable(gift, memberStatus) (propagating any thrown error). It must return the gift when both succeed.

*   GiftService.assertRedeemable(gift, memberStatus) must call gift.checkRedeemable(memberStatus). When checkRedeemable returns {redeemable: false}, it must throw a BadRequestError whose message corresponds to the reason: 'redeemed' → 'This gift has already been redeemed.', 'consumed' → 'This gift has already been consumed.', 'expired' → 'This gift has expired.', 'refunded' → 'This gift has been refunded.', 'paid-member' → 'You already have an active subscription.'. When redeemable, it must return the gift.

*   GiftService.redeem({token, memberId}) must run inside giftRepository.transaction. Within the transaction it must: fetch the gift with giftRepository.getByToken(token, {transacting: trx, forUpdate: true}) and throw NotFoundError('This gift does not exist.') if null; fetch the member with memberRepository.get({id: memberId}, {transacting: trx, forUpdate: true}) and throw NotFoundError('Member not found: <memberId>') if null; assert the gift is redeemable given the member's status (throwing BadRequestError if not); call gift.redeem({memberId, redeemedAt: new Date()}) to produce the redeemed gift; call memberRepository.update({products: [{id: tierId, expiry_at: redeemed.consumesAt}], status: 'gift'}, {id: memberId, transacting: trx}); call giftRepository.save(redeemed, {transacting: trx}); and return the redeemed gift.

*   The GiftRepository interface must declare getByToken(token: string, options?: {transacting?: any, forUpdate?: boolean}): Promise<Gift | null>, save(gift: Gift, options?: {transacting?: any}): Promise<void>, and transaction<T>(callback: (trx: any) => Promise<T>): Promise<T>.


*   Interface details: Type: Class
Name: Gift
Location: ghost/core/core/server/services/gifts/gift.ts
Description: Domain entity representing a gift subscription. The checkRedeemable method must now accept a memberStatus parameter. A new redeem instance method must be added that returns a new Gift without mutating the original.
Signature: checkRedeemable(memberStatus: string | null): {redeemable: true} | {redeemable: false, reason: 'redeemed' | 'consumed' | 'expired' | 'refunded' | 'paid-member'}
Signature: redeem(params: {memberId: string, redeemedAt?: Date}): Gift

---

Type: Class
Name: GiftBookshelfRepository
Location: ghost/core/core/server/services/gifts/gift-bookshelf-repository.ts
Description: Bookshelf ORM adapter for the Gift repository. The getByToken method must forward transaction options to the underlying model. New save and transaction methods must be added.
Signature: getByToken(token: string, options?: {transacting?: any, forUpdate?: boolean}): Promise<Gift | null>
Signature: save(gift: Gift, options?: {transacting?: any}): Promise<void>
Signature: transaction<T>(callback: (trx: any) => Promise<T>): Promise<T>

---

Type: Interface
Name: GiftRepository
Location: ghost/core/core/server/services/gifts/gift-repository.ts
Description: Repository interface for Gift persistence. Must be extended with save and transaction method signatures, and getByToken must be updated to accept transaction options.
Signature: getByToken(token: string, options?: {transacting?: any, forUpdate?: boolean}): Promise<Gift | null>
Signature: save(gift: Gift, options?: {transacting?: any}): Promise<void>
Signature: transaction<T>(callback: (trx: any) => Promise<T>): Promise<T>

---

Type: Class
Name: GiftService
Location: ghost/core/core/server/services/gifts/gift-service.ts
Description: Service layer for gift operations. The existing getRedeemableGiftByToken method must be removed and replaced with decomposed methods. The constructor must NOT accept a labsService dependency (that responsibility moves to GiftController). All new methods are public instance methods.
Signature: getByToken(token: string): Promise<Gift>
Signature: getRedeemable(token: string, memberStatus: string | null): Promise<Gift>
Signature: assertRedeemable(gift: Gift, memberStatus: string | null): Promise<Gift>
Signature: redeem(params: {token: string, memberId: string}): Promise<Gift>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.