---
layout: post
title:  "The Fifty-State Data Problem in Child Care"
post_description: "I set out to answer a question: where are the licensed child care providers in the United States? It turned into a learning exercise."
categories: ["Data", "Childcare", "Software", "Policy"]
---

## Fifty ways to show the same stuff

I set out to answer a question: where are the licensed child care providers in the United States? Each state has this information and each puts it somewhere for parents to find it.


So to get the full picture, I built a scraper for each one. The project now covers 49 states and the District of Columbia, and it publishes the combined output as an [open dataset](https://huggingface.co/datasets/ryan-koch/US-Child-Care-Providers). It's structured as 50 modular bits of software, tied together, because the needs of each one are independent of each other.

The data is public in nearly every state. But getting something you can compare across different states is not so easy. That gap looks like a systems problem and one that might not necessarily need a big new complex system to resolve.

## What "public" actually looks like

If you sort the states by how they publish, you get something like a geological record of two decades of government IT procurement.

| How the state publishes | Examples | What it takes to collect |
| --- | --- | --- |
| JSON or REST API behind the search page | Alaska, Connecticut, Georgia, Idaho, Indiana, Iowa, North Dakota, Pennsylvania, Tennessee, Utah, Virginia, Washington, DC | Find the undocumented endpoint and figure out how to progress through it. |
| Open data portal (Socrata) | New York, Delaware | Download it. Nice and easy. |
| Bulk file download (CSV or spreadsheet) | California, Texas, Illinois, Minnesota | Download it. Nice and easy. |
| Salesforce community portal | Arkansas, Montana, New Hampshire, Rhode Island, Massachusetts, Kentucky | Replay the portal's internal calls, often through a real browser. |
| Power BI or Power Platform dashboard | Nevada, Hawaii | Reverse-engineer a reporting tool. |
| Classic server-rendered pages | Maryland, Mississippi, Missouri, Kansas, Louisiana | Walk page by page |
{:class="table table-bordered"}
<br/>
The good news is there are a bunch that are easy. 6 provide an easy to download resource, and another dozen or so expose a clean JSON API behind their search page. It is undocumented, but it is there.

But some of sit behind invisible reCAPTCHAs. In one case, the scraper has to run a real Chrome browser that passes as human, from an internet address in the Eastern time zone. If it fails, the site does not return an error. It returns an empty list, as if the state had no providers.

In another case the site itself has performance limitations. This can mean a run that takes as long as a day or more to complete.
As you might imagine, that adds risk and complexity when things go wrong and I'm trying to get a dataset pushed up.

At times the data is spread across more than one platform. In at multiple examples the scrapers need to pull from separate licensing and quality program portals in order to get something approaching a complete dataset.

And sometimes things change and break things for me downstream. In cases where I'm scraping the search sites directly there's the risk that improvements to UX fundamentally change the page structure, which makes scraping techniques that rely upon it flimsy. So far I've lucked out in that the state sources using APIs or CSVs haven't switched those contracts up on me in a significant way.

These issues and others shouldn't be surprising and shouldn't be seen as malicious. These systems do their best at what was intended: providing reliable information to the folks living in their state.

## Who pays for the fragmentation

**Families.** A parent searching inside one state is mostly fine. Problems happen at the edges. Families who live near a state line, commute across one, or move for work have to learn a second system with different provider types, different inspection language and different definitions of an opening. Building tools to workaround these issues aren't easy to build and maintain when every source is doing its own thing.

**Researchers.** Child care deserts, workforce supply and subsidy reach all require a map of where care actually exists. Today, building that map means rebuilding my project or buying one from a vendor who already did. Even after collection, the records do not line up. When I profiled the combined dataset, I found the same concepts carried under state-specific names: infant openings in some states, total openings in others, accessibility flags in two, structured weekly hours in seven. Fields as basic as whether a provider's address is hidden for safety vary in meaning. Some states even record that flag with the opposite polarity.

The inspection data is more difficult. One state reports a compliance ratio for each visit. Another reports a count of violations. A third reports a status word that has to be translated through a lookup table. All three are real answers to "is this provider safe?" None of them can be compared without judgment calls that should not fall to whoever happens to be writing the scraper (these are hard to make without local context).

**Policymakers.** Any policymaker who asks "how does our situation compare with other states'?" likely gets an answer that's outdated, expensive, or both. In the meantime agencies doing the work to maintain and iterate on these portals can cause downstream tools to break as they try to make them better. States have to gather this data up for reporting requirements, but the details are behind the multitude of unique interfaces.

## What could fix it

A quick instinct would be to build out a Federal database solution. While that would help, getting there could be slow and politically fraught. And there's also the question of the multitude of existing systems that are already providing a service to families. Something smaller could improve things in the meantime.

**1. Publish the list, not just the search.** Every licensing agency could offer a bulk download of its full provider roster on a predictable schedule, in CSV or JSON, with a stable URL. Most states already have the data in a table. New York and Delaware show what this looks like on an open data portal. The search page stays for parents. The file is for everyone else.

**2. Agree on a small common core.** Not every single field, but a selection that matter for cross-state questions: license number, provider name, type, status, address (with a clear flag when it is withheld), coordinates, licensed capacity, ages served, and the date of the last inspection. Even better if it included available capacity. Inspection data could use the same treatment including fields like visit date, visit type, presence of a violation, and a link to a full report. Regardless of which fields and formats — getting to a common guideline would do wonders. A set of consistent details would let families and researchers make sensible comparisons without losing important details.&#32;

**3. Write it into procurement.** Many of these portals were bought, not built. The cheapest moment to require a bulk export, a documented API and the common core is when the contract is written. A clause in an agreement is likely to cost a whole lost less than negotiating a retro-fit after a big SaaS rollout.

**4. Treat bot traffic as feedback** When a state adds CAPTCHA to a public licensing search, it is usually protecting a fragile system from load. That is reasonable. But it also blocks the researchers and civic technologists who would otherwise build on the data. A bulk file removes the reason to scrape and the reason to block. The best situation would be separating these systems such that the load from downloads has no impact on the experience for folks using search.

## A practitioner's takeaway

The encouraging part is that one frustrated person with a bit of free time can bring this sort of data together. But feeling the need to do it is itself a signal. It shouldn’t be necessary to build 50 scraper processes and a normalization layer to make data the states already have useful.

Folks sometimes think about modernization as the splashy project. They see it as something big, new platforms, new software, or paradigm shifts. It’s often better if it’s something boring like: publishing data you already have in a better shape, putting it in a contract requirement so it survives a vendor shift, or adding a bulk download method to spread the load. With child care data we’re already part of the way there, the data is public. We just need to make it easier to use.
