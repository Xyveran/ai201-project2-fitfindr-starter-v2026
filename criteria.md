# Acceptance criteria — FitFindr

Five criteria that say what "working" means for this agent, written in unit 3
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"The agent handles errors"* is an opinion.
*"When search returns nothing, the agent stops before calling the second tool,
in 5 of 5 tries"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter one. A reason that says something about your tools, your loop, or the
data earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

**Two are written for you. You write three.**

---

## 1. A matching query completes all three tools

Given a query that matches at least one listing, the agent completes all three
tool calls and returns a fit card — in at least 4 of 5 tries.

**Why this target:** `search_listings` scores matches by plain keyword overlap
between `description` and each listing's text fields, with no synonym or
fuzzy matching. A query phrased differently than a listing's title or
style_tags scores zero even though a person would call it a match.

---

## 2. An impossible query stops before the second tool

Given a query that matches no listings, the agent stops before calling
`suggest_outfit` and returns a message naming what to change — 5 of 5 tries.

**Why this target:** An empty list is a single deterministic condition (score zero for every
listing), and the loop either checks for it and stops or it doesn't. Nothing
about this branch depends on how the query is phrased, so it should be
reliable every time.

---

## 3. The item selected for the outfit call matches the item search found.

For queries returning at least one listing, `session["selected_item"]["id"]` is identical to the `id` of the listing the agent passed as `new_item` to `suggest_outfit()`, in 5 of 5 tries.

**Why this target:** A dict-identity bug would make `suggest_outfit()` and `create_fit_card()` talk about a different item than the item intended. I chose to target 5 of 5 because this is a deterministic check to verify that state is correctly maintained.

---

## 4. The fit card contains price data from the listing dict

The exact `price` value from the listing dict appears as a substring of the caption in 5 of 5 tries.

**Why this target:** The price is a verbatim field in the listing dict that my prompt can directly supply the model. Whether the model chooses to use it in the caption is the only variable, and that results from prompt construction. Since nothing here depends on generation luck, I'm holding it to 5 of 5.

---

## 5. A wardrobe with no items is handled

When supplied with an empty wardrobe the model returns a non-empty string, and does not raise, in 5 of 5 tries.

**Why this target:** Whether `wardrobe['items']` is empty is a single deterministic check in my code, not something the model has to figure out. The only thing left to chance is the wording of the advice, not whether a non-empty, non-raising response comes back at all. Since the branch itself doesn't depend on the model, I expect this path to succeed consistently.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 4 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 4. Something about the fit card

         The fit card is different every time.

         **Why this target:** ...

         > **Revised in unit 4:** For 5 different items, the 5 fit cards share
         > no opening sentence.
         >
         > **Why revised:** "different" wasn't checkable — two cards that
         > differed by one word still counted. The new version is something I
         > can actually score.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said the empty search stops it 5 of 5 times, but I got 3 of 5,
            so 3 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
