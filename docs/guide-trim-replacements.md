# Replacement snippets — drop these in place of each guide's current stake-lock explanation

## desktop-app.mdx — replace "Step 8: What Happens When You're Done"

Keep the step title and the two bullet points on letting the session run out /
the ~24h hold. Replace everything after that with:

```mdx
For the full picture — verified on-chain examples, why this happens, and how to
check it yourself — see [Understanding Session Economics](/native-path/understanding-session-economics).
```

---

## morctl.mdx — replace "Step 7: What Happens When You're Done"

Keep the ⚠️ "Updated" line and the three bullets. Replace the closing paragraph with:

```mdx
See [Understanding Session Economics](/native-path/understanding-session-economics)
for verified on-chain examples of exactly this behavior.
```

---

## headless-developer.mdx — replace "Step 7: Session Lifecycle — What Happens Next"

This guide currently has the most detailed version of this explanation. Trim to:

```mdx
- **Let it expire naturally.** Your node auto-submits the close transaction ~1 minute
  after `endsAt`.
- **Closing only returns the small used-cost portion immediately** — the escrow
  itself requires a separate `withdrawUserStakes` claim. See
  [Understanding Session Economics](/native-path/understanding-session-economics)
  for verified on-chain examples and the full explanation.
- **Don't close early** unless you have a reason to.
```

---

## provider.mdx — replace the "Note on the test session's stake" callout

```mdx
> 🔒 **Note on the test session's stake:** this behaves like any consumer session —
> see [Understanding Session Economics](/native-path/understanding-session-economics)
> for how escrow and claiming actually work. This is separate from your **provider**
> stake in Step 5, which follows different rules.
```
