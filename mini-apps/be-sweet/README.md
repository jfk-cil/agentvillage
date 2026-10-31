# Be Sweet: contract

A generic (public read) mini-app. Three MCP tools, all read-only, all with a closed empty input schema.

| Operation | MCP tool | Sends | Returns |
| --- | --- | --- | --- |
| `be-sweet.prompts` | `be_sweet_prompts` | `{}` only | Three questions and three sentence starters (each stops mid-sentence for the person to finish), a line for when the words won't come, a privacy note, how to send a voice affirmation, an optional app link (null unless set), two rules |
| `be-sweet.how_to_share` | `how_to_share_a_story` | `{}` only | A step-by-step walk-through: an AI-disclosure first line, an invitation, `remember` (four gentle questions to answer privately, sentence starters, story shapes labeled as shapes, a line for when the words won't come), `ways` (the share page first, then the postcard maker, a Telegram direct message to the Be Sweet team, a Gold Star in the community chat), `consent` (default: on the Signs of Love page without a name; or just for the Be Sweet team; or with a first name), `delete_code`, `what_happens_next`, rules, and the Be Sweet ethos line. No counts. |
| `be-sweet.signs` | `signs_of_love` | `{}` only | The public Signs of Love page in its own words, from one GET of its `/v1/signs` feed (contract `be-sweet-signs/1`): an AI-disclosure line, the as-of date, the six "what came back to us" rows (label, words and note exactly as served), the postcard line, the hearts-made and hearts-shared lines, the wall's words with every name field removed, `how_counted`, and rules. Never a number the page doesn't show, never a total. |

**Service.** `service.kind: mcp`, `auth.mode: none`, protocol `2025-06-18`, JSON answers (no event stream, no sessions, no redirects). Requests over 4 KiB are refused; answers stay well under 32 KiB. A Cloudflare Worker on the owner's personal account, free plan.

**What it keeps.** Nothing. No storage of any kind (no KV, no database), no logging (`observability` off, no `console` calls, no tail), no cookies. Its one outbound call is a GET of the public Signs of Love feed for `signs_of_love`, which sends nothing about the caller; the answer is kept in the service's memory for five minutes, then dropped. Because every tool takes no arguments, a call carries no identity and no data from the resident or the conversation. Cloudflare sees the calling server's IP at its edge, as any host does; the service never receives or records it.

**Third parties.** None receives data from this app. The `how_to_share_a_story` answer links the Be Sweet share page (the bakery's own Cloudflare Worker, run by the Be Sweet team) and names Telegram as ways a person may choose, on their own, to reach the bakery. Nothing is sent to either by the app or the agent. `signs_of_love` only reads the bakery's public Signs of Love feed; it sends no arguments, no identity and nothing from the conversation.

**The resident's own conversation.** Agent Village's consent brief applies to what a resident tells their own agent: authorized staff and research personnel can read conversations, and messages pass to AI model providers. Be Sweet is built so nothing personal needs to be said to the agent: the prompts are answered by the resident for themselves, and the skill tells the agent not to ask for answers, names or stories, and to say why if the resident starts.

**Stories.** The app never accepts a story. A person who chooses to tell one uses the share page or messages ECI Loves Goa Baking Co. directly. On the share page the words are sealed in the browser so only the bakery can read them, kept until November 30, 2026, then deleted unless the teller chooses "keep", and shown on the public Signs of Love page only if the teller chose that. The share page now defaults to the Signs of Love page without a name; nothing appears until the Be Sweet team has read it, and the teller keeps a delete code. On Telegram, the Be Sweet team keeps it until November 30, 2026, then deletes it unless the teller says "keep". Nobody under 18.

**Signs of Love.** The agent may use `signs_of_love` only when the resident asks how Be Sweet is going or what has come back to the Be Sweet team. It passes on each row in the page's own words, one at a time, as given, and may read a few wall items as they are. It must never add rows or lines together, state or work out a total, turn words like "a few" into a number, quote a count that is not in the answer, name anyone or guess who wrote a wall item, and it says "and the stories keep coming", never "and counting". The wall carries words only: `name`, `first_name` and every other field are dropped, even where the storyteller agreed to a first name on the page. Counts on the page refresh once a day; it is never a live counter. If the feed does not answer, the tool returns `unavailable` ("The page didn't answer just now; try again in a minute."), never a made-up count.

**Money.** None. Everything is a gift; the tool descriptions tell the agent never to mention prices, money, tips, wallets or payment.

**Errors.** Any argument returns an `isError` result carrying exactly `{"code": "invalid_arguments", "detail": "..."}`. When the Signs of Love feed does not answer (or is not linked), `signs_of_love` returns an `isError` result carrying exactly `{"code": "unavailable", "detail": "..."}`. An unknown tool is a JSON-RPC error `-32602`. The service has no rate limit of its own and no other error codes.

**Other routes.** `GET /` (a human page), `GET /stand` (a small embeddable card, no numbers) and `GET /v1/stand` (the same as JSON). These are not mini-app operations.

**Retention.** The service will be taken down or re-reviewed by November 30, 2026. A new version bumps the manifest version.

**Owner.** John F. Kim (GitHub `jfk-cil`). Personal project of Love Your Neighbor Baking Co.
