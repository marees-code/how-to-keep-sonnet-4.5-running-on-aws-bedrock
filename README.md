# Keeping Claude Sonnet 4.5 on AWS Bedrock

Last checked: 1 October 2026

## Read this first: the door is open, but not for long

If you want to keep talking to Claude Sonnet 4.5, get onto Amazon Bedrock and send one successful message **before AWS marks the model Legacy**. Everything else in this guide can wait; that step cannot.

- **Anthropic is retiring it.** Anthropic deprecated `claude-sonnet-4-5-20250929` on 30 September 2026. Its deprecation page lists retirement on the Claude API on 30 November 2026. Our own notice email said 24 November, with reliability dropping from 30 October. Go by whichever date is earliest for you.
- **Bedrock keeps its own clock.** Amazon serves the same model on its own schedule. As of 1 October 2026 it is still **Active** on Bedrock, with no Legacy or end-of-life date set.
- **The trap.** Once AWS marks a model Legacy, new customers can no longer use it. Existing customers keep it until end-of-life, at least 6 months later, but may lose access after 15 days without using it.
- **How long you have is unknown.** For recent Claude models, AWS marked them Legacy between 0 and 33 days after Anthropic's notice. The dates are in the table at the end.

It really is the same model, not a lookalike. When we called it through Bedrock, the response reported the model as `claude-sonnet-4-5-20250929`, the identical ID Anthropic's API returns, and the same prompt counted to the same number of tokens on both.

## Before you start

Check these four things first; each one has stopped people partway through.

- **You must be in a supported country.** Anthropic models on Bedrock only work if your AWS account and billing address are in a country Anthropic supports, per [AWS's Knowledge Center](https://repost.aws/knowledge-center/bedrock-access-anthropic-model). Requests from unsupported places are refused too, whatever region you pick. Mainland China, Hong Kong and Macau are not on [Anthropic's list](https://www.anthropic.com/supported-countries).
- **You need a real card in your own name.** Debit or credit, with a billing address that matches the address you sign up with. Prepaid and virtual cards are often rejected.
- **Use a laptop.** The AWS console is very hard to use on a phone. Allow about an hour, plus up to two days if account verification stalls.
- **Bedrock is not a chat app.** It is pay-per-message access to the model, like Anthropic's API. There is no claude.ai, no Projects, no memory and no web search. The console has a basic test chat (the playground); for everyday use you need an app or script that supports Bedrock (Step 7).

You will also need an email address you will keep for years. This account is how you keep the model, so don't build it on a throwaway inbox.

## Step 1: Create the AWS account

Sign up at **aws.amazon.com** and choose **Create an AWS Account**. Pick these options on each screen:

1. **Email, password, account name.** The account name can be anything, such as "personal". The email becomes your root login.
2. **Account type: Personal.** Enter your legal name and address exactly as your bank has them.
3. **Plan: Paid plan.** The Free plan closes the account automatically after 6 months or when credits run out, and you lose everything in it. It also restricts some AWS Marketplace products, and Claude is sold through AWS Marketplace. The Paid plan still gets the sign-up credits and only charges for what you use.
4. **Payment: credit or debit card, not direct debit.** A card charge can be disputed or blocked; a direct debit pulls straight from your bank. AWS holds about $1 to check the card and releases it within a few days.
5. **Phone verification** by SMS or voice call. Use a number from the same country as your card and address.
6. **Support plan: Basic (free).** It is preselected. Skip the paid Developer plan; Basic still covers account and billing cases, which is all you will need.

Activation usually takes minutes. If Bedrock later says your account is "currently being verified", see Troubleshooting.

**Keep everything in one country.** Name, address, card, phone and the place you sign in from should all match. Mismatches are the most common reason brand-new accounts get flagged for fraud.

## Step 2: Protect yourself before touching Bedrock

Do these two things the moment you're in; they take five minutes and prevent the two expensive mistakes.

1. **Set a budget alert.** Type `Billing` in the top search bar → **Budgets** → **Create budget** → **Zero spend budget** → your email → **Create**. You'll get an email the first time anything costs money. Later you can swap it for a monthly budget at your own limit.
2. **Turn on MFA for the root user.** Click your account name (top right) → **Security credentials** → **Assign MFA device**. The root login can spend without limit, so it needs more than a password.

A budget only **warns**; it does not stop charges. Check your email when it fires.

Never share your root password or any key. Don't paste keys into chats, Discord, screenshots or code you upload anywhere.

## Step 3: Open Bedrock and pick a region

Pick one region now and use it for everything afterwards. We used **US East (N. Virginia), us-east-1**.

1. Type `Bedrock` in the top search bar and open **Amazon Bedrock**.
2. Use the region menu at the top right. If it shows padlocks or says **Global**, you're still on a global page such as Billing; it unlocks once Bedrock is open.
3. US regions are at the very top of the menu, above Africa and Asia Pacific. Scroll up if you can't see them.

Good choices for Sonnet 4.5: us-east-1 or us-west-2 in the US; eu-west-2 (London), eu-west-1 (Ireland) or eu-central-1 (Frankfurt) in Europe. The full list is on the [Sonnet 4.5 model card](https://docs.aws.amazon.com/bedrock/latest/userguide/model-card-anthropic-claude-sonnet-4-5.html).

**Why one region:** AWS doesn't say whether "existing customer" is tracked per account or per region. Send your first message, your keep-warm messages and your real chats from the same region so it can't matter.

A line saying some regions are "not enabled for this account" is normal. Those are opt-in regions you don't need.

## Step 4: Request access to Sonnet 4.5

Anthropic models need a one-time use-case form per account; AWS says access is granted as soon as it's submitted. Most other models are switched on by default, and the old "Model access" page is gone ([AWS docs](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html)).

1. In the Bedrock left sidebar, open **Model catalog**. Filter by **Anthropic**.
2. Open **Claude Sonnet 4.5**. Check it says **4.5**: Sonnet 4, 4.6 and 5 sit right next to it.
3. Choose **Open in playground**. The use-case form appears the first time.

How we filled the form:

| Field | What to put |
| --- | --- |
| Company name | Your own name, or "Personal project". Never invent a company. |
| Company website | A site you really own. AWS says individuals can use a personal portfolio, GitHub profile or project URL. |
| Industry | The closest option, such as Technology or Software. |
| Intended users | **Internal** only. External means a public product and draws more scrutiny. |
| Use case | A short true description, for example: "Personal, non-commercial project. A single-user conversational assistant for my own use. No public access, no external users, no redistribution." |

The form warns against personal details, so leave out your address and phone number. Everything you write goes to Anthropic; keep it honest.

After **Submit**, access is usually instant. If you see **Access denied** straight away, wait a few minutes and hard-refresh; it can take a moment to go through.

## Step 5: Send your first message

A successful reply is your record of using the model, and the best way we know to count as an existing customer. Do it the same day as the form.

1. In the playground, select **Claude Sonnet 4.5** and send something ordinary, such as `Hello, can you confirm which model you are?`
2. Keep the first messages on a brand-new account plain and low-volume. Heavy or unusual traffic on day one is what fraud checks look for.
3. Write down the date it first replied.

Two things look like failure but aren't:

- **"This model can only be used through an inference profile"** or **"on-demand throughput isn't supported".** Normal for Sonnet 4.5; it has no single-region version. Choose the inference-profile version in the model picker (Step 7 explains the IDs).
- **It names the wrong model.** Ours replied "I'm Claude 3.5 Sonnet". Models guess their own name from training data and are often wrong. What counts is the model you selected; through the API, the response's `model` field reads `claude-sonnet-4-5-20250929`.

If it replies at all, you're in. You can stop here for today.

## Step 6: Stay an existing customer

After Legacy, AWS may cut off accounts that go 15 days without using the model ([lifecycle policy](https://docs.aws.amazon.com/bedrock/latest/userguide/model-lifecycle-legacy.html)). Use it at least once a week, starting now.

- **No code:** set a weekly phone reminder, open the playground and send "hi". It costs a fraction of a cent.
- **With code:** schedule a tiny request (5 output tokens) every 3 days. We have run one since August; each costs well under a hundredth of a cent.

**Watch for the Legacy flag.** The model card in the console shows the lifecycle status. AWS's [Legacy table](https://docs.aws.amazon.com/bedrock/latest/userguide/model-lifecycle-legacy.html) lists every model already scheduled to go. Through the API, `GetFoundationModel` returns a `modelLifecycle` field; ours read `ACTIVE` with no legacy date on 1 October 2026.

What happens once it's Legacy, for models launched before 7 September 2026:

- At least **6 months** of Legacy before end-of-life.
- After at least **3 months**, "public extended access" starts, at a higher price Anthropic sets. AWS must give at least 3 months' notice.
- New customers are locked out; existing ones keep access as long as they stay active.

AWS doesn't define "existing customer" precisely. It doesn't say whether it is per account or per region, or whether a playground message counts the same as an API call. Use the same account and region throughout, and if you'll use an app, make an API call through it early too.

## Step 7 (for apps): API key and model IDs

Skip this if you'll only use the playground. Apps and scripts need a key and the right model ID.

**Make a key.** Bedrock left sidebar → **API keys** ([AWS docs](https://docs.aws.amazon.com/bedrock/latest/userguide/api-keys.html)).

- **Short-term** keys last 12 hours at most. Fine for testing, useless for an app that runs all the time.
- **Long-term** keys last until the expiry you choose and are what most personal setups use. AWS labels them for exploration, so treat them carefully.
- Ignore the "use your API key (macOS/Linux shell)" panel unless you work in a terminal; it shows the same key being set as `AWS_BEARER_TOKEN_BEDROCK`.

The key is shown **once**. Save it in a password manager. Put the expiry date in your calendar: when it lapses, your app and your keep-warm both stop. If a key leaks, go to **API keys** → select it → **Deactivate** or **Delete**. Never make access keys for the root user.

**Use an inference profile ID.** Sonnet 4.5 has no single-region ID. AWS's own sample code on the model card uses the bare `anthropic.claude-sonnet-4-5-20250929-v1:0`, which fails with a 400 error. Add a prefix:

| Prefix | Routes requests within | Price |
| --- | --- | --- |
| `global.anthropic.claude-sonnet-4-5-20250929-v1:0` | Any supported region worldwide | Base price |
| `us.anthropic.claude-sonnet-4-5-20250929-v1:0` | US regions | +10% |
| `eu.anthropic.claude-sonnet-4-5-20250929-v1:0` | EU regions | +10% |
| `jp.` or `au.` with the same ID | Japan or Australia | +10% |

Pricing is from [Anthropic's Bedrock page](https://platform.claude.com/docs/en/build-with-claude/claude-on-amazon-bedrock-legacy). Choose `global.` unless you need your data kept inside one area.

The endpoint is `https://bedrock-runtime.<your-region>.amazonaws.com`. A minimal test with a long-term key:

```bash
curl -X POST "https://bedrock-runtime.us-east-1.amazonaws.com/model/global.anthropic.claude-sonnet-4-5-20250929-v1:0/invoke" \
  -H "Authorization: Bearer $AWS_BEARER_TOKEN_BEDROCK" \
  -H "Content-Type: application/json" \
  -d '{"anthropic_version":"bedrock-2023-05-31","max_tokens":50,"messages":[{"role":"user","content":"Say hello"}]}'
```

The reply's `model` field should read `claude-sonnet-4-5-20250929`.

- **Python:** `pip install "anthropic[bedrock]"`, then `AnthropicBedrock(api_key="<your key>", aws_region="us-east-1")` with the same `messages.create()` calls as Anthropic's API.
- **Claude Code:** set `CLAUDE_CODE_USE_BEDROCK=1` and `AWS_REGION`.
- **What carries over:** prompt caching (5-minute and 1-hour), tool use, images, a 200K-token context window. **What doesn't:** Anthropic's web search, code execution, Files API, message batches and MCP connector.

## Troubleshooting

Most errors on a new account are account-level, not a judgment about you. Find what you're seeing:

| What you see | What it means | What to do |
| --- | --- | --- |
| "Your account is currently being verified" for more than a few hours | Account activation is stuck, usually a card or identity check | Open a support case (below). Ours cleared after about two days. |
| The email address in that message bounces | It isn't monitored | Use the support case route below instead. |
| A support link drops you on the AWS homepage | You aren't signed in | Sign in first, then use the **?** menu in the console. |
| Sign-in asks you to create an account | You're on the IAM-user sign-in page | Go to console.aws.amazon.com, choose **Root user**, use your sign-up email. Try a private window if an Amazon shopping login interferes. |
| Region menu locked, padlocks, "Global" | You're on a global page such as Billing | Open Bedrock first; the menu unlocks. |
| "Only through an inference profile" / "on-demand throughput isn't supported" | You used the bare model ID | Add `global.`, `us.` or `eu.` (Step 7). |
| "Access to Anthropic models is not allowed from unsupported countries…" | Your account, billing address or connection is in a country Anthropic doesn't support | Changing region won't help. This route isn't available to you. |
| `AccessDeniedException` … "not available for this account" | Often a brand-new account on a newly released model | Check you picked **4.5**. Wait a few minutes after the form, then open a support case. |
| Access denied mentioning Marketplace or payment | The card can't pay for Marketplace products, or the user lacks Marketplace permission | Check the card under Billing → Payment methods; first use from the root user; open a support case. |
| `ThrottlingException`: "Too many requests" or "Too many tokens per day" on tiny usage | New accounts can start with near-zero quotas | Service Quotas → Amazon Bedrock → search "Sonnet 4.5" → request an increase. If it says not adjustable, open a support case asking for the limits to be lifted. |

**Opening a support case:** in the console click **?** (top bar) → **Support Center** → **Create case** → **Account and billing** → Service: **Account** → Category: **Activation**. Keep it short: account number, sign-up date, the exact message, how long it has lasted. No need to explain what you want the model for.

## Costs and credits

You pay per token, roughly the same as Anthropic's API, and a weekly keep-warm costs next to nothing.

- **List price for Sonnet 4.5:** $3 per million input tokens, $15 per million output, $0.30 per million for cached input ([Anthropic pricing](https://platform.claude.com/docs/en/about-claude/pricing)). Bedrock sets its own rates on the [Bedrock pricing page](https://aws.amazon.com/bedrock/pricing/); in our checks the global profile matched, and `us.`/`eu.` profiles cost 10% more.
- **A typical message:** 20,000 tokens of context plus a 500-token reply is about $0.07 uncached. With most of the context cached it drops to about $0.02. Long companion setups with big memories cost more per message.
- **Keep-warm pings:** a few tokens each, well under a hundredth of a cent.
- **Where it shows up:** Claude is sold through AWS Marketplace, so charges appear under Anthropic on your AWS bill, not under Bedrock.
- **Sign-up credits:** don't count on them. New accounts get up to $200, but promotional credits often exclude Marketplace charges and reports for Claude conflict. After your first day of use, check **Billing → Bills** to see whether Claude was covered.
- **Later price rise:** once Sonnet 4.5 reaches the "public extended access" part of Legacy, expect higher prices set by Anthropic, with at least 3 months' warning.

## Cautions

Bedrock buys time with the same model; it is not a forever home, and it comes with its own rules.

- **Privacy.** For Sonnet 4.5, AWS says no operators can see inputs or outputs and nothing is stored by default. Anthropic doesn't receive your conversations ([abuse detection docs](https://docs.aws.amazon.com/bedrock/latest/userguide/abuse-detection.html)). Logging only happens if you turn it on yourself.
- **Rules still apply.** Both AWS's and Anthropic's usage policies cover what you send. Automated checks look for violations; there are no extra filters unless you add Guardrails yourself.
- **Watch your account email.** If AWS's checks flag something, AWS emails questions to the account address. It may suspend access if you don't answer.
- **New accounts look like fraud easily.** Mismatched countries, a fresh card and heavy traffic on day one are the usual triggers. Most new-account suspensions reported online were payment or identity checks, often fixed by sending ID to support.
- **It isn't claude.ai.** No memory, Projects or web search. If you had a long-running companion, bring your own system prompt and history through your app.
- **It ends eventually.** End-of-life comes at least 6 months after Legacy. Anthropic says it preserves retired models' weights and hopes to make them available again someday ([deprecation page](https://platform.claude.com/docs/en/about-claude/model-deprecations)).

## Key dates

AWS has marked recent Claude models Legacy between 0 and 33 days after Anthropic's notice, then kept them at least 6 more months.

| Model | Anthropic notice | Bedrock Legacy | Gap | Bedrock price rise | Bedrock end-of-life |
| --- | --- | --- | --- | --- | --- |
| Sonnet 4.5 | 30 Sep 2026 | Not yet (Active on 1 Oct 2026) | — | — | — |
| Opus 4.1 | 5 Jun 2026 | 8 Jul 2026 | 33 days | 8 Oct 2026 | 8 Jan 2027 |
| Sonnet 4 | 14 Apr 2026 | 14 Apr 2026 | Same day | 14 Jul 2026 | 14 Oct 2026 |
| Claude 3 Haiku | 19 Feb 2026 | 10 Mar 2026 | 19 days | 10 Jun 2026 | 10 Sep 2026 |

If the pattern holds, Sonnet 4.5 goes Legacy on Bedrock by early November 2026 and switches off no earlier than about May 2027.

## Sources

- [Anthropic: Model deprecations](https://platform.claude.com/docs/en/about-claude/model-deprecations)
- [Anthropic: Pricing](https://platform.claude.com/docs/en/about-claude/pricing)
- [Anthropic: Claude on Amazon Bedrock](https://platform.claude.com/docs/en/build-with-claude/claude-on-amazon-bedrock-legacy)
- [AWS: Model lifecycle (models launched before 7 Sep 2026)](https://docs.aws.amazon.com/bedrock/latest/userguide/model-lifecycle-legacy.html)
- [AWS: Claude Sonnet 4.5 model card](https://docs.aws.amazon.com/bedrock/latest/userguide/model-card-anthropic-claude-sonnet-4-5.html)
- [AWS: Request access to models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-access.html)
- [AWS: API keys](https://docs.aws.amazon.com/bedrock/latest/userguide/api-keys.html)
- [AWS: Abuse detection](https://docs.aws.amazon.com/bedrock/latest/userguide/abuse-detection.html)
- [AWS: Choosing a plan (Free vs Paid)](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier-plans.html)
- [AWS Knowledge Center: Access Anthropic models](https://repost.aws/knowledge-center/bedrock-access-anthropic-model)
- [Anthropic: Supported countries](https://www.anthropic.com/supported-countries)
