
## Mega themes
- Initiative
- Ways of Working - DoD / DoR
- Make your customer the hero
	- How we have used this in our space to take ownership and drive quality delivery

## Outline
- Welcome
- Introduction to the 42 Series + What is 42 Site
- What is Ownership or Own-yo-Shit TM
	- Delight the customer by:
	- Seeing things through
	- Make things happen
	- Be engaged (no ghosting, find excitement and purpose in problems)
	- Solve problems
	- Make them look good


The three ways - Value Chain thinking 

Systems thinking - a single value chain
- bottleneck determines throughput
- exploit & enable bottleneck
- reduce throughput ahead of bottleneck

Applied: 
Examples - provide numbers e.g. deploy to INT cycle takes x steps
	MR to INT branch --> Diagram this
	Review
	Approval
	Jenkins Deployment
		Message QA
		QA Tests
		QA Response
	Feedback loop - 5 minutes to hours
- Manual testing for devs and testers was the bottleneck
- time and effort overheads to deploy to INT
- Slow feedback loop
- Lots of bugs and handovers between QA and Devs

Unit & Local sanity testing
- introduced patterns for adding effective unit testing for business logic
- local environment + enhancements to make it easy to spin up, test, reset
- made these part of DoD
  
  
Diagram these flows -> 

Business logic: 
	Make change / write unit test
	run tests
	repeat
MR
Review approval

E2E tests:
make change
deploy local environment
	change feedback loop - continuous/instant
MR
Review
Approval

Link to WoW

DOR


DOD


## Script

## **Part 1: Introduction & Theme – “42 and the Principle of Ownership”**

### **1. Welcome & Personal Introduction (±1–2 minutes)**

> **[You speaking]**“Good morning  everyone, and thanks for taking the time to join us today.

For those of you who don’t know me, my name is **Chris Langsford**. I’m a **Technical  Lead** supporting multiple teams in the Insurance and Employee Benefits space at FNB”

“One thing you’ll probably notice about me fairly quickly is that I care deeply about seeing things through—not just starting work, but finishing it in a way that genuinely helps people.”

> **[Introduce co‑host]**

“I’m also joined today by **Roshilla Govender**, who I work very closely with on some of the team at FNB. We’ll both be guiding today’s session, and we’ll share a mix of perspectives and real examples rather than just theory.”


---

### **2. Introducing the Session: “42” (±2 minutes)**

> **Transition**

“Today’s session is called **42**.

Now, before anyone asks—no, this isn’t a maths lesson, and no, you don’t need to be a Hitchhiker’s Guide fan to get value from today.”

_(Pause / light humour)_

“At its core, **42 represents a shared way of thinking and working**. It’s about the principles that guide how we show up every day—how we treat our customers, how we work with each other, and how we take responsibility for the outcomes we’re part of.”

“42 isn’t about rigid rules. It’s about **values in action**. It helps us answer questions like:

- _How do I show up when something goes wrong?_
- _How do I respond when a customer is stuck?_
- _What does ‘doing a good job’ really mean?_”

“And today, we’re focusing on one principle in particular—**Ownership**.”

---

### **3. Introducing the Principle of Ownership (±3 minutes)**

> **Set the tone**

“When we talk about **ownership**, we’re not talking about job titles, blame, or working late every night.

Ownership is much simpler—and much harder—than that.”

**Ownership is the ability to see things through.**  
It’s about understanding your customer’s problems and priorities and **making them your own**.

It looks like:

- Acknowledging an email, message, or call **urgently**, even if you don’t yet have the answer
- Showing energy, urgency, and intent—_not silence_
- Refusing to ghost people, especially when things are uncomfortable
- Owning a problem until it’s actually solved, not just handed off”

> **Why ownership matters**

“Ownership matters because it directly impacts **trust**.

From a customer’s perspective, ownership feels like:

- ‘Someone has this.’
- ‘I’m not alone with this problem.’
- ‘I can rely on this person or team.’”

“And internally, ownership strengthens **accountability and outcomes**. Teams that practice ownership:

- Raise their hands early
- Focus on fixing problems rather than explaining them away
- Look for middle ground instead of defaulting to ‘no’
- Take action—even when the solution isn’t perfect yet”

---


### **4. Definition of Done: Owning Quality (±1 minute)**

“I’ll close this section with a quote that really captures ownership for me:

> **‘If your definition of done goes further than your customer’s, you’ll never disappoint anyone.’**”

“This is where ownership meets **ways of working**.

- A **Definition of Ready** helps us ensure alignment before work even begins
- A **Definition of Done** ensures we’re delivering real value at the right quality level—not just ticking a box”

“Ownership means caring about readiness, clarity, and quality—because that’s what sets both us and our customers up for success.”

---

## Transition to Part 2

“With that foundation in place, we’re going to move from **what ownership means** to **what ownership looks like in action**—using a real example from our team.”

To set the scene, this was shortly after I joined the space nearly 3 years ago. 

Things were on fire in a almost every way. The senior dev on the team had recently rotated off project, leaving me as a cheerleader with no access to anything other than the Teams calls I was invited to.

> **Visual depicting reducing capacity, and increasing expectations”

Shortly afterwards, our QA left the team at short notice. Business wanted new features, and were threatening to roll back the whole project to a legacy system if a number of issues were not resolved asap (We even had daily war rooms dedicated to getting this fixed).


> **Insert "This is fine" Meme here**”

But what they didn't count on, was that the lack of access gave me a birds eye view of the end-to-end value stream. This view often evades us when we dive too deep into details around requirements, or new code bases.

Software Delivery often behaves very similarly to manufacturing, with the SDLC often sharing a lot of features with the modern assembly line. Everything follows a flow through a system to deliver value. Units of work will flow as fast as the slowest point - which we call the bottleneck.

Sitting back and observing helped me spot the bottleneck - manual QA was slow, error prone, and introduced multiple actors into a feedback loop. One reason for this was that quality remained the sole responsibility of the person performing the testing.

I asked myself, what if we could share that ownership of quality across the entire team? What if we could shift the process of owning quality earlier into the SDLC where they are cheaper to fix (less friction + fewer messaging handovers)


> **Visual of old feedback loop from pre-prod**”


So Step 1 - developers were to sanity test their own work on pre-prod before handing their changes over to testers. We immediately saw a drop in the number of bugs being raised because developers were performing basic QA on their own work.

This was introduced to our Definition of Done, with evidence being added to our Pull Requests to show developer testing was performed successfully as a quality gate.

There were multiple iterations in-between to improve efficiency, where we eventually ended up with a comprehensive developer testing approach involving unit testing for core business logic, and an easily re-creatable local environment where developers could sanity test with an almost continuous feedback loop.

>**"Visual of new feedback from local** feedback loop"

Challenges? Convincing business to pull up the handbrake on new feature development, allowing us capacity to focus on driving up quality. You can't fix the leak properly if you don't turn off the water first, after all.

## What were the Outcomes?

Well I am glad you asked! In terms of efficiency, the team improved drastically! Certain categories of bugs and errors that were common disappeared entirely and both our bug and defect rates dropped dramatically! 

This approach even enabled the team to undertake even bigger projects in the space since then. This approach has delighted our client, as we are delivering faster at a much higher level of quality compared to before.


## Key Learnings

- Defining your Team's Ways of Working by having clear Definitions of Ready and Definitions of Done are a useful tool for baking quality into your delivery.
- Things don't have to be this way "because its always been this way" - if you spot a problem and have an idea on how to resolve it, raise your hand, fix it, and own the solution.
