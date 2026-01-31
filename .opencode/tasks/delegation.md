## CONTEXT
- Overall Goal: Simplify Denuncia Token to 10 characters without hyphens.
- Current Phase: Implementation.
- Completed So Far: Analysis of token usage.
- Dependencies Met: `server/api/denuncia.post.ts` identified as source.

## OBJECTIVE
Update `generateToken` in `server/api/denuncia.post.ts` to generate a 10-character alphanumeric token (uppercase) using the specified alphabet, and update any validation logic to match.

## SCOPE
MUST do:
- Modify `server/api/denuncia.post.ts`:
  - Update `generateToken` to use alphabet `123456789ABCDEFGHJKMNPQRSTUVWXYZ`.
  - Length: 10 characters.
  - No hyphens.
  - Use `crypto.randomInt`.
- Check and modify `app/utils/validation.ts` (if exists and has token regex) to allow 10-char alphanumeric tokens.
- Check and modify `server/api/denuncia/[token].get.ts` or similar if they use regex validation for the route param or internal validation.

MUST NOT do:
- Change other unrelated logic.

## CONSTRAINTS
- Use `crypto.randomInt`.
- Alphabet must exclude 0, O, I, L.

## SUCCESS CRITERIA
- [ ] `generateToken` produces 10-char tokens like "A1B2C3D4E5".
- [ ] No hyphens in token.
- [ ] Validation logic accepts the new format.

## DELIVERABLES
- `server/api/denuncia.post.ts`
- `app/utils/validation.ts` (if modified)

## RETURN FORMAT
Return ONLY:
- Files modified.
- Brief summary of changes.
- Status: COMPLETE.
