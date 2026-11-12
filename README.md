# Gemini API Pay As You Go: When You Pay and How to Estimate Cost

![Conceptual API request entering a token meter, with two separate funding symbols nearby.](cover.png)

Suppose a prototype starts sending Gemini API requests for real users. The question shifts from whether one prompt works to which balance will pay for the next thousand. “Pay as you go” sounds like a bill that arrives later, but that is not necessarily how a new Gemini Developer API project is funded.

For paid use, Google meters the model work you consume. **The payment plan determines when funds are collected; the usage tier determines the limits you can access; the model price determines the charge for a particular request.** Google currently defaults new paid users to Prepay, so a developer may need to add credits before the first paid call. Some eligible accounts use Postpay. The [Google billing guide](https://ai.google.dev/gemini-api/docs/billing) describes both, but only your own AI Studio billing page can establish your account's current state.

This guide follows a single text request from funding to estimate, then considers a separately published Moduck route. It focuses on the **Gemini Developer API in Google AI Studio**. A Gemini consumer subscription, Gemini CLI quota and Google Cloud Agent Platform have different access or billing surfaces; their names alone cannot tell you who pays this API request.

## What “pay as you go” means for a Gemini API project

Start with the project that owns your API key. On Google's direct route, that project is associated with a billing account and a usage tier. A Free tier can let you experiment, but only with models and usage that the Free tier allows. Moving to a paid tier makes metered, paid model use possible; it does not turn every model into a free or unlimited service. The particular model's published pricing and your active limits still apply.

Google's [billing documentation](https://ai.google.dev/gemini-api/docs/billing), checked November 12, 2026, says new users default to **Prepay**. The documented minimum credit purchase is **$5**. Requests consume the prepaid balance as paid usage is recorded. Unused Prepay credits expire after 12 months, and when the balance reaches zero, linked-project API keys stop serving requests with an HTTP 402 response. Google's page also warns that billing latency can create a small overage before the stop takes effect. These are published rules, not a charge observed on this account.

Eligible **Postpay** accounts instead receive charges after usage, subject to Google's account terms and eligibility. Prepay versus Postpay is therefore about *payment timing*. Free versus paid tier is about *access and capacity*. Neither choice supplies a single universal price: model, processing mode, input/output quantities and any applicable cache or tool charges change the amount. Before treating a model as available in your project, check its current tier and price row.

Do not count Google Cloud's general **$300 Welcome credit** toward an AI Studio Gemini API budget: Google's [free-trial documentation](https://docs.cloud.google.com/free/docs/free-cloud-features) excludes this use. Other promotions may have their own terms, so inspect the actual credit notice rather than assuming every credit is interchangeable.

## Estimate one Gemini 3.1 Pro Preview request

A token is a unit of model text, and pricing is usually quoted per *one million* tokens. A request has at least two quantities: input tokens sent to the model and output tokens returned. Google lists different rates for each. For **Gemini 3.1 Pro Preview, Standard paid tier, prompts at or below 200,000 tokens**, the [published price](https://ai.google.dev/gemini-api/docs/pricing) checked November 12, 2026 is **$2 per million input tokens** and **$12 per million output tokens, including thinking tokens**. The page does not list a free tier for this model. For prompts above 200,000 tokens, that Google Standard row lists **$4 input and $18 output per million**; Batch mode is a separately priced processing mode.

Imagine a text workflow with **10,000 input tokens and 2,000 output tokens** on that Standard bracket. Its base estimate is:

`(10,000 / 1,000,000 × $2) + (2,000 / 1,000,000 × $12) = $0.044`

That is **4.4 US cents for one hypothetical request**, not a measured invoice. At exactly 1,000 identical requests, the simple base estimate is **1,000 × $0.044 = $44**. Real traffic rarely repeats identical token counts. The output count includes any billable thinking tokens; input may include a long conversation or context you did not notice at first. Caching, Google Search or Maps grounding, other tool use, Batch mode, taxes, different prompt lengths and a later price change may alter the result. Use actual usage records to refine the estimate after you have authorized and tested your own account.

The $5 Prepay minimum is a funding requirement, not a $5 charge per request. At the hypothetical base rate, $5 would cover about 113 of these requests *only if* all billable units, settings and terms matched the example and no other usage consumed the balance. Treat that division as a planning illustration, not a capacity guarantee.

## Why a paid balance is not an unlimited quota

An application can have money available and still be throttled. Google's [rate-limit documentation](https://ai.google.dev/gemini-api/docs/rate-limits) separates requests per minute, tokens per minute and requests per day; limits can depend on the model and the billing account's tier, and your project shows its applicable limits. A spend cap or balance is another control: it can interrupt a request even when the per-minute rate limit has not been reached. A request rejected for a rate limit is not solved merely by adding more Prepay credits, and a zero balance is not solved by creating another key in the same project.

Before increasing traffic, inspect **the same project** in AI Studio for its billing plan, remaining credit or postpaid status, model access, current limits and usage dashboard. That check connects the key in your application to the account that will pay. It also gives you a place to reconcile a cost estimate against recorded token counts. This article did not access a signed-in project, so it cannot tell you your actual tier, quota or remaining balance.

## Compare a separate published route carefully

An API aggregator can publish a different price for a model with the same name. The relevant question is whether that separate route supplies the model, features, reliability and billing behavior your application needs. Public prices are valid evidence of *offers*; they do not prove that two routes are interchangeable or that either amount appeared on a real bill.

| Published route, checked November 12, 2026 | Listed model and pricing unit | Input / output quote | Funding note |
| --- | --- | --- | --- |
| [Google](https://ai.google.dev/gemini-api/docs/pricing) direct Developer API | Gemini 3.1 Pro Preview, Standard paid tier, prompt ≤200k, USD per 1M tokens | $2 / $12 (output includes thinking) | New paid users default to Prepay; documented $5 minimum credit purchase. |
| [Moduck](https://moduck.ai/models/c/google/gemini-3.1-pro-preview) separate route | Gemini 3.1 Pro Preview listing, USD per 1M tokens | $0.80 / $4.80; reasoning and caching have separate rows | [Public pricing](https://moduck.ai/pilot/pricing) says $10 minimum top-up and no fixed monthly fee. |

For the same 10,000-input/2,000-output token counts, arithmetic on Moduck's visible **base input/output rows** gives **$0.0176**. That is a posted-rate scenario, not a tested charge or a promise of savings. Google's quote has an explicit Standard and ≤200k bracket; the Moduck listing does not establish an equivalent bracket or identical treatment of thinking, caching and other features. The Moduck page also does not verify callable access in this research. Both published prices can be reported, but a production choice needs account-level access, feature and billing-term checks by the reader under their own authorization. A later authorized model call can test behavior; this article did not perform one.

The different $5 and $10 amounts are minimum funds placed into separate accounts, not matched per-request fees. Compare the per-unit rates only after confirming the processing mode, billable token categories, applicable taxes, model version, availability and whether your application can tolerate the route's behavior.

## Choose a payment route with your own project in view

If you want Google's direct Developer API, first establish whether your project is on Free, Prepay or eligible Postpay, then choose a model and estimate its **input and output separately**. Keep the published price bracket beside your calculation and set a traffic budget that leaves room for longer responses and retries. If you are considering a separate aggregator, use its own price and funding terms, then verify account access and the features your application requires before treating the posted rates as a substitute.

The immediate next step is a check of your actual project and route, not a claim that one public quote is universally cheaper. If Moduck is on your shortlist, [Read the documentation](https://docs.moduck.ai/) and confirm account access before obtaining a key.
