# FitFindr

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, every
> command, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> python app.py listings --full -n 6      # read the data (Milestone 1)
> python app.py fields                    # what you can filter on
> python app.py ask 'vintage graphic tee under $30'
> ```
>
> All three tools are stubs, so that last command will do nothing useful yet.
> That's the starting position.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     This is your submission. Fill each section in as you finish the milestone
     it belongs to — don't leave it all to the end.

     Unit 3 asks for the first five sections. Unit 4 adds the five below them.
     Leave the unit 4 sections alone until then; they're here so you know
     what's coming.

     Everything is pasted as TEXT. No screenshots, no images, no video links.
     A typed block of output gets full credit; a picture of the same output
     gets none.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 3 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Three or four sentences: what a user asks for, and what they get back. -->

FitFindr is a thrift-shopping agent. A user describes what they want, like "a vintage graphic tee under $30", with an optional size and max price. The agent searches the listings data for the best keyword match, then suggests outfits that pair the find with pieces from the user's wardrobe. It finishes with a short fit card caption the user could post about the find. If nothing matches, it stops and tells the user instead of calling the other tools.

---

## Tool Inventory

<!-- Four lines per tool. This is worth 2 points and it's the single most
     common place students lose them.

     "Returns a list" earns NOTHING. The description has to say what is IN
     the list.

     The empty case isn't optional either — it's the thing your loop branches
     on, and if you don't decide it here you'll discover it as a crash in
     Milestone 5. -->

### `search_listings`

- **What it does:** Searches listings data for items that match a description, with options for size and max price.
- **Inputs:** `description` (str), `size` (str), `max_price` (float)
<!-- name and type each: `max_price` (float), not "a price" -->
- **Returns:** A list a dictionaries that match the description which include `title` `category` `style_tags` `size` `condition` `price` `colors` `brand` and `platform`.
- **When it has nothing:** If search_listing returns an empty list, put a message in the session and stop. Otherwise take the next suggestion and go to suggest_outfit.

### `suggest_outfit`

- **What it does:** When given an item and the user's wardrobe, it suggests one of two outfits.
- **Inputs:** `new_item` (dict), `wardrobe` (dict)
- **Returns:** A non-empty string with outfit suggestions.
- **When it has nothing:** If given an empty wardrobe, it returns general styling advice.

### `create_fit_card`

- **What it does:** Creates a short caption that someone would post about the find.
- **Inputs:** `outfit` (str), `new_item` (dict)
- **Returns:** A string with a two-to-four sentence caption.
- **When it has nothing:** If `outfit` is empty or whitespace, it returns a descriptive message.

---

## Planning Loop

<!-- Your branch rule, stated as a rule — the condition AND both paths — plus
     the file and function that holds it.

     Like this:
       "If search_listings returns an empty list, put a message in the session
        and stop. Otherwise take the first result and go to suggest_outfit."
        — agent.py::run_agent

     The grader checks your code against what you claim here, so the file and
     function have to be real. -->

**Branch rule:**If search_listings returns an empty list, put a message in the session and stop. Otherwise take the first result and go to suggest_outfit.

**Where it lives:** `agent.py::run_agent`

**How the query is parsed:** regex <!-- regex, string splitting, or asking the model — say which -->

**What moves through the session:** A user's `description` string goes into `search_listings()` with optional `size` and `max_price`. A list of matching dicts out from `search_listings()`. A single `new_item` dict into `suggest_outfit()` with the users `wardrobe`. An `outfit` suggestion string out from `suggest_outfit()` and into `create_fit_card()` with the same `new_item`. `create_fit_card()` lastly returns a string with either a short caption or descriptive message. <!-- which fields, in what order -->

---

## Sample Run

<!-- Two things go here.

     1. One FULL query and its output, pasted as text.
     2. Your three per-tool terminal tests — the command and what it printed. -->

**One full query**

```
$ python app.py ask 'looking for a vintage graphic tee under $30'
```

> Found:    Graphic Tee — 2003 Tour Bootleg Style — $24.0 on depop
> 
> Outfit:   Here are three styling ideas for the **Graphic Tee — 2003 Tour Bootleg Style** using pieces from your wardrobe:

> ### 1. 2000s Streetwear Edge
> *Play up the vintage bootleg aesthetic with a classic denim-on-black combination.*
> * **Bottoms:** Baggy straight-leg jeans (dark wash)
> * **Outerwear:** Vintage black denim jacket 
> * **Shoes:** Chunky white sneakers
> * **Accessories:** Black crossbody bag
> * **Why it works:** The dark wash jeans and black denim jacket create a cohesive, edgy base that lets the faded graphic on the tee pop. Finish with chunky white sneakers to add a 90s/00s retro contrast.
>
> ### 2. Effortless Casual & Grunge
> *Keep it simple, comfortable, and leaning into the "worn-in" feel of the tee.*
> * **Bottoms:** Wide-leg khaki trousers
> * **Accessories:** Brown leather belt
> * **Shoes:** Black combat boots
> * **Why it works:** Tucking the graphic tee into the wide-leg khaki trousers creates an effortless, relaxed silhouette. Accessorizing witha brown leather belt breaks up the tones, while black combat boots ground the outfit with a touch of grunge.
> 
> ### 3. Layered Transitional Look
> *Layer up for cooler weather while keeping the vintage band-tee front and center.*
> * **Top (Layered Underneath):** White ribbed tank top (let the white hem/straps peek out for contrast)
> * **Bottoms:** Baggy straight-leg jeans (dark wash)
> * **Outerwear (tied around waist or worn):** Black cropped zip hoodie
> * **Shoes:** Black combat boots
> * **Accessories:** Black crossbody bag
> * **Why it works:** Layering a white tank underneath adds dimension and color contrast against the black cotton tee. Pair with baggy jeansand combat boots for an authentic, effortless street-style look.
> 
>   Fit card: Found this insanely soft 2003 tour bootleg tee on Depop for just $24 and I'm obsessed with the faded grunge vibe. It has that perfectly boxy, lived-in fit that usually takes years to thrift. Can't wait to style this with baggy denim and combat boots all fall.

**The three tools, tested one at a time**

```
$ python -c "from tools import search_listings; print(search_listings('graphic tee', max_price=30))"

```

> [{'id': 'lst_002', 'title': 'Y2K Baby Tee — Butterfly Print', 'description': 'Super cute early 2000s baby tee with butterfly graphic. Fitted crop length. Tag says medium but fits like a small.', 'category': 'tops', 'style_tags': ['y2k', 'vintage', 'graphic tee', 'cottagecore'], 'size': 'S/M', 'condition': 'excellent', 'price': 18.0, 'colors': ['white', 'pink', 'purple'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_006', 'title': 'Graphic Tee — 2003 Tour Bootleg Style', 'description': 'Vintage-style bootleg tee with faded graphic. Slightly boxy fit. 100% cotton, soft and worn-in.', 'category': 'tops', 'style_tags': ['graphic tee', 'vintage', 'grunge', 'streetwear', 'band tee'], 'size': 'L', 'condition': 'good', 'price': 24.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_017', 'title': 'Mesh Long-Sleeve Top — Black', 'description': 'Sheer black mesh long-sleeve. Great for layering under a graphic tee or over a bralette. Stretchy material, fits true to size.', 'category': 'tops', 'style_tags': ['y2k', 'grunge', 'goth', 'layering'], 'size': 'S/M', 'condition': 'excellent', 'price': 15.0, 'colors': ['black'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_033', 'title': 'Vintage Band Tee — Faded Grey', 'description': 'Faded grey band-style tee with distressed graphic. Crew neck. Fits boxy. Well-loved but no holes or major damage.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'band tee', 'graphic tee', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 19.0, 'colors': ['grey', 'charcoal'], 'brand': None, 'platform': 'depop'}, {'id': 'lst_011', 'title': 'Low-Rise Cargo Pants — Khaki', 'description': 'Y2K era low-rise cargo pants. Lots of pockets. Khaki color, slightly distressed at the hems. Great for layering with a long tee.', 'category': 'bottoms', 'style_tags': ['y2k', 'cargo', '2000s', 'streetwear'], 'size': 'W29', 'condition': 'fair', 'price': 27.0, 'colors': ['khaki', 'tan'], 'brand': None, 'platform': 'poshmark'}, {'id': 'lst_015', 'title': 'Vintage Graphic Hoodie — Faded Black', 'description': 'Faded black pullover hoodie with barely-visible vintage graphic on the chest. Cozy interior. Some pilling but adds to the worn-in look.', 'category': 'tops', 'style_tags': ['vintage', 'grunge', 'graphic', 'streetwear'], 'size': 'L', 'condition': 'fair', 'price': 26.0, 'colors': ['black', 'charcoal'], 'brand': None, 'platform': 'depop'}]

```
$ python -c "from tools import suggest_outfit; from utils.data_loader import get_example_wardrobe, load_listings; print(suggest_outfit(load_listings()[0], get_example_wardrobe()))"
```

> Here are three styling ideas for the new Vintage Levi's 501 Jeans, using pieces from your current wardrobe:

> **1. Casual & Sporty Everyday**
> *   **Top:** White ribbed tank top
> *   **Outerwear:** Oversized grey crewneck sweatshirt (layered over the tank or worn draped over the shoulders)
> *   **Shoes:** Chunky white sneakers
> *   *Why it works:* A medium-wash 501 paired with a crisp white tank and grey crewneck is an effortless, classic off-duty look. The chunky white sneakers tie the white of the tank into the lower half of the outfit for a balanced aesthetic.

> **2. Edgy Contrast**
> *   **Top:** Black cropped zip hoodie
> *   **Shoes:** Black combat boots
> *   **Accessories:** Black crossbody bag
> *   *Why it works:* Pairing the lighter, vintage blue wash of the jeans with head-to-toe black creates a sharp contrast. The cropped fit of the hoodie balances the straight-leg cut of the 501s, while the combat boots give the vintage denim a tougher, modern edge.

> **3. Timeless Casual with Tailored Details**
> *   **Top:** White ribbed tank top
> *   **Accessories:** Brown leather belt, Black crossbody bag
> *   **Shoes:** Black combat boots (or chunky white sneakers depending on your preference)
> *   *Why it works:* Tucking the white ribbed tank into the mid/high-rise 501s and accentuating the waist with the brown leather belt highlights the classic fit of the jeans. It’s a minimalist, 90s-inspired look that lets the vintage character of the denim take center stage.

```
$ python -c "from tools import create_fit_card; from utils.data_loader import load_listings; print(create_fit_card('jeans and white sneakers', load_listings()[0]))"
```

> Finally found the holy grail of worn-in denim without paying vintage store prices—scored these classic Levi's 501s on Depop for just $38. They have that exact effortless, slouchy 90s skater vibe with the best natural fading at the knees. Can't wait to live in these with my beat-up white sneakers all fall.

---

## How I Used AI

<!-- Two specific moments. What you asked, what came back, what you changed.

     "I used Claude to help me code" is not enough.

     "I gave Claude my search_listings spec. It returned None on no match
     instead of an empty list, so I changed it" is the level we want. -->

**Moment 1**

- *What I asked for:* I asked Claude to review my `search_listings` logic for issues, then asked why it scored keywords against only `title` and `description` instead of every key on the listing.
- *What came back:* It found several real bugs: `.upper` was missing its `()` in `_size_tokens`, the size filter passed `["size"]` instead of `listing["size"]`, and the scoring generator iterated over `sorted_listings` while assigning to it. On my follow-up, it explained that matching on `id`, `size`, `condition`, `price`, and `platform` adds noise, but `category`, `style_tags`, `colors`, and `brand` carry real signal. A listing with "vintage" only in `style_tags` would score zero.
- *What I changed:* I fixed the three bugs. I also changed the scoring to build a `listing_aggregate` from the descriptive fields (`title`, `description`, `category`, `style_tags`, `colors`, `brand`) and skip the metadata fields.

**Moment 2**

- *What I asked for:* While writing my acceptance criteria, I asked Claude whether someone could check criteria 4 and 5 without asking me what I meant.
- *What came back:* For 4 and 5 it said no. "Some information about the outfit price" and "general styling advice" are judgment calls two people could score differently. It suggested checking for the exact `price` value as a substring of the caption, and a non-empty string with no exception for the empty wardrobe.
- *What I changed:* I rewrote criterion 3 to compare `session["selected_item"]["id"]` against the `id` passed as `new_item` to `suggest_outfit()`. I rewrote criterion 4 around the exact `price` value appearing in the caption.

<!-- ═══════════════════════ UNIT 4 — THE TEST ═══════════════════════

     Don't fill these in during unit 3.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Run Log — Before

<!-- Five criteria, five tries each, in this exact format.

     Five, because your criteria are written out of five. Mark each try PASS
     or FAIL, count the passes, and read that count against your target — a
     row targeting 4 of 5 with three PASS cells is MISSED (3/5).

     `python run_eval.py --label before` runs everything and writes the table
     into results/. Paste it here and fill in the verdicts. -->

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Real output from one try**, pasted as text, naming the file and function
that produced it:

```

```

---

## Verdicts and Diagnoses

<!-- MET or MISSED per criterion against LAST UNIT's target, plus a sentence on
     how you decided.

     Then, for every miss: which of the four places it happened — a tool, the
     loop's branch, the session, or the model's output — AND the mechanism.

     Not a diagnosis:  "The fit card was bad."
     A diagnosis:      "The fit card criterion missed on 2 of 5 items. Both had
                        an empty brand field. My prompt puts the brand in the
                        first sentence, so the card opened with a blank and read
                        like a fragment. The tool worked; the prompt assumed a
                        field that isn't always there."

     Look for a pattern. Three misses on the same tool is one problem, not
     three. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**



---

## Loop Trace

<!-- One full run, printed step by step, with the MCP call visible in it.

     `python app.py ask '...' --trace` once you've added the trace.step()
     calls in Milestone 2.

     Worth pasting BOTH the happy path and the empty-search path. The empty
     one should be visibly shorter, because it stops. If your two traces are
     the same length, your branch isn't working — and this is the fastest way
     anyone will ever find that out. -->

**Happy path**

```

```

**Empty search**

```

```

**On the MCP move:** <!-- what changed in your code, and whether anything
behaved differently afterwards. If the rewire didn't work, say exactly where it
broke — the error text and the last thing that worked. That earns the point in
full. -->



---

## The Improvement

<!-- What you changed, why your diagnosis pointed at it, and the after-run in
     the same table format. One change, measured properly.

     `python run_eval.py --label after` -->

**What I changed:**

**Which failure it was meant to fix:**

### Run Log — After

| Criterion | Target | Try 1 | Try 2 | Try 3 | Try 4 | Try 5 | Verdict |
|---|---|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |  |  |
| 2.  |  |  |  |  |  |  |  |
| 3.  |  |  |  |  |  |  |  |
| 4.  |  |  |  |  |  |  |  |
| 5.  |  |  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it made things worse, say that. Honestly reported, that earns full
     credit and is more interesting than one that worked. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped where
     you did. "I ran out of time" is fine if it's true. Pretending nothing is
     left is not. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 3

       [ ] criteria.md has five numbered criteria, each with a target
       [ ] Each criterion has a reason underneath it
       [ ] All five unit 3 sections above have real content
       [ ] Tool Inventory: all three tools, inputs WITH TYPES, a specific
           return value, and the empty case
       [ ] Planning Loop names the branch rule and agent.py::run_agent
       [ ] Sample Run: one full query plus the three per-tool tests, as text
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN, you submit the same one
           next unit

     SUBMISSION CHECKLIST — unit 4

       [ ] mcp_server.py exists with one tool registered
           (or a written record of exactly where the rewire broke)
       [ ] Run Log — Before, five criteria, five tries each
       [ ] Real output pasted underneath, naming file and function
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, naming a place AND a mechanism
       [ ] Loop Trace, with the MCP call visible in it
       [ ] All three failure modes triggered and handled
       [ ] One improvement, with Run Log — After in the same format
       [ ] What's Still Broken
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository. Your commit history is what
     shows your criteria existed before your results did.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
